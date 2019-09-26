```c#
using System;
using System.Windows.Forms;

namespace UsingApplication
{
    class MainApp : Form
    {
        static void Main(string[] args)
        {
            MainApp form = new MainApp();

            form.Click += new EventHandler(
                (sender, EventArgs) =>
                {
                    Console.WriteLine("Closing Window...");
                    Application.Exit();
                });

            Console.WriteLine("Starting Window Application...");
            Application.Run(form);

            Console.WriteLine("Exiting Window Application...");
        }
    }
}

```

```c#
using System;
using System.Windows.Forms;

namespace MessageFilter
{
    class MessageFilter : IMessageFilter
    {
        public bool PreFilterMessage(ref Message m)
        {
            if (m.Msg == 0x0F || m.Msg == 0xA0 || m.Msg == 0x200 || m.Msg == 0x113)
                return false;
            Console.WriteLine($"{m.ToString()} : {m.Msg}");

            if (m.Msg == 0x201)
                Application.Exit();

            return true;
        }
    }
    class MainApp : Form
    { 
        static void Main(string[] args)
        {
            Application.AddMessageFilter(new MessageFilter());
            Application.Run(new MainApp());

            
        }
    }
}
```

```c#
using System;
using System.Windows.Forms;

namespace FormEvent
{
    class MainApp : Form
    { 
        public void MyMouseHandler(object sender, MouseEventArgs e)
        {
            Console.WriteLine($"Sender : {((Form)sender).Text}");
            Console.WriteLine($"X:{e.X}, Y:{e.Y}");
            Console.WriteLine($"Button:{e.Button},Clicks:{e.Clicks}");
            Console.WriteLine();
        }

        public MainApp(string title)
        {
            this.Text = title;
            this.MouseDown +=
                new MouseEventHandler(MyMouseHandler);
            this.MouseWheel +=
                new MouseEventHandler(MyMouseHandler);
        }
        static void Main(string[] args)
        {
            Application.Run(new MainApp("Mouse Event Test"));
        }
    }
}
```

```c#
using System;
using System.Windows.Forms;

namespace FormSize
{
    class MainApp : Form
    { 
        static void Main(string[] args)
        {
            MainApp form = new MainApp();
            form.Width = 300;
            form.Height = 200;

            form.MouseDown += new MouseEventHandler(form_MouseDown);

            Application.Run(form);
        }

        static void form_MouseDown(object sender, MouseEventArgs e)
        {
            Form form = (Form)sender;
            int oldWidth = form.Width;
            int oldHeight = form.Height;

            if(e.Button == MouseButtons.Left)
            {
                if(oldWidth < oldHeight)
                {
                    form.Width = oldHeight;
                    form.Height = oldWidth;
                }
            }
            else if(e.Button == MouseButtons.Right)
            {
                if(oldHeight < oldWidth)
                {
                    form.Width = oldHeight;
                    form.Height = oldWidth;
                }
            }
            Console.WriteLine("윈도우의 크기가 변경 되었습니다.");
            Console.WriteLine($"Width: {form.Width}, Height: {form.Height}");
        }
    }
}
```
