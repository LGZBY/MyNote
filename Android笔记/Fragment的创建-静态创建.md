# Fragment的创建-静态创建

- Fragment的静态创建

- Fragment的动态创建

- 两种方式创建Fragment的生命周期的区别

## 1.Fragment的静态创建

- 添加依赖

- android.app.Fragment

  android.support.v4.app.Fragment

  androidx.fragment.app.Fragment(推荐，包含了上面两个)

  

  dependencies {

  implementation 'androidx.appcompat:appcompat:1.3.0'

  或者也可以用下面这个

  //implementation "androidx.fragment:fragment:1.3.0"

  }

- 使用xml代码引入Fragment

  - 创建一个Fragment

  - 布局代码中用一个容器承接并绑定

    ​	fragment标签

    ​	FragmentContainerView标签

- 注意

  - fragment标签

    ​	必须声明android:id或者android:tag属性，否则会有错

    ​	tools:layout="@layout/example_fragment"，可在预览视图中看到fragment预览界面

  - FragmentContainerView标签

    ​	fragment标签内必须声明android:id属性，否则会有错,不能用tag

  android:name属性也可以用class代替，即class=“com.example.fragmenttest2.ExampleFragment"

## 案例

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220907225818648.png" alt="image-20220907225818648" style="zoom:67%;" />

### build.gradle

在dependencies中将版本改为

```xml
implementation 'androidx.appcompat:appcompat:1.3.0'
```

或者1.3.0及以上

### layout

#### activity_main.xml

```java
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginRight="10dp"
        android:orientation="vertical"
        android:paddingLeft="10dp">

        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="10dp"
            android:onClick="jumpToStaticFragment1"
            android:text="to静态Fragment1"
            android:textAllCaps="false" />

    </LinearLayout>
</ScrollView>
```

#### activity_static_fragment.xml

```java
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".StaticFragmentActivity">

    //tag相当于id，class相当于name，fragment可用id或tag
    <fragment
        android:tag="fragment1"
        android:layout_margin="10dp"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:layout="@layout/fragment_static"
        class="cn.edu.cdut.test_fragment.fragment.StaticFragment"
        tools:ignore="MissingClass" />

    //用下列代码需在依赖中用implementation 'androidx.appcompat:appcompat:1.3.0'及以上版本
    //FragmentContainerView只能用id不能用tag
        <androidx.fragment.app.FragmentContainerView
            android:id="@+id/fragment2"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_margin="10dp"
            tools:layout="@layout/fragment_static"
            class="cn.edu.cdut.test_fragment.fragment.StaticFragment"
            />
</LinearLayout>
```

#### fragment_static.xml

```java
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:padding="10dp"
    android:background="@color/orange_500"
    tools:context=".fragment.StaticFragment">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:gravity="center_vertical"
        >

        <TextView
            android:id="@+id/tv_like"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="喜欢"
            android:textSize="20sp" />

        <RadioGroup
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:orientation="horizontal">

            <RadioButton
                android:id="@+id/rb_like"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="喜欢" />

            <RadioButton
                android:id="@+id/rb_dislike"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="不喜欢" />
        </RadioGroup>

    </LinearLayout>

    <RatingBar
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/rb_star"
        />

</LinearLayout>
```

### Activity

#### MainActivity

```java
package cn.edu.cdut.test_fragment;

import androidx.appcompat.app.AppCompatActivity;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

    }

    public void jumpToStaticFragment1(View view) {
        Intent intent = new Intent(this, StaticFragmentActivity.class);
        startActivity(intent);
    }
}
```

#### StaticFragment

```java
package cn.edu.cdut.test_fragment;

import androidx.appcompat.app.AppCompatActivity;

import android.os.Bundle;
import android.util.Log;

public class StaticFragmentActivity extends AppCompatActivity {

    private static final String TAG = "StaticFragmentActivity.tag";
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_static_fragment);
        Log.d(TAG, "onCreate: StaticFragmentActivity");
    }

    @Override
    protected void onStart() {
        super.onStart();
        Log.d(TAG, "onStart: StaticFragmentActivity");
    }

    @Override
    protected void onResume() {
        super.onResume();
        Log.d(TAG, "onResume: StaticFragmentActivity");
    }

    @Override
    protected void onPause() {
        super.onPause();
        Log.d(TAG, "onPause: StaticFragmentActivity");
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        Log.d(TAG, "onDestroy: StaticFragmentActivity");
    }
}
```

### fragment

#### StaticFragment

```java
package cn.edu.cdut.test_fragment.fragment;

import android.content.Context;
import android.os.Bundle;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.fragment.app.Fragment;

import android.util.AttributeSet;
import android.util.Log;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.CompoundButton;
import android.widget.RadioButton;
import android.widget.RatingBar;
import android.widget.TextView;
import android.widget.Toast;

import cn.edu.cdut.test_fragment.R;

public class StaticFragment extends Fragment {

    private static final String TAG = "StaticFragment.tag";

    private static final String ARG_PARAM1 = "param1";
    private static final String ARG_PARAM2 = "param2";

    private String mParam1;
    private String mParam2;

    private TextView tvLike;
    private RadioButton rbLike,rbDislike;
    private RatingBar rbStar;

    public StaticFragment() {
        Log.d(TAG, "StaticFragment1: construct");
    }


    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        if (getArguments() != null) {
            mParam1 = getArguments().getString(ARG_PARAM1);
            mParam2 = getArguments().getString(ARG_PARAM2);
        }
        Log.d(TAG, "onCreate: StaticFragment1");
    }

    //布局导入阶段
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        Log.d(TAG, "onCreateView: StaticFragment1");
        return inflater.inflate(R.layout.fragment_static, container, false);
    }

    //控件创建完成阶段
    @Override
    public void onViewCreated(@NonNull View view, @Nullable Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);
        Log.d(TAG, "onViewCreated: StaticFragment1");
        tvLike = view.findViewById(R.id.tv_like);
        rbLike = view.findViewById(R.id.rb_like);
        rbDislike = view.findViewById(R.id.rb_dislike);
        rbStar = view.findViewById(R.id.rb_star);

        rbLike.setOnCheckedChangeListener(new CompoundButton.OnCheckedChangeListener() {
            @Override
            public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
                if (isChecked) {
                    tvLike.setText("app喜欢");
                }
            }
        });

        rbDislike.setOnCheckedChangeListener(new CompoundButton.OnCheckedChangeListener() {
            @Override
            public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
                if (isChecked) {
                    tvLike.setText("app不喜欢");
                }
            }
        });

        rbStar.setOnRatingBarChangeListener(new RatingBar.OnRatingBarChangeListener() {
            @Override
            public void onRatingChanged(RatingBar ratingBar, float rating, boolean fromUser) {
                if (fromUser) {
                    Toast.makeText(getActivity(),"点了"+rating,Toast.LENGTH_LONG).show();
                }
            }
        });

    }

    //布局创建完成阶段
    @Override
    public void onActivityCreated(@Nullable Bundle savedInstanceState) {
        super.onActivityCreated(savedInstanceState);
        Log.d(TAG, "onActivityCreated: StaticFragment");
    }

    @Override
    public void onStart() {
        super.onStart();
        Log.d(TAG, "onStart: StaticFragment1");
    }

    @Override
    public void onResume() {
        super.onResume();
        Log.d(TAG, "onResume: StaticFragment1");
    }

    @Override
    public void onPause() {
        super.onPause();
        Log.d(TAG, "onPause: StaticFragment1");
    }

    @Override
    public void onDestroyView() {
        super.onDestroyView();
        Log.d(TAG, "onDestroyView: StaticFragment1");
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        Log.d(TAG, "onDestroy: StaticFragment1");
    }

    @Override
    public void onInflate(@NonNull Context context, @NonNull AttributeSet attrs, @Nullable Bundle savedInstanceState) {
        super.onInflate(context, attrs, savedInstanceState);
        Log.d(TAG, "onInflate: StaticFragment1");
    }
}
```

S