# PixelFree Usage Documentation

## Project Overview

**PixelFree** is a real-time beauty SDK sample project that integrates one-click beautification, skin smoothing, face shaping, filters, and 2D stickers. The project demonstrates how to integrate a third-party beauty engine to achieve real-time image enhancement and effect rendering in audio-video scenarios, suitable for quick integration and secondary development in applications such as short videos, live streaming, and video calls.

The current version is compatible with mainstream audio-video platforms, including: Qiniu Cloud, Agora, Tencent Cloud Real-Time Video Communication (TRTC), and Zego. It can be used as a beauty plugin for independent integration with various platform SDKs.

---

## Function Module Description

### 1. Face Shaping Functions

| Function Name | Description |
|---------------|-------------|
| Eye Size | Adjust the magnification ratio of eyes |
| Face Thinning | Adjust face width to achieve slimming effect |
| Narrow Face | Control the tightness of both sides of the face to create a narrow face effect |
| Chin | Adjust chin length |
| V-shaped Face | Adjust face contour to make it closer to a V-shape |
| Small Face | Comprehensive adjustment of face size |
| Nose | Adjust nose bridge height or width |
| Forehead | Adjust forehead height or width |
| Mouth | Adjust mouth size and shape |
| Philtrum | Adjust philtrum (distance between nose and mouth) |
| Long Nose | Extend or shorten nose length |
| Eye Distance | Control the distance between both eyes |
| Smiling Corners | Enhance the upward curve of the corners of the mouth to increase smile effect |
| Rotate Eyes | Rotate eye angle to adjust gaze direction |
| Open Eye Corner | Adjust the extent of eye corner expansion to create a big eye effect |

### 2. Skin Smoothing Functions

| Function Name | Description |
|---------------|-------------|
| Skin Smoothing | Smooth skin texture, remove blemishes |
| Whitening | Enhance skin brightness, achieve a fairer look |
| Rosy | Increase skin rosy tone, make skin more vibrant |
| Sharpen | Enhance image details and contour clarity |
| New Whitening Algorithm | Use new whitening algorithm to improve naturalness and skin quality |
| Image Enhancement | Comprehensive improvement of image clarity and transparency |
| Brighten Eyes | Brighten eye area to highlight gaze |

### 3. Filter Functions

| Function Name | Description |
|---------------|-------------|
| Filter Types | Switch among multiple style filters, including white bright, pink, black and white, personalized, cool tone, whitening, warm tone, fresh, and textured gray |

### 4. Other Functions

| Function Name | Description |
|---------------|-------------|
| 2D Stickers | Real-time sticker overlays, supporting styles such as small white cat, yellow flower, candy, fluffy flower, bear ears, panda, etc. |
| One-click Beautification | Preset beauty combinations, switch all beauty settings with one click, including natural, cute, goddess, fair, etc. |
| Watermark | Add image watermark |
| Extension Fields | Used for extending more custom beauty items |

## Usage Instructions

#### 1. Introduce SDK
Drag the `sdk-aar` folder into your project and also drag the resource folder (`assets` folder) into the project.

#### 2. Configure Project Dependencies
- In `android -> settings.gradle` add the following:
```kotlin
include ":app"
include ":sdk-aar"
```
- In android -> app -> build.gradle add the dependency:
```kotlin
dependencies {
    implementation project(':sdk-aar')
    // other dependencies
}
```


#### 3. Initialize PixelFree
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

#### 4. Image/Video Processing
- Image Processing
```kotlin
val pxInput = PFIamgeInput().apply {
    wigth = w
    height = h
    p_data0 = rgbaData;// Image data
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

- Video Processing (Use one of the following methods as needed)
```kotlin
// Frame data processing
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
// If you cannot get frame data, only have texture ID, use texture ID for rendering
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


#### 5. Set Beauty/Filters/Stickers
- Beauty Settings
```kotlin
mPixelFree.pixelFreeSetBeautyFiterParam(PFBeautyFiterType.PFBeautyFiterTypeFace_EyeStrength,value.toFloat())
```
- Filter Settings (Example: Black and White Filter)
```kotlin
mPixelFree.pixelFreeSetFiterParam("heibai1", value.toFloat())
```
- Sticker Settings
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

#### 6. Set One-click Beautification
- One-click Beautification (Cute Style)
```kotlin
val typeValue = value.toInt()
mPixelFree.pixelFreeSetBeautyFiterParam(PFBeautyFiterType.PFBeautyFiterTypeOneKey, typeValue)
```
**Note: When one-click beautification is enabled, modifying individual beauty or filter parameters will invalidate the one-click beautification configuration!**

#### 7. Issue Feedback
- Email: [zhaobai851@gmail.com]
- WeChat: [Zhao0_ooo]
