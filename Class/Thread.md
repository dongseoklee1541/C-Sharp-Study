## BackgorundWorker 클래스

BackgorundWorker 클래스는 별도의 쓰레드에게 어떤 일을 시키기 위해 사용하는 클래스, 백그라운드 쓰레드(혹은 워커 쓰레드)
라고 불리우는 별도의 쓰레드에서는 UI Thread와 별도로 어떤 작업을 수행하는데 사용된다.

```c#
//Thread객체 생성
BackgroundWorker worker = new BackgroundWorker();

// 이벤트 핸들러 지정
worker.DoWork += new DoWorkEventHandler(worker_DoWork);
worker.ProgressChanged += new ProgressChangedEventHandler(worker_ProgressChanged);
worker.RunWorkerCompleted += new RunWorkerCompletedEventHandler(worker_RunWorkerCompleted);

// 실행
worker.RunWorkerAsync();
```

* Dowork 이벤트 핸들러 : 실제 작업할 내용을 지정한다. 즉 RunWorkerAsync()가 호출될때(백그라운드 작업 실행 시작,
비동기적으로 실행 되는 작업)사용한다.

* ProgressChanged 이벤트 핸들러 : 진척 사항을 전달, ReportProgress(Int32)가 호출될때(백그라운드 작업의 완료율)
사용한다.

* RunWokerCompleted 이벤트 핸들러 : 이벤트를 통해 완료 후 실행될 작업을 지정. 즉 백그라운드 작업이 완료/취소/예외
를 발생시켰을 때 실행

```c#
public partial class Form1 : Form
{
   private BackgroundWorker worker;

   public Form1()
   {
      InitializeComponent();
      worker = new BackgroundWorker();
      worker.WorkerReportsProgress = true;
      worker.WorkerSupportsCancellation = true;
      worker.DoWork += new DoWorkEventHandler(worker_DoWork);         
      worker.ProgressChanged += new ProgressChangedEventHandler(worker_ProgressChanged);
      worker.RunWorkerCompleted += new RunWorkerCompletedEventHandler(worker_RunWorkerCompleted);
   }

   // Worker Thread가 실제 하는 일
   void worker_DoWork(object sender, DoWorkEventArgs e)
   {
      string srcDir = @"C:\Temp\_Src";
      string destDir = @"C:\Temp\_Dest";

      DirectoryInfo di = new DirectoryInfo(srcDir);
      FileInfo[] fileInfos = di.GetFiles();
      int totalFiles = fileInfos.Length;
      int counter = 0;
      int pct = 0;
      foreach (var fi in fileInfos)
      {
         string destFile = Path.Combine(destDir, fi.Name);
         File.Copy(fi.FullName, destFile);            
            
         pct = ((++counter * 100) / totalFiles);            
         worker.ReportProgress(pct);
      }         
   }

   // Progress 리포트 - UI Thread
   void worker_ProgressChanged(object sender, ProgressChangedEventArgs e)
   {
      this.progressBar1.Value = e.ProgressPercentage;            
   }

   // 작업 완료 - UI Thread
   void worker_RunWorkerCompleted(object sender, RunWorkerCompletedEventArgs e)
   {
      // 에러가 있는지 체크
      if (e.Error != null)
      {
         lblMsg.Text = e.Error.Message;
         MessageBox.Show(e.Error.Message, "Error");          
         return;
      }

      lblMsg.Text = "성공적으로 완료되었습니다";
   }      

   private void btnRun_Click(object sender, EventArgs e)
   {
      // 비동기(Async)로 실행 
      worker.RunWorkerAsync();
   }
}
```

출처 : http://www.csharpstudy.com/WinForms/WinForms-backgroundworker.aspx , 
https://docs.microsoft.com/ko-kr/dotnet/api/system.componentmodel.backgroundworker?view=netframework-4.8#methods

---

## 윈도우 멀티쓰레딩
멀티쓰레드를 사용하기 위해선 **Thread, BackgroundWorker, Wrapper** 클래스를 사용한다.
윈도우 멀티 쓰레딩에서 중요한 사항은 **UI 컨트롤들을 갱신하기 위해서는 항상 해당 UI  컨트롤을 생성한
UI 컨트롤을 생성한 UI Thread에서 갱신해야 한다**는 것이다.

## UI Thread
UI 컨트롤을 생성하고 이 컨트롤의 윈도우 핸들을 소유한 쓰레드를 UI Thread라 부르고, 이러한 UI 컨트롤들을
갖지 않는 쓰레드를 작업 쓰레드라 부른다. 일반적으로 UI 프로그램은 하나의 UI Thread(주로 메인쓰레드)를 가지며
여러개의 작업 쓰레드를 갖는다. 종종 필요한 경우 여러 개의 UI Thread를 갖게 하는 것도 가능하다.

### WinForm UI Thread 
WinForm의 UI 컨트롤들은 Control 클래스로부터 파생된 클래스들이며, Control 클래스는 UI 컨트롤이 
UI 쓰레드에서 돌고 있는지를 체크하는 InvokeRequired 속성을 갖고 있으며, 만약 작업쓰레드에서 실행되고 있는
경우 Control 클래스의 Invoke()나 BeginInvoke() 메소드를 사용하여 UI 쓰레드로 작업 요청을 보낸다.

```C#
using System;
using System.Windows.Forms;
using System.Threading;

namespace MultiThrdApp
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void btnQuery_Click(object sender, EventArgs e)
        {
            // 작업 쓰레드 시작
            Thread worker = new Thread(Run);
            worker.Start();
        }

        private void btnClear_Click(object sender, EventArgs e)
        {
            // UI 쓰레드에서 TextBox 갱신
            UpdateTextBox(string.Empty);
        }

        private void Run()
        { 
            // Long DB query
            Thread.Sleep(3000);
            string dbData = "Query Result";

            // 작업쓰레드에서 TextBox 갱신
            UpdateTextBox(dbData);
        }

        private void UpdateTextBox(string data)
        {
            // 호출한 쓰레드가 작업쓰레드인가?
            if (textBox1.InvokeRequired)
            {
                // 작업쓰레드인 경우, UI 쓰레드로 작업요청을 보냄
                textBox1.BeginInvoke(new Action(() => textBox1.Text = data));
            }
            else 
            {
                // UI 쓰레드인 경우
                textBox1.Text = data;
            }            
        }
    }
}
```


