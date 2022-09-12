# Fragment的动态创建

- 添加依赖

- 创建一个Fragment

- 布局代码中用一个容器承接，但不直接绑定

- 代码中，使用FragmentManager、FragmentTransaction添加Fragment到容器中



FragmentManager fragmentManager = getSupportFragmentManager();

FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();

fragmentTransaction.add(R.id.fragment_container_view2,ExampleFragment.class, null);

fragmentTransaction.commit();



- fragmentTransaction.add(R.id.fragment_container_view2, new ExampleFragment());



- 注意

  - 只有在FragmentActivity及其子类(如，AppCompatActivity）下才有getSupportFragmentManager这个方法。

  - 普通的Activity是没有该方法的，它有的是getFragmentManager。顾名思义，它获取的是非兼容的Fragment。而我们这里用的是AndroidX里面的，具有兼容性的Fragment,自然需要同样具有兼容性的FragmentManager，即需要用getSupportFragmentManager



import android.os.Bundle;

import androidx.appcompat.app.AppCompatActivity;



public class MainActivity2 extends AppCompatActivity {



​	@Override

​	protected void onCreate(Bundle savedInstanceState) {

​		super.onCreate(savedInstanceState);

​		setContentView(R.layout.activity_main2);

​		if (savedInstanceState == null){l防止重复添加

​			getSupportFragmentManager().beginTransaction()

​				.setReorderingAllowed(true)

​				.add(R.id.fragment_container_view2,ExampleFragment.class, null)

​				.commit();

​		}

​	}

}

## 案例

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220908213954747.png" alt="image-20220908213954747" style="zoom:50%;" />

### build.gradle

在dependencies中将版本改为

```xml
implementation 'androidx.appcompat:appcompat:1.3.0'
```

或者1.3.0及以上

### layout

#### activity_main.xml

```xml
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
            android:onClick="jumpToDynamicFragment"
            android:text="to动态Fragment"
            android:textAllCaps="false" />

    </LinearLayout>
</ScrollView>
```

#### activity_dynamic_fragment.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".DynamicFragmentActivity">

<!--    <FrameLayout-->
<!--        android:layout_width="match_parent"-->
<!--        android:layout_height="wrap_content"-->
<!--        -->
<!--        />-->

    <androidx.fragment.app.FragmentContainerView
        android:id="@+id/fcv"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        />

</LinearLayout>
```

#### fragment_example.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:padding="10dp"
    android:background="@color/orange_500"
    tools:context=".fragment.ExampleFragment">

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

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/tv_content"
        android:textSize="25sp"

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

    public void jumpToDynamicFragment(View view) {
        Intent intent = new Intent(this, DynamicFragmentActivity.class);
        startActivity(intent);
    }
}
```

#### DynamicFragmentActivity

```java
package cn.edu.cdut.test_fragment;

import android.os.Bundle;
import android.util.Log;

import androidx.appcompat.app.AppCompatActivity;
import androidx.fragment.app.FragmentManager;
import androidx.fragment.app.FragmentTransaction;

import cn.edu.cdut.test_fragment.fragment.ExampleFragment;

public class DynamicFragmentActivity extends AppCompatActivity {

    private static final String TAG = "DynamicFragmentActivity.tag";
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_dynamic_fragment);
        Log.d(TAG, "onCreate: DynamicFragmentActivity");
//        Log.d(TAG, "onCreate: saveInstance "+savedInstanceState);
        //加上if判断，防止fragment被重复添加
        if (savedInstanceState == null) {
            FragmentManager fragmentManager = getSupportFragmentManager();
            FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();

            Bundle bundle = new Bundle();
            bundle.putString(ExampleFragment.ARG_PARAM1,"这是动态Fragment");
            fragmentTransaction.add(R.id.fcv, ExampleFragment.class, bundle)
                    .setReorderingAllowed(true)//允许在fragment切换时，调整顺序
//                    .addToBackStack(null)
                    .commit();
        }
    }

    @Override
    protected void onStart() {
        super.onStart();
        Log.d(TAG, "onStart: DynamicFragmentActivity");
    }

    @Override
    protected void onResume() {
        super.onResume();
        Log.d(TAG, "onResume: DynamicFragmentActivity");
    }

    @Override
    protected void onPause() {
        super.onPause();
        Log.d(TAG, "onPause: DynamicFragmentActivity");
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        Log.d(TAG, "onDestroy: DynamicFragmentActivity");
    }
}
```

### fragment

#### ExampleFragment

```java
package cn.edu.cdut.test_fragment.fragment;

import android.content.Context;
import android.os.Bundle;
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

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.fragment.app.Fragment;

import cn.edu.cdut.test_fragment.R;

public class ExampleFragment extends Fragment {

    private static final String TAG = "ExampleFragment.tag";

    public static final String ARG_PARAM1 = "param1";
    public static final String ARG_PARAM2 = "param2";

    private String mParam1;
    private String mParam2;

    private TextView tvLike,tvContent;
    private RadioButton rbLike,rSbDislike;
    private RatingBar rbStar;

    public ExampleFragment() {
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
        return inflater.inflate(R.layout.fragment_example, container, false);
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
        tvContent = view.findViewById(R.id.tv_content);

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
        tvContent.setText(mParam1);
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

