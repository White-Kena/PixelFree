# PixelFree 使用文档

## 项目简介

**PixelFree** 是一款实时美颜 SDK 示例项目，集成了一键美颜、美肤、美型、滤镜和 2D 贴纸等功能。项目展示了如何通过集成第三方美颜引擎，实现在音视频场景下的实时图像增强与特效渲染，适合用于短视频、直播、视频通话等应用场景的快速接入与二次开发。

当前版本已适配主流音视频平台，包括：七牛云、声网（Agora）、腾讯云实时音视频（TRTC）、即构（Zego）。可作为各平台 SDK 的美颜插件独立接入使用。

---

## 功能模块说明

### 一、美型类功能

| 功能名称 | 描述 |
|----------|------|
| 眼睛大小 | 调整眼睛的放大比例 |
| 瘦脸 | 调整脸部宽度，实现瘦脸效果 |
| 窄脸 | 控制脸部两侧的紧致程度，打造窄脸效果 |
| 下巴 | 调整下巴长度 |
| V脸 | 调整脸部轮廓，使脸型更接近 V 型 |
| 小脸 | 综合调节脸部大小 |
| 鼻子 | 调整鼻梁高度或宽度 |
| 额头 | 调整额头高度或宽度 |
| 嘴巴 | 调整嘴巴大小、形状 |
| 人中 | 调整人中（鼻子到嘴之间的距离） |
| 长鼻 | 延长或缩短鼻子长度 |
| 眼距 | 控制双眼之间的间距 |
| 微笑嘴角 | 增强嘴角上扬，增加笑容感 |
| 旋转眼睛 | 旋转眼睛角度，调整眼神方向 |
| 开眼角 | 调整眼角外扩程度，打造大眼效果 |

### 二、美肤类功能

| 功能名称 | 描述 |
|----------|------|
| 磨皮 | 平滑皮肤纹理，去除瑕疵 |
| 美白 | 提升肤色亮度，增强白皙效果 |
| 红润 | 增加肤色血色感，让皮肤更有生气 |
| 锐化 | 增强画面细节和轮廓清晰度 |
| 新美白算法 | 使用新版美白算法，提升自然度与肤质表现 |
| 画质增强 | 综合提升图像清晰度与通透感 |
| 亮眼 | 增亮眼部区域，突出眼神 |

### 三、滤镜功能

| 功能名称 | 描述 |
|----------|------|
| 滤镜类型 | 可切换多种风格滤镜，包括白亮、粉嫩、黑白、个性、冷色调、美白、暖色调、小清新、质感灰等 |


### 四、其他功能

| 功能名称 | 描述 |
|----------|------|
| 2D 贴纸 | 实时贴纸挂件，支持样式包括小白猫、小黄花、糖果、毛绒花朵、熊耳朵、熊猫等 |
| 一键美颜 | 预设美颜组合，一键切换所有美颜配置，包含自然、可爱、女神、白净等样式 |
| 水印 | 添加图像水印 |
| 扩展字段 | 用于扩展更多自定义美颜项 |

## 五、使用方法

#### 1. 引入 SDK
将 `sdk-aar`文件夹 拖入工程，并将资源文件（`assets` 文件夹）也拖入项目中。

#### 2. 配置项目依赖项
- 在 `android -> settings.gradle` 中添加引入
```kotlin
include ":app"
include ":sdk-aar"
```
- 在 `android -> app -> build.gradle` 中添加依赖
```kotlin
dependencies {
    implementation project(':sdk-aar')
    // other dependencies
}
```


#### 3. 初始化 PixelFree
```kotlin
import com.hapi.pixelfree.*
private val mPixelFree by lazy {
        PixelFree()
    }
mPixelFree.create()
val authData = mPixelFree.readBundleFile(this@MainActivity, "pixelfreeAuth.lic")
mPixelFree.auth(this.applicationContext, authData, authData.size)
val face_fiter = mPixelFree.readBundleFile(this@MainActivity, "filter_model.bundle")
mPixelFree.createBeautyItemFormBundle(face_fiter,face_fiter.size,PFSrcType.PFSrcTypeFilter)
```

#### 4. 图像/视频处理
- 图像处理
```kotlin
val pxInput = PFIamgeInput().apply {
    wigth = w
    height = h
    p_data0 = rgbaData;// 图像数据
    p_data1 = null
    p_data2 = null
    stride_0 = rowBytes
    stride_1 = 0
    stride_2 = 0
    textureID = 0
    format = PFDetectFormat.PFFORMAT_IMAGE_RGBA
    rotationMode = PFRotationMode.PFRotationMode0
}
mPixelFree.processWithBuffer(pxInput)

val frame = pxInput.p_data0?.let {
    val out = VideoFrame(
        w, h,
        ImgFmt.IMAGE_FORMAT_RGBA,
        it,
        0,
        rowBytes,
        0,
    )
    out.textureID = pxInput.textureID
    out
}
```

- 视频处理（下面两种方式按需使用）
```kotlin
// 帧数据处理
fun onProcessFrame(frame: VideoFrame): VideoFrame {
    if (mPixelFree.isCreate()) {
        val pxInput = PFIamgeInput().apply {
            wigth = frame.width
            height = frame.height
            p_data0 = frame.data
            p_data1 = frame.data
            p_data2 = frame.data
            stride_0 = frame.rowStride
            stride_1 = frame.rowStride
            stride_2 = frame.rowStride
            format = PFDetectFormat.PFFORMAT_IMAGE_RGBA
            rotationMode = PFRotationMode.PFRotationMode90
        }
                        
        frame.textureID = if (isLongPress) 0 else pxInput.textureID;
    }
    return super.onProcessFrame(frame)
}
```
```kotlin
// 如果拿不到帧数据，只有纹理ID，则使用纹理ID进行渲染
fun processTexture(ztextureID: Int,zwidth: Int,zheight: Int,): Int {
    val pxInput = PFIamgeInput().apply {
        textureID = ztextureID
        wigth = zwidth
        height = zheight
        format = PFDetectFormat.PFFORMAT_IMAGE_TEXTURE
        rotationMode = PFRotationMode.PFRotationMode0
    }
    mPixelFree?.processWithBuffer(pxInput)
    return pxInput.textureID
}
```


#### 5. 设置美颜/滤镜/贴纸
- 美颜设置
```kotlin
mPixelFree.pixelFreeSetBeautyFiterParam(PFBeautyFiterType.PFBeautyFiterTypeFace_EyeStrength,value.toFloat())
```
- 滤镜设置（以黑白滤镜为例）
```kotlin
mPixelFree.pixelFreeSetFiterParam("heibai1", value.toFloat())
```
- 贴纸设置
```kotlin
if (name == "origin") {
    val byteArray = ByteArray(0)
    mPixelFree?.createBeautyItemFormBundle(
        byteArray,
        0,
        PFSrcType.PFSrcTypeStickerFile
    )
} else {
    mPixelFree?.pixelFreeSetBeautyExtend(PFBeautyFiterType.PFBeautyFiterExtend,"mirrorX_1");
    val stickerBundle = mPixelFree?.readBundleFile(context, "$name.bundle")
    if (stickerBundle != null) {
        mPixelFree?.createBeautyItemFormBundle(
            stickerBundle,
            stickerBundle.size,
            PFSrcType.PFSrcTypeStickerFile
        )
    }
}
```

#### 6. 设置一键美颜
- 一键美颜（可爱风格）
```kotlin
val typeValue = value.toInt()
mPixelFree.pixelFreeSetBeautyFiterParam(PFBeautyFiterType.PFBeautyFiterTypeOneKey, typeValue)
```
**一键美颜开启后，若单独修改美颜或滤镜参数，会使一键美颜配置失效！**
#### 7. 问题反馈
- 邮箱: [zhaobai851@gmail.com]
- 微信: [Zhao0_ooo]
