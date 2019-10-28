## Bitmap VS BitmapData

### Bitmap 클래스는?
MSDN의 정의에서는 **"그래픽 이미지의 픽셀 데이터와 그 특성으로 구성되는 GDI+ 비트맵을 캡슐화합니다. 
Bitmap은 픽셀 데이터로 정의된 이미지에서 작업하는 데 사용되는 개체입니다."** 라고 한다. 즉 픽셀 데이터 형식으로 저장되는 이미지들 모두 사용
가능하다는 의미로 받아 들여진다.(벡터 방식이 아닌 녀석들)
 * 지원 가능한 파일 형식 : BMP, GIF, EXIF, JPG, PNG 및 TIFF 등

### BitmapData
MSDN의 정의는 **"비트맵 이미지의 특성을 지정합니다. BitmapData 클래스는 
LockBits 클래스의 UnlockBits(BitmapData) 및 Bitmap 메서드에서 사용됩니다."** 라는 것에서 Bitmap 클래스에서 저장된 각종 정보를 저장하는
클래스의 역할을 하는것으로 이해된다. 
* 각종 정보 : Height, PixelFormat, Scan0, Stride, Width 등

----

## 이미지 파일 형식

### BMP
