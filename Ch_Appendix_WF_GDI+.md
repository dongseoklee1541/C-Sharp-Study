## GDI+

### GDI & GDI+

GDI(Graphics Device Interface) -> OS의 영역
GDI+ -> .NET은 Graphics가 담당

#### Graphics 사용방법 2가지

* paint() 이벤트 : e.Graphics()

* CreateGraphics() 사용 : Grpahics 참조 변수 생성

> CreateGraphics() 언제 사용?

**paint 이벤트 외의 메서드에서 사용**

### CreateGraphics() 사용

#### 원형
* public Graphics CreateGrahpics()

* namespace -> system.Windows.Forms

* 사용한 후에는 되도록 Graphics.Dispose() 호출

#### 사용하는 방법
* Graphics 객체를 참조
* Graphics 메서드로 호출

### Graphics 객체 사용 예

* 마우스 클릭마다 원 호출

Paint 이벤트 사용
```c#
        Point[] ptCircle; // Point 라는 배열 생성
        int nCount;

        public Form1()
        {
            InitializeComponent();

            ptCircle = new Point[100];
        }

        private void Form1_Paint(object sender, PaintEventArgs e)
        {
           for(int i=0;i<nCount;i++)
            {
                e.Graphics.DrawEllipse(Pens.Black, ptCircle[i].X - 10,
                    ptCircle[i].Y - 10, 20, 20);
            }
        }

        private void Form1_MouseClick(object sender, MouseEventArgs e)
        {
            ptCircle[nCount].X = e.X;
            ptCircle[nCount].Y = e.Y;
            nCount++;
            Invalidate();
        }
```

graphics 객체로 사용
```c#
        private void Form1_MouseClick(object sender, MouseEventArgs e)
        {
            ptCircle[nCount].X = e.X;
            ptCircle[nCount].Y = e.Y;

            Graphics g = CreateGraphics(); // Graphics 객체 생성
            g.DrawEllipse(Pens.Black, ptCircle[nCount].X - 10,
                ptCircle[nCount].Y - 10, 20, 20);
            nCount++;
            g.Dispose();// Graphics 사용 끝날때 Dispose 로 알리기
        }
```

### Color 구조체

* 구조체 정의 : Color Struct (alpha,red,green,blue) 여기서 alpha는 밝기 값이다.

* 사용 용도 : 펜, 브러시의 색상 설정에 사용

* Color 속성 사용하여 색상 설정 : AliceBlue, AntiqueWhite, Aqua 등 (속성을 통한 색상 설정, Color 리턴)

```c#
public static System.Drawing.Color Aqua {get;}
```

* 사용자 정의할 수 있는 색상 
  * FromArgb()
  * FromArgb (int alpha, int red, int green, int blue)
  
* 시스템 색상에서 A, R, G, B 가져오기
  * Color.색상.A, Color.색상.R 등등..(public byte A {get;} , public byte R {get;})

```c#
        private void Form1_Paint(object sender, PaintEventArgs e)
        {
            string str = "Alpha:" + Color.Blue.A + " Red:" + Color.Blue.R + " Green:" + Color.Blue.G + " Blue:" + Color.Blue.B;
            e.Graphics.DrawString(str, Font, Brushes.Blue, 10, 10);
        }
```

### Pen

* Pen : 생성
* Pens : 기본 제공 펜(시스템에서 제공)
* Pen 생성자 : Pen(Brush), Pen(Color), Pen(Brush, Single), Pen(Color, Single)
* Pen 해제 : Dispose()

Pen과 Pens 사용 예
```c#
        private void Form1_Paint(object sender, PaintEventArgs e)
        {
            Color myColor = Color.FromArgb(255, 0, 0);
            Pen newPen = new Pen(myColor);
            newPen.Width = 5.0f; // new Pen(myColor,5.0f); 처럼 생성자에서 굵기 설정 가능
            e.Graphics.DrawLine(newPen, 10, 10, 100, 10);
            e.Graphics.DrawLine(Pens.Blue, 10, 20, 100, 20);

            newPen.Dispose();
        }
```

## 펜 스타일 

### DashStyle 속성 사용
public System.Drawing.Drawing2D.DashStyle DashStyle {get;set;}

* Using System.Drawing.Drawind2D 선언

* DashStyle 열거형 리턴
 * Custom, Dash, DashDot, DashDotDot, Dot, Solid(실선)
 
DashDot와 Dot 선 스타일 지정, 너비 3으로 설정
```c#
        private void Form1_Paint(object sender, PaintEventArgs e)
        {
            Pen myPen = new Pen(Color.Black, 3.0f);
            myPen.DashStyle = DashStyle.DashDot;
            e.Graphics.DrawLine(myPen, 10, 10, 200, 10);
            myPen.DashStyle = DashStyle.Dot;
            e.Graphics.DrawLine(myPen, 10, 20, 200, 20);
            
            newPen.Dispose();
        }
```

## 브러쉬

* 역할 : **도형 내부를 색 또는 패턴** 으로 채우는 역할
* 브러시 종류 : SolidBrush, HatchBrush, TextureBrush... 등
* 브러시를 요구하는 메서드의 공통점 : Fill~~~ 으로 시작

#### SolidBrush 단색 브러시

* 도형 전체를 한가지 색으로 채우는 브러시

파란색으로 채우는 원 출력
```c#
        private void Form1_Paint(object sender, PaintEventArgs e)
        {
            SolidBrush mybrush  = new SolidBrush(Color.Blue);
            e.Graphics.FillEllipse(mybrush, 10, 10, 100, 100); // Ellipse에 색 채워넣기
            mybrush.Dispose();
        }
```

#### HatchBrush 패넡 브러시

* 격자 패턴으로 도형을 채우는 브러시

* Using System.Drawing.Drawind2D 선언


전경색은 빨간색, 배경은 파란색으로 출력
```c#
        private void Form1_Paint(object sender, PaintEventArgs e)
        {
            HatchBrush Hash = new HatchBrush(HatchStyle.Plaid, Color.Red, Color.Blue);
            e.Graphics.FillRectangle(Hash, 100, 100, 200, 200);
            Hash.Dispose();
        }
```
