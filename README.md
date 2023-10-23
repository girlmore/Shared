# APK重新打包签名
apktool下载地址:
https://ibotpeaches.github.io/Apktool/
## 1.解压apk包
java -jar apktool_2.6.1.jar d app-release.apk
## 2.删除签名文件
签名文件在解压文件后的\app-release\META-INF目录下 C:\Users\xxoo\Downloads\app-release\META-INF
## 3.添加要替换的文件到
C:\Users\xxoo\Downloads\app-release\assets下
## 4.生成签名文件
keytool -genkeypair -alias cert -keyalg RSA -validity 20000 -keystore xxoo.keystore -storepass 123456
## 5.重新打包
java -jar apktool_2.6.1.jar b app-release
## 6.使用重新打包后的apk和签名文件打包
jarsigner -verbose -keystore xxoo.keystore -storepass 123456 -signedjar app-release-signed.apk app-release.apk cert


