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

```c#
using System;
using System.Windows.Forms;
using System.Drawing;

namespace FormBackground
{
    class MainApp : Form
    {
        Random rand;
        public MainApp()
        {
            rand = new Random();

            this.MouseWheel += new MouseEventHandler(MainApp_MouseWheel);
            this.MouseDown += new MouseEventHandler(MainApp_MouseDown);
        }

        void MainApp_MouseDown(object sender,MouseEventArgs e)
        {
            if (e.Button == MouseButtons.Left)
            {
                Color oldColor = this.BackColor;
                this.BackColor = Color.FromArgb(rand.Next(0, 255),
                    rand.Next(0, 255), rand.Next(0, 255));
            }
            else if (e.Button == MouseButtons.Right)
            {
                if(this.BackgroundImage !=null)
                {
                    this.BackgroundImage = null;
                    return;
                }
                string file = "sample.png";
                if (System.IO.File.Exists(file) == false)
                    MessageBox.Show("이미지 파일이 없습니다.");
                else
                    this.BackgroundImage = Image.FromFile(file);
            }
        }

        void MainApp_MouseWheel(object sender, MouseEventArgs e)
        {
            this.Opacity = this.Opacity + (e.Delta > 0 ? 0.1 : -0.1);
            Console.WriteLine($"Opacity : {this.Opacity}");
        }
        static void Main(string[] args)
        {
            Application.Run(new MainApp());
        }
    }
}
```

```c#
using System;
using System.Windows.Forms;

namespace FormAndControl
{
    class MainApp : Form
    {
        static void Main(string[] args)
        {
            Button button = new Button();

            button.Text = "Click Me!";
            button.Left = 100;
            button.Top = 50;

            button.Click +=
                (object sender, EventArgs e) =>
                {
                    MessageBox.Show("딸깍!");
                };
            MainApp form = new MainApp();
            form.Text = "Form & Control";
            form.Height = 150;

            form.Controls.Add(button);
            Application.Run(form);
        }
    }
}
```

