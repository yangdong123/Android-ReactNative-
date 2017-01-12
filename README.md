###### Android-ReactNative-
Android 集成 ReactNative


#### APP gradle 设置
## 添加依赖
dependencies {
compile "com.facebook.react:react-native:+"
}

## 设置支持的SO库架构

 defaultConfig {
 
        minSdkVersion 16   支持最小SDK 版本 不能小于16 ,小于16编译报错
        targetSdkVersion 23
        ndk {
        
            abiFilters "armeabi", "armeabi-v7a" , "x86" //,"x86","arm64-v8a","x86_64"
        }
        
        
    }

## 项目 gradle 设置

maven {
          
            url "$rootDir/react/node_modules/react-native/android" //资源目录,打包配置,这个配置资源显示在react 目录下
            
            
   }
        


## 创建MyReactActivity

public class SearchActivity  extends ReactActivity{

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        getWindow().setBackgroundDrawableResource(R.color.FFFFFF); //设置默认window颜色为白色
    }

## load RN 主入口 


    @Nullable
    @Override
    protected String getMainComponentName() {
    
        return "name"; 
        
        //该返回值必须和index.android.js 中AppRegistry.registerComponent('name', () => prpr); 的name 一致

    }


}

## AndroidManifest 配置

//添加权限
 <uses-permission android:name="android.permission.INTERNET" />
 <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>
 <uses-permission android:name="android.permission.SYSTEM_OVERLAY_WINDOW" />

 <application
        android:name=".MyApplication">
 </application>
 
 //注册Activity
 <activity android:name=".activity.MyReactActivity"
            android:configChanges="keyboard|keyboardHidden|orientation|screenSize"
            android:theme="@style/Theme.AppCompat.NoActionBar">
        </activity>
 //开发设置注册Activity 默认配置
  <activity android:name="com.facebook.react.devsupport.DevSettingsActivity"/>

## 项目Application 配置
public class MyApplication extends Application implements ReactApplication {
 
    @Override
    public ReactNativeHost getReactNativeHost() {
        return mReactNativeHost;
    }

    //设置RN debug 模式
    private final ReactNativeHost mReactNativeHost = new ReactNativeHost(this) {
        @Override
        protected boolean getUseDeveloperSupport() {
            return false;
        }

        @Override
        protected List<ReactPackage> getPackages() {
            return Arrays.<ReactPackage>asList(
                    new MainReactPackage(),
                    // new MyReactPackage()
            );
        }
    };
}

## 打包bundle 相关
在main 目录下创建 assetes 资源目录, 这个目录的作用是用来存储打包后的文件 

# 打包命令 注意: 根据自己的reactnative资源存放位置 修改引入的目录 
react-native bundle --platform android --dev true --entry-file index.android.js --bundle-output ../app/src/main/assets/index.android.bundle --assets-dest ../app/src/main/res/

#打包成功 如下面所示 :

MrDong:react MrDong$ react-native bundle --platform android --dev false --entry-file index.android.js --bundle-output ../app/src/main/assets/index.android.bundle --assets-dest ../app/src/main/res/
[2017-01-12 11:57:40] <START> Initializing Packager
watchman warning:  opendir(/Users/MrDong/Library/Saved Application State/com.adobe.flashplayer.installmanager.savedState) -> Permission denied. Marking this portion of the tree deleted
To clear this warning, run:
`watchman watch-del /Users/MrDong ; watchman watch-project /Users/MrDong`

[2017-01-12 11:57:40] <START> Building Haste Map
[2017-01-12 11:57:40] <END>   Building Haste Map (150ms)
[2017-01-12 11:57:40] <END>   Initializing Packager (569ms)
[2017-01-12 11:57:40] <START> Transforming files
[2017-01-12 11:57:42] <END>   Transforming files (1481ms)
bundle: start
bundle: finish
bundle: Writing bundle output to: ../app/src/main/assets/index.android.bundle
bundle: Copying 12 asset files
bundle: Done writing bundle output
bundle: Done copying assets
MrDong:react MrDong$ 

//这时你会发现你的main/asssets 资源目录里面多了两个文件 : 
index.android.bundle 
index.android.bundle.meta 

#### Android原生界面 和 RN(JS) 交互

1,首先创建出Rn 模块 Modules

public class MyIntentModule extends ReactContextBaseJavaModule {

    public MyIntentModule(ReactApplicationContext reactContext) {
        super(reactContext);
    }

    @Override
    public String getName() {
        return "FinishActivity"; //这个方法相当重要,返回值将会在RN中引用(相当于是对此类进行实例化)
//对应RN中引用为 const FinishActivity = NativeModules.FinishActivity;
//但是Js中必须import NativeModules
    }

    @ReactMethod
    public void finishActivity(String result) {

        Activity currentActivity = getCurrentActivity();

        Intent intent = new Intent();

        intent.putExtra("result", result);

        currentActivity.setResult(11, intent);

        currentActivity.finish();

    }


}

#### 创建ReactPackage 该类实例化在MyApplication 的getPackages() 方法中
public class MyReactPackage implements ReactPackage{
    @Override
    public List<NativeModule> createNativeModules(ReactApplicationContext reactContext) {
        return Arrays.<NativeModule>asList(
        //引入创建出来的模块
               new MyIntentModule(reactContext)
        );
    }

    @Override
    public List<Class<? extends JavaScriptModule>> createJSModules() {
        return Collections.emptyList();
    }

    @Override
    public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
        return new ArrayList<>();
    }
}


#### RN js中调用 MyIntentModule 中finishActivity(String result)方法:
FinishActivity.finishActivity("123");或者 NativeModules.FinishActivity.finishActivity("123"); 


￼￼
