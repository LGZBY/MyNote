# 初始Android中的网络请求

- 子线程中请求网络，显示到本地

- 使用HttpURLConnection从网络获取数据的过程

**从网络获取数据的过程**

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220827185630429.png" alt="image-20220827185630429" style="zoom: 50%;" />

## 案例

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220827191217048.png" alt="image-20220827191217048" style="zoom:50%;" />

### Layout

**activity_main.xml**

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
        
        <TextView
            android:id="@+id/tv_content"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            style="@style/MyTextStyle"
            android:text="结果"
            />

        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="10dp"
            android:text="开始"
            android:onClick="start"
            android:textAllCaps="false" />
    </LinearLayout>
</ScrollView>
```

### Activiity

**MainAcitivity**

```java
public class MainActivity extends AppCompatActivity {

    private TextView tvContent;
    String strFromNet;

    private Handler mHandler = new Handler(Looper.myLooper()){
        @Override
        public void handleMessage(@NonNull Message msg) {
            super.handleMessage(msg);

            if (msg.what == 0) {
                String strData = (String) msg.obj;
                tvContent.setText(strData);

                Toast.makeText(MainActivity.this,"主线程收到来自网络的消息啦！",Toast.LENGTH_SHORT).show();
            }

        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        tvContent = findViewById(R.id.tv_content);
    }

    public void start(View view) {
        // 做一个耗时任务
        new Thread(new Runnable() {
            @Override
            public void run() {
                String stringFromNet = getStringFromNet();

                Message message = new Message();
                message.what = 0;
                message.obj = stringFromNet;

                mHandler.sendMessage(message);

            }
        }).start();

        Toast.makeText(this,"开启子线程请求网络！",Toast.LENGTH_SHORT).show();
    }
    
    private String getStringFromNet() {
        return NetUtil.getWeatherOfCity("101110101");

//        return NetUtil3.doGet();

//        return NetUtil.doGet("hello");

        // 假装从网络获取了一个字符串
//        String result = "";
//
//        StringBuilder stringBuilder = new StringBuilder();
//
//        // 模拟一个耗时操作
//        for (int i = 0; i < 100; i++) {
//            stringBuilder.append("字符串" + i);
//        }
//
//        try {
//            Thread.sleep(6000);
//        } catch (InterruptedException e) {
//            e.printStackTrace();
//        }
//
//        result = stringBuilder.toString();
//
//        return result;
    }
}
```

### Util

**NetUtil**

```java
public class NetUtil {

    public static String doGet(String url) {
        HttpURLConnection urlConnection = null;
        BufferedReader reader = null;
        String bookJSONString = null;

        try {
            // 建立连接
            URL requestURL = new URL(url);
            urlConnection = (HttpURLConnection) requestURL.openConnection();
            urlConnection.setRequestMethod("GET");
            urlConnection.setConnectTimeout(5000);
            urlConnection.connect();

            // 从连接里获取输入流（二进制）
            InputStream inputStream = urlConnection.getInputStream();

            // 将输入流包装转换成 BufferedReader （我们人易懂的文本）
            reader = new BufferedReader(new InputStreamReader(inputStream));

            // 从 BufferedReader 中一行行读取字符串,用 StringBuilder 接收
            StringBuilder builder = new StringBuilder();

            String line;
            while ((line = reader.readLine()) != null) {
                builder.append(line);
//                builder.append("\n");
            }

            if (builder.length() == 0) {
                return null;
            }

            // StringBuilder 拼接成最终的字符串文本
            bookJSONString = builder.toString();


            //...
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //...
            if (urlConnection != null) {
                urlConnection.disconnect();
            }
            if (reader != null) {
                try {
                    reader.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }

        return bookJSONString;
    }

    public static String getWeatherOfCity(String cityCode) {
        String weatherBaseUrl = "http://www.weather.com.cn/data/cityinfo/%s.html";
        String realUrl = String.format(weatherBaseUrl,cityCode);
        Log.d("fan", "---------real url--------------:"+realUrl);
        return doGet(realUrl);
    }
}
```

**NetUtil3**

```java
public class NetUtil3 {

    public static String doGet() {
        String result = "";
        BufferedReader reader = null;
        String bookJSONString = null;
        try {
            // 1. 建立连接
            HttpURLConnection httpURLConnection = null;
            String url = "https://www.baidu.com";
            URL requestUrl = new URL(url);
            httpURLConnection = (HttpURLConnection)requestUrl.openConnection();
            httpURLConnection.setRequestMethod("GET");
            httpURLConnection.setConnectTimeout(5000);
            httpURLConnection.connect();

            // 2. 获取二进制流
            InputStream inputStream = httpURLConnection.getInputStream();
            // 3. 将二进制流包装
            reader = new BufferedReader((new InputStreamReader(inputStream)));
            // 4. 从BufferedReader中读取string字符串
            String line;
            StringBuilder builder = new StringBuilder();

            while ((line = reader.readLine()) != null) {
                builder.append(line);
                builder.append("\n");
            }

            if (builder.length() == 0) {
                return null;
            }

            result = builder.toString();

        } catch (Exception e) {
            e.printStackTrace();
        }

        return result;
    }
}
```