## 마우스

### Click vs MouseClick

Click : 마우스 클릭

MouseClick : 컨트롤 위에서 마우스 클릭

* 폼도 컨트롤을 상속하므로 폼 위에서 클릭은 Click() -> MouseClick()

```c#
        private void button1_MouseDown(object sender, MouseEventArgs e)
        { // 컨트롤 위에서 마우스가 눌려졌을때
            MessageBox.Show("버튼 눌렸네");
        }

        private void Form1_Click(object sender, EventArgs e)
        {
            MessageBox.Show("Click");
        }

        private void Form1_MouseClick(object sender, MouseEventArgs e)
        {
            MessageBox.Show("MouseClick");
        }
```

### 마우스의 좌표를 출력

#### MouseMove 이벤트
MouseEventArgs.X , MouseEventArgs.Y

마우스의 이동좌표를 출력하는 프로그램
```c#
        private void Form1_MouseMove(object sender, MouseEventArgs e)
        {
            strMousePos = "X :" + e.X + "Y :" + e.Y;
            Invalidate();
        }

        private void Form1_Paint_1(object sender, PaintEventArgs e)
        {
            e.Graphics.DrawString(strMousePos, Font, Brushes.Black, 10, 10);
        }
```

### 이벤트 발생 순서

#### MSDN
폼에서 발생하는 클릭 이벤트 순서

* 마우스 클릭 : MouseDown -> Click -> MouseClick -> MouseUp (마우스가 눌렸다가 떨어지니까)
* 마우스 더블 클릭 : MouseDown -> Click -> MouseClick -> MouseUp -> MouseDown -> DoubleClick -> MouseDoubleClick -> MouseUp

마우스를 3번 클릭할 때마다 삼각형 출력
```c#
        List<Point> ListPoint = new List<Point>();
        
        private void Form1_Paint(object sender, PaintEventArgs e)
        {
            if(ListPoint.Count ==3)
            {
                e.Graphics.DrawLine(Pens.Black, ListPoint[0], ListPoint[1]);
                e.Graphics.DrawLine(Pens.Black, ListPoint[1], ListPoint[2]);
                e.Graphics.DrawLine(Pens.Black, ListPoint[2], ListPoint[0]);
            }
        }
        private void Form1_MouseDown(object sender, MouseEventArgs e)
        {
            Point pt = new Point(e.X, e.Y);
            if (ListPoint.Count == 3)
            {
                ListPoint.Clear();
            }
            ListPoint.Add(pt);
            Invalidate();
        }
```

### MouseEventArgs 속성
* Button : 누른 마우스 버튼의 값을 가져온다
  * MouseButtons 열거형 - Left, Middle, None, Right
  
* X, Y : 마우스의 x,y 좌표
* Location : 마우스의 위치
* Delta : 마우스 휠의 회전수

#### 마우스 드래그
마우스 왼쪽 버튼을 클릭한 상태에서 이동(MouseButton.Left && MouseMove)

마우스의 드래그 영역을 사각형으로 출력
```c#
        Rectangle rectMouse;
        private void Form1_Paint(object sender, PaintEventArgs e)
        {
            string str = "Left" + rectMouse.X + " Top:" + rectMouse.Y +
                " right:" + rectMouse.Right + " bottom:" + rectMouse.Bottom;
            e.Graphics.DrawRectangle(Pens.Black, rectMouse); // 사각형 그리기
            e.Graphics.DrawString(str, Font, Brushes.Black, 10, 10); // 문자열 출력

        }
        private void Form1_MouseDown(object sender, MouseEventArgs e)
        {
            rectMouse.X = e.X;
            rectMouse.Y = e.Y;
            rectMouse.Width = 0; // 가로 길이
            rectMouse.Height = 0; // 세로 길이
            Invalidate();
        }
        private void Form1_MouseMove(object sender, MouseEventArgs e)
        {
            if(e.Button == MouseButtons.Left)
            {
                rectMouse.Width = e.X - rectMouse.X + 1; // +1 의 이유는 길이 정보 이기에 자신도 포함
                rectMouse.Height = e.Y - rectMouse.Y + 1;
                Invalidate();
            }
        }
```
