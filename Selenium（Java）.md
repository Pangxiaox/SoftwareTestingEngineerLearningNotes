# Selenium（Java）

### 1. 控制浏览器操作

#### 1.1 控制浏览器窗口大小

- maximize()设置浏览器最大化
- setSize()设置浏览器宽高

```java
import org.openqa.selenium.Dimension;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class Itest {
    public static void main(String[] args) throws InterruptedException{
        System.setProperty("webdriver.chrome.driver", "D:/Driver/chromedriver.exe");
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.baidu.cn");
        driver.manage().window().maximize();
        Thread.sleep(2000);

        driver.get("https://m.baidu.cn");
        driver.manage().window().setSize(new Dimension(480, 800));
        Thread.sleep(2000);
        driver.quit();
    }
}
```

#### 1.2 控制浏览器后退、前进

- back()模拟浏览器后退按钮
- forward()模拟浏览器前进按钮

```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class Itest {
    public static void main(String[] args) throws InterruptedException{
        System.setProperty("webdriver.chrome.driver", "D:/Driver/chromedriver.exe");
        WebDriver driver = new ChromeDriver();
        //get 到百度首页
        driver.get("https://www.baidu.com/");
        System.out.printf("now accesss %s \n", driver.getCurrentUrl());
        Thread.sleep(2000);
        //点击“新闻” 链接
        driver.findElement(By.linkText("新闻")).click();
        System.out.printf("now accesss %s \n", driver.getCurrentUrl());
        Thread.sleep(2000);
        //执行浏览器后退
        driver.navigate().back();
        System.out.printf("back to %s \n", driver.getCurrentUrl());
        Thread.sleep(2000);
        //执行浏览器前面
        driver.navigate().forward();
        System.out.printf("forward to %s \n", driver.getCurrentUrl());
        Thread.sleep(2000);
        driver.quit();
    }
}
```

#### 1.3 刷新页面

```java
......
//刷新页面
driver.navigate().refresh();
......
```



### 2. WebDriver常用方法

- clear()：清除文本（清楚文本输入框内容）
- sendKeys(*value)：模拟按键输入（模拟键盘向输入框输入的内容，发送键盘按键、指定上传的文件）
- click()：单击元素（按钮、文字/图片链接、复选框、单选框、下拉框等）

```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class Itest {
    public static void main(String[] args) throws InterruptedException{
        System.setProperty("webdriver.chrome.driver", "D:/Driver/chromedriver.exe");
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.baidu.com/");
        WebElement search_text = driver.findElement(By.id("kw"));
        WebElement search_button = driver.findElement(By.id("su"));
        search_text.sendKeys("Java");
        search_text.clear();
        search_text.sendKeys("Selenium");
        search_button.click();
        driver.quit();
    }
}
```

- submit()：提交表单（输入框输入内容后回车操作）

```java
……
WebElement search_text = driver.findElement(By.id("kw"));
search_text.sendKeys("Selenium");
search_text.submit();
……
```

- getSize()：返回元素尺寸
- getText()：获取元素文本
- getAttribute(name)：获得属性值
- isDisplayed()：设置该元素是否用户可见

```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class Itest {
    public static void main(String[] args) throws InterruptedException{
        System.setProperty("webdriver.chrome.driver", "D:/Driver/chromedriver.exe");
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.baidu.com/");
        //获得百度输入框的尺寸
        WebElement size = driver.findElement(By.id("kw"));
        System.out.println(size.getSize());
        //返回百度页面底部备案信息
        WebElement text = driver.findElement(By.id("cp"));
        System.out.println(text.getText());
        //返回元素的属性值， 可以是 id、 name、 type 或元素拥有的其它任意属性
        WebElement ty = driver.findElement(By.id("kw"));
        System.out.println(ty.getAttribute("type"));
        //返回元素的结果是否可见， 返回结果为 True 或 False
        WebElement display = driver.findElement(By.id("kw"));
        System.out.println(display.isDisplayed());
        driver.quit();
    }
}
```

输出结果：

```
(500, 22)
©2020 Baidu 使用百度前必读 意见反馈 京ICP证030173号  京公网安备11000002000001号 
text
true
```

### 3. 模拟鼠标操作

- contextClick()：右击
- clickAndHold()：鼠标点击并控制
- doubleClick()：双击
- dragAndDrop()：拖动
- release()：释放鼠标
- perform()：执行所有Actions中存储的行为

```java
import org.openqa.selenium.interactions.Actions;

WebElement search_setting = driver.findElement(By.linkText("设置"));
Actions action = new Actions(driver);
action.clickAndHold(search_setting).perform();
```

如上为百度首页设置悬停下拉菜单

🔺Actions(driver)调用Actions类，将浏览器驱动driver作为参数传入。clickAndHold()方法在调用时需要指定元素定位

其他操作实例：

```java
import org.openqa.selenium.interactions.Actions;
……
Actions action = new Actions(driver);
// 鼠标右键点击指定的元素
action.contextClick(driver.findElement(By.id("element"))).perform();
// 鼠标右键点击指定的元素
action.doubleClick(driver.findElement(By.id("element"))).perform();
// 鼠标拖拽动作， 将 source 元素拖放到 target 元素的位置。
WebElement source = driver.findElement(By.name("element"));
WebElement target = driver.findElement(By.name("element"));
action.dragAndDrop(source,target).perform();
// 释放鼠标
action.release().perform();
```



### 4. 模拟键盘操作

- sendKeys(Keys.BACK_SPACE)：回格键（BackSpace）
- sendKeys(Keys.SPACE)：空格键（Space）
- sendKeys(Keys.TAB)：制表键（Tab）
- sendKeys(Keys.ESCAPE)：回退键（Esc）
- sengKeys(Keys.ENTER)：回车键（Enter）
- sendKeys(Keys.CONTROL,'a')：全选（Ctrl+A）
- sendKeys(Keys.CONTROL,'c')：复制（Ctrl+C）
- sendKeys(Keys.CONTROL,'x')：剪切（Ctrl+X）
- sendKeys(Keys.CONTROL,'v')：粘贴（Ctrl+V）
- sendKeys(Keys.F1)：键盘F1（F2-F12同理）

```java
import org.openqa.selenium.Keys;
......
  WebElement input = driver.findElement(By.id("kw"));
        //输入框输入内容
        input.sendKeys("seleniumm");
        Thread.sleep(2000);
        //删除多输入的一个 m
        input.sendKeys(Keys.BACK_SPACE);
        Thread.sleep(2000);
        //输入空格键+“教程”
        input.sendKeys(Keys.SPACE);
        input.sendKeys("教程");
        Thread.sleep(2000);
        //ctrl+a 全选输入框内容
        input.sendKeys(Keys.CONTROL,"a");
        Thread.sleep(2000);
        //ctrl+x 剪切输入框内容
        input.sendKeys(Keys.CONTROL,"x");
        Thread.sleep(2000);
        //ctrl+v 粘贴内容到输入框
        input.sendKeys(Keys.CONTROL,"v");
        Thread.sleep(2000);
        //通过回车键盘来代替点击操作
        input.sendKeys(Keys.ENTER);
        Thread.sleep(2000);
......
```



### 5. 获取断言信息

- getTitle()：获得当前页面title
- getCurrentUrl()：用户获得当前页面URL
- getText()：获得页面文本信息

```java
......        
System.out.println("Search before================");
        //获取当前的 title 和 url
        System.out.printf("title of current page is %s\n", driver.getTitle());
        System.out.printf("url of current page is %s\n", driver.getCurrentUrl());
        //百度搜索
        WebElement search = driver.findElement(By.id("kw"));
        search.sendKeys("Selenium");
        search.sendKeys(Keys.ENTER);
        Thread.sleep(2000);
        System.out.println("Search after================");
        //获取当前的 title 和 url
        System.out.printf("title of current page is %s\n", driver.getTitle());
        System.out.printf("url of current page is %s\n", driver.getCurrentUrl());
        //获取第一条搜索结果的标题
        WebElement result = driver.findElement(By.xpath("//div[@id='content_left']/div/h3/a"));
        System.out.println(result.getText());
......
```

输出结果：

```
Search before================
title of current page is 百度一下，你就知道
url of current page is https://www.baidu.com/
Search after================
title of current page is Selenium_百度搜索
url of current page is https://www.baidu.com/s?ie=utf-8&f=8&rsv_bp=1&rsv_idx=1&tn=baidu&wd=Selenium&rsv_pq=949ca614003e2803&rsv_t=1ff5YtJZU2iTMHcOLQdZj9yOJBX8%2FA8BYX1Keao%2FBDVkqa5OkLonWWpbIyg&rqlang=cn&rsv_enter=1&rsv_dl=tb&rsv_sug3=8&rsv_sug2=0&inputT=128&rsv_sug4=128
Selenium automates browsers. That's it!
```



### 6. 设置元素等待

#### 6.1 显式等待

WebDriverWait：在设置时间内，默认每隔一段时间检测一次当前页面元素是否存在，如果超时检测不到则抛出异常

WebDriverWait(driver,10,1)：driver：浏览器驱动、10：最长超时时间，默认以秒为单位、1：检测的间隔时间，默认为0.5s

```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.support.ui.WebDriverWait;
import org.openqa.selenium.support.ui.ExpectedCondition;


public class TimeOut01 {
  public static void main(String[]args) throws InterruptedException {
  	System.setProperty("webdriver.chrome.driver", "D:/Driver/chromedriver.exe");
    WebDriver driver = new ChromeDriver();
    driver.get("https://www.baidu.com");
    //显式等待， 针对某个元素等待
    WebDriverWait wait = new WebDriverWait(driver,10,1);
    wait.until(new ExpectedCondition<WebElement>(){
      @Override
      public WebElement apply(WebDriver text) {
            return text.findElement(By.id("kw"));
          }
    }).sendKeys("selenium");
    driver.findElement(By.id("su")).click();
    Thread.sleep(2000);
  	driver.quit();
  }
}
```

#### 6.2 隐式等待

- implicitlyWait()：识别对象时的超时时间。过了这个时间如果对象还没找到则会抛出NoSuchElement异常
- setScriptTimeout()：异步脚本超时时间。设置异步执行脚本 脚本返回结果超时时间
- pageLoadTimeout()：页面加载超时时间。WebDriver会等页面加载完毕再进行后续操作，所以如果页面超过设置时间依然没有加载完成，那么WebDriver会抛出异常

```java
......
//页面加载超时时间设置为 5s
    driver.manage().timeouts().pageLoadTimeout(5, TimeUnit.SECONDS);
    driver.get("https://www.baidu.com/");
    //定位对象时给 10s 的时间, 如果 10s 内还定位不到则抛出异常
   	driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
    driver.findElement(By.id("kw")).sendKeys("selenium");
    //异步脚本的超时时间设置成 3s
    driver.manage().timeouts().setScriptTimeout(3, TimeUnit.SECONDS);
......
```



### 7. 定位一组元素

```java
import org.openqa.selenium.By;
......
findElements(By.id())
findElements(By.name())
findElements(By.className())
findElements(By.tagName())
findElements(By.linkText())
findElements(By.partialLinkText())
findElements(By.xpath())
findElements(By.cssSelector())
```

实例：

```java
......
    WebElement search_text = driver.findElement(By.id("kw"));
   	search_text.sendKeys("selenium");
    search_text.submit();
    Thread.sleep(2000);
    //匹配第一页搜索结果的标题， 循环打印
    List<WebElement> search_result = driver.findElements(By.xpath("//div/div/h3"));
    //打印元素的个数
    System.out.println(search_result.size());
    // 循环打印搜索结果的标题
    for(WebElement result : search_result){
        System.out.println(result.getText());
    }
    System.out.println("-------我是分割线---------");
    //打印第n结果的标题
    WebElement text = search_result.get(search_result.size() - 10);
    System.out.println(text.getText());
......
```

输出结果：

```
10
Selenium automates browsers. That's it!
Selenium_百度百科
selenium库的基本使用 - 简书
selenium中文网 | selenium安装、selenium使用、selenium中文、...
selenium_百度翻译
Python 爬虫基础Selenium库的使用(二十二01) - 凯耐的...-CSDN博客
Selenium教程系列(一):介绍_kingzhsh的博客-CSDN博客
Selenium with Python — Selenium Python Bindings 2 ...
功能自动化测试工具——Selenium篇
Selenium · 社区 · TesterHome
-------我是分割线---------
Selenium automates browsers. That's it!
```



### 8. 多表单切换

对于frame/iframe表单嵌套页面的应用，WebDriver只能在一个页面上对元素识别与定位，需要通过switchTo().frame()方法将当前定位的主体切换为frame/iframe表单内嵌页面中

```html
<html>
  <body>
    ...
    <iframe id="x-URS-iframe" ...>
      <html>
         <body>
           ...
           <input name="email" >
```

```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class MailLogin {
  public static void main(String[] args){
    WebDriver driver = new ChromeDriver();
    driver.get("http://www.126.com");
    WebElement xf = driver.findElement(By.xpath("//*[@id='loginDiv']/iframe"));
    driver.switchTo().frame(xf);
    driver.findElement(By.name("email")).clear();
    driver.findElement(By.name("email")).sendKeys("username");
    driver.findElement(By.name("password")).clear();
    driver.findElement(By.name("password")).sendKeys("password");
    driver.findElement(By.id("dologin")).click();
    // 跳出表单
    driver.switchTo().defaultContent();
    //……
  }
}
```



### 9. 多窗口切换

点击某个链接之后弹出新窗口，WebDriver提供了switchTo().window()方法可以实现在不同窗口之间切换

```java
import java.util.Set;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class MoreWindows {
  public static void main(String[] arge) throws InterruptedException{
    WebDriver driver = new ChromeDriver();
    driver.get("https://www.baidu.com");
    //获得当前窗口句柄
    String search_handle = driver.getWindowHandle()
    //打开百度注册窗口
    driver.findElement(By.linkText("登录")).click();
    Thread.sleep(3000);
    driver.findElement(By.linkText("立即注册")).click();
    //获得所有窗口句柄
    Set<String> handles = driver.getWindowHandles();
    //判断是否为注册窗口， 并操作注册窗口上的元素
    for(String handle : handles){
      if (handle.equals(search_handle)==false){
        //切换到注册页面
        driver.switchTo().window(handle);
        System.out.println("now register window!");
        Thread.sleep(2000);
        driver.findElement(By.name("userName")).clear();
        driver.findElement(By.name("userName")).sendKeys("user name");
        driver.findElement(By.name("phone")).clear();
        driver.findElement(By.name("phone")).sendKeys("phone number");
        //......
        Thread.sleep(2000);
        //关闭当前窗口
        driver.close();
      }
    }
    Thread.sleep(2000); 
    driver.quit();
  }
}
```

- getWindowHandle()：获得当前窗口句柄
- getWindowHandles()：返回所有窗口句柄到当前会话



### 10. 下拉框选择

Select类用于定位select标签，selectByValue()方法用于选取<option>标签的value值

```html
<select id="nr" name="NR">
  <option value="10" selected>每页显示 10 条</option>
  <option value="20">每页显示 20 条</option>
  <option value="50">每页显示 50 条</option>
<select>
```

```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.support.ui.Select;

public class Itest {
    public static void main(String[] arge) throws InterruptedException{
        System.setProperty("webdriver.chrome.driver", "D:/Driver/chromedriver.exe");
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.baidu.com");
        driver.findElement(By.linkText("设置")).click();
        driver.findElement(By.linkText("搜索设置")).click();
        Thread.sleep(2000);
        //<select>标签的下拉框选择
        WebElement el = driver.findElement(By.xpath("//select"));
        Select sel = new Select(el);
        sel.selectByValue("20");
        Thread.sleep(2000);
        driver.quit();
    }
}
```



### 11. 警告框处理

处理JavaScript生成的alert、confirm、prompt做法是使用switch_to_alert()方法定位到alert/confirmprompt，然后使用getText/accept/dismiss/sendKeys等方法操作

- getText()：返回alert/confirm/prompt中文字信息
- accept()：接受现有警告框
- dismiss()：解散现有警告框
- sendKeys（keysToSend）：发送文本至警告框
- keysToSend：将文本发送至警告框

```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class Itest {
    public static void main(String[] arge) throws InterruptedException{
        System.setProperty("webdriver.chrome.driver", "D:/Driver/chromedriver.exe");
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.baidu.com");
        driver.findElement(By.linkText("设置")).click();
        driver.findElement(By.linkText("搜索设置")).click();
        Thread.sleep(2000);
        //保存设置
        driver.findElement(By.className("prefpanelgo")).click();
        //接收弹窗
        driver.switchTo().alert().accept();
        Thread.sleep(2000);
        driver.quit();
    }
}
```



### 12. 文件上传

对于通过input标签实现的上传功能，可以将其看作是一个输入框，通过sendKeys()指定本地文件上传路径方式实现文件上传

upfile.html

```html
<html>
<head>
    <meta http-equiv="content-type" content="text/html;charset=utf-8" />
    <title>upload_file</title>
    <link href="http://cdn.bootcss.com/bootstrap/3.3.0/css/bootstrap.min.css" rel="stylesheet" />
</head>

<body>
<div class="row-fluid">
    <div class="span6 well">
        <h3>upload_file</h3>
        <input type="file" name="file" />
    </div>
</div>
</body>
<script src="http://cdn.bootcss.com/bootstrap/3.3.0/css/bootstrap.min.js"></script>
</html>
```

Itest.java

```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import java.io.File;

public class Itest {
    public static void main(String[] arge) throws InterruptedException{
        System.setProperty("webdriver.chrome.driver", "D:/Driver/chromedriver.exe");
        WebDriver driver = new ChromeDriver();
        File file = new File("D:\\mytestcase\\src\\main\\resources\\HTMLFile\\upfile.html");
        String filePath = file.getAbsolutePath();
        driver.get(filePath);
        //定位上传按钮， 添加本地文件
        driver.findElement(By.name("file")).sendKeys("D:\\upload_file.txt");
        Thread.sleep(5000);
        driver.quit();
    }
}
```



### 13. 浏览器cookie操作

有时需要验证浏览器Cookie是否正确，基于真实Cookie的测试是无法通过白盒测试和集成测试进行。WebDriver提供了操作Cookie相关方法可以获取、添加和删除Cookie信息

- getCookies()：获得所有Cookie信息
- getCookieNamed（String name)：返回字典的key为“name”的Cookie信息
- addCookie（cookie dict）：添加Cookie。“cookie_dict"指字典对象，必须要有name和value值
- deleteCookieNamed（String name）：删除Cookie信息。”name“是要删除的cookie名称，”optionsString“是该Cookie的选项，目前支持选项包括”路径“，”域“
- deleteAllCookies()：删除所有Cookie信息

```java
import org.openqa.selenium.Cookie;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import java.util.Set;

public class Itest {
    public static void main(String[] arge) throws InterruptedException{
        System.setProperty("webdriver.chrome.driver", "D:/Driver/chromedriver.exe");
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.baidu.com");
        Cookie c1 = new Cookie("name", "key-aaaaaaa");
        Cookie c2 = new Cookie("value", "value-bbbbbb");
        driver.manage().addCookie(c1);
        driver.manage().addCookie(c2);
        //获得 cookie
        Set<Cookie> coo = driver.manage().getCookies();
        System.out.println(coo);
        //删除所有 cookie
        //driver.manage().deleteAllCookies();
        driver.quit();
    }
}
```

输出结果：

```
[delPer=0; path=/; domain=.baidu.com, BIDUPSID=5DCBDB908ED2ADA5DEDE58983935CC59; expires=星期一, 01 三月 2088 02:46:41 CST; path=/; domain=.baidu.com, BDORZ=B490B5EBF6F3CD402E515D22BCDA1598; expires=星期四, 13 二月 2020 11:32:35 CST; path=/; domain=.baidu.com, name=key-aaaaaaa; path=/; domain=www.baidu.com;secure;, PSTM=1581478416; expires=星期一, 01 三月 2088 02:46:41 CST; path=/; domain=.baidu.com, H_PS_PSSID=30746_1469_21126_26350_30494; path=/; domain=.baidu.com, BD_UPN=12314753; expires=星期六, 22 二月 2020 11:32:35 CST; path=/; domain=www.baidu.com, value=value-bbbbbb; path=/; domain=www.baidu.com;secure;, BAIDUID=5DCBDB908ED2ADA50142803DA5581931:FG=1; expires=星期四, 11 二月 2021 11:32:34 CST; path=/; domain=.baidu.com, BD_HOME=0; path=/; domain=www.baidu.com]
```

### 

### 14. 调用JavaScript代码

借助JavaScript控制浏览器滚动条。WebDriver提供了executeScript()方法执行JavaScript代码。

```javascript
<!-- window.scrollTo(左边距,上边距); -->
window.scrollTo(0,450);
```

window.scrollTo()方法设置浏览器窗口滚动条水平和垂直位置。第一个参数表示水平的左间距，第二个参数表示垂直的上边距。

```java
import org.openqa.selenium.*;
import org.openqa.selenium.chrome.ChromeDriver;

public class Itest {
    public static void main(String[] arge) throws InterruptedException{
        System.setProperty("webdriver.chrome.driver", "D:/Driver/chromedriver.exe");
        WebDriver driver = new ChromeDriver();
        //设置浏览器窗口大小
        driver.manage().window().setSize(new Dimension(700, 600));
        driver.get("https://www.baidu.com");
        //进行百度搜索
        driver.findElement(By.id("kw")).sendKeys("webdriver api");
        driver.findElement(By.id("su")).click();
        Thread.sleep(2000);
        //将页面滚动条拖到底部
        ((JavascriptExecutor)driver).executeScript("window.scrollTo(100,600);");
        Thread.sleep(3000);
        driver.quit();
    }
}
```



### 15. 获取窗口截图

自动化用例是由程序去执行，有时打印的错误信息并不十分明确。WebDriver提供了截图函数getScreenshotAs()来截取当前窗口。

```java
import java.io.File;
import java.io.IOException;
import org.apache.commons.io.FileUtils;
import org.openqa.selenium.OutputType;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.TakesScreenshot;

public class GetImg {
  public static void main(String[] arge){
    WebDriver driver = new ChromeDriver();
    driver.get("https://www.baidu.com"); 
    File srcFile = ((TakesScreenshot)driver).getScreenshotAs(OutputType.FILE);
    try {
      FileUtils.copyFile(srcFile,new File("d:\\screenshot.png"));
    } catch (IOException e) {
      e.printStackTrace();
    }
    driver.quit();
  }
}
```



### 16. 元素定位

8种定位的使用方法：

- id：findElement(By.id())
- name：findElement(By.name())
- class name：findElement(By.className())
- tag name：findElement(By.tagName())
- link text：findElement(By.linkText())
- partial link text：findElement(By.partialLinkText())
- xpath：findElement(By.xpath())
- css selector：findElement(By.cssSelector())

```html
<html>
  <head>
  <body link="#0000cc">
    <a id="result_logo" href="/" οnmοusedοwn="return c({'fm':'tab','tab':'logo'})">
    <form id="form" class="fm" name="f" action="/s">
      <span class="soutu-btn"></span>
        <input id="kw" class="s_ipt" name="wd" value="" maxlength="255" autocomplete="off">
```

通过id定位：

```java
driver.findElement(By.id("kw"))
```

通过name定位：

```java
driver.findElement(By.name("wd"))
```

通过class name定位：

```java
driver.findElement(By.className("s_ipt"))
```

通过tag name定位：

```java
driver.findElement(By.tagName("input"))
```

通过xpath定位：

```java
driver.findElement(By.xpath("//*[@id='kw']"))
driver.findElement(By.xpath("//*[@name='wd']"))
driver.findElement(By.xpath("//input[@class='s_ipt']"))
driver.findElement(By.xpath("/html/body/form/span/input"))
driver.findElement(By.xpath("//span[@class='soutu-btn']/input"))
driver.findElement(By.xpath("//form[@id='form']/span/input"))
driver.findElement(By.xpath("//input[@id='kw' and @name='wd']"))
```

通过css定位：

```java
driver.findElement(By.cssSelector("#kw")
driver.findElement(By.cssSelector("[name=wd]")
driver.findElement(By.cssSelector(".s_ipt")
driver.findElement(By.cssSelector("html > body > form > span > input")
driver.findElement(By.cssSelector("span.soutu-btn> input#kw")
driver.findElement(By.cssSelector("form#form > span > input")
```

另一份页面代码：

```html
<a class="mnav" href="http://news.baidu.com" name="tj_trnews">新闻</a>
<a class="mnav" href="http://www.hao123.com" name="tj_trhao123">hao123</a>
```

通过linkText定位：

```java
driver.findElement(By.linkText("新闻")
driver.findElement(By.linkText("hao123")
```

通过partialLinkText定位：

```java
driver.findElement(By.partialLinkText("新")
driver.findElement(By.partialLinkText("hao")
driver.findElement(By.partialLinkText("123")
```

