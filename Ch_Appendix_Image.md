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

### 더블 버퍼링
이미지의 출력의 깜빡임을 줄이기 위한 처리. 원본 화면과 똑같은 크기의 백버퍼를 미리 준비해놓고, 필요할때 불러서 화면전환 하는 방식으로 사용한다.

* 용어 : 백버퍼, 오프스크린

* 더블 버퍼링 관련된 객체
 * BufferedGraphicsManager class <- 참조 ---- BufferedGraphicsContext class ---- 생성 -> **BufferedGraphics class(실체)**
 
* 폼의 DoubleBuffered 속성 true로 설정

* DoubleBuffered 속성 코드로 설정하는 방법 : SetStyle(ControlStyles.OptimizedDoubleBuffer, true); 이지만 속성 값을 트루로 바꾸는게 더 편함

* 더블 버퍼링에 출력하는 객체와 메서드
  * BufferedGraphics.Graphics
  * BufferedGraphics.Graphics.DrawImage() : 그리기
  * BufferedGraphics.Graphics.Clear() : 더블버퍼 지우기
  
* 화면에 출력 : BufferedGraphics.Render() : public void Render( Graphics target)

더블 버퍼링 객체 생성, 더블버퍼링을 이용하여 이미지 출력, 배경은 Yellow, 이미지 100번 출력
```c#
    public partial class Form1 : Form
    {
        BufferedGraphicsContext context; // 필수 사항
        BufferedGraphics graphics; // 필수 사항
        Image myImage;

        public Form1()
        {
            InitializeComponent();

            context = BufferedGraphicsManager.Current; // 참조
            context.MaximumBuffer = new Size(850, 850); // 백버퍼 크기 설정
            graphics = context.Allocate(CreateGraphics(),
                new Rectangle(0, 0, 850, 850)); // 버퍼 그래픽스 객체 생성 및 참조
            graphics.Graphics.Clear(Color.Yellow); // 노란색으로 백버퍼가 지워짐
            myImage = Image.FromFile("C:/Users/dlsef/source/repos/WindowsFormsApp1/WindowsFormsApp1/obj/Debug/1546432834.jpg");
            SetClientSizeCore(850, 850);
           
        }
        private void Form1_Paint(object sender, PaintEventArgs e)
        {
            // 더블 버퍼에 출력
            Random rand = new Random();
            for(int i=0;i<100;i++)
            {
                graphics.Graphics.DrawImage(myImage,
                    rand.Next(0, 700), rand.Next(0, 500));
            } // 모두 백버퍼에 출력된 상태

            //화면에 출력, 백버퍼에 저장된 녀석들을 메인화면으로 넘겨주세요.
            graphics.Render(e.Graphics); 
        }
```
