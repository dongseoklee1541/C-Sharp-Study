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
