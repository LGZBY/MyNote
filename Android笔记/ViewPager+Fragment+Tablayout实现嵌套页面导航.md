# ViewPager+Fragment+Tablayout实现嵌套页面导航

ViewPager+Fragment+TabLayout+BottomNavigationView实现底部导航页

ViewPager的用法

FragmentPagerAdapter的使用

BottomNavigationView的使用

ViewPager切换页面与BottomNavigationView的联动

TabLayout的用法

嵌套Fragment (ChildFragment）的用法



## ViewPager的用法

数据+适配器+ViewPager

数据:

List<View>、List<Fragment>

适配器:

FragmentPagerAdapter

## FragmentPagerAdapter的用法

重写两个方法:

@NonNull

@Override

public Fragment getltem(int position) {

return mFragmentList == null ? null : mFragmentList.get(position);

}



@Override

public int getCount() {

return mFragmentList == null ? 0 : mFragmentList.size();

}

## BottomNavigationView

<com.google.android.material.bottomnavigation.BottomNavigationView

android:layout_width="match_parent"

android:layout_height="wrap_content"

android:background="?android:windowBackground"

app:menu="@menu/bottom_nav_menu"

app:labelVisibilityMode="labeled"

android:id="@+id/bnv"

/>



app:menu

声明导航按钮，它的值是一个menu文件



app:menu声明导航按钮，它的值是一个menu文传

app:labelVisibilityMode导航按钮的显示模式，取值有auto、selected、labeled、unlabeled



- labeled : 几个导航按钮全部显示文字

- unlabeled : 几个导航按钮全部不显示文字

- selected:只有选中的按钮显示文字

-  auto :小于等于3个按钮时全部显示文字，即labeled;大于3个按钮时只有选中的按钮显示文字，即selected

## ViewPager与BottomNavigationView的联动

ViewPager联动BottomNavigationView

- mViewPager.addOnPageChangeListener(...)

- mBottomNavigationView.setSelectedltemld(R.id.menu_xxx);

BottomNavigationView联动ViewPager

- mBottomNavigationView.setOnltemSelectedListener(...)

- mViewPager.setCurrentltem(index)

## TabLayout

<com.google.android.material.tabs.TabLayout

​	android:id="@+id/home_tab_layout"

​	android:layout_width="match_parent"

​	android:layout_height="wrap_content"

​	app:tabGravity="start“

​	app:tabMode="auto"/>



app:tabMode设置tab的模式的，有这几个取值

- fixed:固定的，也就是标题不可滑动，有多少展示多少，平均分配长度

- scrollable:可滑动的，小于等于5个默认靠左固定，大于五个tab后就可以滑动

- auto:自动选择是否可滑动，小于等于5个默认居中固定，大于5个自动滑动

- app:tabGravity设置tab的位置

- start:居左fill:平均分配，铺满屏幕宽度center:居中



注意：



```java
//在fragment中，View，是创建出来的根视图，在activity中可以直接mViewPager =findViewById(R.id.home_vp);但在fragment中需mViewPager = view.findViewById(R.id.home_vp);
@Override
public View onCreateView(LayoutInflater inflater, ViewGroup container,
                         Bundle savedInstanceState) {
    return inflater.inflate(R.layout.fragment_vp_home, container, false);
}
```

## 案例

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220910152618902.png" alt="image-20220910152618902" style="zoom: 67%;" />































