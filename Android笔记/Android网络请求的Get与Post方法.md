# Android网络请求的Get与Post方法

- 理解Android网络请求的本质:客户端与服务端交互，获取符合条件的数据

- 理解URI与URL

- GET请求

- POST请求

## 理解URI与URL

- URL:(Uniform Resource Locator，统一资源定位器)，它是WWW的统一资源定位标志，就是指网络地址。

- URl:(Uniform Resource ldentifier，统一资源标志符)，表示的是web上每一种可用的资源，如HTML文档、图像、视频片段、程序等。

- https:/img01.dmhmusic.com/0103/MO0/5E/73/ChR45GDeOwiAWObhAAHWRa6yrVE360.jpg

- https:lstatic1-music.taihe.com/clientimg/3e1deb9.png

结论：

- URL是URl的一种。

- URI是对网络资源更宽泛的一种标识。

- URL是我们对网络链接最常见的一个说法，更多的指http://www开头的

## Get请求

Get：得到，获得。

Get请求的URL举例: https://www.tianqiapi.com/free/day? cityid=10010&cityname=北京&date=20210708

![image-20220827195629093](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220827195629093.png)



## Post请求

主要目的:向服务端提交数据。

- post:邮寄，邮递，有抛出，提交的感觉。

- 场景举例:用户注册的时候，填写表格里一堆的信息（姓名、性别、年龄、电话...) ,这些都是要提交给服务端的数据

- Get请求的URL举例: https://www.tianqiapi.com/free/day? cityid=10010&cityname=北京&date=20210708

![image-20220827200343047](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220827200343047.png)

## Get与Post请求的对比

![image-20220827200526885](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220827200526885.png)

## 一个免费天气API网址

- 天气API网址: https://www.tianqiapi.com/index/doc

APl(Application Programming Interface)应用程序接,可是是函数、http接口等

- 登录后会有APPID和APPSecret

## 案例

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220903185230137.png" alt="image-20220903185230137" style="zoom:50%;" />

### Manifests

//在<manifest>中加入

```java
<uses-permission android:name="android.permission.INTERNET" />
```

### Layout

activity_main.xml

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
        <Spinner
            android:id="@+id/sp_cities"
            android:layout_width="match_parent"
            android:layout_height="48dp"
            android:entries="@array/cities"
            android:spinnerMode="dropdown"
            android:visibility="gone"
            />
    </LinearLayout>
</ScrollView>
```

### Activity

MainActivity

```java
package cn.edu.cdut.test_networkgetpost;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;

import android.os.Bundle;
import android.os.Handler;
import android.os.Looper;
import android.os.Message;
import android.view.View;
import android.widget.TextView;
import android.widget.Toast;

import cn.edu.cdut.test_networkgetpost.util.NetUtil;

public class MainActivity extends AppCompatActivity {

    private TextView tvContent;
    private String[] cities;

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
        return NetUtil.getWeatherOfCity("西安");
    }
}
```

#### Util

NetUtil

```java
package cn.edu.cdut.test_networkgetpost.util;

import android.util.Log;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.net.HttpURLConnection;
import java.net.URL;
import java.nio.charset.StandardCharsets;
import java.util.HashMap;
import java.util.Map;
import java.util.Set;

public class NetUtil {

    public static String BASE_URL = "https://www.tianqiapi.com/free/day";
    public static String APP_ID = "36646344";
    public static String APP_SECRET = "c1lgQbP9";

    public static String doGet(String url) {
        HttpURLConnection urlConnection = null;
        BufferedReader reader = null;
        String bookJSONString = null;

        try {
            // 1.建立连接
            URL requestURL = new URL(url);
            urlConnection = (HttpURLConnection) requestURL.openConnection();
            urlConnection.setRequestMethod("GET");
            urlConnection.setConnectTimeout(5000);
            urlConnection.setRequestProperty("Charset", "utf-8");
            urlConnection.connect();

            // 2.从连接里获取输入流（二进制）
            InputStream inputStream = urlConnection.getInputStream();

            // 3.将输入流包装转换成 BufferedReader （我们人易懂的文本）
            reader = new BufferedReader(new InputStreamReader(inputStream, StandardCharsets.UTF_8));

            // 4.从 BufferedReader 中一行行读取字符串,用 StringBuilder 接收
            StringBuilder builder = new StringBuilder();

            String line;
            while ((line = reader.readLine()) != null) {
                builder.append(line);
//                builder.append("\n");
            }

            if (builder.length() == 0) {
                return null;
            }

            // 5.StringBuilder拼接成最终的字符串文本
            bookJSONString = builder.toString();

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 关闭连接
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
    
    public static boolean doPost(String urlStr) {
        HttpURLConnection urlConnection = null;
        OutputStream outputStream = null;
        boolean result = false;

        try {
            URL url = new URL(urlStr);
            // 1. 打开连接
            urlConnection = (HttpURLConnection) url.openConnection();
            // 2.准备请求数据
            Map<String, String> paramMap = new HashMap<>();
            paramMap.put("userName", "fxj");
            paramMap.put("pass", "123");
            String paramData = paramMapToString(paramMap);
            // 3.设置连接信息
            urlConnection.setRequestMethod("POST");
            urlConnection.setConnectTimeout(10 * 1000);//10 * 1000单位为毫秒
            urlConnection.setRequestProperty("Content-Length", String.valueOf(paramData.length()));
            // 设置conn可以向服务端输出内容
            urlConnection.setDoOutput(true);
            // 4.获取输出流，并进行输出
            outputStream = urlConnection.getOutputStream();
            outputStream.write(paramData.getBytes());
            // 5.获取服务端的响应结果
            int code = urlConnection.getResponseCode();
            if (code == 200) {
                result = true;
            }

        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            if (urlConnection != null) {
                urlConnection.disconnect();
            }
            
            if (outputStream != null) {
                try {
                    outputStream.close();//关闭输出流
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
        return result;
    }

    public static String paramMapToString(Map<String, String> paramMap) {
        StringBuilder sb = new StringBuilder();

        Set<Map.Entry<String, String>> entries = paramMap.entrySet();

        for (Map.Entry<String, String> entry :
                entries) {
            sb.append(entry.getKey())
                    .append("=")
                    .append(entry.getValue())
                    .append("&");
        }

        // 去掉最后一个&
        sb.deleteCharAt(sb.length() - 1);

        return sb.toString();

    }
    
    public static String getWeatherOfCity(String city) {
        // 拼接出get请求的url
        String weatherUrl = BASE_URL + "?" + "appid=" + APP_ID + "&" + "appsecret=" + APP_SECRET + "&city=" + city;
        Log.d("fan", "----weatherUrl----" + weatherUrl);

        String weatherResult = doGet(weatherUrl);
        return decodeUnicode(weatherResult);
    }

    //将unicode字符转换成汉字
    public static String decodeUnicode(String unicodeStr) {
        if (unicodeStr == null) {
            return null;
        }
        StringBuffer retBuf = new StringBuffer();
        int maxLoop = unicodeStr.length();
        for (int i = 0; i < maxLoop; i++) {
            if (unicodeStr.charAt(i) == '\\') {
                if ((i < maxLoop - 5) && ((unicodeStr.charAt(i + 1) == 'u') || (unicodeStr.charAt(i + 1) == 'U')))
                    try {
                        retBuf.append((char) Integer.parseInt(unicodeStr.substring(i + 2, i + 6), 16));
                        i += 5;
                    } catch (NumberFormatException localNumberFormatException) {
                        retBuf.append(unicodeStr.charAt(i));
                    }
                else
                    retBuf.append(unicodeStr.charAt(i));
            } else {
                retBuf.append(unicodeStr.charAt(i));
            }
        }
        return retBuf.toString();
    }
}
```



