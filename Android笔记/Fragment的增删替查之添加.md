# Fragment的增删替查之添加

- Fragment的添加、查找、移除、替换、显示、隐藏

  (add、 find、 remove、 replace、show、hide、attach、detach)

- 理解Fragment的BackStack

- 命令行查看当前存在的Fragment

## 1.Fragment的添加-add

方式一:

FragmentManager fragmentManager = getSupportFragmentManager();

FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();

fragmentTransaction.add(R.id.fragment, ExampleFragment.class, null).commit();

方式二:

FragmentManager fragmentManager = getSupportFragmentManager();

FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();

fragmentTransaction.add(R.id.fragment, ExampleFragment.class, null)

​	.addToBackStack("myFragment2")

​	.commit();

方式三:

FragmentManager fragmentManager = getSupportFragmentManager();

FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();

ExampleFragment fragment = new,fExampleFragment();

fragmentTransaction.add(R.id.fragment, fragment).commit();



## 1.1 Fragment的添加-add-动画演示

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909171407332.png" alt="image-20220909171407332" style="zoom: 67%;" />

- 一个容器里可以添加多个Fragment，依次盖在上面。

- 类比FrameLayout
- 

注意:由于事件分发机制，同时添加多个Fragment时，在空白处点击，会产生穿透的效果,也就是作用到下层的Fragment。

解决方法:在Fragment里拦截onTouch事件。

https://blog.csdn.net/alex440681/articleldetails/102240699



因此，最好不要用add方式，而用replace添加fragment，保证容器中里只有一个Fragment

### 命令行查看当前存在的Fragment

命令行输入: adb shell dumpsys activity fragment

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909171810511.png" alt="image-20220909171810511" style="zoom:67%;" />

如果出现下列情况

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909192811856.png" alt="image-20220909192811856" style="zoom:67%;" />

以下是解决方法

1.点击设置里的”高级设置“，点”确定“

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909194409280.png" alt="image-20220909194409280" style="zoom: 50%;" />

2.选择”环境变量“，点”确定“

3.找到系统变量里的”Path“，点”编辑“”确定“

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909194606819.png" alt="image-20220909194606819" style="zoom: 50%;" />



## 1.2 Fragment的添加-addToBackStack动画演示

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909172313277.png" alt="image-20220909172313277" style="zoom:67%;" />

addToBackStack的作用:将Fragment添加到容器的同时也添加到BackStack里。

效果是:

这时点击手机的back按钮,就会依次弹出BackStack中的Fragment，而不是直接退出Activity。

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909172527408.png" alt="image-20220909172527408" style="zoom:67%;" />

不同名字的BackStack，也是按添加的先后顺序依次盖在上面

## 2.Fragment的删除和查找-remove

FragmentManager fragmentManager = getSupportFragmentManager();

FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();

Fragment fragment = fragmentManager.findFragmentByld(R.id.fragment);

if (fragment != null) {

​	fragmentTransaction.remove(fragment).commit();

}

- 删除是以查找为前提的













## 案例

















































