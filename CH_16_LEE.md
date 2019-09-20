## 리플렉션
------

#### Object.GetType() 메소드 & Type 클래스

```c#
using System;
using System.Collections.Generic;
using System.Text;
using System.Reflection;

namespace GetType
{
    class MainApp
    {
        static void PrintInterfaces(Type type)
        {
            Console.WriteLine("------ Interfaces ------");

            Type[] interfaces = type.GetInterfaces();
            foreach (Type i in interfaces)
                Console.WriteLine("Name:{0}", i.Name);
        }
        static void PrintFields(Type type)
        {
            Console.WriteLine("--- Fields ---");
            FieldInfo[] fields = type.GetFields(
                BindingFlags.NonPublic |
                BindingFlags.Public |
                BindingFlags.Static |
                BindingFlags.Instance);

            foreach( FieldInfo field in fields)
            {
                String accessLevel = "protected";
                if (field.IsPublic) accessLevel = "public";
                else if (field.IsPrivate) accessLevel = "private";

                Console.WriteLine("Access:{0}, Type:{1}, Name:{2}",
                    accessLevel, field.FieldType.Name, field.Name);
            }
            Console.WriteLine();
        }

        static void PrintMethods(Type type)
        {
            Console.WriteLine("--- Methods ---");

            MethodInfo[] methods = type.GetMethods();
            foreach (MethodInfo method in methods)
            {
                Console.Write("Type:{0}, Name:{1}, Parameter:", method.ReturnType.Name,
                    method.Name);
                ParameterInfo[] args = method.GetParameters();
                for(int i =0;i<args.Length;i++)
                {
                    Console.Write("{0}", args[i].ParameterType.Name);
                    if (i < args.Length - 1)
                        Console.Write(", ");
                }
                Console.WriteLine();
            }
            Console.WriteLine();
        }

        static void PrintProperties(Type type)
        {
            Console.WriteLine("--- Properties ---");
            PropertyInfo[] properties = type.GetProperties();
            foreach (PropertyInfo property in properties)
                Console.WriteLine("Type:{0}, Name:{1}",
                    property.PropertyType.Name, property.Name);

            Console.WriteLine();
        }

        static void Main(string[] args)
        {
            int a = 0;
            Type type = a.GetType();

            PrintInterfaces(type);
            PrintFields(type);
            PrintProperties(type);
            PrintMethods(type);
        }
    }
}
```

```c#
using System;
using System.Reflection;

namespace DynamicInstance
{
    class Profile
    {
        private string name;
        private string phone;
        public Profile()
        {
            name = ""; phone = "";
        }
        public Profile(string name, string phone)
        {
            this.name = name;
            this.phone = phone;
        }
        public void Print()
        {
            Console.WriteLine($"{name}, {phone}");
        }
        public string Name
        {
            get { return name; }
            set { name = value; }
        }

        public string Phone
        {
            get { return phone; }
            set { phone = value; }
        }
    }
    class MainApp
    {
        static void Main(string[] args)
        {
            Type type = Type.GetType("DynamicInstance.Profiles");
            MethodInfo methodInfo = type.GetMethod("Print");
            PropertyInfo nameProperty = type.GetProperty("Name");
            PropertyInfo phoneProperty = type.GetProperty("Phone");

            object profile = Activator.CreateInstance(type, "박상현", "512-1234");
            methodInfo.Invoke(profile, null);
            profile = Activator.CreateInstance(type);
            nameProperty.SetValue(profile, "박찬호", null);
            phoneProperty.SetValue(profile, "997-5511", null);

            Console.WriteLine("{0}, {1}", nameProperty.GetValue(profile, null),
                phoneProperty.GetValue(profile, null));
        }
    }
}
```
