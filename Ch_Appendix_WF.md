## 이벤트 핸들러 매개 변수

* 윈도우 이벤트 핸들러는 모두 같은 형식

```
private void Form1_Paint(object sender, PaintEventArgs e)
```

* sender : 이벤트를 호출하는 객체 참조, object는 최상위 클래스(boxing -> unboxing)

```c#
int a = 3;
object c = a; // Boxing
int k = (int) c; // Unboxing
```

* e : 이벤트에 관련된 정보를 제공하는 클래스


## 화면 출력 과정

* 화면 출력 과정

WM_PAINT => Paint event => paint event handler => 이벤트 처리 메소드

```
Form1_Paint()
{
}
```

* Graphics : 출력 객체, e.Graphics 를 통해 get(public Graphics Graphics {get;})


## 텍스트 출력

* Graphics.DrawString() : 오버로드(단일 함수명 + 다양한 매개변수)된 함수

* DrawString() 기본

  * 예시 : DrawString(string s, Font font, Brush brush, float x, float y)
  
  * font : Control.Font 속성 설정
  
  * brush : Brushes.Black
 
```c#
private void Form1_Paint(object sender, PaintEventArgs e)
  {
     e.Graphics.DrawString("Hello buno", Font, Brushes.BlueViolet, 10, 10);
  }
 ```

## 도형 출력
* Grpahics에서 제공
 * 선 : DrawLine()
 * 원형 : DrawEllipse()
 * 사각형 : DrawRectangle()
 
* Pen : 선 그리기 객체

```
System.Object
 System.Drawing.Pens
 
public sealed class Pens // 상속 불가한 클래스
public static Pen Black{get;}
```

```c#
private void Form1_Paint(object sender, PaintEventArgs e)
  {
     e.Graphics.DrawLine(Pens.Red, 10, 10, 200, 20);
     e.Graphics.DrawEllipse(Pens.BlueViolet, 10, 20, 100, 100);
     e.Graphics.DrawRectangle(Pens.BurlyWood, 10, 20, 100, 150);
  }
```

---
출처 : C#으로 살아남기 15강 - 나우캠퍼스
