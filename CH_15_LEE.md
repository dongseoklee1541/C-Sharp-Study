
### from
```c#

using System;
using System.Linq;

namespace From
{ 
    class MainApp
    {
        static void Main(string[] args)
        {
            int[] numbers = { 9, 2, 6, 4, 5, 3, 7, 8, 1, 10 };

            var result = from n in numbers
                         where n % 2 == 0 // 짝수니?
                         orderby n
                         select n;

            foreach (int n in result)
                Console.WriteLine($"짝수 : {n}");
        }
    }
}
```

### where
필터 역할을 하는 연산자

### orderby
데이터의 정렬을 수행하는 연산자

### select
최종 결과를 추출하는 쿼리식의 마침표 같은 존재

```c#
using System;
using System.Collections.Generic;
using System.Linq;

namespace SimpleLinq
{ 
    class Profile
    {
        public string Name { get; set; }
        public int Height { get; set; }
    }
    class MainApp
    {
        static void Main(string[] args)
        {
            Profile[] arrProfile =
            {
                new Profile(){Name="정우성",Height=186},
                new Profile(){Name="김태희",Height=158},
                new Profile(){Name="고현정",Height=172},
                new Profile(){Name="이동석",Height=186},
                new Profile(){Name="문병인",Height=187}
            };

            var profiles = from profile in arrProfile
                           where profile.Height < 175
                           orderby profile.Height
                           select new
                           {
                               Name = profile.Name,
                               InchHeight = profile.Height * 0.393
                           };
            foreach( var profile in profiles)
                Console.WriteLine($"{profile.Name},{profile.InchHeight}");
        }
    }
}
```

### 여러 개의 데이터 원본에 질의하기

```c#
using System;
using System.Linq;

namespace FromFrom
{ 
    class Class
    {
        public string Name { get; set; }
        public int[] Score { get; set; }
    }
    class MainApp
    {
        static void Main(string[] args)
        {
            Class[] arrClass =
            {
                new Class(){Name = "연두반", Score = new int[]{99,80,70,24} },
                new Class(){Name = "분홍반", Score = new int[]{60,45,87,72} },
                new Class(){Name = "레드반", Score = new int[]{92,30,85,94} },
                new Class(){Name = "빵반", Score = new int[]{90,88,0,17} }
            };

            var classes = from c in arrClass
                          from s in c.Score
                          where s < 60
                          orderby s
                          select new { c.Name, Lowest = s };

            foreach (var c in classes)
                Console.WriteLine($"낙제 : {c.Name} ({c.Lowest})");
            

        }
    }
}
```

### group by

```c#
using System;
using System.Linq;

namespace GroupBy
{
    class Profile
    {
        public string Name { get; set; }
        public int Height { get; set; }
    }
    class MainApp
    {
        static void Main(string[] args)
        {
            Profile[] arrProfile =
            {
                new Profile(){Name="정우성",Height=186},
                new Profile(){Name="김태희",Height=158},
                new Profile(){Name="고현정",Height=172},
                new Profile(){Name="이동석",Height=186},
                new Profile(){Name="문병인",Height=187}
            };

            var listProfile = from profile in arrProfile
                              orderby profile.Height
                              group profile by profile.Height < 175 into g
                              select new
                              {
                                  GroupKey = g.Key,
                                  Profiles = g};

            foreach (var Group in listProfile)
            {
                Console.WriteLine($"- 175cm 미만? : {Group.GroupKey}");

                foreach (var profile in Group.Profiles)
                {
                    Console.WriteLine($"    {profile.Name}, {profile.Height}");
                }
            }
        }
    }
}
```

### join
join은 두 데이터의 원본을 특정 필드의 값을 비교하여 일치하는 데이터끼리 연결을 수행하는 연산이다. 

```c#
using System;
using System.Linq;

namespace Join
{
    class Profile
    {
        public string Name { get; set; }
        public int Height { get; set; }
    }
    class Product
    {
        public string Title { get; set; }
        public string Star { get; set; }
    }
    class MainApp
    {
        static void Main(string[] args)
        {
            Profile[] arrProfile =
            {
                new Profile(){Name="정우성",Height=186},
                new Profile(){Name="김태희",Height=158},
                new Profile(){Name="고현정",Height=172},
                new Profile(){Name="이동석",Height=186},
                new Profile(){Name="문병인",Height=187}
            };

            Product[] arrProduct =
            {
                new Product(){Title="비트", Star = "정우성"},
                new Product(){Title="CF 다수", Star = "김태희"},
                new Product(){Title="아이리스", Star = "김태희"},
                new Product(){Title="모래시계", Star = "고현정"},
                new Product(){Title="뷰노", Star = "문병인"},
            };

            var listProfile =
                from profile in arrProfile
                join product in arrProduct on profile.Name equals product.Star
                select new
                {
                    Name = profile.Name,
                    Work = product.Title,
                    Height = profile.Height
                };

            Console.WriteLine("--- 내부 조인 결과 ---");

            foreach (var profile in listProfile)
            {
                Console.WriteLine("이름 :{0}, 작품:{1}, 키:{2}cm",
                    profile.Name, profile.Work, profile.Height);
            }

            listProfile =
                from profile in arrProfile
                join product in arrProduct on profile.Name equals product.Star
                into ps
                from product in 
                    ps.DefaultIfEmpty(new Product() { Title = "그런거없다" })// 외부 조인
                select new
                {
                    Name = profile.Name,
                    Work = product.Title,
                    Height = profile.Height
                };
            Console.WriteLine();
            Console.WriteLine("--- 외부 조인 결과 ---");
            foreach(var profile in listProfile)
            {
                Console.WriteLine("이름:{0}, 작품:{1}, 키:{2}cm",
                    profile.Name, profile.Work, profile.Height);
            }
        }
    }
}
```

### LINQ 표준연산자

위의 SimpleLinq 예제에서 LINQ 쿼리식만 메소드 호출 코드로 바꾼 코드이다.

```c#
            var profiles = arrProfile.
                Where(profile => profile.Height < 175).
                OrderBy(profile => profile.Height).
                Select(profile =>
                new
                {
                    Name = profile.Name,
                    InchHeight = profile.Height
                });
```

연예인의 프로필 중에서 키를 175 이상과 175 미만으로 그룹을 나누고, 각 그룹에서 키가 가장 큰 연에인, 가장 작은 연에인의 수를 뽑아보자.
```c#
using System;
using System.Collections.Generic;
using System.Linq;

namespace SimpleLinq
{
    class Profile
    {
        public string Name { get; set; }
        public int Height { get; set; }
    }
    class MainApp
    {
        static void Main(string[] args)
        {
            Profile[] arrProfile =
            {
                new Profile(){Name="정우성",Height=186},
                new Profile(){Name="김태희",Height=158},
                new Profile(){Name="고현정",Height=172},
                new Profile(){Name="이동석",Height=186},
                new Profile(){Name="문병인",Height=187},
                new Profile(){Name="서강준",Height=177}
            };

            var heightStat = from profile in arrProfile
                             group profile by profile.Height < 175 into g
                             select new
                             {
                                 Group = g.Key == true ? "175미만" : "175이상",
                                 Count = g.Count(),
                                 Max = g.Max(profile => profile.Height),
                                 Min = g.Min(profile => profile.Height),
                                 Average = g.Average(profile => profile.Height)
                             };
            foreach(var stat in heightStat)
            {
                Console.Write("{0} - Count:{1}, Max:{2},",
                    stat.Group, stat.Count, stat.Max);
                Console.WriteLine("Min:{0}, Average:{1}",
                    stat.Min, stat.Average);
            }
        }
    }
}
```
