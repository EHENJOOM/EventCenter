# EventCenter事件分发中心

[![](https://jitpack.io/v/EHENJOOM/EventCenter.svg)](https://jitpack.io/#EHENJOOM/EventCenter)

EventCenter事件分发中心基于**对象池**及接口回调实现，主要解决在Activity/Fragment/Service等组件之间的消息事件传递问题。

### 什么是对象池？

**在java中，对象的生命周期包括对象创建、对象使用，对象消失三个时间段，其中对象的使用是对象真正需要存活的时间，不好修改，该用的时候还得使用啊。对象的创建和消失就得好好控制下了。对象的创建是比较费时间的，也许感觉不到，好比一个赋值操作int i=1，也是需要耗时的，在比如构造一个对象，一个数组就更加消耗时间。再说对象的消除，在 java 里面使用GC来进行对象回收，其实也是需要对对象监控每一个运行状态，包括引用，赋值等。在Full GC的时候，会暂停其他操作，独占CPU。所以，我们需要控制对象的创建数量，也不要轻易的让对象消失，让他的复用更加充分。**

## 使用方法：

### 1.添加依赖

首先，在build.gradle文件下加入 maven {url 'https://jitpack.io'}

```javascript
allprojects {
	repositories {
		google()
		jcenter()
		maven {url "https://jitpack.io"}
	}
}
```

然后在dependencies下加入依赖

```js
implementation 'com.github.EHENJOOM:EventCenter:1.0.0'
```

### 2.注册、注销监听器

在A Activity的onCreate()方法中，调用**CEventCenter.registerEventListener(I_CEventListener listener, String topic/String[] topics) **方法注册监听器，A Activity需要实现I_CEventListener接口，重写**onCEvent(String topic, int msgCode, int resultCode, Object obj)**方法，同时不要忘记在onDestroy()方法中调用**CEventCenter.unregisterEventListener(I_CEventListener listener, String topic/String[] topics) **方法注销监听器。

```java
public class A_Activity extends AppCompatActivity implements I_CEventListener{
    private static final String[] EVENTS={
        Events.SINGLE_CHAT,
        Events.DATA_CHANGE
    }
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_a);
        CEventCenter.registerEventListener(this, EVENTS);
    }
    
    @Override
    public void onDestroy(){
        super.onDestroy();
        CEventCenter.unregisterEventListener(this,EVENTS);
    }
    
    /**
     * 对接收到的事件进行处理
     * @param topic
     *              事件名称
     * @param msgCode
     *              消息类型
     * @param resultCode
     *              预留参数
     * @param object
     *              回调返回数据
     */
    @Override
    public void onCEvent(String topic,int msgCode,int resultCode,Object object){
        switch(topic){
            case Events.SINGLE_CHAT:
                break;
            case Events.DATA_CHANGE:
                break;
            default:
                break;
        }
    }
}
```

### 3.发布事件

在B Activity中调用**CEventCenter.dispatchEvent(CEvent event/ String topic, int msgCode, int resultCode, Object obj)** 方法发布事件即可。

```java
public class B_Activity extends AppCompatActivity{
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_a);
        // 若无传递信息，msgCode、resultCode、object可随便写
        CEventCenter.dispatchEvent(Events.DATA_CHANGE,1,1,1);
    }
}
```

### 4.建立Events管理中心

当项目比较大时，建议建立Events类集中管理所有的事件。

```java
public class Events {
    public static final String SINGLE_CHAT = "single_chat";
    public static final String DATA_CHANGE = "data_change";
}
```

### 写在最后

EventCenter支持一对一、一对多发布主题事件，事件分发完毕后，把对象放回对象池里面，便于对象复用。相对于BroadcastReceiver更加轻量级，资源消耗较少。

EventCenter代码来自**FreddyChen**。