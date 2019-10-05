## 이미지

#### GDI+ 에서 다룰 수 있는 이미지 형식
bmp,jpg,png,gif,tiff,exif 등

#### 이미지 다루는 클래스
* Image : 추상 기본 클래스(abstract base class)

* 이미지 로딩 메서드 : public **static** Image FromFile (string filename); // static 이므로 추상클래스의 메서드도 그냥 사용 가능

* 이미지 출력 메서드(Graphics)
```c#
public void DrawImage(Image image, int x, int y)
```

```c#
        private void Form1_Paint(object sender, PaintEventArgs e)
        {
            Image myimage = Image.FromFile("C:/Users/dlsef/source/repos/WindowsFormsApp1/WindowsFormsApp1/obj/Debug/photo.png");
            e.Graphics.DrawImage(myimage, 0, 0);
        }
```


### 비트맵

#### Bitmap 클래스
* Image를 상속한 클래스

* public **sealed** calss Bitmap : Image // sealed : 상속 불가능

* 기능적으로 Image < Bitmap

* 다양한 형식으로 저장 가능 : bmp, emf, exif, gif, icon, jpeg, png, tiff, wmf 등

#### 비트맵을 이용한 이미지 로딩

* public Bitmap(string filename) : BMP, GIF, EXIF, JPG, PNG, TIFF 로딩 가능

* Bitmap.SetPixcel(), Bitmap.GetPixel() : 로딩된 이미지의 픽셀 정보

```c#
public Color GetPixel(int x, int y)
public void SetPixel(int x, int y, Color color)
```

* Bitmap 출력 : Graphics.DrawImage()

로딩한 이미지와 폼의 크기가 같도록 출력
```c#
        private void Form1_Paint(object sender, PaintEventArgs e)
        {
            Bitmap myBitmap = new Bitmap("C:/Users/dlsef/source/repos/WindowsFormsApp1/WindowsFormsApp1/obj/Debug/1546432834.jpg");
            SetClientSizeCore(myBitmap.Width, myBitmap.Height); // 클라이언트 영역 지정, 비트맵의 가로길이와 세로길이를 가져온다
            e.Graphics.DrawImage(myBitmap, 0, 0);
        }
```

### 임의의 크기의 비트맵 생성 및 클리어
지금까지는 이미지 파일을 받아와서 생성했지만, 이제부터는 직접 메모리에 저장해보자.

* 임의의 비트맵 생성 : Public Bitmap(int width, int height)
* Graphics 객체로 비트맵 참조 : public static Graphics FromImage( Image image)
* Graphics 를 사용하여 비트맵 지우기 : Graphics.Clear()

예제 : 400x300 크기의 비트맵을 생성하고 "C# Programming" 출력 x10, 배경색은 Yellow 로 출력, 사각형 출력
```c#
   public partial class Form1 : Form
    {
        Bitmap bitmap;
        public Form1()
        {
            InitializeComponent();

            bitmap = new Bitmap(400, 300);
            SetClientSizeCore(400, 300);//클라이언트 영역 지정
        }

        private void Form1_Paint(object sender, PaintEventArgs e)
        {   // 메모리에서 그려지는 부분
            Graphics bitmapGraphics = Graphics.FromImage(bitmap);
            bitmapGraphics.Clear(Color.Yellow);
            for(int i=0;i<10;i++)
            {
                bitmapGraphics.DrawString("C# Programming", Font, Brushes.Black, 10+ (i * 2), 10+ (i * 2));
                bitmapGraphics.DrawRectangle(Pens.Black, 100+(i*2), 10 + (i * 2), 200, 100);
            }
            // 폼에 출력
            e.Graphics.DrawImage(bitmap, 0, 0);
        }
    }
```
