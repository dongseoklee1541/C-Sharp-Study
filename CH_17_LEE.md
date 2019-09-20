### 오리 타이핑

실행안됨
```c#
using System;


namespace DuckTyping
{
    class Duck
    {
        public void Walk()
        {
            Console.WriteLine(this.GetType() + "walk");
        }
        public void Swim()
        {
            Console.WriteLine(this.GetType() + "Swim");
        }
        public void Quack()
        {
            Console.WriteLine(this.GetType() + "Quack");
        }
    }
    
    class Mallard : Duck
    { }
    
    class Robot
    {
        public void Walk()
        {
            Console.WriteLine("Robot.Walk");
        }
        public void Swim()
        {
            Console.WriteLine("Robot.Swim");
        }
        public void Quack()
        {
            Console.WriteLine("Robot.Quack");
        }
    }
    class MainApp
    {
        static void Main(string[] args)
        {
            dynamic[] arr = new dynamic[] {new Duck(), new Mallard(), new Robot()};

            foreach(dynamic duck in arr)
            {
                Console.WriteLine(duck.GetType());
                duck.walk();
                duck.Swim();
                duck.Quack();

                Console.WriteLine();
            }
        }
    }
}
```


```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

using Microsoft.Scripting;
using Microsoft.Scripting.Hosting;
using IronPython.Hosting;

namespace WithPython
{
    class MainApp
    {
        static void Main(string[] args)
        {
            ScriptEngine engine = Python.CreateEngine();
            ScriptScope scope = engine.CreateScope();
            scope.SetVariable("n", "박상현");
            scope.SetVariable("p", "010-123-4566");

            ScriptSource source = engine.CreateScriptSourceFromString(
                @"
class NameCard : 
    name = ''
    phone = ''

    def __init__(self, name, phone) :
        self.name = name
        self.phone = phone

    def printNameCard(self) :
        print self.name + ', ' + self.phone

NameCard(n,p)
");
            dynamic result = source.Execute(scope);
            result.printNameCard();
            Console.WriteLine("{0},{1}", result.name, result.phone);
        }
    }
}
```
