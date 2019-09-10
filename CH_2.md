'이것이 C#이다'를 공부하면서 개인적으로 정리한 자료입니다.
--------------------------------------
```cs
using System;
using static System.Console;

namespace BrainCSharp
{
    class HelloWorld
    {
        static void Main(string[] args)
        {
            if(args.Length == 0)
            {
                Console.WriteLine("사용법 : HelloWorld.exe <이름>");
                return;
            }

            WriteLine("Hello,{0}!", args[0]);
        }
    }
}
```

> namespace : 성격이나 하는일 비슷한 클래스,구조체,인터페이스,대리자,열거 형식 등을 하나의 이름 아래 묶는 일을 한다. 
```cs
namespace 네임스페이스_이름
{
   //클래스
   //구조체
   //인터페이스 등...
}
```
다른 네임스페이스에서 HelloWorld 클래스를 사용하려면 using BrainCSharp; 문장을 이용해서 BrainCSharp 네임스페이스를 BrainCSharp.HelloWrold처럼 클래스가 소속되어 있는 네임스페이스와 클래스의 이름을 붙여줘야 한다.

> static void Main(String[] args){} : 메소드의 일종이지만 특별한 메소드다. 이 메소드는 프로그램의 진입점(Entry Point)고 프로그램이 시작하면 실행되고 이 메소드가 종료되면 프로그램도 종료된다. 따라서 몯느 프로그램은 반드시 Main이라는 이름을 가진 메소드가 있어야 한다.

static ? 한정자로 메소드나 변수를 수식하는 역할을 한다. C# 프로그램의 각 요소는 코드가 실행되는 시점에 메모리에 할당되지만 static 키워드로 수식되는 코드는 프로그램이 처음 구동될 때부터 메모리에 할당된다.

프로그램이 실행되면 CLR(Common Language Runtime)은 프로그램을 메모리에 올린 후, 프로그램의 진입점을 찾는데 이때 만약 Main() 메소드가 static으로 수식되있지 않으면 CLR은 진입점을 찾지 못할것이다. 


```cs
using System;
using static System.Console;
// CH.2의 연습문제
namespace BrainCSharp
{
    class HelloWorld
    {
        static void Main(string[] args)
        {
            if(args.Length == 0)
            {
                Console.WriteLine("여러분, 안녕하세요?");
                Console.WriteLine("반갑습니다!");
                return;
            }

            WriteLine("Hello,{0}!", args[0]);
        }
    }
}
```
출처 : 이것이 C#이다(저자 : 박상현 , 출판사 : 한빛미디어) CH.2 처음 만드는 C#프로그램
