# PictureSelectorNoCrop 2.0 无裁剪功能版  
  一款针对android平台下的图片选择器，支持从相册或拍照选择图片或视频、音频，支持动态权限获取、压缩、主题自定义配置等功能、适配android 6.0+系统的开源图片选择框架。 
  
  项目一直维护哦，发现问题欢迎提出isuess，用得着的朋友点个star。 

## 目录
-[功能特点](#功能特点)<br>
-[集成方式](#集成方式)<br>
-[常见错误](#常见错误)<br>
-[功能配置](#功能配置)<br>
-[缓存清除](#缓存清除)<br>
-[主题配置](#主题配置)<br>
-[常用功能](#常用功能)<br>
-[结果回调](#结果回调)<br>
-[更新日志](#更新日志)<br>
-[混淆配置](#混淆配置)<br>
-[兼容性测试](#兼容性测试)<br>
-[演示效果](#演示效果)<br>

### 功能特点  

* 1.适配android6.0+系统
* 2.解决图片过大oom闪退问题
* 3.动态获取系统权限，避免闪退
* 4.支持相片or视频的单选和多选
* 5.支持视频预览
* 6.支持gif图片
* 7.支持.webp格式图片 
* 8.支持一些常用场景设置：如:是否预览图片、是否显示相机等
* 9.新增自定义主题设置
* 10.新增图片勾选样式设置
* 11.新增图片压缩处理
* 12.新增录视频最大时间设置
* 13.新增视频清晰度设置
* 14.新增QQ选择风格，带数字效果
* 15.新增自定义 文字颜色 背景色让风格和项目更搭配
* 16.新增LuBan多图压缩
* 17.新增单独拍照功能
* 18.新增压缩大小设置
* 19.新增Luban压缩档次设置
* 20.新增音频功能查询

******那些遇到拍照闪退问题的同学，请记得看清下面适配6.0的配置~******

重要的事情说三遍记得添加权限

```
  <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  <uses-permission android:name="android.permission.CAMERA" />
    
```

******注：适配android6.0以上拍照问题，请在AndroidManifest.xml中添加标签******

```
<provider
   android:name="android.support.v4.content.FileProvider"
   android:authorities="${applicationId}.provider"
   android:exported="false"
   android:grantUriPermissions="true">
     <meta-data
         android:name="android.support.FILE_PROVIDER_PATHS"
         android:resource="@xml/file_paths" />
</provider>

```

## 集成方式

方式一 implementation引入

```
dependencies {
    implementation 'com.github.small-DarkHorse:PictureSelectorNoCrop:v3.0.0'
}

```

项目根目录build.gradle加入 

```
allprojects {
   repositories {
      jcenter()
      maven { url 'https://jitpack.io' }
   }
}
```

方式二 maven引入 

step 1.
```
<repositories>
       <repository>
       <id>jitpack.io</id>
	<url>https://jitpack.io</url>
       </repository>
 </repositories>
```
step 2.
```
<dependency>
   <groupId>com.github.small-DarkHorse</groupId>
   <artifactId>PictureSelectorNoCrop</artifactId>
   <version>v3.0.0</version>
</dependency>


```

## 常见错误
```
 问题一：
 rxjava冲突：在app build.gradle下添加
 packagingOptions {
   exclude 'META-INF/rxjava.properties'
 }  
 
 问题二：
 java.lang.NullPointerException: 
 Attempt to invoke virtual method 'android.content.res.XmlResourceParser 
 android.content.pm.ProviderInfo.loadXmlMetaData(android.content.pm.PackageManager, java.lang.String)'
 on a null object reference
 
 application下添加如下节点:
 
 <provider
      android:name="android.support.v4.content.FileProvider"
      android:authorities="${applicationId}.provider"
      android:exported="false"
      android:grantUriPermissions="true">
       <meta-data
         android:name="android.support.FILE_PROVIDER_PATHS"
         android:resource="@xml/file_paths" />
</provider>

注意：如已添加其他sdk或项目中已使用过provider节点，
[请参考我的博客](http://blog.csdn.net/luck_mw/article/details/54970105)的解决方案

问题三：
PhotoView 库冲突，可以删除自己项目中引用的，Picture_library中已经引用过
com.github.chrisbanes.photoview:library:1.2.4
 
问题四：
经测试在小米部分低端机中，Fragment调用PictureSelector 2.0 拍照有时内存不足会暂时回收activity,
导致其fragment会重新创建 建议在fragment所依赖的activity加上如下代码:
if (savedInstanceState == null) {
      // 添加显示第一个fragment
      	fragment = new PhotoFragment();
      		getSupportFragmentManager().beginTransaction().add(R.id.tab_content, fragment,
                    PictureConfig.FC_TAG).show(fragment)
                    .commit();
     } else {
      	fragment = (PhotoFragment) getSupportFragmentManager()
          .findFragmentByTag(PictureConfig.FC_TAG);
}
这里就是如果是被回收时，则不重新创建 通过tag取出fragment的实例。

```

## 功能配置
```
// 进入相册 以下是例子：不需要的api可以不写
   PictureSelector.create(MainActivity.this)
         .openGallery()// 全部.PictureMimeType.ofAll()、图片.ofImage()、视频.ofVideo()
         .theme()//主题样式(不设置为默认样式) 也可参考demo values/styles下 例如：R.style.picture.white.style
         .maxSelectNum()// 最大图片选择数量 int
         .minSelectNum()// 最小选择数量 int 
	 .imageSpanCount(4)// 每行显示个数 int 
         .selectionMode()// 多选 or 单选 PictureConfig.MULTIPLE or PictureConfig.SINGLE
         .previewImage()// 是否可预览图片 true or false
         .previewVideo()// 是否可预览视频 true or false
	 .enablePreviewAudio() // 是否可播放音频  true or false
         .compressGrade()// luban压缩档次，默认3档 Luban.THIRD_GEAR、Luban.FIRST_GEAR、Luban.CUSTOM_GEAR
         .isCamera()// 是否显示拍照按钮 ture or false
	 .isZoomAnim(true)// 图片列表点击 缩放效果 默认true
	 .sizeMultiplier(0.5f)// glide 加载图片大小 0~1之间 如设置 .glideOverride()无效
	 .setOutputCameraPath("/Chinayie/App")// 自定义拍照保存路径,可不填
         .compress()// 是否压缩 true or false
         .compressMode()//系统自带 or 鲁班压缩 PictureConfig.SYSTEM_COMPRESS_MODE or LUBAN_COMPRESS_MODE
         .glideOverride()// glide 加载宽高，越小图片列表越流畅，但会影响列表图片浏览的清晰度 int
         .isGif()// 是否显示gif图片 true or false
         .openClickSound()// 是否开启点击声音 true or false 
         .selectionMedia()// 是否传入已选图片 List<LocalMedia> list
         .previewEggs()// 预览图片时 是否增强左右滑动图片体验(图片滑动一半即可看到上一张是否选中) true or false      
         .compressMaxKB()//压缩最大值kb compressGrade()为Luban.CUSTOM_GEAR有效 int 
         .compressWH() // 压缩宽高比 compressGrade()为Luban.CUSTOM_GEAR有效 int 
         .videoQuality()// 视频录制质量 0 or 1 int 
         .videoSecond()//显示多少秒以内的视频or音频也可适用 int 
	 .recordVideoSecond()//录制视频秒数 默认60s int 
         .forResult(PictureConfig.CHOOSE_REQUEST);//结果回调onActivityResult code
```
## 缓存清除
```
 //包括压缩后的缓存，要在上传成功后调用，注意：需要系统sd卡权限 
 PictureFileUtils.deleteCacheDirFile(MainActivity.this);
 
```
## 主题配置

```
<!--默认样式 注意* 样式只可修改，不能删除任何一项 否则报错-->
    <style name="picture.default.style" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
        <!--标题栏背景色-->
        <item name="colorPrimary">@color/bar_grey</item>
        <!--状态栏背景色-->
        <item name="colorPrimaryDark">@color/bar_grey</item>
        <!--是否改变图片列表界面状态栏字体颜色为黑色-->
        <item name="picture.statusFontColor">false</item>
        <!--返回键图标-->
        <item name="picture.leftBack.icon">@drawable/picture_back</item>
        <!--标题下拉箭头-->
        <item name="picture.arrow_down.icon">@drawable/arrow_down</item>
        <!--标题上拉箭头-->
        <item name="picture.arrow_up.icon">@drawable/arrow_up</item>
        <!--标题文字颜色-->
        <item name="picture.title.textColor">@color/white</item>
        <!--标题栏右边文字-->
        <item name="picture.right.textColor">@color/white</item>
        <!--图片列表勾选样式-->
        <item name="picture.checked.style">@drawable/checkbox_selector</item>
        <!--开启图片列表勾选数字模式-->
        <item name="picture.style.checkNumMode">false</item>
        <!--选择图片样式0/9-->
        <item name="picture.style.numComplete">false</item>
        <!--图片列表底部背景色-->
        <item name="picture.bottom.bg">@color/color_fa</item>
        <!--图片列表预览文字颜色-->
        <item name="picture.preview.textColor">@color/tab_color_true</item>
        <!--图片列表已完成文字颜色-->
        <item name="picture.complete.textColor">@color/tab_color_true</item>
        <!--图片已选数量圆点背景色-->
        <item name="picture.num.style">@drawable/num_oval</item>
        <!--预览界面标题文字颜色-->
        <item name="picture.ac_preview.title.textColor">@color/white</item>
        <!--预览界面已完成文字颜色-->
        <item name="picture.ac_preview.complete.textColor">@color/tab_color_true</item>
        <!--预览界面标题栏背景色-->
        <item name="picture.ac_preview.title.bg">@color/bar_grey</item>
        <!--预览界面底部背景色-->
        <item name="picture.ac_preview.bottom.bg">@color/bar_grey_90</item>
        <!--预览界面状态栏颜色-->
        <item name="picture.status.color">@color/bar_grey_90</item>
        <!--预览界面返回箭头-->
        <item name="picture.preview.leftBack.icon">@drawable/picture_back</item>
        <!--是否改变预览界面状态栏字体颜色为黑色-->
        <item name="picture.preview.statusFontColor">false</item>
        <!--裁剪页面标题背景色-->
        <item name="picture.crop.toolbar.bg">@color/bar_grey</item>
        <!--裁剪页面状态栏颜色-->
        <item name="picture.crop.status.color">@color/bar_grey</item>
        <!--裁剪页面标题文字颜色-->
        <item name="picture.crop.title.color">@color/white</item>
        <!--相册文件夹列表选中图标-->
        <item name="picture.folder_checked_dot">@drawable/orange_oval</item>
    </style>

```
## 常用功能

******启动相册并拍照******       
```
  PictureSelector.create(MainActivity.this)
       .openGallery(PictureMimeType.ofImage())
       .forResult(PictureConfig.CHOOSE_REQUEST);
```

******单独启动拍照或视频 根据PictureMimeType自动识别******       
```
 PictureSelector.create(MainActivity.this)
       .openCamera(PictureMimeType.ofImage())
       .forResult(PictureConfig.CHOOSE_REQUEST);
```
******预览图片******       
```
 // 预览图片 可自定长按保存路径
PictureSelector.create(MainActivity.this).externalPicturePreview(position, "/custom_file", selectList);
PictureSelector.create(MainActivity.this).externalPicturePreview(position, selectList);

```
******预览视频****** 
```
PictureSelector.create(MainActivity.this).externalPictureVideo(video_path);
```
## 结果回调
```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (resultCode == RESULT_OK) {
            switch (requestCode) {
                case PictureConfig.CHOOSE_REQUEST:
                    // 图片选择结果回调
                    selectList = PictureSelector.obtainMultipleResult(data);
                    // 例如 LocalMedia 里面返回两种path
                    // 1.media.getPath(); 为原图path
                    // 2.media.getCompressPath();为压缩后path，需判断media.isCompressed();是否为true 
                    adapter.setList(selectList);
                    adapter.notifyDataSetChanged();
                    DebugUtil.i(TAG, "onActivityResult:" + selectList.size());
                    break;
            }
        }
    }
    
```

## 更新日志

### 当前版本：
* v2.1.1
* 升级glide4.0为正式版

### 历史版本：
* v2.1.0
* 修复android 7.1.1系统 PopupWindow弹出位置不对问题
* 删除多余代码
* 修复全部模式下设置查询视频秒数无效问题

* v2.0.9
* 修复直接播放视频闪退bug
* 升级glide为4.0.0 rc1
* 新增图片列表点击缩放效果api

* v2.0.7
* 修复已知bug

* v2.0.6
* 新增拍照自定义保存路径

### 项目使用第三方库：
* glide:4.0.0 RC1
* rxjava:2.0.5
* rxandroid:2.0.1
* PhotoView:1.2.4
* luban

## 混淆配置
```
#PictureSelector 2.0
 -keep class com.luck.picture.lib.** { *; }
   
 #rxjava
-dontwarn sun.misc.**
-keepclassmembers class rx.internal.util.unsafe.*ArrayQueue*Field* {
 long producerIndex;
 long consumerIndex;
}
-keepclassmembers class rx.internal.util.unsafe.BaseLinkedQueueProducerNodeRef {
 rx.internal.util.atomic.LinkedQueueNode producerNode;
}
-keepclassmembers class rx.internal.util.unsafe.BaseLinkedQueueConsumerNodeRef {
 rx.internal.util.atomic.LinkedQueueNode consumerNode;
}

#rxandroid
-dontwarn sun.misc.**
-keepclassmembers class rx.internal.util.unsafe.*ArrayQueue*Field* {
   long producerIndex;
   long consumerIndex;
}
-keepclassmembers class rx.internal.util.unsafe.BaseLinkedQueueProducerNodeRef {
    rx.internal.util.atomic.LinkedQueueNode producerNode;
}
-keepclassmembers class rx.internal.util.unsafe.BaseLinkedQueueConsumerNodeRef {
    rx.internal.util.atomic.LinkedQueueNode consumerNode;
}

#glide
-keep public class * implements com.bumptech.glide.module.GlideModule
-keep public class * extends com.bumptech.glide.AppGlideModule
-keep public enum com.bumptech.glide.load.resource.bitmap.ImageHeaderParser$** {
