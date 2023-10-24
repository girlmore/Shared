# APK重新打包签名
apktool下载地址:
https://ibotpeaches.github.io/Apktool/
## 1.解压apk包
java -jar apktool_2.9.0.jar d app-release.apk  
$ apktool d -s -f test.apk  
-d 反编译 apk 文件  
-s 不反编译 dex 文件，而是将其保留  
-r 参数不会反编译资源文件  
-f 如果目标文件夹存在，则删除后重新反编译
## 2.删除签名文件
签名文件在解压文件后的\app-release\META-INF目录下 C:\Users\xxoo\Downloads\app-release\META-INF
## 3.添加要替换的文件到
C:\Users\xxoo\Downloads\app-release\assets下
## 4.生成签名文件
keytool -genkeypair -alias cert -keyalg RSA -validity 20000 -keystore xxoo.keystore -storepass 123456
## 5.重新打包
java -jar apktool_2.9.0.jar b app-release  
$apktool b b_test -o newtest.apk  
-b 是指 build  
-o 用于指定新的文件名称，这里指定为「newtest.apk」  
b_test 是刚才反编译出的文件所在的目录  
## 6.使用重新打包后的apk和签名文件打包
jarsigner -verbose -keystore xxoo.keystore -storepass 123456 -signedjar app-release-signed.apk app-release.apk cert  
或者  
java -jar apksigner.jar sign -v --ks xxoo.keystore --ks-key-alias cert --ks-pass pass:123456 --key-pass pass:123456 --in in.apk --out out_signed.apk
## 7.字节对齐
* 如果您使用的是 apksigner，则必须在为 APK 文件签名之前使用 zipalign。如果您在使用 apksigner 为 APK 签名之后对 APK 做出了进一步更改，签名便会失效。
* 如果您使用的是 jarsigner（不推荐），则必须在为 APK 文件签名之后使用 zipalign。  
zipalign -p -f -v 4 infile.apk outfile.apk  
zipalign -c -v 4 outfile.apk
