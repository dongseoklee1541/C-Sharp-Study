## 키보드

* 키보드 동작 : 눌림과 놓임

* 키 구분 
  * 문자키 : A to Z, a to z, 0 to 9,Space bar, 등
  * 기능키 : F1 ~ F12, Ctrl, Shift, Alt, Insert 등
  
* 키 관련 메시지 : WM_CHAR, WM_KEYDOWN, WM_KEYUP (운영체제에서 처리)

* 키 관련 이벤트 : KeyDown, KeyUp, KeyPress

* 키 눌림 이벤트 : KeyDown

* 문자키 눌림 이벤트 (대소문자 구분) : KeyPress

---

#### 메시지 박스 활용
각종 값 또는 이벤트를 확인하기 위해 사용

MessageBox.Show("문자열");

* 키 눌림과 문자키 입력 확인
  * KeyDown + MessageBox (키 눌림)
  * KeyPress + MessageBox (문자키 눌림)
  
```c#
  private void Form1_KeyDown(object sender, KeyEventArgs e)
  {
     MessageBox.Show("KeyDown");
  }

  private void Form1_KeyPress(object sender, KeyPressEventArgs e)
  {
     MessageBox.Show("KeyPress");
  }
```

### KeyDown 이벤트 처리

#### KeyDown 이벤트

* **키 눌림** 을 위한 이벤트(눌림만 감지한다)
* Keys 열거형으로 키 구분(대소문자 구분, Keys.A 등)

#### Keys 열거형

* MSDN 검색
* 문자키는 모두 대문자
  * Keys.A, Keys.B

#### KeyCode
* 키보드 키에 대한 Keys 열거값
* Keys.A , Keys.D0 , Keys.F1 등
* Alt, Control, Shift 정보가 없음

#### KeyCode의 Alt, Control, Shift 보완?
* KeyEventArgs 속성 활용 , e.Alt, e.Control, e.Shift
* &&(AND연산) 와 ||(OR연산)로 조합

```c#
private void Form1_KeyDown(object sender, KeyEventArgs e)
   {
     if (e.KeyCode == Keys.A && e.Shift && e.Control) // A 와 Shift 그리고 Control을 동시에 눌렀나?
      {
         MessageBox.Show("KeyCode + Shift + Control");
      }
   }
```

#### Modifiers

* 보조 플래그 Alt, Control, Shift 조합

* KeyCode 키 와 Alt, Control, Shift 조합
  * e.Modifiers
  * 비트 연산자 **'|'** 로 키 조합 ex) e.Modifiers == (Keys.Shift | Keys.Control)
  
```c#
private void Form1_KeyDown(object sender, KeyEventArgs e)
   {
      if (e.Modifiers == (Keys.Shift | Keys.Control))
        MessageBox.Show("Modifiers + Shift + Control");
        
      if ((e.KeyCode == Keys.A) && (e.Modifiers == (Keys.Shift | Keys.Alt)))
        MessageBox.Show("A + Shift + Alt");
   }
```

#### KeyData
* 키 값과 Alt, Control, Shift 정보 포함
* 키 값과 보조플래그의 조합은 '|' 사용

```c#
private void Form1_KeyDown(object sender, KeyEventArgs e)
   {
      if (e.KeyData == (Keys.A | Keys.Shift | Keys.Control))
          MessageBox.Show("A + Shift + Control")
   }
```

### KeyPress

* 위의 방식과는 다른 이벤트 처리기
* 문자키의 **대소문자** 를 구분, 문자 ASC 값과 유니코드 값이 같음
* WM_CHAR 메시지 발생
* 이벤트 메서드
```
private void Form1_KeyPress(object sender, KeyPressEvenetArgs e) // KeyPressEvenetArgs 로 실행됨
```

* KeyChar 속성 - Public char KeyChar {get; set;} => ASC  값을 리턴
* 문자키를 입력받아 화면에 출력

```C#

private void Form1_KeyPress(object sender, KeyPressEventArgs e)
 {
    strMessage += e.KeyChar; // 키보드를 통해 입력된 char가 추가됨
    Invalidate(); // 실행 메서드 (Paint로 실행됨)
 }
 
private void Form1_Paint(object sender, PaintEventArgs e)
 {
    e.Graphics.DrawString(strMessage, Font, Brushes.Black, 10, 10);
 }


---

출처 : C#으로 살아남기 16강 - 나우캠퍼스
```
