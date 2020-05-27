## 题目类型：Reverse

## 题目名称：java 

下载题目 java.zip， 解压发现一个文件java.apk

用jadx-gui打开java.apk


分析 `MainActivity.java`
```
package ctf.crack.java;

import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

public class MainActivity extends AppCompatActivity {
    EditText m;
    Button n;
    String o;
    c p;

    /* access modifiers changed from: protected */
    public void onCreate(Bundle bundle) {
        super.onCreate(bundle);
        setContentView((int) R.layout.activity_main);
        this.n = (Button) findViewById(R.id.button);
        this.m = (EditText) findViewById(R.id.editText);
        this.n.setOnClickListener(new View.OnClickListener() {
            public void onClick(View view) {
                MainActivity mainActivity;
                String str;
                MainActivity.this.o = MainActivity.this.m.getText().toString();
                MainActivity.this.p = new c();
                if (MainActivity.this.o == null) {
                    mainActivity = MainActivity.this;
                    str = "Need Input";
                } else if (MainActivity.this.p.a(MainActivity.this.o)) {
                    mainActivity = MainActivity.this;
                    str = "Congratulations";
                } else {
                    mainActivity = MainActivity.this;
                    str = "Wrong Flag";
                }
                Toast.makeText(mainActivity, str, 1).show();
            }
        });
    }
}
```
关键点在于下面几行代码

```
MainActivity.this.o = MainActivity.this.m.getText().toString();
MainActivity.this.p = new c();
if (MainActivity.this.o == null) {
    mainActivity = MainActivity.this;
    str = "Need Input";
} else if (MainActivity.this.p.a(MainActivity.this.o)) {
    mainActivity = MainActivity.this;
    str = "Congratulations";
```
用户输入的字符串是flag，调用MainActivity.this.p.a校验flag，

如果校验成功就输出 `Congratulations`

而p是class c的实例，我们去看看class c

分析class c
class c的代码如下

```
package ctf.crack.java;

import android.support.v7.a.a;

public class c {
    public static String a = "aes_check_key!@#";
    public static String b = "VsBDJCvuhD65/+sL+Hlf587nWuIa2MPcqZaq7GMVWI0Vx8l9R42PXWbhCRftoFB3";
    int[] c = {214, 144, 233, 254, 204, 225, 61, 183, 22, 182, 43, a.j.AppCompatTheme_textColorAlertDialogListItem, 20, 194, 40, 251, 44, 5, 43, a.j.AppCompatTheme_textColorAlertDialogListItem, 154, a.j.AppCompatTheme_windowMinWidthMinor, 42, 190, 4, 195, 43, a.j.AppCompatTheme_textColorAlertDialogListItem, 170, 68, 19, 38, 73, 134, 43, a.j.AppCompatTheme_textColorAlertDialogListItem, 153, 156, 66, 80, 244, 145, 80, a.j.AppCompatTheme_textColorAlertDialogListItem, 239, 152, 122, 98, 50, 214};
    d d;

    public boolean a(String str) {
        byte[] bArr = new byte[0];
        byte[] a2 = e.a(str.getBytes(), a.getBytes());
        this.d = new d();
        this.d.a(a2, 22, this.c);
        return new String(b.a(a2)).equals(b);
    }
}
```
## e.a AES 加密
```
package ctf.crack.java;

import javax.crypto.Cipher;
import javax.crypto.spec.SecretKeySpec;

public class e {
    public static byte[] a(byte[] bArr, byte[] bArr2) {
        int length = 16 - (bArr.length % 16);
        byte[] bArr3 = bArr;
        for (int i = 0; i < length; i++) {
            bArr3 = a.a(bArr3, new byte[]{(byte) length});
        }
        try {
            SecretKeySpec secretKeySpec = new SecretKeySpec(bArr2, "AES");
            Cipher instance = Cipher.getInstance("AES/ECB/NoPadding");
            instance.init(1, secretKeySpec);
            return instance.doFinal(bArr3);
        } catch (Exception e) {
            e.printStackTrace();
            return new byte[0];
        }
    }
}
```

## d.a 异或运算

```
package ctf.crack.java;

public class d {
    private static d a = null;
    private byte[] b = {-42, -112, -23, -2, -52, -31, 61, -73, 22, -74, 20, -62, 40, -5, 44, 5, 43, 103, -102, 118, 42, -66, 4, -61, -86, 68, 19, 38, 73, -122, 6, -103, -100, 66, 80, -12, -111, -17, -104, 122, 51, 84, 11, 67, -19, -49, -84, 98, -28, -77, 28, -87, -55, 8, -24, -107, Byte.MIN_VALUE, -33, -108, -6, 117, -113, 63, -90, 71, 7, -89, -4, -13, 115, 23, -70, -125, 89, 60, 25, -26, -123, 79, -88, 104, 107, -127, -78, 113, 100, -38, -117, -8, -21, 15, 75, 112, 86, -99, 53, 30, 36, 14, 94, 99, 88, -47, -94, 37, 34, 124, 59, 1, 33, 120, -121, -44, 0, 70, 87, -97, -45, 39, 82, 76, 54, 2, -25, -96, -60, -56, -98, -22, -65, -118, -46, 64, -57, 56, -75, -93, -9, -14, -50, -7, 97, 21, -95, -32, -82, 93, -92, -101, 52, 26, 85, -83, -109, 50, 48, -11, -116, -79, -29, 29, -10, -30, 46, -126, 102, -54, 96, -64, 41, 35, -85, 13, 83, 78, 111, -43, -37, 55, 69, -34, -3, -114, 47, 3, -1, 106, 114, 109, 108, 91, 81, -115, 27, -81, -110, -69, -35, -68, Byte.MAX_VALUE, 17, -39, 92, 65, 31, 16, 90, -40, 10, -63, 49, -120, -91, -51, 123, -67, 45, 116, -48, 18, -72, -27, -76, -80, -119, 105, -105, 74, 12, -106, 119, 126, 101, -71, -15, 9, -59, 110, -58, -124, 24, -16, 125, -20, 58, -36, 77, 32, 121, -18, 95, 62, -41, -53, 57, 72};
    private int[] c = {462357, 472066609, 943670861, 1415275113, 1886879365, -1936483679, -1464879427, -993275175, -521670923, -66909679, 404694573, 876298825, 1347903077, 1819507329, -2003855715, -1532251463, -1060647211, -589042959, -117504499, 337322537, 808926789, 1280531041, 1752135293, -2071227751, -1599623499, -1128019247, -656414995, -184876535, 269950501, 741554753, 1213159005, 1684763257};

    public byte[] a(byte[] bArr, int i, int[] iArr) {
        if (bArr == null || bArr.length == 0) {
            return null;
        }
        int length = bArr.length;
        System.out.println("data.length");
        System.out.println(length);
        for (int i2 = 0; i2 < length; i2++) {
            bArr[i2] = (byte) (bArr[i2] ^ i);
        }
        for (int i3 = 0; i3 < bArr.length; i3++) {
            bArr[i3] = (byte) (bArr[i3] ^ iArr[i3]);
        }
        return bArr;
    }
}
```

## b.a base64 编码

```
package ctf.crack.java;

import android.util.Base64;

public class b {
    public static byte[] a(byte[] bArr) {
        return Base64.encode(bArr, 2);
    }
}
```

这里是破解的关键，我们来梳理一下流程

    第一步 aes加密
    byte[] a2 = e.a(str.getBytes(), a.getBytes());

    第二步 异或运算
    this.d.a(a2, 22, this.c);

    第三步 base64编码
    new String(b.a(a2))

    第四步 比较结果
    new String(b.a(a2)).equals(b);
    
比较第三步的结果与b是否一致，

而b为 `VsBDJCvuhD65/+sL+Hlf587nWuIa2MPcqZaq7GMVWI0Vx8l9R42PXWbhCRftoFB3`

到这里，解题思路就很清晰了，逆向整个算法

    第一步
    把b通过base64解编码，结果赋值给x

    第二步
    把x经过异或运算，结果赋值给y

    第三步
    把y经过aes解密，结果赋值给z，z则为我们要的flag

实现脚本如下：

```
import base64
from Crypto.Cipher import AES

b = "VsBDJCvuhD65/+sL+Hlf587nWuIa2MPcqZaq7GMVWI0Vx8l9R42PXWbhCRftoFB3"

c = [214, 144, 233, 254, 204, 225, 61, 183, 22, 182, 43, 103, 20, 194, 40, 251, 44, 5, 43, 103, 154, 118, 42, 190, 4,
     195, 43, 103, 170, 68, 19, 38, 73, 134, 43, 103, 153, 156, 66, 80, 244, 145, 80, 103, 239, 152, 122, 98, 50, 214]

x = base64.b64decode(b)

y = []
for i in range(len(x)):
    y.append(x[i] ^ 22 ^ c[i])
y = bytes(y)

aeskey = 'aes_check_key!@#'
cipher = AES.new(aeskey, AES.MODE_ECB)
z = cipher.decrypt(y)

print(z)
```
执行上述脚本，发现解密后的数据不对，再回去看看apk代码，发现在App类中，

有一行代码 `c.a = c.a.replace("e", "o");`

而c.a就是我们看到的aes密钥

```
package ctf.crack.java;

import android.app.Application;

public class App extends Application {
    private static App a;

    static {
        c.a = c.a.replace("e", "o");
    }

    public void onCreate() {
        super.onCreate();
        a = this;
        a = this;
    }
}
```

最终脚本：

```
import base64
from Crypto.Cipher import AES

b = "VsBDJCvuhD65/+sL+Hlf587nWuIa2MPcqZaq7GMVWI0Vx8l9R42PXWbhCRftoFB3"

x = base64.b64decode(b)

c = [214, 144, 233, 254, 204, 225, 61, 183, 22, 182, 43, 103, 20, 194, 40, 251, 44, 5, 43, 103, 154, 118, 42, 190, 4,
     195, 43, 103, 170, 68, 19, 38, 73, 134, 43, 103, 153, 156, 66, 80, 244, 145, 80, 103, 239, 152, 122, 98, 50, 214]

y = []
for i in range(len(x)):
    y.append(x[i] ^ 22 ^ c[i]) # this.d.a(a2, 22, this.c);
y = bytes(y)

aeskey = 'aes_check_key!@#'.replace("e", "o")
cipher = AES.new(aeskey, AES.MODE_ECB)
z = cipher.decrypt(y)

print(z.decode("utf-8")) # byte to str

```

运行结果：

```
root@kali:/tmp/demo# python3 t.py 
flag{67587AAF-C20A-4B6D-991B-A40FD3C2098E}

```
![](./flag.png)

## 参考链接：

https://www.freebuf.com/column/237625.html