# ListView

- ListView

- 简单列表--ArrayAdapter

- 图文列表--SimpleAdapter

- 图文复杂列表--BaseAdapter

  ## 列表中的适配器原理--Adapter

![image-20220816225421279](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220816225421279.png)

## 案例

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220816225605971.png" alt="image-20220816225605971" style="zoom: 50%;" />



### 一.简单列表--ArrayAdapter

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220817155630635.png" alt="image-20220817155630635" style="zoom:50%;" />

**1.layout——activity_array_list.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".ArrayListActivity">

    <ListView
        android:id="@+id/lv"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>

</LinearLayout>
```

  先准备数据跟List View布局，用适配器将它们连起来，List View设置该设配器，最后给每条条目设置监听器

```java
private ListView mListView;
private List<String> mStringList;
private ArrayAdapter mArrayAdapter;

@Override

protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_array_list);

    mListView = findViewById(R.id.lv);
    mStringList = new ArrayList<>();

    for (int i = 0; i < 50; i++) {
        mStringList.add("条目" + i);
    }

    //注意布局要能与ArrayAdapter设配
    mArrayAdapter = new ArrayAdapter(this, android.R.layout.simple_list_item_activated_1, mStringList);//用适配器将数据跟List View布局连起来
    mListView.setAdapter(mArrayAdapter);

    mListView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
        @Override
        public void onItemClick(AdapterView<?> adapterView, View view, int i, long l) {
            Toast.makeText(ArrayListActivity.this, "点击了"+i, Toast.LENGTH_SHORT).show();
        }
    });

    mListView.setOnItemLongClickListener(new AdapterView.OnItemLongClickListener() {
        @Override
        public boolean onItemLongClick(AdapterView<?> adapterView, View view, int i, long l) {
            Toast.makeText(ArrayListActivity.this, "长按了"+i, Toast.LENGTH_SHORT).show();
            return true;//返回true，表示已经响应
        }
    });
}
```

### 二、图文列表--SimpleAdapter

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220818110417769.png" alt="image-20220818110417769" style="zoom:50%;" />

**1、layout——list_item_layout.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingLeft="10dp"
    android:paddingTop="5dp"
    android:paddingRight="10dp"
    android:paddingBottom="5dp">

    <ImageView
        android:id="@+id/iv_img"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:scaleType="centerCrop"
        android:src="@drawable/ic_launcher_background" />

    <TextView
        android:id="@+id/tv_title"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_toRightOf="@id/iv_img"
        android:ellipsize="end"
        android:maxLines="1"
        android:text="雨中漫步"
        android:textSize="20sp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/tv_content"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_below="@id/tv_title"
        android:layout_marginLeft="10dp"
        android:layout_toRightOf="@id/iv_img"
        android:ellipsize="end"
        android:maxLines="3"
        android:text="人生就像一场旅行，不必在乎目的地，在乎的是沿途的风景以及看风景的心情，让心灵去旅行"
        android:textSize="16sp" />
</RelativeLayout>
```

**layout——activity_simple_list**

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".SimpleListActivity">

    <ListView
        android:id="@+id/lv"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
</LinearLayout>
```

**2.Activity——SimpleListActivity**

```java
 private ListView mListView;
 private SimpleAdapter mSimpleAdapter;
 private List<Map<String,Object>> mList;
//提供图片
 private int[] imgs = {
     R.drawable.test1,
     R.drawable.test2,
     R.drawable.test3,
     R.drawable.test4,
     R.drawable.test5,
     R.drawable.test6,
     R.drawable.test7,
     R.drawable.test8,
     R.drawable.test9
 };

 @Override
 protected void onCreate(Bundle savedInstanceState) {
     super.onCreate(savedInstanceState);
     setContentView(R.layout.activity_simple_list);

     mListView = findViewById(R.id.lv);
     mList = new ArrayList<>();

     for (int i = 0; i < 50; i++) {
         Map<String,Object> map = new HashMap<>();//键值对
         map.put("img",imgs[i%imgs.length]);//i%imgs.length，防止数组越界
         map.put("title","这是标题"+i);
         map.put("content","这是内容"+i);
         mList.add(map);
     }

     mSimpleAdapter = new SimpleAdapter(this,
             mList,
             R.layout.list_item_layout,
             new String[]{"img", "title", "content"},
             new int[]{R.id.iv_img, R.id.tv_title, R.id.tv_content}
     );//String[]与int[]里的值一一对应，比如img对应R.id.iv_img

     mListView.setAdapter(mSimpleAdapter);
     mListView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
         @Override
         public void onItemClick(AdapterView<?> adapterView, View view, int i, long l) {
             Map<String, Object> map = mList.get(i);
             String title = (String) map.get("title");
             Toast.makeText(SimpleListActivity.this, "你点击了" + i + title, Toast.LENGTH_SHORT).show();
         }
     });
 }
```

### 三、图文复杂列表--BaseAdapter

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220818103038998.png" alt="image-20220818103038998" style="zoom:50%;" />

### 1.布局

**activity_base_adapter.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".BaseAdapterActivity">

    <ListView
        android:id="@+id/lv"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        />

</LinearLayout>
```

**list_item_layout.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingLeft="10dp"
    android:paddingTop="5dp"
    android:paddingRight="10dp"
    android:paddingBottom="5dp">

    <ImageView
        android:id="@+id/iv_img"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:scaleType="centerCrop"
        android:src="@drawable/ic_launcher_background" />

    <TextView
        android:id="@+id/tv_title"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_toRightOf="@id/iv_img"
        android:ellipsize="end"
        android:maxLines="1"
        android:text="雨中漫步"
        android:textSize="20sp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/tv_content"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_below="@id/tv_title"
        android:layout_marginLeft="10dp"
        android:layout_toRightOf="@id/iv_img"
        android:ellipsize="end"
        android:maxLines="3"
        android:text="人生就像一场旅行，不必在乎目的地，在乎的是沿途的风景以及看风景的心情，让心灵去旅行"
        android:textSize="16sp" />
</RelativeLayout>
```

**activity_news_detail.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        android:paddingLeft="10dp"
        android:paddingRight="10dp"
        tools:context=".NewsDetailActivity">
        <TextView
            android:id="@+id/tv_title"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="10dp"
            android:gravity="center"
            android:text="林间漫步"
            android:textSize="25sp"
            android:textStyle="bold" />

        <ImageView
            android:id="@+id/iv_img"
            android:layout_width="400dp"
            android:layout_height="300dp"
            android:layout_marginTop="10dp"
            android:scaleType="centerCrop"
            android:src="@color/colorPrimaryVariant" />

        <TextView
            android:id="@+id/tv_content"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="10dp"
            android:layout_marginBottom="40dp"
            android:gravity="start|left"
            android:text="一根线如果绷得太紧，总有一天会断裂，一颗心若是禁锢得太久，总有一天会失去平衡，我们需要放飞心灵，让心翱翔在自由的太空。"
            android:textSize="16sp"

            />
    </LinearLayout>
</ScrollView>
```

### 2.bean

```java
package cn.edu.cdut.test_listview_app.bean;

import java.io.Serializable;

public class ItemBean implements Serializable {
    private String title;
    private String content;
    private int imgResId;

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getContent() {
        return content;
    }

    public void setContent(String content) {
        this.content = content;
    }

    public int getImgResId() {
        return imgResId;
    }

    public void setImgResId(int imgResId) {
        this.imgResId = imgResId;
    }

    @Override
    public String toString() {
        return "ItemBean{" +
                "title='" + title + '\'' +
                ", content='" + content + '\'' +
                ", imgResId=" + imgResId +
                '}';
    }
}
```

### 3.设配器Adapter

**MyAdapter**

```java
package cn.edu.cdut.test_listview_app.Adapter;

import android.content.Context;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.ImageView;
import android.widget.TextView;

import java.util.List;

import cn.edu.cdut.test_listview_app.R;
import cn.edu.cdut.test_listview_app.bean.ItemBean;

public class MyAdapter extends BaseAdapter {

    private List<ItemBean> mBeanList;
    private LayoutInflater mLayoutInflater;//填充布局
    private Context mContext;//上下文

    //构造函数
    public MyAdapter(Context context,List<ItemBean> beanList){
        this.mContext = context;
        this.mBeanList = beanList;
        mLayoutInflater = LayoutInflater.from(mContext);//拿到填充器，先要得到上下文的填充器，接着才能填充子布局
    }

    //返回列表元素有多少条
    @Override
    public int getCount() {
        return mBeanList.size();
    }

    //获取条目
    @Override
    public Object getItem(int i) {
        return mBeanList.get(i);
    }

    @Override
    public long getItemId(int i) {
        return i;
    }

    //每当滑动时，都会创建该方法，inflate()都会被重新调用，以及控件的寻找创建，所以该方法会比较耗时
    //getView()方法在每个子项滚进屏幕时调用
    //将数据显示到条目
    @Override
    public View getView(int i, View view, ViewGroup viewGroup) {
        // 创建视图控件
        view = mLayoutInflater.inflate(R.layout.list_item_layout, viewGroup, false);//用得到的填充器填充布局,只是将布局填充进去，还未填充数据
        ImageView imageView = view.findViewById(R.id.iv_img);
        TextView tvTitle = view.findViewById(R.id.tv_title);
        TextView tvContent = view.findViewById(R.id.tv_content);

        // 为控件填充数据
        ItemBean itemBean = mBeanList.get(i);//得到条目在的位置
        imageView.setImageResource(itemBean.getImgResId());
        tvTitle.setText(itemBean.getTitle());
        tvContent.setText(itemBean.getContent());
        return view;
    }
}
```

**MyAdapter2**

​	在MyAdapter的基础上将getView()方法修改

```java
//MyAdapter2与MyAdapter相比，inflate()不会被多次调用,但控件会重新寻找创建
//getView()方法在每个子项滚进屏幕时调用
//将数据显示到条目
@Override
public View getView(int i, View view, ViewGroup viewGroup) {
    // 创建视图控件
    if (view == null){
        view = mLayoutInflater.inflate(R.layout.list_item_layout, viewGroup, false);//用得到的填充器填充布局,只是将布局填充进去，还未填充数据
    }
    ImageView imageView = view.findViewById(R.id.iv_img);
    TextView tvTitle = view.findViewById(R.id.tv_title);
    TextView tvContent = view.findViewById(R.id.tv_content);

    // 为控件填充数据
    ItemBean itemBean = mBeanList.get(i);//得到条目在的位置
    imageView.setImageResource(itemBean.getImgResId());
    tvTitle.setText(itemBean.getTitle());
    tvContent.setText(itemBean.getContent());
    return view;
}
```

**MyAdapter3**

​	在MyAdapter的基础上将getView()方法修改

```java
//getView()方法在每个子项滚进屏幕时调用
//将数据显示到条目
@Override
public View getView(int i, View view, ViewGroup viewGroup) {
    // 创建视图控件
    ViewHolder viewHolder= new ViewHolder();
    if (view == null){
        view = mLayoutInflater.inflate(R.layout.list_item_layout, viewGroup, false);//用得到的填充器填充布局,只是将布局填充进去，还未填充数据

        ImageView imageView = view.findViewById(R.id.iv_img);
        TextView tvTitle = view.findViewById(R.id.tv_title);
        TextView tvContent = view.findViewById(R.id.tv_content);

        viewHolder.imageView = imageView;
        viewHolder.tvTitle = tvTitle;
        viewHolder.tvContent = tvContent;

        view.setTag(viewHolder);
    }else{
        viewHolder  = (ViewHolder) view.getTag();
    }

    // 为控件填充数据
    ItemBean itemBean = mBeanList.get(i);//得到条目在的位置
    viewHolder.imageView.setImageResource(itemBean.getImgResId());
    viewHolder.tvTitle.setText(itemBean.getTitle());
    viewHolder.tvContent.setText(itemBean.getContent());
    return view;
}

//创建个用于暂时存储控件的类
class ViewHolder{
    ImageView imageView;
    TextView tvTitle;
    TextView tvContent;
}
```

### 4.Activity

**BaseAdapterActivity**

```java
package cn.edu.cdut.test_listview_app;

import androidx.appcompat.app.AppCompatActivity;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.AdapterView;
import android.widget.ListView;
import android.widget.Toast;

import java.util.ArrayList;
import java.util.List;

import cn.edu.cdut.test_listview_app.Adapter.MyAdapter3;
import cn.edu.cdut.test_listview_app.bean.ItemBean;

public class BaseAdapterActivity extends AppCompatActivity {
    private ListView mListView;
    private List<ItemBean> mBeanList;
    private MyAdapter3 mMyAdapter;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_base_adapter);

        initView();
        initData();
        initEvent();
    }

    private void initEvent() {
        mMyAdapter = new MyAdapter3(this,mBeanList);
        mListView.setAdapter(mMyAdapter);
        mListView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> adapterView, View view, int i, long l) {
                ItemBean itemBean = mBeanList.get(i);
                String title = itemBean.getTitle();
                Intent intent = new Intent(BaseAdapterActivity.this, NewsDetailActivity.class);
                intent.putExtra("item",itemBean);
                startActivity(intent);
                Toast.makeText(BaseAdapterActivity.this, "点击了"+i+title, Toast.LENGTH_SHORT).show();
            }
        });
    }

    private void initData() {
        mBeanList = new ArrayList<>();

        ItemBean newsBean1 = new ItemBean();
        newsBean1.setTitle("雨中漫步");
        newsBean1.setContent("人生就像一场旅行，不必在乎目的地，在乎的是沿途的风景以及看风景的心情，让心灵去旅行。\n只想进行一场漫无目的的旅行，在一个有花有海、安静缓慢的地方晒着太阳无所事事。\n背着背包的路上，看过许多人，听过许多故事，见过旅行风景，就这样，慢慢学会了长大。");
        newsBean1.setImgResId(R.drawable.test1);

        ItemBean newsBean2 = new ItemBean();
        newsBean2.setTitle("林间穿梭");
        newsBean2.setContent("只想进行一场漫无目的的旅行，在一个有花有海、安静缓慢的地方晒着太阳无所事事。\n路旁边浪似地滚着高高低低的黄土。太阳给埋在黄土里，发着肉红色。可是太阳还烧得怪起劲的，把他们的皮肉烧得变成紫黑色，似乎还闻得到一股焦味儿。");
        newsBean2.setImgResId(R.drawable.test2);

        ItemBean newsBean3 = new ItemBean();
        newsBean3.setTitle("旅行花海");
        newsBean3.setContent("只想进行一场漫无目的的旅行，在一个有花有海、安静缓慢的地方晒着太阳无所事事。\n背着背包的路上，看过许多人，听过许多故事，见过旅行风景，就这样，慢慢学会了长大。\n" +
                "\n" +
                "想呼吸着每座城市的空气，想感受着每座城市的人儿，想看着每座城市的风景。");
        newsBean3.setImgResId(R.drawable.test3);


        ItemBean newsBean4 = new ItemBean();
        newsBean4.setTitle("非平衡的线");
        newsBean4.setContent("一根线如果绷得太紧，总有一天会断裂，一颗心若是禁锢得太久，总有一天会失去平衡，我们需要放飞心灵，让心翱翔在自由的太空。每个人在他的人生发轫之初，总有一段时光，没有什么可留恋，只有抑制不住的梦想，没有什么可凭仗，只有他的好身体，没有地方可去，只想到处流浪。");
        newsBean4.setImgResId(R.drawable.test4);

        ItemBean newsBean5 = new ItemBean();
        newsBean5.setTitle("说走就走");
        newsBean5.setContent("说走就走的旅行，要么缘由幸福稳定和宽裕，要么祸起无力无奈和逃避。\n在旅途中，我遇见了你，你我相识是缘分！看着你手中的戒指，你说，你可以把它取下来吗？当我要取的时候，你淘气的躲开了，你说只有有缘人才可以取下，我看着你手中的戒指，想做你的有缘人，可是我知道结果是惨淡的，但还是心存希望！");
        newsBean5.setImgResId(R.drawable.test5);

        ItemBean newsBean6 = new ItemBean();
        newsBean6.setTitle("美好的记忆");
        newsBean6.setContent("旅行不在乎终点，而是在意途中的人和事还有那些美好的记忆和景色。人生就是一次充满未知的旅行，在乎的是沿途的风景，在乎的是看风景的心情，旅行不会因为美丽的风景终止。走过的路成为背后的风景，不能回头不能停留，若此刻停留，将会错过更好的风景，保持一份平和，保持一份清醒。享受每一刻的感觉，欣赏每一处的风景，这就是人生。（作者：《遇到另一种生活》");
        newsBean6.setImgResId(R.drawable.test6);

        ItemBean newsBean7 = new ItemBean();
        newsBean7.setTitle("久违的感动");
        newsBean7.setContent("人生最好的旅行，就是你在一个陌生的地方，发现一种久违的感动。人生最好的旅行，就是你在一个陌生的地方，发现一种久违的感动");
        newsBean7.setImgResId(R.drawable.test7);

        ItemBean newsBean8 = new ItemBean();
        newsBean8.setTitle("流浪日记");
        newsBean8.setContent("着背包的路上，看过许多人，听过许多故事，见过旅行风景，就这样，慢慢学会了长大。\n每个人在他的人生发轫之初，总有一段时光，没有什么可留恋，只有抑制不住的梦想，没有什么可凭仗，只有他的好身体，没有地方可去，只想到处流浪。");
        newsBean8.setImgResId(R.drawable.test8);

        ItemBean newsBean9 = new ItemBean();
        newsBean9.setTitle("山的尽头");
        newsBean9.setContent("在向山靠近一点，才发现这座山，好象一位诗人遥望远方，等待故人的归来。山上的树，大多数是松树比较突出。松树亭亭玉立的耸立在周围小草小花的中间，仿佛松树就是一位威风的将军，守护着国家的国民。\n在向山靠近一点，才发现这座山，好象一位诗人遥望远方，等待故人的归来。山上的树，大多数是松树比较突出。松树亭亭玉立的耸立在周围小草小花的中间，仿佛松树就是一位威风的将军，守护着国家的国民。\n在向山靠近一点，才发现这座山，好象一位诗人遥望远方，等待故人的归来。山上的树，大多数是松树比较突出。松树亭亭玉立的耸立在周围小草小花的中间，仿佛松树就是一位威风的将军，守护着国家的国民。");
        newsBean9.setImgResId(R.drawable.test9);

        ItemBean newsBean10 = new ItemBean();
        newsBean10.setTitle("秦河魅影");
        newsBean10.setContent("秦淮河、夫子庙，繁华依旧，只少了些当年桨声灯影的风味。喝一碗鸭血粉丝，吃一客蟹黄汤包，坐一次龙舟游船，览一河别样风景，发一回思古幽情，我想，这也许就是所谓夜游秦淮的小资情调了。独游，却只有看客的心情。\n秦淮河、夫子庙，繁华依旧，只少了些当年桨声灯影的风味。喝一碗鸭血粉丝，吃一客蟹黄汤包，坐一次龙舟游船，览一河别样风景，发一回思古幽情，我想，这也许就是所谓夜游秦淮的小资情调了。独游，却只有看客的心情。\n秦淮河、夫子庙，繁华依旧，只少了些当年桨声灯影的风味。喝一碗鸭血粉丝，吃一客蟹黄汤包，坐一次龙舟游船，览一河别样风景，发一回思古幽情，我想，这也许就是所谓夜游秦淮的小资情调了。独游，却只有看客的心情。");
        newsBean10.setImgResId(R.drawable.test10);

        mBeanList.add(newsBean1);
        mBeanList.add(newsBean2);
        mBeanList.add(newsBean3);
        mBeanList.add(newsBean4);
        mBeanList.add(newsBean5);
        mBeanList.add(newsBean6);
        mBeanList.add(newsBean7);
        mBeanList.add(newsBean8);
        mBeanList.add(newsBean9);
        mBeanList.add(newsBean10);
        mBeanList.add(newsBean1);
        mBeanList.add(newsBean2);
        mBeanList.add(newsBean3);
        mBeanList.add(newsBean4);
        mBeanList.add(newsBean5);
        mBeanList.add(newsBean6);
        mBeanList.add(newsBean7);
        mBeanList.add(newsBean8);
        mBeanList.add(newsBean9);
        mBeanList.add(newsBean10);
    }

    private void initView() {
        mListView = findViewById(R.id.lv);
    }
}
```

**NewsDetailActivity**

```java
package cn.edu.cdut.test_listview_app;

import android.content.Intent;
import android.os.Bundle;
import android.widget.ImageView;
import android.widget.TextView;

import androidx.appcompat.app.AppCompatActivity;

import cn.edu.cdut.test_listview_app.bean.ItemBean;

public class NewsDetailActivity extends AppCompatActivity {

    private TextView mTvTitle;
    private TextView mTvContent;
    private ImageView mIvImage;

    private ItemBean mNewsBean;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_news_detail);

        initView();
        initData();
        initEvent();
    }

    private void initView() {
        mIvImage = findViewById(R.id.iv_img);
        mTvContent = findViewById(R.id.tv_content);
        mTvTitle = findViewById(R.id.tv_title);
    }
    private void initData() {
        Intent intent = getIntent();
        mNewsBean = (ItemBean) intent.getSerializableExtra("item");
    }
    private void initEvent() {
        if (mNewsBean != null) {
            mTvTitle.setText(mNewsBean.getTitle());
            mTvContent.setText(mNewsBean.getContent());
            mIvImage.setImageResource(mNewsBean.getImgResId());
        }
    }
}
```

