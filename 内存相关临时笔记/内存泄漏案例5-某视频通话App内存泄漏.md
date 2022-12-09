# 内存泄漏案例5-某App121个内存泄漏

视频通话App是屏端实时通讯功能的App，提供语音通话、视频通话、好友列表查询、好友添加等功能。

笔者在工作之余编写内存泄漏案例分析的时候，抓取了一份海尔666型号下的视频通话App内存Hprof文件，发现有该App存在121个Fragment/Activity类型内存泄漏点，下面我们来分析下这些内存泄漏问题。

## 影响范围

某App以下页面

![hprof-视频通话影响范围](http://cdn.yangchaofan.cn/typora/hprof-视频通话影响范围.jpg)

下面我们来分析下666型号的内存问题。

## 复现步骤

1. 屏端进入视频通话App，每个页面快速进入退出10次，在每个页面随机点10次该页面内的按钮
2. 手机智家App发起音频通话10次，发起视频通话10次，发起群组通话10次；
3. 操作结束后抓取hprof文件。



或者也可monkey执行特定App的事件

```java
adb shell monkey -p com.haier.fridge.multideviceschat  --throttle 300 --ignore-crashes --ignore-timeouts --ignore-native-crashes --monitor-native-crashes --ignore-security-exceptions --pct-touch 5 --pct-motion 15 --pct-appswitch 40   --pct-trackball 10 --pct-nav 5  --pct-majornav 20 --pct-syskeys 5  --bugreport -v -v -v 100000  0>E:/monkey_log0.log 2>E:/monkey_log2.log 1>E:/monkey_log1.log
```

## 内存日志抓取指令

由笔者曾经编写的软件故障日志提取步骤可知——抓取App内存Hprof日志指令如下： 

```shell
adb shell am dumpheap  com.haier.fridge.multideviceschat  /data/anr/multideviceschat-多个页面点至少10次.hprof
```

## 问题现场录屏/截图 

App所用内存情况：App占用内存$315，053，776$`byte`

![image-20221128181733394](http://cdn.yangchaofan.cn/typora/image-20221128181733394.png)

由笔者之前写过的案例《内存泄漏案例1》可知，`Memory Profile`能显示出`Activity/Fragment`类型的泄漏点，我们查阅文件，可以清楚的看到App有121个内存泄漏点。

>  值得重视的是，这还未包含非`Activity/Fragment`类型的内存泄漏。

![hprof分析-视频通话内存泄漏-好友详情页](http://cdn.yangchaofan.cn/typora/hprof分析-视频通话内存泄漏-好友详情页.png)

1. 选择对象类型为`Activity/Fragment`
2. 查看直接泄漏点，可以看到`hprof`文件里有121处`Activity/Fragment`相关的内存泄漏
3. 点击`Allocations`,选择按分配数量排序，从上至下我们先来看泄漏数量较多的类

笔者默认读者已经阅读过内存泄漏案例系列1-4，已经熟悉`Memory Profile`的工具使用，接下来分析每个类型以及每个类型的多个实例。



## 原因分析

## **FriendDetailActivity**

![hprof分析-视频通话内存泄漏-好友详情页-第一个实例](http://cdn.yangchaofan.cn/typora/hprof分析-视频通话内存泄漏-好友详情页-第一个实例.png)

### **泄漏点1**

老规矩查查看实例是否符合泄漏的条件：1、实例是否处于生命周期销毁阶段 2、内存确实未回收

1. 在`classname`区域选择要观察的类型`FriendDetailActivity`
2. 在`Instance`区域选择要观察的具体实例
3. 在`Fields`区域选择观察实例的属性，这是判断实例是否应该被回收的重要依据

可以看到`FriendDetailActivity#mDestroyed` 为`true`，表明页面已经离开了窗口，此实例本应该回收，却未回收，符合内存泄漏的条件.

4. 在`References`区域,查看`GC root`，找寻谁持有了`FriendDetailActivity`实例

![hprof分析-视频通话内存泄漏-好友详情页-第一个实例-rxjava和匿名内部类的内存泄漏](http://cdn.yangchaofan.cn/typora/hprof分析-视频通话内存泄漏-好友详情页-第一个实例-rxjava和匿名内部类的内存泄漏.png)

第三步，我们先来看`FriendDetailActivity`问题源码：

```java
    private void initCallDevice() {
        MDVService.get().queryPermission2Friend(selectedFriend.getFriendId(), new DeviceListCB() {
            @Override
            public void onSuccess(List<DeviceInfo> deviceInfos) {
                if (deviceInfos != null && deviceInfos.size() > 0) {
                	...
                } else {
                	...
                }
            }

            @Override
            public void onFailed(MDVError MDVError) {
                ...
            }
        });

    }
```

此处代码调用视频通话`SDKMDVService.get().queryPermission2Friend`,传入了匿名内部类`DeviceListCB`,此类是SDK内部定义的抽象类，定义了一个`CallBack`事件

```java
public abstract class DeviceListCB extends BaseCallback {
    public DeviceListCB() {
    }

    public abstract void onSuccess(List<DeviceInfo> var1);
}
```

我们跟随SDK代码，追踪调用链

```java
MDVService.queryPermission2Friend
- HaierContactsManager.get().unsetDinyDevices
-- HaierContactsManager.compositeDisposable.add(this.unsetDinyDevicesDisposable);
```

每一次调用SDK接口，SDK都会初始化一个`unsetDinyDevicesDisposable`实例，此实例持有页面传入的`DeviceListCB`实例，而`DeviceListCB`实例又持有了页面`Activity`实例。

我们分析出了导致内存泄漏的原因，下面有2个解决思路：

1. 阻断`DeviceListCB`对页面`Activity`的引用
2. 页面退出时，释放`SDK`对`DeviceListCB`引用

查询SDK文档后，未能找到满足思路2需求的API接口，遂转而采用思路1，实现思路1的方法又很多种，前面的案例也都有涉及，我们在这里采取静态内部类+弱引用的方式。

**修改前：**

```java
    private void initCallDevice() {

        MDVService.get().queryPermission2Friend(selectedFriend.getFriendId(), new DeviceListCB() {
            @Override
            public void onSuccess(List<DeviceInfo> deviceInfos) {
                if (deviceInfos != null && deviceInfos.size() > 0) {
                    FriendDetailActivity.this.mDeviceInfoList.clear();
                    FriendDetailActivity.this.mDeviceInfoList.addAll(deviceInfos);
                    callPermissionAdapter.notifyDataSetChanged();
                } else {
                    tv_permission.setText("暂无设备");
                }
            }

            @Override
            public void onFailed(MDVError MDVError) {
                tv_permission.setText("获取设备失败");
            }
        });

    }
```

**修改后：**

```java
 static class MyDeviceList extends  DeviceListCB{
        private WeakReference<List<DeviceInfo>> deviceInfoList ;
        private WeakReference<CallPermissionAdapter> callPermissionAdapterWeakReference ;
        private WeakReference<TextView> textViewWeakReference ;

        public MyDeviceList(List<DeviceInfo> pageDeviceInfoList,CallPermissionAdapter callPermissionAdapter,TextView tv_permission){
            deviceInfoList = new WeakReference<List<DeviceInfo>>(pageDeviceInfoList);
            callPermissionAdapterWeakReference = new WeakReference<>(callPermissionAdapter);
            textViewWeakReference = new WeakReference<>(tv_permission);
        }
        @Override
        public void onSuccess(List<DeviceInfo> deviceInfos) {
            if (deviceInfos != null && deviceInfos.size() > 0) {
                deviceInfoList.get().clear();
                deviceInfoList.get().addAll(deviceInfos);
                callPermissionAdapterWeakReference.get().notifyDataSetChanged();
            } else {
                textViewWeakReference.get().setText("暂无设备");
            }
        }

        @Override
        public void onFailed(MDVError mdvError) {
            textViewWeakReference.get().setText("获取设备失败");
        }
    }

   private void initCallDevice() {
        MyDeviceList myDeviceList = new MyDeviceList(mDeviceInfoList,callPermissionAdapter,tv_permission);
        MDVService.get().queryPermission2Friend(selectedFriend.getFriendId(),myDeviceList);
    }
```



我们用上述方法分析**FriendDetailActivity**剩余的18个实例，定位到所有的泄漏点，都是调用视频通话SDK相关的，下面我们逐一优化视频通话SDK的使用

### **泄漏点2**

**优化前：**

```java
private void initCallRecording() {
        MDVService.get().queryCallRecord2Friend(selectedFriend.getFriendId(), new CallRecordCB() {
            @Override
            public void onSuccess(CallRecord callRecord) {
                if (callRecord != null) {
                    //拼接显示内容
                    String displayStartTime = DateUtil.getDateToString(callRecord.getStartTime());
                    String displayCallTime = DateUtil.generateTime(callRecord.getCallTime());
                    String fromText = "";
                    if (callRecord.getUsers() != null && callRecord.getUsers().size() > 0) {
                        if (callRecord.getUsers().get(0).getFromUserId().equals(selectedFriend.getFriendId())) {
                            fromText = "呼入";
                        } else {
                            fromText = "呼出";
                        }
                    } else {
                        fromText = "无通话记录";
                    }

                    String displayCallWay = callRecord.getCallType() == 1 ? "     语音" : "     视频";
                    tv_recent_contact.setText("【 " + fromText + " 】        " + displayStartTime + displayCallWay + displayCallTime);
                } else {
                    tv_recent_contact.setText("无通话记录");
                }
            }

            @Override
            public void onFailed(MDVError reason) {
                tv_recent_contact.setText("查询与好友最近一次通话记录失败， " + reason);
            }
        });
    }
```

优化后：

```java
    static class MyFriendCallRecordCB extends CallRecordCB{
        private WeakReference<Friend> selectedFriendWeak;
        private WeakReference<TextView> textViewWeakReference;
        public MyFriendCallRecordCB(Friend pageSelectedFriend,TextView tvRecentContact){
            selectedFriendWeak = new WeakReference<Friend>(pageSelectedFriend);
            textViewWeakReference = new WeakReference<TextView>(tvRecentContact);
        }
        @Override
        public void onSuccess(CallRecord callRecord) {
            if (callRecord != null) {
                //拼接显示内容
                String displayStartTime = DateUtil.getDateToString(callRecord.getStartTime());
                String displayCallTime = DateUtil.generateTime(callRecord.getCallTime());
                String fromText = "";
                if (callRecord.getUsers() != null && callRecord.getUsers().size() > 0) {
                    if (callRecord.getUsers().get(0).getFromUserId().equals(selectedFriendWeak.get().getFriendId())) {
                        fromText = "呼入";
                    } else {
                        fromText = "呼出";
                    }
                } else {
                    fromText = "无通话记录";
                }

                String displayCallWay = callRecord.getCallType() == 1 ? "     语音" : "     视频";
                textViewWeakReference.get().setText("【 " + fromText + " 】        " + displayStartTime + displayCallWay + displayCallTime);
            } else {
                textViewWeakReference.get().setText("无通话记录");
            }
        }

        @Override
        public void onFailed(MDVError mdvError) {
            textViewWeakReference.get().setText("查询与好友最近一次通话记录失败， " + mdvError);

        }
    }
    private void initCallRecording() {
        MyFriendCallRecordCB myFriendCallRecordCB = new MyFriendCallRecordCB(selectedFriend,tv_recent_contact);
        MDVService.get().queryCallRecord2Friend(selectedFriend.getFriendId(),myFriendCallRecordCB);
    }
```

### **泄漏点3**

优化前：

```java
MDVService.get().setPermission4Friend(friendId, deviceId, isChecked, new DeviceListCB() {
        @Override
        public void onSuccess(List<DeviceInfo> deviceInfos) {
            myLoadDialog.dismissLoadProgress();
            if( deviceInfos != null ){
                LogUtil.instance().logE(TAG,"服务端返回的设备列表：" + deviceInfos.size());
                // 当前位置是否拒绝呼入 ，获取服务端该设备设置禁止呼入后的结果，
                mDeviceInfoList.clear();
                mDeviceInfoList.addAll(deviceInfos);
                // 数据集的字段发生改变，刷新
                callPermissionAdapter.notifyDataSetChanged();
            }else {
                LogUtil.instance().logE(TAG,"服务端返回的设备列表 为空");
            }
        }

        @Override
        public void onFailed(MDVError MDVError) {
            LogUtil.instance().logE(TAG,"设置来电提醒失败，"+MDVError.errorCode+","+MDVError.errorInfo);
            myLoadDialog.dismissLoadProgress();
            ToastMng.toastShow(getString(R.string.set_not_call_error));
        }
 });
```

优化后：

```java
static class MyFriendDeviceListCB extends DeviceListCB{
        private WeakReference<MyLoadDialog> loadDialogWeakReference;
        private WeakReference<List<DeviceInfo>> listWeakReference;
        private WeakReference<CallPermissionAdapter> callPermissionAdapterWeakReference;
        private WeakReference<Activity> activityWeakReference;

        public  MyFriendDeviceListCB(MyLoadDialog loadDialog,List<DeviceInfo> deviceInfoList,CallPermissionAdapter callPermissionAdapter,Activity activity){
            loadDialogWeakReference = new WeakReference<>(loadDialog);
            listWeakReference = new WeakReference<>(deviceInfoList);
            callPermissionAdapterWeakReference = new WeakReference<>(callPermissionAdapter);
            activityWeakReference = new WeakReference<>(activity);
        }
        @Override
        public void onSuccess(List<DeviceInfo> deviceInfos) {
            loadDialogWeakReference.get().dismissLoadProgress();
            if( deviceInfos != null ){
                LogUtil.instance().logE(TAG,"服务端返回的设备列表：" + deviceInfos.size());
                // 当前位置是否拒绝呼入 ，获取服务端该设备设置禁止呼入后的结果，
                listWeakReference.get().clear();
                listWeakReference.get().addAll(deviceInfos);
                // 数据集的字段发生改变，刷新
                callPermissionAdapterWeakReference.get().notifyDataSetChanged();
            }else {
                LogUtil.instance().logE(TAG,"服务端返回的设备列表 为空");
            }
        }

        @Override
        public void onFailed(MDVError MDVError) {
            LogUtil.instance().logE(TAG,"设置来电提醒失败，"+MDVError.errorCode+","+MDVError.errorInfo);
            loadDialogWeakReference.get().dismissLoadProgress();
            ToastMng.toastShow(activityWeakReference.get().getString(R.string.set_not_call_error));
        }
    }

   MyFriendDeviceListCB myFriendDeviceListCB = new MyFriendDeviceListCB(myLoadDialog,mDeviceInfoList,callPermissionAdapter,FriendDetailActivity.this);
            MDVService.get().setPermission4Friend(friendId, deviceId, isChecked,myFriendDeviceListCB);
```

### **泄漏点4**

优化前

```java
   private void deleteFriend() {
        List<String> friendIds = new ArrayList<>();
        friendIds.add(selectedFriend.getFriendId());
        MDVService.get().deleteFriend(friendIds, new NormalCB() {
            @Override
            public void onSuccess() {
                ToastMng.getInstance().showToast( "解除好友关系成功");
                finish();
            }

            @Override
            public void onFailed(MDVError reason) {
                ToastMng.getInstance().showToast( "解除好友关系失败");
            }
        });

    }
```



优化后：

```java
    static class MyFriendNormalCB extends NormalCB{
        private WeakReference<Activity> activityWeakReference;
        public MyFriendNormalCB(Activity activity){
            activityWeakReference = new WeakReference<>(activity);
        }
        @Override
        public void onSuccess() {
            ToastMng.getInstance().showToast( "解除好友关系成功");
            activityWeakReference.get().finish();
        }

        @Override
        public void onFailed(MDVError reason) {
            ToastMng.getInstance().showToast( "解除好友关系失败");
        }
    }
  private void deleteFriend() {
        MyFriendNormalCB myFriendNormalCB = new MyFriendNormalCB();
        List<String> friendIds = new ArrayList<>();
        friendIds.add(selectedFriend.getFriendId());
        MDVService.get().deleteFriend(friendIds, myFriendNormalCB);
    }

```

### **泄漏点5**

优化前:

```java
private void changeNickName(String newName) {
        if (TextUtils.isEmpty(newName)) {
            return;
        }

        if (newName.length() > 6) {
            Toast.makeText(FriendDetailActivity.this.getApplicationContext(), "备注过长，最大支持6位", Toast.LENGTH_LONG).show();
            return;
        }

        MDVService.get().editFriendNickName(selectedFriend.getFriendId(), newName, new NormalCB() {
            @Override
            public void onSuccess() {
                Toast.makeText(FriendDetailActivity.this.getApplicationContext(), "修改成功", Toast.LENGTH_LONG).show();
                selectedFriend.setFriendNickName(newName);
                tv_remark_name.setText(newName);
                hideInput();
            }

            @Override
            public void onFailed(MDVError reason) {
                Toast.makeText(FriendDetailActivity.this.getApplicationContext(), "修改失败", Toast.LENGTH_LONG).show();
                Log.i(TAG, reason + "--- modify remark name failed");
                hideInput();
            }
        });
    }
```

优化后

```java
static class MyChangeNormalCB extends NormalCB{
        private WeakReference<Activity> activityWeakReference;
        private WeakReference<Friend> selectFriendWeakReference;
        private WeakReference<TextView> tvRemarkNameWeakReference;
        private WeakReference<ImageView> imageViewWeakReference;
        private WeakReference<EditText> editTextWeakReference;
        private WeakReference<TextView> editOkWeakReference;
        private String newName;

        public MyChangeNormalCB(Activity activity,Friend selectedFriend,TextView tvRemarkName,ImageView ivEdit,EditText etRemark,TextView editOk,String newName){
            activityWeakReference = new WeakReference<>(activity);
            selectFriendWeakReference = new WeakReference<>(selectedFriend);
            tvRemarkNameWeakReference = new WeakReference<>(tvRemarkName);
            imageViewWeakReference = new WeakReference<>(ivEdit);
            editTextWeakReference = new WeakReference<>(etRemark);
            editOkWeakReference = new WeakReference<>(editOk);
            this.newName = newName;
        }
        @Override
        public void onSuccess() {
            ToastMng.getInstance().showToast( "修改成功");
            selectFriendWeakReference.get().setFriendNickName(newName);
            tvRemarkNameWeakReference.get().setText(newName);
            hideInput();
        }

        @Override
        public void onFailed(MDVError reason) {
            ToastMng.getInstance().showToast( "修改失败");
            Log.i(TAG, reason + "--- modify remark name failed");
            hideInput();
        }

        /**
         * 隐藏键盘
         */
        protected void hideInput() {
            tvRemarkNameWeakReference.get().setVisibility(View.VISIBLE);
            imageViewWeakReference.get().setVisibility(View.VISIBLE);
            editTextWeakReference.get().setVisibility(View.GONE);
            editOkWeakReference.get().setVisibility(View.GONE);
            InputMethodManager imm = (InputMethodManager)activityWeakReference.get().getSystemService(INPUT_METHOD_SERVICE);
            View v = activityWeakReference.get().getWindow().peekDecorView();
            if (null != v) {
                imm.hideSoftInputFromWindow(v.getWindowToken(), 0);
            }
        }
    }

    private void changeNickName(String newName) {
        if (TextUtils.isEmpty(newName)) {
            return;
        }
        MyChangeNormalCB myChangeNormalCB = new MyChangeNormalCB(FriendDetailActivity.this,
                selectedFriend,tv_remark_name,iv_edit,et_remark,edit_ok,newName);
        if (newName.length() > 6) {
            Toast.makeText(FriendDetailActivity.this.getApplicationContext(), "备注过长，最大支持6位", Toast.LENGTH_LONG).show();
            return;
        }
        MDVService.get().editFriendNickName(selectedFriend.getFriendId(), newName, myChangeNormalCB);
    }
```

### 优化结果

我们按照同样的步骤，进入好友详情,随机点击该页面按钮，退出好友详情，重复十次。

![hprof分析-视频通话-好友详情优化后](http://cdn.yangchaofan.cn/typora/hprof分析-视频通话-好友详情优化后.jpg)

在相同的操作次数下，皆进入、退出10次该页面

结论：由图`GC Root=null,depth=null`可知，`FriendDetailActivity`不再被视频通话SDK引用，`FriendDetailActivity`页面销毁后，内存会被回收,该页面内存泄漏优化成功。

优化前单个`FriendDetailActivity`的`retained Size`为$322,815$`byte`，优化后为$304$`byte`，单个实例内存占用减少为原先的$1/1061$。

优化前`FriendDetailActivity`总占据内存$4,149,181$`byte`；优化后`FriendDetailActivity`总占据内存$912$`byte,`内存占用减少为原先的$1/4549$,内存节省了$4$mb

## DeviceReminderSettingsActivity

第二个类型`DeviceReminderSettingsActivity`，是视频通话的设备提醒页面，用于设置设备之间的来电提醒开关。

![hprof分析-视频通话-设备提醒-优化前](http://cdn.yangchaofan.cn/typora/hprof分析-视频通话-设备提醒-优化前.jpg)

根据前面的案例，我们已经很熟练的能够找到泄漏点源码位置,这个页面有2个泄漏点，泄漏点的原因也是非静态的匿名内部类持有当前类的引用，下面介绍下优化前后的效果对比

### **泄漏点1**

优化前

```java
 MDVService.get().setTotalPermission(bean.getDeviceId(), !bean.getIsDeny(),
                            new DeviceListCB() {
                                @Override
                                public void onSuccess(List<DeviceInfo> deviceInfos) {
                                    showData(deviceInfos);
                                    dataViewHolder.setSwitchBtnClickable(true);
                                }

                                @Override
                                public void onFailed(MDVError MDVError) {
                                    dataViewHolder.setSwitchBtnClickable(true);
                                    ToastMng.getInstance().showToast("设置权限失败，移动通讯设备不允许设置权限");
                                }
                            });
```

**泄漏点1**优化后

```java
           MyUpdateDevicesList myUpdateDevicesList = new MyUpdateDevicesList(
                            true,
                            DeviceReminderSettingsActivity.this,
                            device_list_info,
                            default_view_in_settings,
                            deviceListClosed,
                            deviceListOpen,
                            myDevicesAdapterOpen,
                            myDevicesAdapterClosed,
                            recycleView_has_open,
                            recycleView_has_closed,
                            tv_has_open,
                            tv_has_closed,
                            dataViewHolder
                    );
                    MDVService.get().setTotalPermission(bean.getDeviceId(),
                            !bean.getIsDeny(),
                            myUpdateDevicesList);
```



### **泄漏点2**

优化前

```java
      MDVService.get().queryMyDeviceList(new DeviceListCB() {
            @Override
            public void onSuccess(List<DeviceInfo> deviceInfos) {
                if (deviceInfos != null && deviceInfos.size() > 0) {
                    showData(deviceInfos);
                } else {
                    device_list_info.setText("获取设备列表成功，当前用户名下没有设备.");
                }
            }

            @Override
            public void onFailed(MDVError reason) {
                device_list_info.setText("获取设备列表失败." + reason.toString());
            }
        });
```



**泄漏点2**优化后

```java
   private void updateDevicesList() {
        MyUpdateDevicesList myUpdateDevicesList = new MyUpdateDevicesList(
                false,
                DeviceReminderSettingsActivity.this,
                device_list_info,
                default_view_in_settings,
                deviceListClosed,
                deviceListOpen,
                myDevicesAdapterOpen,
                myDevicesAdapterClosed,
                recycleView_has_open,
                recycleView_has_closed,
                tv_has_open,
                tv_has_closed,
                null
        );
        MDVService.get().queryMyDeviceList(myUpdateDevicesList);
    }


```

两个泄漏点共同使用同一个静态内部类+弱引用绑定页面的元素

下面是一个极端示例，静态内部类传入过多的参数，实际上我们可以只传一个`Activity`，通过`Activity.`的方式引用`Activity`的成员属性，读者可以自行取舍

```java
 static class MyUpdateDevicesList extends DeviceListCB {
        /**
         * 是点击态，则传入viewholder，响应viewholder的业务
         */
        private boolean isItemClick;
        private WeakReference<Activity> activityWeakReference;
        private WeakReference<TextView> deviceListInfoWeakReference;
        private WeakReference<LinearLayout> defaultViewInSettingstWeakReference;
        private WeakReference<ArrayList<DeviceInfo>> deviceListClosedWeakReference;
        private WeakReference<ArrayList<DeviceInfo>> deviceListOpenWeakReference;
        private WeakReference<MyDevicesAdapter> myDevicesOpenAdapterWeakReference;
        private WeakReference<MyDevicesAdapter> myDevicesCloseAdapterWeakReference;
        private WeakReference<RecyclerView> recycleViewHasOpenWeakReference;
        private WeakReference<RecyclerView> recycleViewHasCloseWeakReference;
        private WeakReference<TextView> tvHasOpenWeakReference;
        private WeakReference<TextView> tvHasCloseWeakReference;
        /**
         * 是点击态，则传入viewholder
         */
        private WeakReference<ItemViewHolder> itemViewHolderWeakReference;


        public MyUpdateDevicesList(boolean isItemClick,
                                   Activity activity,
                                   TextView deviceListInfo,
                                   LinearLayout defaultViewInSettings,
                                   ArrayList<DeviceInfo> deviceListClosed,
                                   ArrayList<DeviceInfo> deviceListOpen,
                                   MyDevicesAdapter myDevicesOpenAdapter,
                                   MyDevicesAdapter myDevicesCloseAdapter,
                                   RecyclerView recycleViewHasOpen,
                                   RecyclerView recycleViewHasClose,
                                   TextView tvHasOpen,
                                   TextView tvHasClosed,
                                   ItemViewHolder dataViewHolder
        ) {
            this.isItemClick = isItemClick;
            activityWeakReference = new WeakReference<>(activity);
            deviceListInfoWeakReference = new WeakReference<TextView>(deviceListInfo);
            defaultViewInSettingstWeakReference = new WeakReference<LinearLayout>(defaultViewInSettings);
            deviceListClosedWeakReference = new WeakReference<ArrayList<DeviceInfo>>(deviceListClosed);
            deviceListOpenWeakReference = new WeakReference<ArrayList<DeviceInfo>>(deviceListOpen);
            myDevicesOpenAdapterWeakReference = new WeakReference<MyDevicesAdapter>(myDevicesOpenAdapter);
            myDevicesCloseAdapterWeakReference = new WeakReference<MyDevicesAdapter>(myDevicesCloseAdapter);
            recycleViewHasOpenWeakReference = new WeakReference<RecyclerView>(recycleViewHasOpen);
            recycleViewHasCloseWeakReference = new WeakReference<RecyclerView>(recycleViewHasClose);
            tvHasOpenWeakReference = new WeakReference<TextView>(tvHasOpen);
            tvHasCloseWeakReference = new WeakReference<TextView>(tvHasClosed);
            if(isItemClick){
                itemViewHolderWeakReference = new WeakReference<>(dataViewHolder);
            }
        }

        @Override
        public void onSuccess(List<DeviceInfo> deviceInfos) {
            if (isItemClick) {
                showData(deviceInfos);
                itemViewHolderWeakReference.get().setSwitchBtnClickable(true);
            } else {
                if (deviceInfos != null && deviceInfos.size() > 0) {
                    showData(deviceInfos);
                } else {
                    deviceListInfoWeakReference.get().setText("获取设备列表成功，当前用户名下没有设备.");
                }
            }
        }

        @Override
        public void onFailed(MDVError reason) {
            if (isItemClick) {
                itemViewHolderWeakReference.get().setSwitchBtnClickable(true);
                ToastMng.getInstance().showToast("设置权限失败，移动通讯设备不允许设置权限");
            } else {
                deviceListInfoWeakReference.get().setText("获取设备列表失败." + reason.toString());
            }
        }

        private void showData(List<DeviceInfo> deviceInfos) {
            //初始化数据
            defaultViewInSettingstWeakReference.get().setVisibility(View.GONE);
            deviceListClosedWeakReference.get().clear();
            deviceListOpenWeakReference.get().clear();
            for (int i = 0; i < deviceInfos.size(); i++) {
                if (deviceInfos.get(i).getIsDeny()) {
                    deviceListClosedWeakReference.get().add(deviceInfos.get(i));//1.1
                } else {
                    deviceListOpenWeakReference.get().add(deviceInfos.get(i));//1.2
                }
            }
            //展示数据1.1 open status

            recycleViewHasOpenWeakReference.get().setAdapter(myDevicesOpenAdapterWeakReference.get());
            myDevicesOpenAdapterWeakReference.get().notifyDataSetChanged();

            //展示数据1.2 closed status
            recycleViewHasCloseWeakReference.get().setAdapter(myDevicesCloseAdapterWeakReference.get());
            myDevicesCloseAdapterWeakReference.get().notifyDataSetChanged();

            if (deviceListClosedWeakReference.get() != null) {
                tvHasCloseWeakReference.get().setVisibility(deviceListClosedWeakReference.get().size() == 0 ? View.INVISIBLE : View.VISIBLE);
            }
            if (deviceListOpenWeakReference.get() != null) {
                tvHasOpenWeakReference.get().setVisibility(deviceListOpenWeakReference.get().size() == 0 ? View.INVISIBLE : View.VISIBLE);
            }
            LocalBroadcastManager.getInstance(activityWeakReference.get()).sendBroadcast(
                    new Intent("DEVICE_PERMISSION_UPDATE"));
        }
    }
```

![hprof分析-视频通话-设备提醒页](http://cdn.yangchaofan.cn/typora/hprof分析-视频通话-设备提醒页.jpg)

### 优化结果

优化后，由图`GC Root`为`null`，`Depth`为`null`可知，`DeviceReminderSettingsActivity`不再被视频通话SDK引用，页面销毁后，内存会被回收，该页面内存泄漏优化成功

优化前，3022634`byte`；优化后948`byte`,内存占用减少为原先的 $1/3188$,内存节省了3mb



## NewFriendActivity

`NewFriendActivity`是申请好友页面，用于二维码申请添加好友，也会展示出申请添加记录。

按图索骥找到问题代码

![hprof分析-视频通话-申请好友页](E:\能力提升\hprof分析-视频通话-申请好友页.jpg)

### **泄漏点1**

优化前：

```java
    private void getQrCode() {
        MDVService.get().getQrCode(new QrCB() {
            @Override
            public void onSuccess(String base64String) {
                byte[] decodedString = Base64.decode(base64String.split(",")[1], Base64.DEFAULT);
                Bitmap decodedByte = BitmapFactory.decodeByteArray(decodedString, 0, decodedString.length);
                qr_code.setImageBitmap(decodedByte);
            }

            @Override
            public void onFailed(MDVError reason) {
                if (reason != null && !TextUtils.isEmpty(reason.toString())) {
                    ToastMng.getInstance().showToast(reason.toString());
                }
            }
        });
    }
```

**泄漏点1**优化后：

```java
     static  class MyQrCb extends QrCB{
        private WeakReference<ImageView> imageViewWeakReference;
        public MyQrCb(ImageView qrCode){
            imageViewWeakReference = new WeakReference<>(qrCode);
        }
        @Override
        public void onSuccess(String base64String) {
            byte[] decodedString = Base64.decode(base64String.split(",")[1], Base64.DEFAULT);
            Bitmap decodedByte = BitmapFactory.decodeByteArray(decodedString, 0, decodedString.length);
            imageViewWeakReference.get().setImageBitmap(decodedByte);
        }

        @Override
        public void onFailed(MDVError reason) {
            if (reason != null && !TextUtils.isEmpty(reason.toString())) {
                ToastMng.getInstance().showToast(reason.toString());
            }
        }
    }

    private void getQrCode() {
        MyQrCb myQrCb = new MyQrCb(qr_code);
        MDVService.get().getQrCode(myQrCb);
    }
```

### **泄漏点2**

优化前：

```java
private void addFriend(String targetAccount, String message) {
    MDVService.get().AddFriend(targetAccount, message, new NormalCB() {
        @Override
        public void onSuccess() {
            ToastMng.getInstance().showToast("等待好友确认");
        }

        @Override
        public void onFailed(MDVError reason) {
            ToastMng.getInstance().showToast("发起好友申请失败");
        }
    });
}
```

**泄漏点2**优化后：

```java
    static class MyAddNormalCb extends NormalCB{
        @Override
        public void onSuccess() {
            ToastMng.getInstance().showToast("等待好友确认");
        }

        @Override
        public void onFailed(MDVError reason) {
            ToastMng.getInstance().showToast("发起好友申请失败");
        }
    }
    private void addFriend(String targetAccount, String message) {
        MyAddNormalCb myAddNormalCb = new MyAddNormalCb();
        MDVService.get().AddFriend(targetAccount, message, myAddNormalCb);
    }

```

### **泄漏点3**

优化前：

```java
       MDVService.get().handleFriendReq(recordId, agree, new NormalCB() {
            @Override
            public void onSuccess() {
                dismissLoadProgress();
                ToastMng.getInstance().showToast("操作成功");
                queryFriendApply();
            }

            @Override
            public void onFailed(MDVError reason) {
                dismissLoadProgress();
                LogUtil.instance().logE(TAG,"errorCode"+reason.errorCode);
                LogUtil.instance().logE(TAG,"errorInfo"+reason.errorInfo);
                ToastMng.getInstance().showToast("操作失败");
                queryFriendApply();
            }
        });
```

**泄漏点3**优化后：

```java
    static class ReqNormalCB extends NormalCB{
        private WeakReference<BaseActivity> activityWeakReference;
        public ReqNormalCB(BaseActivity activity){
            activityWeakReference = new WeakReference<>(activity);
        }
        @Override
        public void onSuccess() {
            activityWeakReference.get().dismissLoadProgress();
            ToastMng.getInstance().showToast("操作成功");
            queryFriendApply();
        }

        @Override
        public void onFailed(MDVError reason) {
            activityWeakReference.get().dismissLoadProgress();
            LogUtil.instance().logE("errorCode"+reason.errorCode);
            LogUtil.instance().logE("errorInfo"+reason.errorInfo);
            ToastMng.getInstance().showToast("操作失败");
            queryFriendApply();
        }
    }

 
        ReqNormalCB reqNormalCB = new ReqNormalCB(NewFriendActivity.this);
        MDVService.get().handleFriendReq(recordId, agree, reqNormalCB);

```



### **泄漏点4**

优化前：

```java
     MDVService.get().queryAllFriendReqRecord(new AllFriendReqRecordCB() {
            @Override
            public void onSuccess(List<FriendReqRecord> friendReqRecords) {
                dismissLoadProgress();
                LogUtil.instance().logE(TAG,"好友申请列表查询成功");
                if (friendReqRecords != null && friendReqRecords.size() > 0) {
                    HashSet<String> set = new HashSet<>(friendReqRecords.size());
                    delete_all_records.setVisibility(View.VISIBLE);
                    applyList.clear();
                    for (FriendReqRecord friend : friendReqRecords) {
                        if (set.add(friend.getFromUserId())) {
                            applyList.add(friend);
                        }
                    }
                    applyAdapter.notifyDataSetChanged();
                } else {
                    delete_all_records.setVisibility(View.INVISIBLE);
                    ToastMng.getInstance().showToast("暂无好友申请");
                    applyList.clear();
                    applyAdapter.notifyDataSetChanged();
                }
            }

            @Override
            public void onFailed(MDVError reason) {
                dismissLoadProgress();
                LogUtil.instance().logE(TAG,"好友申请列表查询失败");
                delete_all_records.setVisibility(View.INVISIBLE);
                ToastMng.getInstance().showToast("查询失败!");
            }
        });
```

**泄漏点4**优化后：

```java
 static class MyAllFriendReqRecordCB extends AllFriendReqRecordCB{
        private WeakReference<BaseActivity> activityWeakReference;
        private WeakReference<Button> deleteAllRecordsWeakReference;
        private WeakReference<List<FriendReqRecord>> applyListWeakReference;
        private WeakReference<FriendApplyAdapter> applyAdapterWeakReference;

        public MyAllFriendReqRecordCB(BaseActivity activity,
                                      Button deleteAllRecords,
                                      List<FriendReqRecord> applyList,
                                      FriendApplyAdapter applyAdapter
                                      ){
            activityWeakReference = new WeakReference<>(activity);
            deleteAllRecordsWeakReference = new WeakReference<>(deleteAllRecords);
            applyListWeakReference = new WeakReference<>(applyList);
            applyAdapterWeakReference = new WeakReference<>(applyAdapter);
        }
        @Override
        public void onSuccess(List<FriendReqRecord> friendReqRecords) {
            activityWeakReference.get().dismissLoadProgress();
            LogUtil.instance().logE("好友申请列表查询成功");
            if (friendReqRecords != null && friendReqRecords.size() > 0) {
                HashSet<String> set = new HashSet<>(friendReqRecords.size());
                deleteAllRecordsWeakReference.get().setVisibility(View.VISIBLE);
                applyListWeakReference.get().clear();
                for (FriendReqRecord friend : friendReqRecords) {
                    if (set.add(friend.getFromUserId())) {
                        applyListWeakReference.get().add(friend);
                    }
                }
                applyAdapterWeakReference.get().notifyDataSetChanged();
            } else {
                deleteAllRecordsWeakReference.get().setVisibility(View.INVISIBLE);
                ToastMng.getInstance().showToast("暂无好友申请");
                applyListWeakReference.get().clear();
                applyAdapterWeakReference.get().notifyDataSetChanged();
            }
        }

        @Override
        public void onFailed(MDVError reason) {
            activityWeakReference.get().dismissLoadProgress();
            LogUtil.instance().logE("好友申请列表查询失败");
            deleteAllRecordsWeakReference.get().setVisibility(View.INVISIBLE);
            ToastMng.getInstance().showToast("查询失败!");
        }
    }


MyAllFriendReqRecordCB myAllFriendReqRecordCB = new MyAllFriendReqRecordCB(NewFriendActivity.this,
                delete_all_records,
                applyList,
                applyAdapter
                );
 MDVService.get().queryAllFriendReqRecord(myAllFriendReqRecordCB);
```

### **泄漏点5**

优化前：

```java
 static class DeleteNormalCB extends  NormalCB{
        private WeakReference<BaseActivity> activityWeakReference;
        private WeakReference<Button> deleteAllRecordsWeakReference;
        private WeakReference<List<FriendReqRecord>> applyListWeakReference;
        private WeakReference<FriendApplyAdapter> applyAdapterWeakReference;

        public DeleteNormalCB(BaseActivity activity,
                    Button deleteAllRecords,
                    List<FriendReqRecord> applyList,
                    FriendApplyAdapter applyAdapter){
            activityWeakReference = new WeakReference<>(activity);
                deleteAllRecordsWeakReference = new WeakReference<>(deleteAllRecords);
                applyListWeakReference = new WeakReference<>(applyList);
                applyAdapterWeakReference = new WeakReference<>(applyAdapter);
        }
        @Override
        public void onSuccess() {
            activityWeakReference.get().dismissLoadProgress();
            LogUtil.instance().logE("删除好友申请记录成功");
            ToastMng.getInstance().showToast("删除好友申请记录成功");
            queryFriendApply();
        }

        @Override
        public void onFailed(MDVError MDVError) {
            activityWeakReference.get().dismissLoadProgress();
            LogUtil.instance().logE("删除好友申请记录失败:errorInfo:"+MDVError.errorInfo+"--errorCode:"+MDVError.errorCode);
            ToastMng.getInstance().showToast("删除好友申请记录失败");
        }
        private void queryFriendApply() {
            MyAllFriendReqRecordCB myAllFriendReqRecordCB = new MyAllFriendReqRecordCB(activityWeakReference.get(),
                    deleteAllRecordsWeakReference.get(),
                    applyListWeakReference.get(),
                    applyAdapterWeakReference.get()
            );
            MDVService.get().queryAllFriendReqRecord(myAllFriendReqRecordCB);
        }
    }

    private void deleteFriendApply(List<Long> recordIds) {
        LogUtil.instance().logE(TAG,"开始删除选中的好友："+recordIds.toString());
        showLoadProgress();
        delete_all_records.setVisibility(View.INVISIBLE);
        DeleteNormalCB deleteNormalCB = new DeleteNormalCB(NewFriendActivity.this,delete_all_records,applyList,applyAdapter);
        MDVService.get().deleteFriendApply(recordIds, deleteNormalCB);
    }
```

### 优化结果

![hprof分析-视频通话-申请好友页](http://cdn.yangchaofan.cn/typora/hprof分析-视频通话-申请好友页.jpg)

相同的操作步骤和操作次数$10$次，优化前，该页面总共持有`Retained size`为$7,597,880$`byte`；

![hprof分析-视频通话-申请好友页-优化后](http://cdn.yangchaofan.cn/typora/hprof分析-视频通话-申请好友页-优化后.jpg)

相同的操作步骤和操作次数10次，优化后，该页面总共持有`Retained size`为$328$`byte`，可知内存占用减少为原先的$1/23164$，



## ChatActivity

`ChatActivity`是视频通话App的单人聊天页面，用于当前设备与其他设备1对1聊天

单人聊天页面有两个典型业务——语音聊天和视频聊天。

首先来看测试了语音聊天情况：

1. 拨打单人聊天
2. 接通
3. 挂断

重复10次，得到一份hprof文件如图所示

![hprof分析-视频通话-单人聊天-优化前](http://cdn.yangchaofan.cn/typora/hprof分析-视频通话-单人聊天-优化前.jpg)

老步骤，如图所示进入问题代码区域

```java
    case R.id.btn_accept:
                //单人语音被动呼叫接听
                LogUtil.instance().logE(TAG,"单人语音被动呼叫接听");
                MDVService.get().acceptCall(new OnCallActionResultCallback() {
                    @Override
                    public void onActionResult(boolean b, @NotNull HaierCallError haierCallError) {
                        if (b) {
                            Message message = new Message();
                            message.what = ACCEPT_SUCCESS;
                            mUiHandler.sendMessage(message);
                        } else {
                            LogUtil.instance().logD(TAG, "接听失败：" + haierCallError.getErrorMsg());
                            ToastUtils.getInstance(getApplicationContext()).showToast("接听失败：" + haierCallError.getErrorMsg(), marginTop);
                        }
                    }
                });
```

优化后：

```java
	MyOnCallActionResultCallback myOnCallActionResultCallback = new 		MyOnCallActionResultCallback(ChatActivity.this, mUiHandler);
	MDVService.get().acceptCall(myOnCallActionResultCallback);


   /**
     * 单人语音呼叫
     */
    static class MyOnCallActionResultCallback implements OnCallActionResultCallback {
        private WeakReference<ChatActivity> activityWeakReference;
        private WeakReference<Handler> handlerWeakReference;

        public MyOnCallActionResultCallback(ChatActivity activity, Handler handler) {
            activityWeakReference = new WeakReference<ChatActivity>(activity);
            handlerWeakReference = new WeakReference<>(handler);
        }

        @Override
        public void onActionResult(boolean b, @NotNull HaierCallError haierCallError) {
            if (activityWeakReference.get() == null || handlerWeakReference.get() == null) {
                // Activity回收了，说明是异常情况，页面早销毁了，任务才回来
                return;
            }
            if (b) {
                Message message = new Message();
                message.what = ACCEPT_SUCCESS;
                handlerWeakReference.get().sendMessage(message);
            } else {
                LogUtil.instance().logD(TAG, "接听失败：" + haierCallError.getErrorMsg());
                ToastUtils.getInstance(activityWeakReference.get().getApplicationContext()).showToast("接听失败：" + haierCallError.getErrorMsg(), activityWeakReference.get().marginTop);
            }
        }
    }
```

值得注意的是，此页面语音通话相关的流程**有20多处泄漏点**，凡是涉及视频通话SDK接口导致的泄漏点就不一一举例了，省略不写；

下面介绍一下`ChatActivity`非视频通话SDK相关的内存泄漏点

### **泄漏点1**-定时器

优化前:

实例`mCountDownTimer`的作用是用于开启通话后，在界面上显示通话时间。

```java
          mCountDownTimer = new CountDownTimer(MAX_CALL_TIME_LIMIT, 1000) {
                @Override
                public void onTick(long millisUntilFinished) {
                    millisHasFinished = MAX_CALL_TIME_LIMIT - millisUntilFinished;
                    mTvCallTime.setText(timeFormat(MAX_CALL_TIME_LIMIT - millisUntilFinished));
                    mTvCallTimeWhenVideoCall.setText(timeFormat(MAX_CALL_TIME_LIMIT - millisUntilFinished));
                }

                @Override
                public void onFinish() {
                    mTvCallTime.setVisibility(View.GONE);
                    mTvCallTimeWhenVideoCall.setVisibility(View.GONE);
                }
            };
```

优化后：

```java
            mCountDownTimer = new MyTimer(MAX_CALL_TIME_LIMIT, 1000,ChatActivity.this);
            mCountDownTimer.start(); 


static class MyTimer extends CountDownTimer {
        private WeakReference<ChatActivity> activityWeakReference;

        public MyTimer(long millisInFuture, long countDownInterval, ChatActivity activity) {
            super(millisInFuture, countDownInterval);
            activityWeakReference = new WeakReference<>(activity);
        }

        @Override
        public void onTick(long millisUntilFinished) {
            if (activityWeakReference.get() == null) {
                return;
            }
            activityWeakReference.get().millisHasFinished = MAX_CALL_TIME_LIMIT - millisUntilFinished;
            activityWeakReference.get().mTvCallTime.setText(timeFormat(MAX_CALL_TIME_LIMIT - millisUntilFinished));
            activityWeakReference.get().mTvCallTimeWhenVideoCall.setText(timeFormat(MAX_CALL_TIME_LIMIT - millisUntilFinished));
        }

        @Override
        public void onFinish() {
            if (activityWeakReference.get() == null) {
                return;
            }
            activityWeakReference.get().mTvCallTime.setVisibility(View.GONE);
            activityWeakReference.get().mTvCallTimeWhenVideoCall.setVisibility(View.GONE);
        }

        private String timeFormat(long time) {
            SimpleDateFormat sdf = new SimpleDateFormat("HH:mm:ss");
            sdf.setTimeZone(TimeZone.getTimeZone("UTC"));
            return sdf.format(new Date(time));
        }

    }
```

优化后`Retained size`内存如图所示，连续开启、结束10次语音聊天，`ChatActivity`类型只占据$982$`byte`内存,相比优化前的$695,512$`byte`，内存占用变为原来的$1/708$

![hprof-单人聊天语音聊天优化后](http://cdn.yangchaofan.cn/typora/hprof-单人聊天语音聊天优化后.jpg)



### **泄漏点2**-View与Context

优化前：

![hprof分析-视频通话-单人聊天-视频类型](http://cdn.yangchaofan.cn/typora/hprof分析-视频通话-单人聊天-视频类型.jpg)

定位到问题代码：`hashMap`缓存了`HaierOpenGlView`,`HaierOpenGlView`实例持有了`Activity`的引用，导致页面引用的内存无法释放

```java
    private Map<String, HaierOpenGlView> viewMap = new HashMap<>();
...
    viewMap.put(memberId, view);

    /**
     * 创建远端图像
     */
    private HaierOpenGlView createHaierOpenGlView() {
        HaierOpenGlView view = new HaierOpenGlView(this);
        view.setGlViewSize(259, 315);
//        view.setGlType(HaierOpenGlView.RenderType.RENDER_PREVIEW);
        // 按照图像的比例显示（分辨率和图像分辨率不等时上下、左右会出现一种黑边情况）
        //view.setAspectMode(HaierOpenGlView.AspectMode.FIT);
        //view.setAspectMode(HaierOpenGlView.AspectMode.FILL);
        view.setAspectMode(HaierOpenGlView.AspectMode.CROP);
        return view;
    }

```



**泄漏点2**优化后代码：

在创建view实例的时候，传入application context，而非Activity；

```java
 HaierOpenGlView view = new HaierOpenGlView(getApplicationContext());
```

在页面退出的时候，清空`map`持有的`view`，进而释放`view`持有的`Activity`

```java
    @Override
    protected void onDestroy() {  
		viewMap.clear();
  }

```



### **泄漏点3**-匿名线程

优化前：

![image-20221124201029329](http://cdn.yangchaofan.cn/typora/image-20221124201029329.png)

```java
	onCreate(...){
          mHctvTextureData = findViewById(R.id.hctv_texture_data);
    }  

	private void startCapture() {
        LogUtil.instance().logE(TAG, "开启预览界面startCapture");
        new Thread(new Runnable() {
            @Override
            public void run() {
                MDVService.get().setLocalVideoPreview(mHctvTextureData);
                isPublish = true;
            }
        }).start();
    }

```

view所在的布局参数

```java
    <!-- 预览的view,一般设置1px即可，采集数据无需显示-->
    <com.haier.mdv.call.core.ytx.widgets.HaierCaptureTextureView
        android:id="@+id/hctv_texture_data"
        android:layout_width="1px"
        android:layout_height="1px" />
```

三个问题点

1. `view` 的定义是有问题的，构造函数默认传的是`Activity`，`view`不会主动释放持有的`Activity`，导致`Activity`内存泄漏
2. 匿名`Runnbale`持有当前`Activity`的引用——静态内部类+弱引用
3. 视频通话`sdk`持有`view`，且无法释放  ——排查`SDK`是否提供释放`view`的接口

泄漏点3优化后：

移除`view`在`xml`的定义，改为动态创建，传入`BaseApplicationContext`；在`onDestroy`中释放`view`对`context`的引用

```java
  private HaierCaptureTextureView mHctvTextureData;

	onCreate(...){
        // 替代xml中定义HaierCaptureTextureView，避免绑定Activity
        mHctvTextureData = new HaierCaptureTextureView(getApplicationContext());
        mRlFullScreenTexture.addView(mHctvTextureData,1,1);
    }

	static class  MyThread extends Thread{
        private WeakReference<ChatActivity> activityWeakReference;
        public void MyThread(ChatActivity activity){
            activityWeakReference = new WeakReference<>(activity);
        }
        @Override
        public void run() {
            super.run();
            MDVService.get().setLocalVideoPreview(activityWeakReference.get().mHctvTextureData);
            activityWeakReference.get().isPublish = true;
        }
    }
    private void startCapture() {
        LogUtil.instance().logE(TAG, "开启预览界面startCapture");
        MyThread myThread = new MyThread(ChatActivity.this);
        myThread.start();
    }

    @Override
    protected void onDestroy() {
        // 尽力释放本地视频占用的context
        MDVService.get().cancelSelfVideo(new MyNullOnCallActionResultCallback());
        viewMap.clear();
        viewMap = null;
        mRlFullScreenTexture.removeAllViews();
        mRlFullScreenTexture = null;
        mHctvTextureData = null;
        mRlSmallLocalTexture.removeAllViews();
        mRlSmallLocalTexture = null;   
    }       
```

### 优化结果

解决完该页面所有处泄漏点后,我们来总结下ChatActivity页面的内存优化效果：

![hprof分析-视频通话-单人聊天-优化前](http://cdn.yangchaofan.cn/typora/hprof分析-视频通话-单人聊天-优化前.jpg)

`ChatActivity`页面优化前，retainedSzie为$695，703$`byte`,`Activity`多个实例无法被回收

![hprof-单人聊天视频聊天优化后](http://cdn.yangchaofan.cn/typora/hprof-单人聊天视频聊天优化后.jpg)

优化完，`retainedSize`为$491$`byte`，内存节省为原先的$1/1416$,`Activity`只剩一个实例，`depth`为0，表明过一会垃圾回收器会回收

此外，连续多次拨打视频类型通话，10次通话前后的视频通话`App`内存一直稳定在$149$mb左右，从内存变化曲线也可知，表明此次`ChatActivity`内存优化成功。

![hprof分析-单人聊天优化后](http://cdn.yangchaofan.cn/typora/hprof分析-单人聊天优化后.jpg)



## GroupChatActivity

`GroupChatActivity`是多人通话页面，内存泄漏情况有视频通话SDK导致的，也有其他原因的，原因都与`ChatActivity`类似，就不一一举例了，解决内存泄漏的方法参考同上。



## DeviceSetFragment

### 泄漏点1

老步骤，按图所示，查看问题代码

![hprof-设备列表Fragment泄漏](http://cdn.yangchaofan.cn/typora/hprof-设备列表Fragment泄漏.jpg)

可知内存泄漏的原因在于`SDK`不正确的使用，非静态内部类持有当前`DeviceSetFragment`实例的引用，导致`DeviceSetFragment`内存泄漏

```java
  MDVService.get().queryMyDeviceList(new DeviceListCB() {
            @Override
            public void onSuccess(List<DeviceInfo> deviceInfos) {
                dismissLoadProgress();
                if (devices != null && deviceInfos.size() > 0) {
                    recycleView_default.setVisibility(View.GONE);
                    devices.clear();
                    devices.addAll(deviceInfos);
                    myDevicesAdapter.notifyDataSetChanged();
                } else {
                    device_list_info.setText("获取设备列表成功，当前用户名下没有设备.");
                }
            }

            @Override
            public void onFailed(MDVError reason) {
                dismissLoadProgress();
                device_list_info.setText("获取设备列表失败." + reason.toString());
            }
        });
```

优化后：

```
MyDeviceListCB myDeviceListCB = new MyDeviceListCB(this);
MDVService.get().queryMyDeviceList(myDeviceListCB);

 static class MyDeviceListCB extends DeviceListCB {
        private WeakReference<DeviceSetFragment> deviceSetFragmentWeakReference;

        public MyDeviceListCB(DeviceSetFragment fragment) {
            deviceSetFragmentWeakReference = new WeakReference<>(fragment);
        }

        @Override
        public void onSuccess(List<DeviceInfo> deviceInfos) {
            if (deviceSetFragmentWeakReference.get() == null) {
                return;
            }
            deviceSetFragmentWeakReference.get().dismissLoadProgress();
            if (deviceSetFragmentWeakReference.get().devices != null && deviceInfos.size() > 0) {
                deviceSetFragmentWeakReference.get().recycleView_default.setVisibility(View.GONE);
                deviceSetFragmentWeakReference.get().devices.clear();
                deviceSetFragmentWeakReference.get().devices.addAll(deviceInfos);
                deviceSetFragmentWeakReference.get().myDevicesAdapter.notifyDataSetChanged();
            } else {
                deviceSetFragmentWeakReference.get().device_list_info.setText("获取设备列表成功，当前用户名下没有设备.");
            }
        }

        @Override
        public void onFailed(MDVError reason) {
            deviceSetFragmentWeakReference.get().dismissLoadProgress();
            deviceSetFragmentWeakReference.get().device_list_info.setText("获取设备列表失败." + reason.toString());
        }
    }
```

上述问题优化后，持续复测此页面10次，又发现了新的泄漏点。

### 泄漏点2-内部类广播

![hprof-设备列表页广播导致fragment内存泄漏](http://cdn.yangchaofan.cn/typora/hprof-设备列表页广播导致fragment内存泄漏.jpg)

泄漏点问题代码：未释放、未解绑的广播，广播是Fragment的内部类，持有了当前`Fragment`的实例引用，造成`Fragment`内存泄漏

```java
  private DeviceLocalBroadcastReceiver deviceBroadcastReceiver;
  private void initListenerAndEvent(Context context) {
        intentFilter = new IntentFilter("DEVICE_PERMISSION_UPDATE");
        deviceBroadcastReceiver = new DeviceLocalBroadcastReceiver();
        localBroadcastManager.registerReceiver(deviceBroadcastReceiver, intentFilter);
    }

    public class DeviceLocalBroadcastReceiver extends BroadcastReceiver {

        @Override
        public void onReceive(Context context, Intent intent) {
            //刷新用户设备列表
            if (SystemUtils.isSdkOk()) {
                getMyDeviceList();
            }
        }
    }
```

泄漏点2优化后：

```java
    deviceBroadcastReceiver = new DeviceLocalBroadcastReceiver(this);

public static class DeviceLocalBroadcastReceiver extends BroadcastReceiver {
    private WeakReference<DeviceSetFragment> deviceSetFragmentWeakReference;
    public DeviceLocalBroadcastReceiver(DeviceSetFragment deviceSetFragment){
        deviceSetFragmentWeakReference = new WeakReference<>(deviceSetFragment);
    }

    @Override
    public void onReceive(Context context, Intent intent) {
        //刷新用户设备列表
        if (SystemUtils.isSdkOk()) {
            if(deviceSetFragmentWeakReference.get()!=null &&
                    !deviceSetFragmentWeakReference.get().isAdded()){
                return;
            }
            if (!SystemUtils.isNetworkAvailable(deviceSetFragmentWeakReference.get().getContext())) {
                LogUtil.instance().logE("设备列表页", "没网禁止后续");
                ToastMng.toastShow(deviceSetFragmentWeakReference.get().getString(R.string.network_error));
                return;
            }
            MyDeviceListCB myDeviceListCB = new MyDeviceListCB(deviceSetFragmentWeakReference.get());
            MDVService.get().queryMyDeviceList(myDeviceListCB);
        }
    }
}
```



泄漏点1和2优化完后，增加了一些新步骤持续测试10次，又发现了新的泄漏点。

### 泄漏点3-MVP架构

![hprof-设备列表页Fragment内存泄漏-由于Activity未释放导致Fragment泄漏](http://cdn.yangchaofan.cn/typora/hprof-设备列表页Fragment内存泄漏-由于Activity未释放导致Fragment泄漏.jpg)

可以看到引用链为`MDVService->MainPresenter->Activity—>Fragment`，点击查看源码，看到又是视频通话SDK持有了`MainPresenter`，`Activity`作为`MainPresente`r实现类，也被视频通话SDK引用，造成了内存泄漏。此处是典型的MVP内存泄漏场景。

泄漏点3问题代码

```java
MainPresenter.java
private void login(MDVConfig config) {
    try {
        MDVService.get().loginMDVSdk(config, new AccountStateCallback.OnLoginStateCallBack() {
            @Override
            public void onLoginSuccess() {
                callback.loginSuccess();
            }

            @Override
            public void onLoginFailed(int errorCode, String errorMsg) {
                //code = 520007，系统时间错误
     			...
                callback.loginFailed(fail);
            }
        });
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

泄漏点3优化后：

```java
    private void login(MDVConfig config) {
        try {
            MyLoginCallBack myLoginCallBack = new MyLoginCallBack(context, callback);
            MDVService.get().loginMDVSdk(config, myLoginCallBack);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

  static class MyLoginCallBack implements AccountStateCallback.OnLoginStateCallBack {
        private WeakReference<Context> contextWeakReference;
        private WeakReference<MainConstract.View> viewWeakReference;

        public MyLoginCallBack(Context context, MainConstract.View callBack) {
            contextWeakReference = new WeakReference<>(context);
            viewWeakReference = new WeakReference<>(callBack);
        }

        @Override
        public void onLoginSuccess() {
            viewWeakReference.get().loginSuccess();
        }

        @Override
        public void onLoginFailed(int errorCode, String errorMsg) {
            //code = 520007，系统时间错误
            viewWeakReference.get().loginFailed(fail);
        }
    }
```



泄漏点3优化完后，继续复测10次，又出现了新的泄漏点，看来此Fragment的泄漏问题非常严重。

![hprof-设备聊天页-Application的持续引用导致Fragment泄漏](http://cdn.yangchaofan.cn/typora/hprof-设备聊天页-Application的持续引用导致Fragment泄漏.jpg)

点击查看源码，发现我们自定义的Application里持有了Activity，间接持有了Fragment，导致了内存泄漏

### 泄漏点4

问题代码：

```java
UMApplication.java
    
 ActivityLifecycleCallbacks activityLifecycleCallbacks = new ActivityLifecycleCallbacks() {    
    
       public void onActivityResumed(Activity activity) {
            Log.d(activity.getClass().getSimpleName(), "onResumed");
            UMApplication.this.mTopActivity = activity;
			...
            UMApplication.this.onActivityResumed(activity);
            if (UMApplication.this.mTopActivity instanceof UMApplication.OnNetworkStateChangeListener) {
                UMApplication.this.networkStateChangeListener = (UMApplication.OnNetworkStateChangeListener)UMApplication.this.mTopActivity;
            }

        }
    public void onActivityDestroyed(Activity activity) {
            Log.d(activity.getClass().getSimpleName(), "onDestroy");
            if (UMApplication.this.mTopActivity == activity) {
                if (UMApplication.this.mTopActivity instanceof UMApplication.OnNetworkStateChangeListener) {
                    UMApplication.this.networkStateChangeListener = null;
                }

                UMApplication.this.mTopActivity = null;
            }

            UMApplication.this.onActivityDestroyed(activity);
        }
    
}
```

可以清晰的看到`Application`中定义了一个非静态内部类`ActivityLifecycleCallbacks`，它持有了`Application`也持有了`Activity`,这样导致`Activity`即使调用`finsh`，处于`Destroyed`状态，`Activity`申请的内存依然无法释放，导致`Activity`的内存泄漏。

同时我也注意到，上一任开发者已经关注此处的内存泄漏问题了，已经再内部类中的另外一个内部方法`onActivityDestroyed`释放了`Activity`和`networkStateChangeListener`,可依然会导致Activity内存泄漏，这是为什么呢？

答案是：`Activity`的与`Fragment`的引用并未斩断,`Activity`进入`onDestroyed`的时候，`Fragment`还没来得及释放，导致`Fragment`仍然引用着`Activity`，此时`Activity`的内存也就泄漏了。

怎么办？

1. 在`Activity#onDestroy` ·释放Fragment

```java
Activity.java
    @Override
    protected void onDestroy() {
        super.onDestroy();
        releaseFragment();
    }
    public void releaseFragment(){
        mainPresenter = null;
        friendListFragment = null;
        deviceSetFragment = null;
        fragmentManager = null;
    }

```

2. `Application`里判断`Activity`的状态，处于`Destroyed`状态且内存未释放时，再次释放持有的`fragment`

```java
Application.java
@Override
public void onActivityDestroyed(Activity activity) {
    if (activity instanceof MultiDeviceVideoMainActivity && activity.isDestroyed()) {
        MultiDeviceVideoMainActivity release = (MultiDeviceVideoMainActivity) activity;
        release.releaseFragment();
    }
}
```

### 优化结果

优化后`Fragment`只存在一份实例，对比优化之前存在多份实例，表明内存优化成功；由于该`Fragment`是App首页，所以并不会销毁：

![hprof分析-设备列表优化后](http://cdn.yangchaofan.cn/typora/hprof分析-设备列表优化后.jpg)

优化前：$702，609$`byte`；优化后$1，734$`byte`，内存减少至原先的$1/405$

另外一个`FriendListFragment`也存在此类SDK调用传入匿名内部类问题，

## FriendListFragment

本页面有3处SDK调用，存在3处内存泄漏点

### 泄漏点1

优化前：

匿名内部类持有`Fragment`的实例引用

```java
  /**
     * 小红点更新
     */
    private void queryFriendApply() {
        showLoadProgress();
        MDVService.get().queryAllFriendReqRecord(new AllFriendReqRecordCB() {
            @Override
            public void onSuccess(List<FriendReqRecord> friendReqRecords) {
                dismissLoadProgress();
                if (friendReqRecords != null && friendReqRecords.size() > 0) {
                    for (FriendReqRecord friendReqRecord : friendReqRecords) {
                        if (friendReqRecord.getStatus() == 1) {
                            notification_message.setVisibility(View.VISIBLE);
                            return;
                        } else {
                            notification_message.setVisibility(View.GONE);
                        }
                    }
                } else {
                    notification_message.setVisibility(View.GONE);
                }
            }

            @Override
            public void onFailed(MDVError reason) {
                dismissLoadProgress();
            }
        });
    }
```



泄漏点1优化后

```java
 static class MyAllFriendReqRecordCB extends AllFriendReqRecordCB{
        private WeakReference<FriendListFragment> friendListFragmentWeakReference;
        public MyAllFriendReqRecordCB(FriendListFragment friendListFragment){
            friendListFragmentWeakReference = new WeakReference<>(friendListFragment);
        }
        @Override
        public void onSuccess(List<FriendReqRecord> friendReqRecords) {
            friendListFragmentWeakReference.get().dismissLoadProgress();
            if (friendReqRecords != null && friendReqRecords.size() > 0) {
                for (FriendReqRecord friendReqRecord : friendReqRecords) {
                    if (friendReqRecord.getStatus() == 1) {
                        friendListFragmentWeakReference.get().notification_message.setVisibility(View.VISIBLE);
                        return;
                    } else {
                        friendListFragmentWeakReference.get().notification_message.setVisibility(View.GONE);
                    }
                }
            } else {
                friendListFragmentWeakReference.get().notification_message.setVisibility(View.GONE);
            }
        }

        @Override
        public void onFailed(MDVError reason) {
            friendListFragmentWeakReference.get().dismissLoadProgress();
        }
    }

    /**
     * 小红点更新
     */
    private void queryFriendApply() {
        showLoadProgress();
        MyAllFriendReqRecordCB myAllFriendReqRecordCB = new MyAllFriendReqRecordCB(this);
        MDVService.get().queryAllFriendReqRecord(myAllFriendReqRecordCB);
    }
```



### 泄漏点2

优化前：

```java
 /**
     * 小红点更新
     */
    private void queryFriendApply() {
        showLoadProgress();
        MDVService.get().queryAllFriendReqRecord(new AllFriendReqRecordCB() {
            @Override
            public void onSuccess(List<FriendReqRecord> friendReqRecords) {
                dismissLoadProgress();
                if (friendReqRecords != null && friendReqRecords.size() > 0) {
                    for (FriendReqRecord friendReqRecord : friendReqRecords) {
                        if (friendReqRecord.getStatus() == 1) {
                            notification_message.setVisibility(View.VISIBLE);
                            return;
                        } else {
                            notification_message.setVisibility(View.GONE);
                        }
                    }
                } else {
                    notification_message.setVisibility(View.GONE);
                }
            }

            @Override
            public void onFailed(MDVError reason) {
                dismissLoadProgress();
            }
        });
    }
```

泄漏点2优化后：

```java
    private void queryAllFriends() {
        lv_friend.setVisibility(View.GONE);
        MyQeryFriendsCB myQeryFriendsCB = new MyQeryFriendsCB(this);
        MDVService.get().queryAllFriends(myQeryFriendsCB);
    } 

	static class MyQeryFriendsCB extends FriendsCB{
        private WeakReference<FriendListFragment> friendListFragmentWeakReference;
        public MyQeryFriendsCB(FriendListFragment friendListFragment){
            friendListFragmentWeakReference = new WeakReference<>(friendListFragment);
        }

        @Override
        public void onSuccess(List<Friend> friends) {
            Handler mainHandler = new Handler(Looper.getMainLooper());
            mainHandler.post(new Runnable() {
                @Override
                public void run() {
                    //在主线程中，更新friendslist
                    //否则更新会失败
                    friendListFragmentWeakReference.get().dismissLoadProgress();
                    //获取账号
                    friendListFragmentWeakReference.get().accounts.clear();
                    if (friends.size() != 0) {
                        for (int i = 0; i < friends.size(); i++) {
                            String names = friends.get(i).getFriendNickName();
                            String fullName = PinYinUtil.getPinYin(names);
                            String names_change = "";
                            if (!TextUtils.isEmpty(fullName)) {
                                names_change = fullName.substring(0, 1).toUpperCase();
                            }
                            String name = friends.get(i).getFriendName();
                            friendListFragmentWeakReference.get().accounts.add(name);
                            if (names_change.matches("[A-Z]")) {
                                friends.get(i).setNameFirstLetter(names_change);
                            } else {
                                friends.get(i).setNameFirstLetter("#");
                            }
                        }
                    }

                    Collections.sort(friends, new Comparator<Friend>() {
                        @Override
                        public int compare(Friend lhs, Friend rhs) {
                            String lhsname = lhs.getNameFirstLetter();
                            String rhsname = rhs.getNameFirstLetter();
                            int lhs_ascll = lhs.getNameFirstLetter().subSequence(0, 1).charAt(0);
                            int rhs_ascll = rhs.getNameFirstLetter().subSequence(0, 1).charAt(0);
                            if (lhs_ascll < 65 || lhs_ascll > 90) {
                                return 1;
                            } else if (rhs_ascll < 65 || rhs_ascll > 90) {
                                return -1;
                            } else {
                                return lhsname.compareTo(rhsname);
                            }
                        }
                    });

                    friendListFragmentWeakReference.get().friendList.clear();
                    friendListFragmentWeakReference.get().friendList.addAll(friends);
                    friendListFragmentWeakReference.get().friendAdapter.notifyDataSetChanged();
                    friendListFragmentWeakReference.get().lv_friend.setVisibility(View.VISIBLE);
                    friendListFragmentWeakReference.get().friend_account = friendListFragmentWeakReference.get().friendAdapter.getItemCount();
                    friendListFragmentWeakReference.get().tv_friend_count.setText("共" +  friendListFragmentWeakReference.get().friend_account + "位好友");
                    // 搜索一下
                    String searchText = friendListFragmentWeakReference.get().et_search.getText().toString().trim();
                    if (!searchText.isEmpty()) {
                        if(!SystemUtils.isNetworkAvailable(friendListFragmentWeakReference.get().getContext())){
                            LogUtil.instance().logE(TAG,"没网禁止后续");
                            ToastMng.toastShow(friendListFragmentWeakReference.get().getString(R.string.network_error));
                            return;
                        }
                        String searchTextInput = friendListFragmentWeakReference.get().et_search.getText().toString().trim();
                        if(!TextUtils.isEmpty(searchTextInput)){
                            StatisticsUtilChat.search(searchTextInput);
                        }
                        MyFriendsCB myFriendsCB = new MyFriendsCB(friendListFragmentWeakReference.get());
                        MDVService.get().filtFriend(searchTextInput.toString(), myFriendsCB);
                    }
                }
            });
        }

        @Override
        public void onFailed(MDVError reason) {
            friendListFragmentWeakReference.get().dismissLoadProgress();
            ToastMng.getInstance().showToast("获取通讯录失败！");
        }
    }

```



### 泄漏点3

优化前：

```java
private void searchFriends() {
     ...
        MDVService.get().filtFriend(searchText.toString(), new FriendsCB() {
         ....   
        }
 }
```

泄漏点3优化后：

```
static class MyFriendsCB extends FriendsCB{
    private WeakReference<FriendListFragment> friendListFragmentWeakReference;
    public MyFriendsCB(FriendListFragment friendListFragment){
        friendListFragmentWeakReference = new WeakReference<>(friendListFragment);
    }
 ...   
 }
 
private void searchFriends() {
   ...
        MyFriendsCB myFriendsCB = new MyFriendsCB(this);
        MDVService.get().filtFriend(searchText.toString(), myFriendsCB);
}
```

### 优化结果

优化前，点击切换10次，`FriendListFragment#retainedSzie`为$470019$`byte`，内存中会有多份实例

![image-20221128172652413](http://cdn.yangchaofan.cn/typora/image-20221128172652413.png)

优化后,点击切换10次，`FriendListFragment#retainedSzie`为$453，257$`byte`，当显示`FriendListFragment`时，内存中只有一份实例，表明此次 优化成功：

![hprof-好友列表优化后](http://cdn.yangchaofan.cn/typora/hprof-好友列表优化后.jpg)



## 全部泄漏点优化结果

总结一下视频通话App的内存优化结果：

「每个页面点击十次，页面内按钮随机点击10次,在同样的操作步骤下」

优化前，App占用内存$315，053，776$`byte`；优化后，App占用内存$97，970，395$`byte`，App总内存节省至原先的$1/3$，极大的节省了系统资源

优化前内存泄漏数为121，优化后内存泄为0，表明优化过的地方不再出现泄漏，121个`Activity/Fragment`泄漏点已经完全解决。

![hprof分析-视频通话优化121个泄漏点前后](http://cdn.yangchaofan.cn/typora/hprof分析-视频通话优化121个泄漏点前后.jpg)



