### 대리자란?
<details>
<summary>예시 코드</summary>
<div markdown="1">
    
```c#
using System;

namespace Delgate
{
    delegate int MyDelgate(int a, int b);

    class Calculator
    {
        public int Plus(int a, int b)
        {
            return a + b;
        }
        public static int Minus(int a, int b)
        {
            return a - b;
        }
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            Calculator Calc = new Calculator();
            MyDelgate Callback;

            Callback = new MyDelgate(Calc.Plus);
            Console.WriteLine(Callback(3, 4));

            Callback = new MyDelgate(Calculator.Minus);
            Console.WriteLine(Callback(7, 5));
        }
    }
}
```

</div>
</details>

### 대리자는 왜, 언제 사용 하나?
<details>
<summary>예시 코드</summary>
<div markdown="1">
    
```c#
using System;

namespace UsingCallback
{
    delegate int Compare(int a, int b);

   
    class MainApp
    {
        static int AscendCompare(int a, int b)
        {
            if (a > b)
                return 1;
            else if (a == b)
                return 0;
            else
                return -1;
        }
        static int DescendCompare(int a, int b)
        {
            if (a < b)
                return 1;
            else if (a == b)
                return 0;
            else
                return -1;
        }
        static void BubbleSort(int[] DataSet, Compare Comparer)
        {
            int i = 0;
            int j = 0;
            int temp = 0;

            for(i=0;i<DataSet.Length -1;i++)
            {
                for(j=0;j<DataSet.Length -(i+1);j++)
                {
                    if(Comparer(DataSet[j],DataSet[j+1]) > 0)
                    {
                        temp = DataSet[j + 1];
                        DataSet[j + 1] = DataSet[j];
                        DataSet[j] = temp;
                    }
                }
            }
        }
        static void Main(string[] args)
        {
            int[] array = { 3, 7, 4, 2, 10 };

            Console.WriteLine("Sorting ascending...");
            BubbleSort(array, new Compare(AscendCompare)); // 오름차순으로 정렬

            for(int i=0;i<array.Length;i++)
            {
                Console.Write($"{array[i]}");
            }

            int[] array2 = { 7, 2, 8, 10, 11 };
            Console.WriteLine("\nSorting ascending...");
            BubbleSort(array, new Compare(DescendCompare));// 내림차순으로 정렬

            for (int i = 0; i < array.Length; i++)
            {
                Console.Write($"{array[i]}");
            }
            Console.WriteLine();
        }
    }
}
```


</div>
</details>

### Generic 버전
<details>
<summary>예시 코드</summary>
<div markdown="1">
    
```c#
using System;

namespace GenericDelegate
{
    delegate int Compare<T>(T a, T b);

   
    class MainApp
    {
        static int AscendCompare<T>(T a, T b) where T : IComparable<T>
        {
            return a.CompareTo(b);
        }
        static int DescendCompare<T>(T a, T b) where T :IComparable<T>
        {
            return a.CompareTo(b) * -1;
        }
        static void BubbleSort<T>(T[] DataSet, Compare<T> Comparer)
        {
            int i = 0;
            int j = 0;
            T temp;

            for(i=0;i<DataSet.Length -1;i++)
            {
                for(j=0;j<DataSet.Length -(i+1);j++)
                {
                    if(Comparer(DataSet[j],DataSet[j+1]) > 0)
                    {
                        temp = DataSet[j + 1];
                        DataSet[j + 1] = DataSet[j];
                        DataSet[j] = temp;
                    }
                }
            }
        }
        static void Main(string[] args)
        {
            int[] array = { 3, 7, 4, 2, 10 };

            Console.WriteLine("Sorting ascending...");
            BubbleSort<int>(array, new Compare<int>(AscendCompare)); // 오름차순으로 정렬

            for(int i=0;i<array.Length;i++)
            {
                Console.Write($"{array[i]} ");
            }

            string[] array2 = { "abc","def","ghi","jkl","mno" };
            Console.WriteLine("\nSorting ascending...");
            BubbleSort<string>(array2, new Compare<string>(DescendCompare));// 내림차순으로 정렬

            for (int i = 0; i < array.Length; i++)
            {
                Console.Write($"{array[i]} ");
            }
            Console.WriteLine();
        }
    }
}
```

</div>
</details>

### 대리자 체인(Delegate Chain)

<details>
<summary>예시 코드</summary>
<div markdown="1">
    
```c#
using System;

namespace DelegateChains
{
    delegate void Notify(string message); // 대리자 선언

    class Notifier
    {
        public Notify eventOccured; // delegate 형을 갖고 있는 녀석.
    }

    class EventListener
    {
        private string name;
        public EventListener(string name)
        {
            this.name = name;
        }

        public void SomethingHappend(string message)
        {
            Console.WriteLine($"{name}.SomethingHappend : {message}");
        }
    }
    
    
  
    class MainApp
    {
        static void Main(string[] args)
        {
            Notifier notifier = new Notifier(); // eventOccured 생성.
            EventListener listener1 = new EventListener("Listener1");
            EventListener listener2 = new EventListener("Listener2");
            EventListener listener3 = new EventListener("Listener3");
            
            notifier.eventOccured += listener1.SomethingHappend; // 대리자에 메소드 저장
            notifier.eventOccured += listener2.SomethingHappend;
            notifier.eventOccured += listener3.SomethingHappend;
            notifier.eventOccured("You've got mail.");

            Console.WriteLine();

            notifier.eventOccured -= listener2.SomethingHappend;
            notifier.eventOccured("Download Complete.");
            
            Console.WriteLine();

            notifier.eventOccured = new Notify(listener2.SomethingHappend)
                + new Notify(listener3.SomethingHappend);
            notifier.eventOccured("Nuclear launch Detected.");

            Notify notifiy1 = new Notify(listener1.SomethingHappend);
            Notify notifiy2 = new Notify(listener2.SomethingHappend);

            notifier.eventOccured = (Notify)Delegate.Combine(notifiy1, notifiy2);
            notifier.eventOccured("Fire!!");

            Console.WriteLine();
            notifier.eventOccured = (Notify)Delegate.Remove(notifier.eventOccured, notifiy2);
            notifier.eventOccured("RPG!!");

        }
    }
}
```


</div>
</details>

### 익명 메소드
<details>
<summary>예시 코드</summary>
<div markdown="1">
    
```c#
using System;

namespace AnontmousMethod
{
    delegate int Compare(int a, int b); // 대리자 선언

    
   
    class MainApp
    {
        static void BubbleSort(int[] DataSet, Compare Comparer)
        {
            int i = 0;
            int j = 0;
            int temp = 0;

            for(i=0;i<DataSet.Length;i++)
            {
                for(j=0;j<DataSet.Length -(i+1);j++)
                {
                    if(Comparer(DataSet[j],DataSet[j+1])>0)
                    {
                        temp = DataSet[j + 1];
                        DataSet[j + 1] = DataSet[j];
                        DataSet[j] = temp;
                    }
                }
            }
        }
        static void Main(string[] args)
        {
            int[] array = { 3, 7, 4, 2, 10 };

            Console.WriteLine("Sorting ascending...");
            BubbleSort(array,delegate(int a,int b)
            {
                if (a > b)
                    return 1;
                else if (a == b)
                    return 0;
                else
                    return -1;
            });

            for (int i = 0; i < array.Length; i++)
                Console.Write($"{array[i]} ");

            int[] array2 = { 7, 2, 8, 10, 11 };
            Console.WriteLine("\nSorting descending...");
            BubbleSort(array2, delegate (int a, int b)
             {
                 if (a < b)
                     return 1;
                 else if (a == b)
                     return 0;
                 else
                     return -1;
             });

            for (int i = 0; i < array2.Length; i++)
                Console.Write($"{array2[i]} ");

            Console.WriteLine();
        }
    }
}
```

</div>
</details>

### 이벤트 : 객체에 일어난 사건 알리기

<details>
<summary>예시 코드</summary>
<div markdown="1">
    
```c#
using System;

namespace EventTest
{
    delegate void EventHandler(string message);

    class MyNotifier
    {
        public event EventHandler SomethingHappend;
        public void DoSomething(int number)
        {
            int temp = number % 10;

            if (temp != 0 && temp % 3 == 0)
            {
                SomethingHappend(string.Format($"{number} : 짝"));
            }
        }
    }

    class MainApp
    {
        static public void MyHandler(string message)
        {
            Console.WriteLine(message);
        }
        static void Main(string[] args)
        {
            MyNotifier notifier = new MyNotifier();
            notifier.SomethingHappend += new EventHandler(MyHandler);

            for(int i=1;i<30;i++)
            {
                notifier.DoSomething(i);
            }
        }
    }
}
```

</div>
</details>

#### 연습문제

<details>
<summary>예시 코드</summary>
<div markdown="1">
    
```c#
using System;

namespace Ex13_2
{
    delegate void Mydelgate(int a);
    class Market
    {
        public event Mydelgate CustomEvent;

        public void BuySomething(int CustomerNo)
        {
            if (CustomerNo == 30)
                CustomEvent(CustomerNo);
        }
    }
    
    class MainApp
    {
        public static void eve(int a)
        {
            Console.WriteLine($"축하합니다! {a}번쨰 고객 이벤트에 당첨되셨습니다.");
        }
        static void Main(string[] args)
        {
            Market market = new Market();
            market.CustomEvent += new Mydelgate(eve);

            for (int customerNo = 0; customerNo < 100; customerNo += 10)
                market.BuySomething(customerNo);
        }
    }
}
```

</div>
</details>
