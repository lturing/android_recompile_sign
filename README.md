
# ubuntu(20.04)下反编译安卓apk

```
# git clone https:/githu

# 配置apktool
#从https://github.com/iBotPeaches/Apktool/releases 下载最新的apktool.jar
# 示例
wget https://github.com/iBotPeaches/Apktool/releases/download/v2.9.0/apktool_2.9.0.jar -o apktool.jar 

# 配置dex2jar，并添加环境变量
wget https://github.com/pxb1988/dex2jar/releases/download/v2.1/dex2jar-2.1.zip
unzip dex2jar-2.1.zip


# 配置jd-gui
# https://github.com/java-decompiler/jd-gui/releases
wget https://github.com/java-decompiler/jd-gui/releases/download/v1.6.6/jd-gui-1.6.6.deb
sudo dpkg -i jd-gui-1.6.6.deb
# sudo dpkg -l # 查看已经安装的deb包
# sudo dpkg --purge deb_name


# 安装jdk，并添加环境变量
# https://www.oracle.com/java/technologies/downloads/#java11


# 安装sdkmanger，并添加环境变量
# https://developer.android.com/studio/command-line/sdkmanager

# sdkmanager 命令测试
sdkmanager --list --sdk_root=/path/to/sdkmanager_root # list所有相关的包
sdkmanager "platforms;android-28" # 安装android-28(android sdk)
sdkmanager "ndk;21.0.6113669" # 安装ndk version=21.0.6113669


# 反编译
# apktool if app-debug.apk
apktool d app-debug.apk
# the result will be saved in app-debug

# 获得源码
cp app-debug.apk app-debug.zip
unzip -d app-debug_unpack app-debug.zip
cd app-debug_unpack
sh d2j-dex2jar.sh -f  classes.dex # 生成classes-dex2jar.jar

# 查看源码
java -jar /opt/jd-gui/jd-gui.jar classes-dex2jar.jar


# 重新编译apk
# apktool b name-of-the-app-folder
apktool b app-debug
#the result will be saved in app-debug/dist/app-debug.apk
cp app-debug/dist/app-debug.apk app-debug-recomplie.apk 

# 签名
# You need to generate a keystore once and use it to sign your unsigned apk. Use the keytool provided by the JDK found in %JAVA_HOME%/bin/

keytool -genkey -v -keystore my.keystore -keyalg RSA -keysize 2048 -validity 10000 -alias app

# zipalign which is a tool provided by the Android SDK found in e.g. %ANDROID_HOME%/sdk/build-tools/24.0.2/ is a mandatory optimization step if you want to upload the apk to the Play Store.

/home/spurs/app/sdk_root/build-tools/28.0.2/zipalign -p 4 app-debug-recomplie.apk  app-debug-recomplie_1.apk 

# vertify the alignment
/home/spurs/app/sdk_root/build-tools/28.0.2/zipalign -c 4 app-debug-recomplie_1.apk

/home/spurs/app/sdk_root/build-tools/28.0.2/apksigner sign --ks-key-alias app --ks my.keystore app-debug-recomplie_1.apk 

# verified the sign
/home/spurs/app/sdk_root/build-tools/28.0.2/apksigner verify app-debug-recomplie_1.apk 

```

