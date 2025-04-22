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
| Filter Strength | Adjust filter effect strength |

### 4. Other Functions

| Function Name | Description |
|---------------|-------------|
| Green Screen | Achieve background replacement,可用于 virtual background functionality |
| 2D Stickers | Real-time sticker overlays, supporting styles such as small white cat, yellow flower, candy, fluffy flower, bear ears, panda, etc. |
| One-click Beautification | Preset beauty combinations, switch all beauty settings with one click, including natural, cute, goddess, fair, etc. |
| Watermark | Add image watermark |
| Extension Fields | Used for extending more custom beauty items |

## Usage Instructions

#### 1. Introduce SDK
Drag `PixelFree.framework` into your Xcode project and also drag the resource folder (`res`) into the project.

#### 2. Configure Project Dependencies
In `easy_beauty -> Targets -> General -> Frameworks, Libraries and Embedded Content`:

- Add: `libc++.tbd`, `libz.tbd`, `Accelerate.framework`, `CoreImage.framework`, `CoreML.framework`
- Set `PixelFree.framework` Embed to `Embed & Sign`

#### 3. Initialize PixelFree
```objc
- (void)initPixelFree {
    NSString *face_FiltePath = [[NSBundle mainBundle] pathForResource:@"filter_model.bundle" ofType:nil];
    NSString *authFile = [[NSBundle mainBundle] pathForResource:@"pixelfreeAuth.lic" ofType:nil];
    self.mPixelFree = [[SMPixelFree alloc] initWithProcessContext:nil srcFilterPath:face_FiltePath authFile:authFile];
}
```

#### 4. Image/Video Processing
- Image Processing
```objc
UIImage *newImage = [self.mPixelFree processWithImage:self.image rotationMode:PFRotationMode0];
dispatch_async(dispatch_get_main_queue(), ^{
    self->_imageView.image = newImage;
});
```

- Video Processing
```objc
- (void)didOutputVideoSampleBuffer:(CMSampleBufferRef)sampleBuffer {
    CVPixelBufferRef pixbuffer = CMSampleBufferGetImageBuffer(sampleBuffer);
    CVPixelBufferLockBaseAddress(pixbuffer, 0);
    if (pixbuffer && self.mPixelFree) {
        CFAbsoluteTime startTime = CFAbsoluteTimeGetCurrent();
        [self.mPixelFree processWithBuffer:pixbuffer rotationMode:PFRotationMode0];
    }
    CVPixelBufferUnlockBaseAddress(pixbuffer, 0);
}
```

#### 5. Set Beauty/Filters/Stickers
- Beauty Settings
```objc
[_mPixelFree pixelFreeSetBeautyFiterParam:PFBeautyFiterTypeFace_EyeStrength value:&value];
```
- Filter Settings (Example: Black and White Filter)
```objc
const char *filterName = [@"heibai1" UTF8String];
[_mPixelFree pixelFreeSetBeautyFiterParam:PFBeautyFiterName value:(void *)filterName];
[_mPixelFree pixelFreeSetBeautyFiterParam:PFBeautyFiterStrength value:&value];
```
- Sticker Settings (Example: Candy Sticker)
```objc
NSString *name = @"candy.bundle";
NSString *paths = [[NSBundle mainBundle] pathForResource:name ofType:nil];
[self.mPixelFree pixelFreeSetBeautyFiterParam:PFBeautyFiterSticker2DFilter value:(void *)[paths UTF8String]];
```

#### 6. Set One-click Beautification
- One-click Beautification (Cute Style)
```objc
int value = PFBeautyTypeOneKeyCute;
[_mPixelFree pixelFreeSetBeautyFiterParam:PFBeautyFiterTypeOneKey value:&value];
```
**Note: When one-click beautification is enabled, modifying individual beauty or filter parameters will invalidate the one-click beautification configuration!**

#### 7. Issue Feedback
- Email: [zhaobai851@gmail.com]
- WeChat: [Zhao0_ooo]
