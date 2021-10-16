# Fragment空构造函数



## **为什么关注空构造函数？**



因为谷歌恢复Activity机制：在Activity异常原因被杀死后，重新恢复Activity的数据，Activity如果包含Fragment的话，会走Fragment的无参空构造函数。换而言之，谷歌的实现如此，要求开发者遵循这个规范。



## **为什么关注这个问题？**



编码实现Fragment不规范，App运行过程中会抛出**could not find Fragment constructor**的错误，造成App Crash影响用户体验和品牌价值



## **为什么会Android要去调用Fragment空构造函数而不调用我们写好的构造函数？**



**FragmentManager**的实现类**FragmentManagerImpl**中，恢复状态的代码**restoreAllState**如下



```
void restoreAllState(Parcelable state, FragmentManagerNonConfig nonConfig) { 
        // 其它代码......
        for (int i=0; i<fms.mActive.length; i++) {
            FragmentState fs = fms.mActive[i];
            if (fs != null) {
                // 其它代码...... 
                Fragment f = fs.instantiate(mHost, mParent, childNonConfig);
                // 其它代码......
            }
        }
        // 其它代码......      
}
```

**FragmentManagerImpl#restoreAllState**调用了**Fragment#instantiate方法**，**Fragment#instantiate**里面又调用了**Fragment#instantiate**



```
/**
     * Create a new instance of a Fragment with the given class name.  This is
     * the same as calling its empty constructor.
     */
    public static Fragment instantiate(Context context, String fname, @Nullable Bundle args) {
        try {
            Class<?> clazz = sClassMap.get(fname);
            if (clazz == null) {
                // Class not found in the cache, see if it's real, and try to add it
                clazz = context.getClassLoader().loadClass(fname);
                sClassMap.put(fname, clazz);
            }
            // 最关键的代码
            // 最关键的代码
            // 最关键的代码
            Fragment f = (Fragment)clazz.newInstance();
            if (args != null) {
                args.setClassLoader(f.getClass().getClassLoader());
                f.mArguments = args;
            }
            return f;
        } catch (ClassNotFoundException e) {
        	// 开发者熟悉的异常提示
            throw new InstantiationException("Unable to instantiate fragment " + fname
                    + ": make sure class name exists, is public, and has an"
                    + " empty constructor that is public", e);
        } catch (java.lang.InstantiationException e) {
           // 开发者熟悉的异常提示
            throw new InstantiationException("Unable to instantiate fragment " + fname
                    + ": make sure class name exists, is public, and has an"
                    + " empty constructor that is public", e);
        } catch (IllegalAccessException e) {
           // 开发者熟悉的异常提示
            throw new InstantiationException("Unable to instantiate fragment " + fname
                    + ": make sure class name exists, is public, and has an"
                    + " empty constructor that is public", e);
        }
    }
```



可以看到Android恢复Fragment状态的时候，是使用的**Class#newInstance**方法，这个方法调用的就是该类的无参构造方法。

换而言之，如果该类没有无参构造方法，Android虚拟机会抛出**java.lang.InstantiationException  could not find Fragment constructor**



## **这个问题有什么启示？**



1. 不要用Fragment的构造函数传递数据。

2. Fragment必须要有空的构造方法,见Fragment API原话**Applications should generally not implement a constructor**[^1]

3. Activity与Fragment通信最好使用[setArguments](https://developer.android.google.cn/reference/androidx/fragment/app/Fragment#setArguments(android.os.Bundle))和[getArguments](https://developer.android.google.cn/reference/androidx/fragment/app/Fragment#getArguments())[^1]

   

**Google也给出一段正确初始化Fragment的用法**

使用空构造柱初始化Fragment与原先的自定义Fragment构造函数相比，传参发生了变化

1. 空构造函数在开发实现的newInstance调用，在newInstance里面使用Bundle存储首次初始化所需的数据，并通过**Fragment#setArguments**持久化存储
2. 在onCreate里**Fragment#getArguments()**拿数据

```
public class BlankFragment extends Fragment {
    // TODO: Rename parameter arguments, choose names that match
    // the fragment initialization parameters, e.g. ARG_ITEM_NUMBER
    private static final String ARG_PARAM1 = "param1";
    private static final String ARG_PARAM2 = "param2";
 
    // TODO: Rename and change types of parameters
    private String mParam1;
    private String mParam2;
 
    public BlankFragment() {
        // Required empty public constructor
    }
 
    /**
     * Use this factory method to create a new instance of
     * this fragment using the provided parameters.
     *
     * @param param1 Parameter 1.
     * @param param2 Parameter 2.
     * @return A new instance of fragment BlankFragment.
     */
    // TODO: Rename and change types and number of parameters
    public static BlankFragment newInstance(String param1, String param2) {
        BlankFragment fragment = new BlankFragment();
        Bundle args = new Bundle();
        args.putString(ARG_PARAM1, param1);
        args.putString(ARG_PARAM2, param2);
        fragment.setArguments(args);
        return fragment;
    }
 
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        if (getArguments() != null) {
            mParam1 = getArguments().getString(ARG_PARAM1);
            mParam2 = getArguments().getString(ARG_PARAM2);
        }
    }
}
```







[1]:[Fragment  | Android Developers (google.cn)](https://developer.android.google.cn/reference/androidx/fragment/app/Fragment?hl=en#Fragment())