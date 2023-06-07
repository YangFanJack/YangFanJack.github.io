# win10壁纸bingtoday


## 注册表指定壁纸路径

* WIN+R打开运行对话框：输入regedit打开注册表
* 到达路径：`计算机\HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Policies\System`下，一个叫Wallpaper的名称就是壁纸的指定路径，更换成你希望的壁纸路径：例如：`C:\wallpaper\wallpaper.jpg`
* 或者直接将`计算机\HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Policies\System`注册机目录直接删掉

## 每日bing壁纸下载

* 通过java代码实现下载每日bing壁纸到刚才注册表指定的壁纸路径

  ```java
  public class DownloadMain {
      public static void main(String[] args) throws IOException {
          final String imgResource = getImgResource();
          final URL url = new URL(imgResource);
          final URLConnection urlConnection = url.openConnection();
          final BufferedInputStream bis = new BufferedInputStream(urlConnection.getInputStream());
          final BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("C:\\wallpaper\\wallpaper.jpg"));
          final byte[] bytes = new byte[1024];
          int len = 0;
          while ((len=bis.read(bytes)) != -1){
              bos.write(bytes,0,len);
          }
          bis.close();
          bos.close();
      }
  
      public static String getImgResource() throws IOException{
          String url = "http://cn.bing.com/?mkt=zh-CN";
          final Document document = Jsoup.parse(new URL(url), 30000);
          final Element vs_cont = document.getElementById("vs_cont");
          final Elements downloadLink = vs_cont.getElementsByClass("downloadLink");
          final String[] split = downloadLink.toString().split("\"");
          return "http://cn.bing.com/" + split[1];
      }
  }
  ```

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
  
      <groupId>org.example</groupId>
      <artifactId>bingDownload</artifactId>
      <version>1.0-SNAPSHOT</version>
  
      <properties>
          <maven.compiler.source>8</maven.compiler.source>
          <maven.compiler.target>8</maven.compiler.target>
      </properties>
  
      <dependencies>
          <dependency>
              <groupId>org.jsoup</groupId>
              <artifactId>jsoup</artifactId>
              <version>1.10.2</version>
          </dependency>
      </dependencies>
  
      <build>
          <plugins>
              <plugin>
                  <groupId>org.apache.maven.plugins</groupId>
                  <artifactId>maven-shade-plugin</artifactId>
                  <version>1.2.1</version>
                  <executions>
                      <execution>
                          <phase>package</phase>
                          <goals>
                              <goal>shade</goal>
                          </goals>
                          <configuration>
                              <transformers>
                                  <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                      <!-- 在这里指定入口类 -->
                                      <mainClass>DownloadMain</mainClass>
                                  </transformer>
                              </transformers>
                          </configuration>
                      </execution>
                  </executions>
              </plugin>
          </plugins>
      </build>
  
  </project>
  ```

* 打包java为jar包，只要电脑安装的jre环境，双击就能下载壁纸到指定路径

  

