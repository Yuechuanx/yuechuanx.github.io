---
title: RadioGroup实现按钮互斥效果
categories:
  - Android
tags:
  - android
---



在Android应用中，常常需要用到按钮互斥的效果，比如在一组按钮里面作单选，我们很容易想到用RadioButton去实现，但在更进一步的场景，比如微信App底部Tab的切换，在点击底部Button的时候会出现按钮高亮的效果，点击其他按钮会取消掉上一个高亮效果，Touch已经处于高亮效果的Button，会取消高亮，而释放时会继续高亮效果。

## 效果展示

文字的描述比较费力还不容易理解，那么先展示一下效果吧。

![ButtonMutex](http://oslixehxy.bkt.clouddn.com/18-5-3/93041749.jpg)



## 环境配置

Win10专业版

Android Studio 3.1.1

JDK 1.8.0

### 工程文件目录：（文末附源码）

![1525362107114](http://oslixehxy.bkt.clouddn.com/18-5-3/54060288.jpg)

- MainActivity.java:首页的界面

## 代码实现

### 布局文件

1. activity_main.xml 

   ~~~xml
   <?xml version="1.0" encoding="utf-8"?>
   <android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
       xmlns:app="http://schemas.android.com/apk/res-auto"
       xmlns:tools="http://schemas.android.com/tools"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       android:background="#000000"
       tools:context=".MainActivity">

       <LinearLayout
           android:layout_width="match_parent"
           android:layout_height="match_parent"
           android:orientation="horizontal">

           <RadioGroup
               android:id="@+id/rg_entry"
               android:layout_width="match_parent"
               android:layout_height="match_parent" >

               <RadioButton
                   android:id="@+id/rb_sync"
                   android:layout_width="wrap_content"
                   android:layout_height="wrap_content"
                   android:layout_weight="1"
                   android:checked="false"
                   android:button="@null"
                   android:drawableTop="@drawable/selector_rb_sync"/>

               <RadioButton
                   android:id="@+id/rb_apa"
                   android:layout_width="wrap_content"
                   android:layout_height="wrap_content"
                   android:layout_weight="1"
                   android:checked="false"
                   android:button="@null"
                   android:drawableTop="@drawable/selector_rb_apa"/>

               <RadioButton
                   android:id="@+id/rb_dvr"
                   android:layout_width="wrap_content"
                   android:layout_height="wrap_content"
                   android:layout_weight="1"
                   android:checked="false"
                   android:button="@null"
                   android:drawableTop="@drawable/selector_rb_dvr"/>
           </RadioGroup>

       </LinearLayout>
   </android.support.constraint.ConstraintLayout>
   ~~~

2. selector_rb_dvr.xml： dvr按钮被选中时对应的icon状态

   另外两个的selector_xxx.xml 代码与此相同，区别是`@drawable/xxx`加载的对象不同

   ~~~xml
   <?xml version="1.0" encoding="utf-8"?>
   <selector xmlns:android="http://schemas.android.com/apk/res/android">

       <item android:drawable="@drawable/icon_dvr_hl" android:state_pressed="false" android:state_selected="true" />
       <item android:drawable="@drawable/icon_dvr_hl" android:state_checked="true" android:state_pressed="false" />
       <item android:drawable="@drawable/icon_dvr_normal" />

   </selector>
   ~~~

### Activity代码

MainActivity.java

~~~java
package shu.shaw.buttonmutex;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.widget.RadioButton;
import android.widget.RadioGroup;
import android.widget.Toast;

public class MainActivity extends AppCompatActivity {

    private RadioGroup radioGroup;
    private RadioButton rbDvr;
    private RadioButton rbApa;
    private RadioButton rbSync;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);


        final RadioButton rbDvr = (RadioButton)findViewById(R.id.rb_dvr);
        final RadioButton rbApa = (RadioButton)findViewById(R.id.rb_apa);
        RadioButton rbSync = (RadioButton)findViewById(R.id.rb_sync);

        RadioGroup radioGroup = (RadioGroup)findViewById(R.id.rg_entry);
        radioGroup.setOnCheckedChangeListener(new RadioGroup.OnCheckedChangeListener() {
            @Override
            public void onCheckedChanged(RadioGroup group, int checkedId) {
                if (checkedId == rbDvr.getId()) {
                    Toast.makeText(getApplicationContext(), "click the DVR button", Toast.LENGTH_LONG).show();
                } else if (checkedId == rbApa.getId()) {
                    Toast.makeText(getApplicationContext(), "click the APA button", Toast.LENGTH_LONG).show();
                } else {
                    Toast.makeText(getApplicationContext(), "click the SYNC button", Toast.LENGTH_LONG).show();
                }
            }
        });
    }
}

~~~



附：[完整代码](https://github.com/yuechuanx/android-demo/tree/other/ButtonMutex)

## 参考

[Android自定义控件----RadioGroup实现APP首页底部Tab的切换](http://www.cnblogs.com/smyhvae/p/4463931.html)