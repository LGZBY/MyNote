# RecyclerView

- RecyclerView.Adapter的写法

- 线性列表LinearLayoutManager

- 网格列表GridLayoutManager

- 瀑布流列表StaggeredGridLayoutManager

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220821113526335.png" alt="image-20220821113526335" style="zoom: 67%;" />

## 一、布局

### Layout

**activity_main.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/rlv"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textAllCaps="false" />

</LinearLayout>
```

**list_item_layout.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:id="@+id/rl_item_container"
    android:paddingTop="5dp"
    android:paddingBottom="5dp">

<!--注意RelativeLayout里的android:layout_height="wrap_content"是wrap_content-->

    <ImageView
        android:id="@+id/iv_img"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:scaleType="centerCrop"
        android:src="@drawable/ic_launcher_background" />

    <TextView
        android:id="@+id/tv_title"
        android:layout_width="wrap_content"
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
        android:layout_width="wrap_content"
        android:layout_height="70dp"
        android:layout_below="@id/tv_title"
        android:layout_marginLeft="10dp"
        android:layout_toRightOf="@id/iv_img"
        android:ellipsize="end"
        android:text="人生就像一场旅行，不必在乎目的地，在乎的是沿途的风景以及看风景的心情，让心灵去旅行"
        android:textSize="16sp" />
</RelativeLayout>
```

### menu

**menu_main.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <item android:id="@+id/menu_linear"
        android:title="LinearLayout"
        app:showAsAction="never"
        />
    <item android:id="@+id/menu_gird"
        android:title="GridLayout"
        app:showAsAction="never"
        />
    <item android:id="@+id/menu_stagger"
        android:title="StaggerLayout"
        app:showAsAction="never"
        />

</menu>
```

## 二、代码

### Adapter

**MyAdapter**

//RecyclerView无法直接设置条目的监听，所以不可在Activity里设置，要想设置监听可以在onBindViewHolder()方法里设置

```java
package cn.edu.cdut.test_recyclerview.Adapter;

import android.annotation.SuppressLint;
import android.content.Context;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ImageView;
import android.widget.RelativeLayout;
import android.widget.TextView;
import android.widget.Toast;

import androidx.annotation.NonNull;
import androidx.recyclerview.widget.RecyclerView;

import cn.edu.cdut.test_recyclerview.R;
import cn.edu.cdut.test_recyclerview.bean.NewsBean;

import java.util.List;
import java.util.Random;

public class MyAdapter extends RecyclerView.Adapter<MyAdapter.MyViewHolder> {

    private List<NewsBean> mBeanList;
    private LayoutInflater mLayoutInflater;
    private Context mContext;

    public MyAdapter(Context context,List<NewsBean> mBeanList){
        this.mBeanList = mBeanList;
        this.mContext = context;
        mLayoutInflater = LayoutInflater.from(mContext);
    }

    //获取条目数量
    @Override
    public int getItemCount() {
        return mBeanList.size();
    }

    //创建ViewHolder
    @NonNull
    @Override
    public MyViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = mLayoutInflater.inflate(R.layout.list_item_layout, parent, false);
        MyViewHolder myViewHolder = new MyViewHolder(view);
        return myViewHolder;
    }

    //绑定ViewHolder，将视图跟数据绑定
    //RecyclerView无法直接设置条目的监听，所以不可在Activity里设置，要想设置监听可以在onBindViewHolder()方法里设置
    @Override
    public void onBindViewHolder(@NonNull MyViewHolder holder, @SuppressLint("RecyclerView") int position) {
        NewsBean newsBean = mBeanList.get(position);
        holder.mTvTitle.setText(newsBean.getTitle());
        holder.mTvContent.setText(newsBean.getContent());
        holder.mIvImage.setImageResource(newsBean.getImageResourceId());

        Random random = new Random();//随机数
        int ran = random.nextInt(40)-10;//-10到30，random.nextInt(40)是0到40
        holder.mIvImage.setLayoutParams(new RelativeLayout.LayoutParams(dp2px(100),dp2px(100+ran)));//设置宽高//100默认是像素，需要转化成dp，自定义个像素转化dp的方法

        holder.rlContainer.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Toast.makeText(mContext,"你点击了"+position,Toast.LENGTH_SHORT).show();
            }
        });
    }

   /**
    *根据手机的分辨率从 dp的单位转成为 px(像素)
    */
    private int dp2px(int dp) {
        final float scale = mContext.getResources().getDisplayMetrics().density;
        return (int) (dp * scale + 0.5f);
    }

    class MyViewHolder extends RecyclerView.ViewHolder{
        TextView mTvTitle;
        TextView mTvContent;
        ImageView mIvImage;
        RelativeLayout rlContainer;

        public MyViewHolder(@NonNull View itemView) {
            super(itemView);
            this.mIvImage = itemView.findViewById(R.id.iv_img);
            this.mTvTitle = itemView.findViewById(R.id.tv_title);
            this.mTvContent = itemView.findViewById(R.id.tv_content);
            this.rlContainer = itemView.findViewById(R.id.rl_item_container);
        }
    }
}
```

### bean

**NewsBean**

```java
package cn.edu.cdut.test_recyclerview.bean;

import java.io.Serializable;

public class NewsBean implements Serializable {

    private String title;
    private String content;
    private int imageResourceId;

    public NewsBean() {
    }

    public NewsBean(String title, String content, int imageResourceId) {
        this.title = title;
        this.content = content;
        this.imageResourceId = imageResourceId;
    }

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

    public int getImageResourceId() {
        return imageResourceId;
    }

    public void setImageResourceId(int imageResourceId) {
        this.imageResourceId = imageResourceId;
    }
}
```

### Activity

**MainActivity**

```java
package cn.edu.cdut.test_recyclerview;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;
import androidx.recyclerview.widget.GridLayoutManager;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;
import androidx.recyclerview.widget.StaggeredGridLayoutManager;

import android.os.Bundle;
import android.view.Menu;
import android.view.MenuItem;

import java.util.ArrayList;
import java.util.List;

import cn.edu.cdut.test_recyclerview.Adapter.MyAdapter;
import cn.edu.cdut.test_recyclerview.bean.NewsBean;

public class MainActivity extends AppCompatActivity {

    private RecyclerView mRecyclerView;
    private List<NewsBean> mNewsBeanList;
    private MyAdapter mMyAdapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        initView();
        intData();
        initEvent();
    }

    private void initEvent() {
        mMyAdapter = new MyAdapter(this, mNewsBeanList);
        mRecyclerView.setAdapter(mMyAdapter);
        //设置RecyclerView显示的方式
        //线性列表 LinearLayoutManager
        RecyclerView.LayoutManager layoutManager = new LinearLayoutManager(this,LinearLayoutManager.VERTICAL,false);//reverseLayout是否反转//
          //网格列表 GridLayoutManager
//        RecyclerView.LayoutManager layoutManager = new GridLayoutManager(this, 2);
          //瀑布流列表 StaggeredGridLayoutManager
//        RecyclerView.LayoutManager layoutManager = new StaggeredGridLayoutManager(3, StaggeredGridLayoutManager.VERTICAL);
        mRecyclerView.setLayoutManager(layoutManager);
    }

    private void intData() {
        mNewsBeanList = new ArrayList<>();

        NewsBean newsBean1 = new NewsBean();
        newsBean1.setTitle("雨中漫步");
        newsBean1.setContent("人生就像一场旅行，不必在乎目的地，在乎的是沿途的风景以及看风景的心情，让心灵去旅行。\n只想进行一场漫无目的的旅行，在一个有花有海、安静缓慢的地方晒着太阳无所事事。\n背着背包的路上，看过许多人，听过许多故事，见过旅行风景，就这样，慢慢学会了长大。");
        newsBean1.setImageResourceId(R.drawable.test1);

        NewsBean newsBean2 = new NewsBean();
        newsBean2.setTitle("林间穿梭");
        newsBean2.setContent("只想进行一场漫无目的的旅行，在一个有花有海、安静缓慢的地方晒着太阳无所事事。\n路旁边浪似地滚着高高低低的黄土。太阳给埋在黄土里，发着肉红色。可是太阳还烧得怪起劲的，把他们的皮肉烧得变成紫黑色，似乎还闻得到一股焦味儿。");
        newsBean2.setImageResourceId(R.drawable.test2);

        NewsBean newsBean3 = new NewsBean();
        newsBean3.setTitle("旅行花海");
        newsBean3.setContent("只想进行一场漫无目的的旅行，在一个有花有海、安静缓慢的地方晒着太阳无所事事。\n背着背包的路上，看过许多人，听过许多故事，见过旅行风景，就这样，慢慢学会了长大。\n" +
                "\n" +
                "想呼吸着每座城市的空气，想感受着每座城市的人儿，想看着每座城市的风景。");
        newsBean3.setImageResourceId(R.drawable.test3);


        NewsBean newsBean4 = new NewsBean();
        newsBean4.setTitle("非平衡的线");
        newsBean4.setContent("一根线如果绷得太紧，总有一天会断裂，一颗心若是禁锢得太久，总有一天会失去平衡，我们需要放飞心灵，让心翱翔在自由的太空。每个人在他的人生发轫之初，总有一段时光，没有什么可留恋，只有抑制不住的梦想，没有什么可凭仗，只有他的好身体，没有地方可去，只想到处流浪。");
        newsBean4.setImageResourceId(R.drawable.test4);

        NewsBean newsBean5 = new NewsBean();
        newsBean5.setTitle("说走就走");
        newsBean5.setContent("说走就走的旅行，要么缘由幸福稳定和宽裕，要么祸起无力无奈和逃避。\n在旅途中，我遇见了你，你我相识是缘分！看着你手中的戒指，你说，你可以把它取下来吗？当我要取的时候，你淘气的躲开了，你说只有有缘人才可以取下，我看着你手中的戒指，想做你的有缘人，可是我知道结果是惨淡的，但还是心存希望！");
        newsBean5.setImageResourceId(R.drawable.test5);

        NewsBean newsBean6 = new NewsBean();
        newsBean6.setTitle("美好的记忆");
        newsBean6.setContent("旅行不在乎终点，而是在意途中的人和事还有那些美好的记忆和景色。人生就是一次充满未知的旅行，在乎的是沿途的风景，在乎的是看风景的心情，旅行不会因为美丽的风景终止。走过的路成为背后的风景，不能回头不能停留，若此刻停留，将会错过更好的风景，保持一份平和，保持一份清醒。享受每一刻的感觉，欣赏每一处的风景，这就是人生。（作者：《遇到另一种生活》");
        newsBean6.setImageResourceId(R.drawable.test6);

        NewsBean newsBean7 = new NewsBean();
        newsBean7.setTitle("久违的感动");
        newsBean7.setContent("人生最好的旅行，就是你在一个陌生的地方，发现一种久违的感动。人生最好的旅行，就是你在一个陌生的地方，发现一种久违的感动");
        newsBean7.setImageResourceId(R.drawable.test7);

        NewsBean newsBean8 = new NewsBean();
        newsBean8.setTitle("流浪日记");
        newsBean8.setContent("着背包的路上，看过许多人，听过许多故事，见过旅行风景，就这样，慢慢学会了长大。\n每个人在他的人生发轫之初，总有一段时光，没有什么可留恋，只有抑制不住的梦想，没有什么可凭仗，只有他的好身体，没有地方可去，只想到处流浪。");
        newsBean8.setImageResourceId(R.drawable.test8);

        NewsBean newsBean9 = new NewsBean();
        newsBean9.setTitle("山的尽头");
        newsBean9.setContent("在向山靠近一点，才发现这座山，好象一位诗人遥望远方，等待故人的归来。山上的树，大多数是松树比较突出。松树亭亭玉立的耸立在周围小草小花的中间，仿佛松树就是一位威风的将军，守护着国家的国民。\n在向山靠近一点，才发现这座山，好象一位诗人遥望远方，等待故人的归来。山上的树，大多数是松树比较突出。松树亭亭玉立的耸立在周围小草小花的中间，仿佛松树就是一位威风的将军，守护着国家的国民。\n在向山靠近一点，才发现这座山，好象一位诗人遥望远方，等待故人的归来。山上的树，大多数是松树比较突出。松树亭亭玉立的耸立在周围小草小花的中间，仿佛松树就是一位威风的将军，守护着国家的国民。");
        newsBean9.setImageResourceId(R.drawable.test9);

        NewsBean newsBean10 = new NewsBean();
        newsBean10.setTitle("秦河魅影");
        newsBean10.setContent("秦淮河、夫子庙，繁华依旧，只少了些当年桨声灯影的风味。喝一碗鸭血粉丝，吃一客蟹黄汤包，坐一次龙舟游船，览一河别样风景，发一回思古幽情，我想，这也许就是所谓夜游秦淮的小资情调了。独游，却只有看客的心情。\n秦淮河、夫子庙，繁华依旧，只少了些当年桨声灯影的风味。喝一碗鸭血粉丝，吃一客蟹黄汤包，坐一次龙舟游船，览一河别样风景，发一回思古幽情，我想，这也许就是所谓夜游秦淮的小资情调了。独游，却只有看客的心情。\n秦淮河、夫子庙，繁华依旧，只少了些当年桨声灯影的风味。喝一碗鸭血粉丝，吃一客蟹黄汤包，坐一次龙舟游船，览一河别样风景，发一回思古幽情，我想，这也许就是所谓夜游秦淮的小资情调了。独游，却只有看客的心情。");
        newsBean10.setImageResourceId(R.drawable.test10);

        mNewsBeanList.add(newsBean1);
        mNewsBeanList.add(newsBean2);
        mNewsBeanList.add(newsBean3);
        mNewsBeanList.add(newsBean4);
        mNewsBeanList.add(newsBean5);
        mNewsBeanList.add(newsBean6);
        mNewsBeanList.add(newsBean7);
        mNewsBeanList.add(newsBean8);
        mNewsBeanList.add(newsBean9);
        mNewsBeanList.add(newsBean10);
        mNewsBeanList.add(newsBean1);
        mNewsBeanList.add(newsBean2);
        mNewsBeanList.add(newsBean3);
        mNewsBeanList.add(newsBean4);
        mNewsBeanList.add(newsBean5);
        mNewsBeanList.add(newsBean6);
        mNewsBeanList.add(newsBean7);
        mNewsBeanList.add(newsBean8);
        mNewsBeanList.add(newsBean9);
        mNewsBeanList.add(newsBean10);
    }

    private void initView() {
        mRecyclerView = findViewById(R.id.rlv);
    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.menu_main,menu);
        return super.onCreateOptionsMenu(menu);
    }

    @Override
    public boolean onOptionsItemSelected(@NonNull MenuItem item) {
        switch (item.getItemId()) {
//线性列表 LinearLayoutManager
            case R.id.menu_linear:
                RecyclerView.LayoutManager llManager = new LinearLayoutManager(this,LinearLayoutManager.VERTICAL,false);
                mRecyclerView.setLayoutManager(llManager);
                return true;
//网格列表 GridLayoutManager
            case R.id.menu_gird:
                RecyclerView.LayoutManager gridLayoutManager = new GridLayoutManager(this, 2);
                mRecyclerView.setLayoutManager(gridLayoutManager);
                return true;
//瀑布流列表 StaggeredGridLayoutManager
            case R.id.menu_stagger:
                RecyclerView.LayoutManager staggeredGridLayoutManager = new StaggeredGridLayoutManager(3, StaggeredGridLayoutManager.VERTICAL);
                mRecyclerView.setLayoutManager(staggeredGridLayoutManager);
                return true;
            default:
                return super.onOptionsItemSelected(item);
        }
    }
}
```