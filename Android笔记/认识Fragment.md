# 认识Fragment

- Fragment直观印象

- 为什么要有Fragment?

- Fragment的详细理解

## 1.Fragment直观印象

- Fragment:片段、碎片。是一部分内容构成的片段，体现在屏幕上是一块内容区域。

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220907152609206.png" alt="image-20220907152609206" style="zoom:67%;" />

## 2.为什么要有Fragment?

- 在Fragment之前，我们通常把一个Activity作为一个页面。

- 但随着页面元素的增加以及场景的复杂，单个页面已经不能满足需要，在屏幕上通常要同时展示多个区域、多个页面内容，这些内容的切换通常是整体的。

- 因此，为了让屏幕展示更多内容，以及对这些内容统一管理，引入了Fragment。

## 3.Fragment的详细理解

- Fragment，就是将一块内容区域封装在一起，统一管理，构成一个Fragment。

- Fragment是依附在Activity上而存在的。一个Activity中可以有多个Fragment，各个Fragment之间可以传递数据、互相切换。

- Fragment与Activity很相似，也有生命周期函数(onCreate、onPause、onDestroy等)如下是一个Fragment从开始到结束的生命周期流程:

onAttach->oncreate->onCreateView->onViewCreated->onActivityCreated->onStart->onResume->onpause->onStop->onDestroyView->onDestroy->onDetach

## 4.Fragment与Activity的直观理解

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220907153114183.png" alt="image-20220907153114183" style="zoom:67%;" />

















