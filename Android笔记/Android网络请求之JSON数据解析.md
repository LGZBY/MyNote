## Android网络请求之JSON数据解析

- 认识JSON数据格式

- 解析JSON数据

- 使用GSON库解析JSON数据到JavaBean

## 认识JSON数据格式

- JSON:(JavaScript Object Notation, JS 对象简谱)是一种轻量级的数据交换格式。

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220829172739675.png" alt="image-20220829172739675"  />

## JSON数据解析

- JSONObject表示一个JSON节点

- JSONObject.opt("key"')根据键获取值，如果没找到匹配的键，则返回空。（推荐)

- JSONObject.get("key")根据键获取值，如果没找到匹配的键，则抛出异常。

根据值的类型获取

- JSONObject.optString("key")

- JSONObject.optInt("key")

- JSONObject.optBoolean("key")

- JSONObject.optJSONObject("key")

- JSONObject.optJSONArray("key")

## 使用GSON库解析JSON数据到JavaBean

- 使用GSON库解析JSON数据到JavaBean

- 把json字符串直接转换成JavaBean对象

- 把JavaBean对象转换成json字符串

## 案例

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220903221111634.png" alt="image-20220903221111634" style="zoom:50%;" />

### manifests

在<manifest>后添加

```java
<uses-permission android:name="android.permission.INTERNET" />
```

### build.gradle

在dependencies{}里添加

```java
implementation("com.google.code.gson:gson:2.8.5")
```

最后再Sync Now下载下来

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20220903221526712.png" alt="image-20220903221526712" style="zoom:67%;" />

### Layout

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

        <TextView
            android:id="@+id/tv_content"
            style="@style/MyTextStyle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            tools:text="结果" />

        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="10dp"
            android:onClick="start"
            android:text="获取天气"
            android:textAllCaps="false" />

        <Spinner
            android:id="@+id/sp_cities"
            android:layout_width="match_parent"
            android:layout_height="48dp"
            android:entries="@array/cities"
            android:spinnerMode="dropdown"
            android:visibility="gone" />

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_marginRight="10dp"
            android:orientation="horizontal"
            android:paddingLeft="10dp">
            <TextView
                style="@style/MyTextStyle"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="天气：" />
            <TextView
                android:id="@+id/tv_weather"
                style="@style/MyTextStyle"
                android:layout_width="match_parent"
                tools:text="晴天" />
        </LinearLayout>

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_marginRight="10dp"
            android:layout_marginTop="10dp"
            android:orientation="horizontal"
            android:paddingLeft="10dp">
            <TextView
                style="@style/MyTextStyle"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="风向：" />
            <TextView
                android:id="@+id/tv_win"
                style="@style/MyTextStyle"
                android:layout_width="match_parent"
                tools:text="东北风" />
        </LinearLayout>

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_marginRight="10dp"
            android:layout_marginTop="10dp"
            android:orientation="horizontal"
            android:paddingLeft="10dp">
            <TextView
                style="@style/MyTextStyle"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="风力：" />
            <TextView
                android:id="@+id/tv_win_level"
                style="@style/MyTextStyle"
                android:layout_width="match_parent"
                tools:text="1级" />
        </LinearLayout>

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_marginRight="10dp"
            android:layout_marginTop="10dp"
            android:orientation="horizontal"
            android:paddingLeft="10dp">
            <TextView
                style="@style/MyTextStyle"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="最低温度：" />
            <TextView
                android:id="@+id/tv_tem_low"
                style="@style/MyTextStyle"
                android:layout_width="match_parent"
                tools:text="30°" />
        </LinearLayout>

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_marginRight="10dp"
            android:orientation="horizontal"
            android:layout_marginTop="10dp"
            android:paddingLeft="10dp">
            <TextView
                style="@style/MyTextStyle"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="最高温度：" />
            <TextView
                android:id="@+id/tv_tem_high"
                style="@style/MyTextStyle"
                android:layout_width="match_parent"
                tools:text="36°" />
        </LinearLayout>
    </LinearLayout>
</ScrollView>
```

### Activity

#### MainActivity

```java
package cn.edu.cdut.test_networkjson;

import android.os.Bundle;
import android.os.Handler;
import android.os.Looper;
import android.os.Message;
import android.util.Log;
import android.view.View;
import android.widget.TextView;
import android.widget.Toast;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;

import cn.edu.cdut.test_networkjson.util.NetUtil;
import com.google.gson.Gson;

import org.json.JSONException;
import org.json.JSONObject;

public class MainActivity extends AppCompatActivity {

    private TextView tvContent;
    private TextView tvWeather,tvWin,tvWinLevel,tvTemLow,tvTemHigh;

    private String[] cities;

    private Handler mHandler = new Handler(Looper.myLooper()){
        @Override
        public void handleMessage(@NonNull Message msg) {
            super.handleMessage(msg);

            if (msg.what == 0) {
                String strData = (String) msg.obj;
                tvContent.setText(strData);
                parseJsonDataAndShow(strData);

                Toast.makeText(MainActivity.this,"主线程收到来自网络的消息啦！",Toast.LENGTH_SHORT).show();
            }

        }
    };

    /**
     * json数据示例
     * {
     *     "cityid":"101120101",
     *     "city":"济南",
     *     "update_time":"20:55",
     *     "wea":"晴",
     *     "wea_img":"qing",
     *     "tem":"11",
     *     "tem_day":"17",
     *     "tem_night":"7",
     *     "win":"东南风 ",
     *     "win_speed":"1级",
     *     "win_meter":"小于12km/h",
     *     "air":"73"
     * }
     *
     * @param jsonStr
     */
    public void parseJsonDataAndShow(String jsonStr) {

        try {
            JSONObject jsonObject = new JSONObject(jsonStr);
            String cityId = jsonObject.optString("cityid");
            String city = jsonObject.optString("city");
            String updateTime = jsonObject.optString("update_time");
            String weather = jsonObject.optString("wea");
            String weatherImg = jsonObject.optString("wea_img");
            String tem = jsonObject.optString("tem");
            String wemDay = jsonObject.optString("tem_day");
            String weNight = jsonObject.optString("tem_night");
            String win = jsonObject.optString("win");
            String winSpeed = jsonObject.optString("win_speed");
            String winMeter = jsonObject.optString("win_meter");
            String air = jsonObject.optString("air");

            // 显示json数据
            getSupportActionBar().setTitle(city);

            tvWeather.setText(weather);
            tvWin.setText(win);
            tvWinLevel.setText(winSpeed);
            tvTemLow.setText(weNight);
            tvTemHigh.setText(wemDay);

            Gson gson = new Gson();
            // 把json字符串直接转换成JavaBean对象
            WeatherBean weatherBean = gson.fromJson(jsonStr, WeatherBean.class);
            Log.d("fan", "--weatherBean----" + weatherBean.toString());

            // 把JavaBean对象转换成json字符串
            String jsonWeather = gson.toJson(weatherBean);
            Log.d("fan", "--jsonWeather----" + jsonWeather);

        } catch (JSONException e) {
            e.printStackTrace();
        }
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        tvContent = findViewById(R.id.tv_content);
        tvWeather = findViewById(R.id.tv_weather);
        tvWin = findViewById(R.id.tv_win);
        tvWinLevel = findViewById(R.id.tv_win_level);
        tvTemLow = findViewById(R.id.tv_tem_low);
        tvTemHigh = findViewById(R.id.tv_tem_high);
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
        return NetUtil.getWeatherOfCity("北京");
    }
}
```

#### WeatherBean

```java
package cn.edu.cdut.test_networkjson;

import com.google.gson.annotations.SerializedName;

public class WeatherBean {

    //解析的数据要与json的值一一对应 //序列化名字
    @SerializedName("cityid") //用@SerializedName("cityid")可以让cityid与cityId匹配，如不加，则要改cityId成cityid与其一致
    private String cityId;

    @SerializedName("city")
    private String city;

    @SerializedName("wea")
    private String wea;

    @SerializedName("update_time")
    private String updateTime;

    @SerializedName("wea_img")
    private String weatherImg;

    @SerializedName("tem")
    private String tem;

    @SerializedName("tem_day")
    private String temDay;

    @SerializedName("tem_night")
    private String temNight;

    @SerializedName("win")
    private String win;

    @SerializedName("win_speed")
    private String winSpeed;

    @SerializedName("win_meter")
    private String winMeter;

    @SerializedName("air")
    private String air;

    public String getCityId() {
        return cityId;
    }

    public void setCityId(String cityId) {
        this.cityId = cityId;
    }

    public String getCity() {
        return city;
    }

    public void setCity(String city) {
        this.city = city;
    }

    public String getWea() {
        return wea;
    }

    public void setWea(String wea) {
        this.wea = wea;
    }

    public String getUpdateTime() {
        return updateTime;
    }

    public void setUpdateTime(String updateTime) {
        this.updateTime = updateTime;
    }

    public String getWeatherImg() {
        return weatherImg;
    }

    public void setWeatherImg(String weatherImg) {
        this.weatherImg = weatherImg;
    }

    public String getTem() {
        return tem;
    }

    public void setTem(String tem) {
        this.tem = tem;
    }

    public String getTemDay() {
        return temDay;
    }

    public void setTemDay(String temDay) {
        this.temDay = temDay;
    }

    public String getTemNight() {
        return temNight;
    }

    public void setTemNight(String temNight) {
        this.temNight = temNight;
    }

    public String getWin() {
        return win;
    }

    public void setWin(String win) {
        this.win = win;
    }

    public String getWinSpeed() {
        return winSpeed;
    }

    public void setWinSpeed(String winSpeed) {
        this.winSpeed = winSpeed;
    }

    public String getWinMeter() {
        return winMeter;
    }

    public void setWinMeter(String winMeter) {
        this.winMeter = winMeter;
    }

    public String getAir() {
        return air;
    }

    public void setAir(String air) {
        this.air = air;
    }

    @Override
    public String toString() {
        return "WeatherBean{" +
                "cityId='" + cityId + '\'' +
                ", city='" + city + '\'' +
                ", weather='" + wea + '\'' +
                ", updateTime='" + updateTime + '\'' +
                ", weatherImg='" + weatherImg + '\'' +
                ", tem='" + tem + '\'' +
                ", wemDay='" + temDay + '\'' +
                ", weNight='" + temNight + '\'' +
                ", win='" + win + '\'' +
                ", winSpeed='" + winSpeed + '\'' +
                ", winMeter='" + winMeter + '\'' +
                ", air='" + air + '\'' +
                '}';
    }
}
```

### util

#### NetUtil

```java
package cn.edu.cdut.test_networkjson.util;

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
            urlConnection.setConnectTimeout(10 * 1000);
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
                    outputStream.close();
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







