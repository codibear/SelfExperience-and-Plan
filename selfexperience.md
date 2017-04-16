# 1.悬浮按钮 FloatingActionButton
FloatingActionButton改变icon颜色用`android:tint:”@color/.....”`
FloatingActionButton改变background颜色用`app:backgroundTint:”@color/.....”`
~~~
<android.support.design.widget.FloatingActionButton
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:src="@drawable/ic_add"
    app:backgroundTint="@color/orange"
    app:borderWidth="0dp"
    app:elevation="6dp"
    app:fabSize="normal" >
~~~

设置<LinearLayout>标签的 android:showDividers属性可以显示分隔线。  
如果有多个LinearLayout，显示效果和在 LinearLayout之间加分隔线是一样的。  
android:showDividers属性可以设置如下4个值：  
none：不显示分隔线；  
beginning：在LinearLayout的开始处显示分隔线；  
end：在Linearlayout的结尾处显示分隔线；  
middle：在LinearLayout中的每两个组件间显示分隔线。  
除了需要设置android:showDividers属性外，还要设置android:divider属性，该属性表示分隔线的图像。  

# 2.AdapterView
~~~
setOnItemClickListner\setOnItemSelectedListener //可以为其添加点击事件
 
listview.setOnItemClickListener\selected (new OnItemClickListener)
 @Override
//ItemClick  Public void onItemClick(AdapterView<?> parent, View view , int position, long id)
{
Shuchu(name[position])
}
~~~
注意使用select的时候要同时重写 onNotingSelected 方法

# 3.ListView divider 
footer\headerDividerHeight
# 4.ArrayAdapter  
ArrayAdapter <String> name = new ArrayAdapter <String >(Context this,textViewResource R.layout.textview, shuju list/array)

# 5.二维数组 
Private String [] []arms = nnew String [] []{  {“s”,”a”,”d”},{ “idj”,”doah”,”territory” }   } ;

# 6.[fragment的数据保存与恢复](http://www.jianshu.com/p/015c79bedb41)
单个Fragment遭遇突发情况
仍然是用以下突发情况进行测试：

- 点击back键
- 点击锁屏键
- 点击home键
- 其他APP进入前台
- 启动了另一个Activity
- 屏幕方向旋转
- APP被Kill


这么做的目的是当屏幕方向发生改变的时候，fragment所依附的Activity并不会重新销毁再创建，让情况相对简单一点。  

Fragment回到返回栈
1.临时数据 对于临时数据，我们使用onSaveInstanceState方法进行保存，并且在onCreateView方法中恢复（请注意是onCreateView）。  


2.永久数据 对于持久性数据，我们要在onPause方法中进行存储。  
　
对比这两张生命周期方法的图，能得出两个结论。
###  - 1.无论任务栈中fragment数量为多少，onSaveInstanceState方法都没有调用
###  - 2.当fragment任务栈中有多个fragment时，进入下一个fragment时，并不会销毁fragment实例，而是仅仅销毁视图，最终调用的方法为onDestoryView。
　　所以此时我们要去保存临时数据，并不能仅保存在onSaveInstanceState中（因为它可能不会调用），还应该在onDestoryView方法中进行保存临时数据的操作，源码如下：  

结束之前我们来一句话总结下本文：  
Fragment对临时数据的保存，仅仅依靠onSaveInstanceState方法是不行的，还需要在onDestoryView中进行相应操作，具体参考上面的代码。  
Fragment中对于一些持久性的数据，仍应在onPause中保存。  

# 7.Activity数据保存与恢复

在我写的这个demo中，onSaveInstanceState的调用是处于onPause和onStop之间的，（下面关于Activity的生命周期方法，会讲解一些值得大家注意的），我查阅了一下资料，能保证的是onSaveInstanceState方法会在onStop之前调用，但是是否在onPause之前就不一定了。  

结论： google工程师们对onSaveInstanceState如此设计就是让其完成对一些临时的、非永久数据存储并进行恢复。什么样的数据属于临时数据呢？举个例子，比如EditText中输入的内容，CheckBox是否勾选，ScrollView的滑动位置，目前视频的播放位置等等。  

（TextView）其他View控件都有相似的实现原理（自动保存）。值得一提的是，只有当你给这个wiget在xml中指定id时，它才具有保存数据并且恢复的能力，并且不同的wiget还不能共用这个id，否则会出现数据覆盖的情况。  

onSaveInstanceState的使用姿势   
比如我们要保存当前视频的播放进度，这个显然控件没有帮我们实现onSaveInstanceState，所以就只能靠自己了，代码如下所示。    

保存临时数据  

恢复临时数据  
当在onCreate取出临时数据时，记得加一个非空判断。  
看到这里，也许你认为本文该就此结束了，不过在回过头看看，我们刚才一直强调的是临时数据，毕竟onSaveInstanceState本身就是为临时数据服务的，但是一些永久性质的数据，比如插入数据库的操作，我们应该在什么方法中对其进行保存呢？  

# 8.三元操作“ ?  :  ;”的用法（i>0?i++:j=2）
**判断正确的话执行i++，错误的话执行j=2**

public static boolean isNotNull(String txt){
        return txt!=null && txt.trim().length()>0 ? true: false;
    }


# 9. chronometer 用于计时的实现
- bug1：当点击计时过一段时间再点击结束，然后在点击开始在点击结束，并不会继续第一次计时，而是返回新的时间。
- bug2：还是点击计时后在点击结束，再点击计时会出现乱码的现象，但是并不影响计时的时间。

在fragment上实现的：
~~~

import android.os.Bundle;
import android.os.SystemClock;
import android.support.annotation.Nullable;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.Button;
import android.widget.Chronometer;
import android.widget.ImageButton;
import android.widget.TextView;
import android.widget.Toast;


public class FragmentTimer extends android.support.v4.app.Fragment  {

  static  long start_time = 0 ;
  static  long end_time = 0 ;
  static  long mRecordTime = 0;

    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, Bundle savedInstanceState) {

        View view =  inflater.inflate(R.layout.fragment_timer,container,false);
        final ImageButton startButton = (ImageButton)view.findViewById(R.id.start);
        final ImageButton stopButton = (ImageButton)view.findViewById(R.id.stop);
        final Chronometer chronometer = (Chronometer)view.findViewById(R.id.timer);
        final Button submit = (Button)view.findViewById(R.id.submit);
        final TextView textTime = (TextView)view.findViewById(R.id.learntime) ;
        submit.setEnabled(false);




        startButton.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {

            if (mRecordTime != 0) {
                chronometer.setBase(chronometer.getBase() + (SystemClock.elapsedRealtime() - mRecordTime));
            } else {
                chronometer.setBase(SystemClock.elapsedRealtime());
            }

            start_time = SystemClock.elapsedRealtime();
            chronometer.start();
            startButton.setEnabled(false);
            stopButton.setEnabled(true);
            submit.setEnabled(false);
            textTime.setText("正在计时");

        }
    });
        stopButton.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {

            chronometer.stop();
            mRecordTime = SystemClock.elapsedRealtime();
            end_time = chronometer.getBase();
            startButton.setEnabled(true);
            stopButton.setEnabled(false);
            submit.setEnabled(true);
            textTime.setText("计时停止");
        }
    });

        submit.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
               long time1 = SystemClock.elapsedRealtime() - start_time;
               long time2 = (time1/1000)/59;
               long  time = (time1/1000)%59;
                textTime.setText("您学习了"+time2+"分"+time+"秒");
                chronometer.setBase(SystemClock.elapsedRealtime());
                chronometer.stop();
                Toast.makeText(getActivity(),"您的数据已提交",Toast.LENGTH_SHORT).show();
                submit.setEnabled(false);
            }
        });


        return view;
    }

}
~~~
**相应的xml部分:**

~~~
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:id="@+id/fragmenttimer" android:layout_height="match_parent">


    <ImageButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/start"
        android:layout_gravity="center"
        android:layout_weight="1"
        android:background="@drawable/start"
        android:layout_marginBottom="29dp"
        android:layout_above="@+id/timer"
        android:layout_toStartOf="@+id/timer" />
    <ImageButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:layout_gravity="center"
        android:id="@+id/stop"
        android:background="@drawable/stop"
        android:layout_alignTop="@+id/start"
        android:layout_toRightOf="@+id/timer"
        android:layout_toEndOf="@+id/timer" />

    <Chronometer
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/timer"
        android:textSize="50dp"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true" />

    <Button
        android:id="@+id/submit"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="59dp"
        android:text="提交"
        android:layout_below="@+id/timer"
        android:layout_centerHorizontal="true" />

    <TextView
        android:id="@+id/learntime"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textSize="20sp"
        android:layout_marginTop="27dp"
        android:layout_below="@+id/timer"
        android:layout_centerHorizontal="true" />

</RelativeLayout>
~~~
