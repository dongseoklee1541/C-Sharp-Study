```{.cs}
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
