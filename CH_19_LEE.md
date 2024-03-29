```c#
using System;
using System.Threading;

namespace BasicThread
{
    class MainApp
    {
        static void DoSomething()
        {
            for(int i =0;i<5;i++)
            {
                Console.WriteLine($"DoSomething : {i}");
                Thread.Sleep(10);
            }
        }
        static void Main(string[] args)
        {
            Thread t1 = new Thread(new ThreadStart(DoSomething)); // 밑의 2줄을 한번에 작성한 코드
            // ThreadStart thstart = new ThreadStart(Dosomething);
            // Thread t1 = new Tthread(thstart);
            t1.Start();

            for (int i = 0; i < 5; i++)
            {
                Console.WriteLine($"Main : {i}");
                Thread.Sleep(10);
            }

            Console.WriteLine("Wating until thread stops...");
            t1.Join();

            Console.WriteLine("Finished");

        }   
    }
}
```


### Aborting

```c#
using System;
using System.Threading;

namespace AbortingThread
{
    class SideTask
    {
        int count;

        public SideTask(int count)
        {
            this.count = count;
        }

        public void KeepAlive()
        {
            try
            {
                while(count>0)
                {
                    Console.WriteLine($"{count--} left");
                    Thread.Sleep(10);
                }
                Console.WriteLine("Count : 0");
            }
            catch (ThreadAbortException e)
            {
                Console.WriteLine(e);
                Thread.ResetAbort();
            }
            finally
            {
                Console.WriteLine("Clearing resource...");
            }
        }
    }
    class MainApp
    {
        static void Main(string[] args)
        {
            SideTask task = new SideTask(100);
            Thread t1 = new Thread(new ThreadStart(task.KeepAlive));
            t1.IsBackground = false;

            Console.WriteLine("Starting thread...");
            t1.Start();

            Thread.Sleep(100);

            Console.WriteLine("Aborting thread...");
            t1.Abort();

            Console.WriteLine("Wating until thread stops...");
            t1.Join();

            Console.WriteLine("Finished");

        }   
    }
}
```

### Interrupt

```c#
using System;
using System.Security.Permissions;
using System.Threading;

namespace InterruptingThread
{
    class SideTask
    {
        int count;

        public SideTask(int count)
        {
            this.count = count;
        }
        public void KeepAlive()
        {
            try
            {
                Console.WriteLine("Running thread isn't gonna be interrupted");
                Thread.SpinWait(100000000); // SpinWait는 Sleep()과 유사하게 쓰래드를 대기시킴
                //그러나 Running 상태를 유지하게 만듬
                while(count>0)
                {
                    Console.WriteLine($"{count--} left");
                    Console.WriteLine("Entering into WaitJoinSleep State...");
                    Thread.Sleep(10); // 이때 interrupt가 작동된다. 만약 이 라인이 주석처리 된다면 ?? 0까지 쭉 진행되고 finally문이 실행된다.
                }
                Console.WriteLine("Count : 0");
            }
            catch(ThreadInterruptedException e)
            {
                Console.WriteLine(e);
            }
            finally
            {
                Console.WriteLine("Clearing resource...");
            }
        }
    }
    class MainApp
    {
        static void Main(string[] args)
        {
            SideTask task = new SideTask(100);
            Thread t1 = new Thread(new ThreadStart(task.KeepAlive));
            t1.IsBackground = false; //포어그라운드 설정. main이 종료되도 계속 이어짐

            Console.WriteLine("Starting thread...");
            t1.Start();

            Thread.Sleep(100);

            Console.WriteLine("Interrupting thread...");
            t1.Interrupt();

            Console.WriteLine("Wating until thread stops...");
            t1.Join();

            Console.WriteLine("Finished");
        }
    }
}
```


### 스래드 간의 동기화

#### lock 키워드

```c#
using System;
using System.Threading;

namespace Synchronize
{
    class Counter
    {
        const int LOOP_COUNT = 1000;

        readonly object thisLock;

        private int count;
        public int Count
        {
            get { return count; }
        }

        public Counter()
        {
            thisLock = new object();
            count = 0;
        }

        public void Increase()
        {
            int loopCount = LOOP_COUNT;
            while(loopCount-->0)
            {
                lock (thisLock)
                {
                    count++;
                }
                Console.Write($"{count} ");
                Thread.Sleep(1);
            }
        }
        public void Decrease()
        {
            int loopCount = LOOP_COUNT;
            while (loopCount-- > 0)
            {
                lock (thisLock)
                {
                    count--;
                }
                Console.Write($"{count} ");
                Thread.Sleep(1);
            }
        }
    }
    class MainApp
    {
        static void Main(string[] args)
        {
            Counter counter = new Counter();

            Thread incTherad = new Thread(
                new ThreadStart(counter.Increase));
            Thread decThread = new Thread(
                new ThreadStart(counter.Decrease));

            incTherad.Start();
            decThread.Start();

            incTherad.Join();
            decThread.Join();

            Console.WriteLine(counter.Count);
        }
    }
}
```

#### Wait,Pulse

```c#
using System;
using System.Threading;

namespace WaitPulse
{
    class Counter
    {
        const int LOOP_COUNT = 1000;

        readonly object thisLock;
        bool lockedCount = false;

        private int count;
        public int Count
        {
            get { return count; }
        }

        public Counter()
        {
            thisLock = new object();
            count = 0;
        }

        public void Increase()
        {
            int loopCount = LOOP_COUNT;
            while (loopCount-- > 0)
            {
                lock (thisLock)
                {
                    while (count > 0 || lockedCount == true)
                        Monitor.Wait(thisLock);
                    Console.Write(Thread.CurrentThread.GetHashCode());

                    lockedCount = true;
                    count++;
                    lockedCount = false;

                    Monitor.Pulse(thisLock);
                }
            }
        }

        public void Decrease()
        {
            int loopCount = LOOP_COUNT;
            while (loopCount-- > 0)
            {
                lock (thisLock)
                {
                    while (count < 0 || lockedCount == true)
                        Monitor.Wait(thisLock);
                    Console.Write(Thread.CurrentThread.GetHashCode());


                    lockedCount = true;
                    count--;
                    lockedCount = false;

                    Monitor.Pulse(thisLock);
                }
            }
        }
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            Counter counter = new Counter();

            Thread incThread = new Thread(
                new ThreadStart(counter.Increase));
            Thread decThread = new Thread(
                new ThreadStart(counter.Decrease));

            incThread.Start();
            decThread.Start();

            incThread.Join();
            decThread.Join();

            Console.WriteLine(counter.Count);
        }
    }
}
```

### Task 동기/비동기 실행

```c#
using System;
using System.IO;
using System.Threading;
using System.Threading.Tasks;

namespace UsingTask
{
    class MainApp
    {
        static void Main(string[] args)
        {
            string srcFile = args[0];

            Action<object> FileCopyAction = (object state) =>
            {
                String[] paths = (string[])state;
                File.Copy(paths[0], paths[1]);

                Console.WriteLine("TaskID:{0}, ThreadID:{1}, {2} was copied to {3}",
                    Task.CurrentId, Thread.CurrentThread.ManagedThreadId, paths[0], paths[1]);
            };

            Task t1 = new Task(FileCopyAction, new string[] { srcFile, srcFile + ".copy1" });

            Task t2 = Task.Run(() =>
            {
                FileCopyAction(new string[] { srcFile, srcFile + ".copy2" });
            });

            t1.Start();

            Task t3 = new Task(
                FileCopyAction,
                new string[] { srcFile, srcFile + ".copy3" });

            t3.RunSynchronously(); // 동기 실행을 위한 메소드

            t1.Wait();
            t2.Wait();
            t3.Wait();
        }
    }
}
```

#### Task<"TResult">

```c#
using System;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace TaskResult
{
    class MainApp
    {
        static bool IsPrime(long number)
        {
            if (number < 2)
                return false;

            if (number % 2 == 0 && number != 2)
                return false;

            for (long i = 2; i<number;i++)
            {
                if (number % i == 0)
                    return false;
            }
            return true;
        }
        static void Main(string[] args)
        {
            long from = Convert.ToInt64(args[0]);
            long to = Convert.ToInt64(args[1]);
            int taskCount = Convert.ToInt32(args[2]);

            Func<object, List<long>> FindPrimeFunc =
                (objRange) =>
                {
                    long[] range = (long[])objRange;
                    List<long> found = new List<long>();

                    for (long i = range[0]; i < range[1]; i++)
                    {
                        if (IsPrime(i))
                            found.Add(i);
                    }
                    return found;
                };

            Task<List<long>>[] tasks = new Task<List<long>>[taskCount];
            long currentFrom = from;
            long currentTo = to / tasks.Length;
            for(int i =0;i<tasks.Length;i++)
            {
                Console.WriteLine("Task[{0}] : {1} ~ {2}", i, currentFrom, currentTo);

                tasks[i] = new Task<List<long>>(FindPrimeFunc,
                    new long[] { currentFrom, currentTo });
                currentFrom = currentTo + 1;

                if (i == tasks.Length - 2)
                    currentTo = to;
                else
                    currentTo = currentTo + (to / tasks.Length);
            }

            Console.WriteLine("Please press enter to start...");
            Console.ReadLine();
            Console.WriteLine("Started...");

            DateTime startTime = DateTime.Now;

            foreach (Task<List<long>> task in tasks)
                task.Start();

            List<long> total = new List<long>();

            foreach(Task<List<long>> task in tasks)
            {
                task.Wait();
                total.AddRange(task.Result.ToArray());
            }
            DateTime endTime = DateTime.Now;

            TimeSpan ellapsed = endTime - startTime;

            Console.WriteLine("Prime number count between {0} and {1} : {2}",
                from, to, total.Count);
            Console.WriteLine("Ellapsed time : {0}", ellapsed);
        }
    }
}
```

### Parallel, 손쉬운 병렬처리
Parallel.For() 메소드는 메소드를 병렬로 호출하면서 몇 개의 스레드를 사용할지는 Parallel 클래스가 내부적으로 판단하여 최적화 한다. 즉 위의 코드에서 Task의 갯수와 범위를 지정해주는 코드는 필요가 없다.

```c#
using System;
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;

namespace ParallelLoop
{
    class MainApp
    {
        static bool IsPrime(long number)
        {
            if (number < 2)
                return false;

            if (number % 2 == 0 && number != 2)
                return false;

            for (long i = 2; i < number; i++)
            {
                if (number % i == 0)
                    return false;
            }
            return true;
        }
        static void Main(string[] args)
        {
            long from = Convert.ToInt64(args[0]);
            long to = Convert.ToInt64(args[1]);
           

            Console.WriteLine("Please press enter to start...");
            Console.ReadLine();
            Console.WriteLine("Started...");

            DateTime startTime = DateTime.Now;
            List<long> total = new List<long>();

            Parallel.For(from, to, (long i) =>
              {
                  if (IsPrime(i))
                      total.Add(i);
              });
            
            DateTime endTime = DateTime.Now;

            TimeSpan ellapsed = endTime - startTime;

            Console.WriteLine("Prime number count between {0} and {1} : {2}",
                from, to, total.Count);
            Console.WriteLine("Ellapsed time : {0}", ellapsed);
        }
    }
}
```
위와 같이 단순히 범위를 지정하면 Parallel이 알아서 스레드의 개수를 지정하여 병렬로 실행한다.

### Async

```c#
using System;
using System.Threading.Tasks;

namespace Async
{
    class MainApp
    {
        async static private void MyMethodAsync(int count) // async로 들어와서 
        {
            Console.WriteLine("C");
            Console.WriteLine("D");

            await Task.Run(async () => // await에서 실행시킨체 호출자로 돌아간다.
            {
                for (int i = 0; i <= count; i++)
                {
                    Console.WriteLine($"{i}/{count} ...");
                    await Task.Delay(100); // Task.Delay()는 Thread.Sleep()의 비동기 버전
                }
            });

            Console.WriteLine("G");
            Console.WriteLine("H");
        }
        static void Caller()
        {
            Console.WriteLine("A");
            Console.WriteLine("B");

            MyMethodAsync(3);

            Console.WriteLine("E");
            Console.WriteLine("F");
        }
            static void Main(string[] args)
        {
            Caller();

            Console.ReadLine();
        }
    }
}
```


```c#
using System;
using System.IO;
using System.Threading.Tasks;

namespace AsyncFileIO
{
    class MainApp
    {
        //파일 복사 후 복사한 파일 용량 반환
        static async Task<long> CopyAsync(string FromPath, string ToPath)
        {
            using (
                var fromStream = new FileStream(FromPath, FileMode.Open)) // 파일 열어
            {
                long totalCopied = 0;

                using (
                    var toStream = new FileStream(ToPath, FileMode.Create)) // 파일 복사
                {
                    byte[] buffer = new byte[1024];
                    int nRead = 0;
                    while ((nRead =
                        await fromStream.ReadAsync(buffer, 0, buffer.Length)) != 0)
                    {
                        await toStream.WriteAsync(buffer, 0, nRead);
                        totalCopied += nRead;
                    }
                }
                return totalCopied;
            }
        }
        static async void DoCopy(string FromPath,string ToPath)
        {
            long totalCpoied = await CopyAsync(FromPath, ToPath);
            Console.WriteLine($"Copied Total {totalCpoied} Bytes.");
        }
            static void Main(string[] args)
        {
            if(args.Length <2)
            {
                Console.WriteLine("Usage : AsyncFileIO <Source> <Destination>");
                return;
            }
            DoCopy(args[0], args[1]);
            Console.ReadLine();
        }
    }
}
```
