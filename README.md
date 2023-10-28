# APK重新打包签名
主要工具:
#### [apktool](https://ibotpeaches.github.io/Apktool):apktool.bat,apktool.jar
#### [Oracle jdk8](https://download.oracle.com/java/18/latest/jdk-18_windows-x64_bin.zip):keytool.exe,jarsigner.jar
#### [Android Build Tool](https://dl.google.com/android/repository/build-tools_r34-windows.zip):  apksigner.bat,apksigner.jar,zipalign.exe
## 1.解压apk包
java -jar apktool.jar d app-release.apk  
$ apktool d -s -f -s -r test.apk  
* -d 反编译 apk 文件
* -s 不反编译 dex 文件，而是将其保留
* -r 不会反编译资源文件
* -f 如果目标文件夹存在，则删除后重新反编译
## 2.删除签名文件
签名文件在解压文件后的\app-release\META-INF目录下，删除里面的MANIFEST.MF、\*.SF和\*.RSA（或*.DSA）
## 3.修改或替换文件
C:\Users\xxoo\Downloads\app-release
## 4.生成签名文件
~~keytool -genkeypair -alias cert -keyalg RSA -validity 20000 -keystore xxoo.keystore -storepass 123456~~
~~* 一次性生成：keytool -genkeypair -alias mykey -keypass 123456 -keyalg RSA -keysize 2048 -validity 20000 -keystore  xxoo.keystore -storepass 123456 -dname "CN=名字与姓氏,OU=组织 单位名称,O=组织名称,L=城市或区域名称,ST=州或省份名称,C=单位 的两字母国家代码"~~
* keytool -genkeypair -alias xxoo -keyalg RSA -keysize 1024 -sigalg SHA1withRSA -dname "CN= Ovital,OU= Ovital,O= Ovital,L= Ovital,ST= Ovital,C=Ovital" -validity 30000 -keypass 123456  -keystore xxoo. keystore  -storepass 123456 -v
* keytool -list -alias xxoo -keystore xxoo.keystore -storepass 123456 -v
## 5.重新打包
java -jar apktool.jar b app-release -o newtest.apk  
$apktool b app-release -o newtest.apk  
* -b 是指 build
* -o 用于指定新的文件名称，这里指定为「newtest.apk」
* app-release 是刚才反编译出的文件所在的目录
## 6.使用重新打包后的apk和签名文件打包
~~* jarsigner -verbose -keystore xxoo.keystore -storepass 123456 -signedjar app-release-signed.apk app-release.apk cert~~
* jarsigner –keystore xxoo.keystore -storepass 123456 -keypass 123456 -sigfile CERT -digestalg SHA1 -sigalg SHA1withRSA  -verbose –signedjar omapAndroidV989_signed.apk omapAndroidV989.apk xxoo
* java -jar apksigner.jar sign -v --ks xxoo.keystore --ks-key-alias cert --ks-pass pass:123456 --key-pass pass:123456 --in in.apk --out out_signed.apk  
~~java -jar apksigner.jar sign --ks [签名证书存放路径] --ks-key-alias [签名证书别名] --ks-pass pass:[签名证书密钥] --key-pass pass:[签名证书别名密钥] --min-sdk-version [指定应用支持最低的安卓API版本] --max-sdk-version [指定应用支持最高的安卓API版本] --v1-signing-enabled true --v2-signing-enabled true --v3-signing-enabled true --v4-signing-enabled true --out [签名后文件存放路径] [未签名的文件路径]~~
## 7.验证 APK 签名
在受支持的平台上确认 APK 签名是否成功通过验证的语法如下：  
* jarsigner -verify xxx.apk -verbose  
* apksigner verify -v --print-certs C:\app-name.apk  
使用 apksigner verify 校验已签名的 apk 文件。包括查看签名方式和使用的证书信息。  
apksigner verify [options] app-name.apk  
经常使用参数有：  
* -v 显示签名详情，是否使用 v1 、v2 签名。  
* --in 指定待校验的 apk 文件路径。当 apk 路径放在命令末尾时，此参数能够省略。  
* --print-certs 显示 apk 文件中包含的签名文件证书信息。  
  示例
* apksigner verify --in app-release.apk -v -print-certs  
~~* apksigner verify -v -print-certs app-release.apk~~
* keytool -printcert -file META-INF/CERT.RSA
## 8.字节对齐
* 如果您使用的是 apksigner，则必须在为 APK 文件签名之前使用 zipalign。如果您在使用 apksigner 为 APK 签名之后对 APK 做出了进一步更改，签名便会失效。
* 如果您使用的是 jarsigner（不推荐），则必须在为 APK 文件签名之后使用 zipalign。  
zipalign -p -f -v 4 infile.apk outfile.apk  
zipalign -c -v 4 outfile.apk
## 9.其他
###解决验证签名出现的告警提示：“此jar 包含证书链未验证的条目”
* keytool -exportcert -alias xxoo  -file xxoo.cert -keystore xxoo.keystore -storepass 123456 -v
* keytool -import -alias xxoo -file xxoo.cer -keystore %JAVA_HOME%\jre\lib\security\ cacerts -storepass changeit
###解决签名时出现的告警提示：“未提供-tsa或-tsacert，此jar没有时间戳……”
* jarsigner –keystore xxoo.keystore -storepass 123456 -keypass 123456 -sigfile CERT -digestalg SHA1 -sigalg SHA1withRSA  -verbose -tsa https://timestamp.geotrust.com/tsa –signedjar omapAndroidV989_signed.apk omapAndroidV989.apk xxoo
  免费时间戳服务器：
- http://timestamp.apple.com/ts01
- https://timestamp.geotrust.com/tsa
- http://timestamp.digicert.com
- http://sha256timestamp.ws.symantec.com/sha256/timestamp
- http://timestamp.verisign.com/scripts/timstamp.dll 
- http://timestamp.comodoca.com/authenticode
- http://tsa.startssl.com/timestamp
- http://timestamp.sectigo.com
- http://timestamp.sectigo.com?td =sha256
- http://timestamp.sectigo.com/qualiYed
