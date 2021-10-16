# 问题：Android Handler移除Message详解及实例代码
问题：

1.removeMessage(what)函数是否只能移除对应what值的Message？答：能

2.对于Delayed发送的Message，能否提前remove？

## 测试代码

```java
package javine.k.testhandler; 
 
import android.app.Activity; 
import android.os.Bundle; 
import android.os.Handler; 
import android.os.HandlerThread; 
import android.os.Message; 
import android.util.Log; 
import android.view.View; 
import android.view.View.OnClickListener; 
import android.widget.Button; 
 
public class TestHandlerActivity extends Activity implements OnClickListener { 
 
  private Button startBtn; 
  private Button endBtn; 
  public Handler threadHandler; //子线程Handler 
 
  private Handler mHandler = new Handler() { 
    public void handleMessage(android.os.Message msg) { 
      threadHandler.sendEmptyMessageDelayed(1, 2000); 
      Log.d("info", "handle main-thread message..."); 
    }; 
  }; 
 
  @Override 
  protected void onCreate(Bundle savedInstanceState) { 
    // TODO Auto-generated method stub 
    super.onCreate(savedInstanceState); 
    setContentView(R.layout.main); 
 
    startBtn = (Button) findViewById(R.id.startButton); 
    endBtn = (Button) findViewById(R.id.endButton); 
    startBtn.setOnClickListener(this); 
    endBtn.setOnClickListener(this); 
 
    new Thread(new Runnable() { 
      @Override 
      public void run() { 
        HandlerThread handlerThread = new HandlerThread("handler"); 
        handlerThread.start(); 
        threadHandler = new Handler(handlerThread.getLooper()) { 
          @Override 
          public void handleMessage(Message msg) { 
            //mHandler.sendEmptyMessageDelayed(0, 2000);
			mHandler.sendEmptyMessageDelayed(1, 2000); 
			Log.d("info", "handle sub-thread message...");
          }
        };
      }
    }).start();
  }
    
    @Overridepublic 
    void onClick(View v) 
	{
        // TODO Auto-generated method stubs
        witch (v.getId()) 
        {
            case R.id.startButton: 
				//开始发送消息
         		mHandler.sendEmptyMessage(1);
         		break;
         	case R.id.endButton:
				//移除主线程Handler的消息
            	mHandler.removeMessages(1);
            break;
            	default:
            break;
            }
        }
	}
```

## 测试结果

1. removeMassage（1）无法移除what=0的Message。



## 在子线程中执行完

```java
mHandler.sendEmptyMessageDelayed(1, 2000); 
```

 之后，即可通过removeMesage(1)来移除消息，mHandler将不能接收到该条消息

## **源码分析**

1.Android如何移除一条Message？

查看源码可知，Handler.removeMessage(int what)内部调用MessageQueue.removeMessage(this, what, null)

查看MessageQueue的removeMessage方法如下：

```java
void removeMessages(Handler h, int what, Object object) { 
    if (h == null) { 
      return; 
    } 
 
    synchronized (this) { 
      Message p = mMessages; 
 
      // Remove all messages at front. 
      while (p != null && p.target == h && p.what == what 
         && (object == null || p.obj == object)) { 
        Message n = p.next; 
        mMessages = n; 
        p.recycle(); 
        p = n; 
      } 
 
      // Remove all messages after front. 
      while (p != null) { 
        Message n = p.next; 
        if (n != null) { 
          if (n.target == h && n.what == what 
            && (object == null || n.obj == object)) { 
            Message nn = n.next; 
            n.recycle(); 
            p.next = nn; 
            continue; 
          } 
        } 
        p = n; 
      } 
    } 
  } 
```

筛选要移除的Message的条件是：target（handler），what，object

该函数分两步来移除Message：

1）.移除在前端的符合条件的Message

2）.移除后面的符合条件的Message

2.为何延迟发送的Message在延迟时间到达之前就可以被移除？

Handler.sendEmptyMessageDelayed() ---调用---> sendMessageAtTime() -----调用---> enqueueMessage() ----调用MessageQueue.enqueueMessage()

实际进行处理的就是MessageQueue，源码如下：

```java
boolean enqueueMessage(Message msg, long when) { 
    if (msg.isInUse()) { 
      throw new AndroidRuntimeException(msg + " This message is already in use."); 
    } 
    if (msg.target == null) { 
      throw new AndroidRuntimeException("Message must have a target."); 
    } 
 
    synchronized (this) { 
      if (mQuitting) { 
        RuntimeException e = new RuntimeException( 
            msg.target + " sending message to a Handler on a dead thread"); 
        Log.w("MessageQueue", e.getMessage(), e); 
        return false; 
      } 
 
      msg.when = when; 
      Message p = mMessages; 
      boolean needWake; 
      if (p == null || when == 0 || when < p.when) { 
        // New head, wake up the event queue if blocked. 
        msg.next = p; 
        mMessages = msg; 
        needWake = mBlocked; 
      } else { 
        // Inserted within the middle of the queue. Usually we don't have to wake 
        // up the event queue unless there is a barrier at the head of the queue 
        // and the message is the earliest asynchronous message in the queue. 
        needWake = mBlocked && p.target == null && msg.isAsynchronous(); 
        Message prev; 
        for (;;) { 
          prev = p; 
          p = p.next; 
          if (p == null || when < p.when) { 
            break; 
          } 
          if (needWake && p.isAsynchronous()) { 
            needWake = false; 
          } 
        } 
        msg.next = p; // invariant: p == prev.next 
        prev.next = msg; 
      } 
 
      // We can assume mPtr != 0 because mQuitting is false. 
      if (needWake) { 
        nativeWake(mPtr); 
      } 
    } 
    return true; 
  } 

```

由上可知：MessageQueue会对需要延迟发送的Message排序，按照需要延迟的时间长短（when）。

即，虽然是延迟发送的消息，其实当你调用发送函数之后，Message就已经被添加到MessageQueue中去了。