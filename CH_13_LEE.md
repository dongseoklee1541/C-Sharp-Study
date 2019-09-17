### 대리자란?

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

### 대리자는 왜, 언제 사용 하나?

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

### Generic 버전
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
