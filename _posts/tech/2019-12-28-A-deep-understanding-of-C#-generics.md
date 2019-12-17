---
layout: post
title: C#泛型的深入理解
category: 技术
tags: [C#]
keywords: C#,泛型
description:
---

# 为什么要有泛型？请大家思考一个问题：由你来实现一个最简单的冒泡排序算法，如果没有使用泛型的经验，可能会毫不犹豫的写出以下代码：

```
public class SortHelper  
    {  
        //参数为int数组的冒泡排序  
        public void BubbleSort(int[] array)  
        {  
            int length = array.Length;  
  
            for (int i = 0; i <= length - 2; i++)  
            {  
                for (int j = length - 1; j >= 1; j--)  
                {  
                    //对两个元素进行交换  
                    if (array[j] < array[j - 1])  
                    {  
                        int temp = array[j];  
                        array[j] = array[j - 1];  
                        array[j - 1] = temp;  
                    }  
                }  
            }  
        }  
    } 
```

现在通过对这个程序进行一个简单的测试：
  
```
static void Main(string[] args)  
  {  
      #region  没有泛型前的演示  
      SortHelper sorter = new SortHelper();  
  
      int[] arrayInt = { 8, 1, 4, 7, 3 };  
  
      //对int数组排序  
      sorter.BubbleSort(arrayInt);  
  
      foreach (int i in arrayInt)  
      {  
          Console.Write("{0} ", i);  
      }  
  
      Console.ReadLine();  
      #endregion  
  }
 ```
 
我们发现它运行良好，欣喜的认为这便是最好的解决方案了。直到不久后，需要对一个byte类型的数组进行排序，而上面的排序算法只能接受一个int类型的数组。C#是一个强类型的语言，
无法在一个接受int数组类型的地方传入一个byte数组。不过没关系，现在看来最快的解决方法是把代码复制一遍，然后将方法的签名改一下：
  
```
public class SortHelper  
   {  
       //参数为byte数组的冒泡排序  
       public void BubbleSort(byte[] array)  
       {  
           int length = array.Length;  
  
           for (int i = 0; i <= length - 2; i++)  
           {  
               for (int j = length - 1; j >= 1; j--)  
               {  
                   //对两个元素进行交换  
                   if (array[j] < array[j - 1])  
                   {  
                       byte temp = array[j];  
                       array[j] = array[j - 1];  
                       array[j - 1] = temp;  
                   }  
               }  
           }  
       }  
   }
```

好了，再一次解决问题。可通过认证观察我们发现，这两个方法除了传入的参数类型不同外，方法的实现很相似，是可以进行进一步抽象的，于是我们思考，为什么在定义参数的时候不用一个占位符T代替呢？
T是Type的缩写，可以代表任何类型，这样就可以屏蔽两个方法签名的差异：   

```  
public class SortHelper<T>  
 {  
     //参数为T的冒泡排序  
     public void BubbleSort(T[] array)  
     {  
         int length = array.Length;  
  
         for (int i = 0; i <= length - 2; i++)  
         {  
             for (int j = length - 1; j >= 1; j--)  
             {  
                 //对两个元素进行交换  
                 if (array[j] < array[j - 1])  
                 {  
                     T temp = array[j];  
                     array[j] = array[j - 1];  
                     array[j - 1] = temp;  
                 }  
             }  
         }  
     }
```
	 
通过代码我们可以发现，使用泛型极大的减少了重复代码，使代码更加清爽。泛型类就类似于一个模板，可以再需要时为这个模板传入任何需要的类型。现在更专业些，为占位符T起一个正式的名称，在.Net中叫做“类型参数”。
  
# 类型参数约束
实际上，如果运行上面的代码就会发现，它连编译都通不过去，为什么呢？考虑这样一个问题：假设我们自定义一个类型，名字叫做Book，它包含两个字段：一个是int类型的Price代表书的价格；一个是string类型的Title，代表书的标题：

```
public class Book   
    {  
        //价格字段  
        private int price;  
        //标题字段  
        private string title;  
  
        //构造函数  
        public Book() { }  
  
        public Book(int price, string title)  
        {  
            this.price = price;  
            this.title = title;  
        }  
  
        //价格属性  
        public int Price  
        {  
            get { return this.price; }  
        }  
  
        //标题属性  
        public string Titie  
        {  
            get { return this.title; }  
        }  
    } 
```

现在创建一个Book类型的数组，然后使用上面定义的泛型类对它进行排序，代码应该像下面这样：

```
Book[] bookArray = new Book[2];  
  
Book book1 = new Book(30, "HTML5解析");  
Book book2 = new Book(21, "JavaScript实战");  
  
bookArray[0] = book1;  
bookArray[1] = book2;  
  
SortHelper<Book> sorterGeneric = new SortHelper<Book>();  
sorterGeneric.BubbleSort(bookArray);  
  
foreach (Book b in bookArray)  
{  
    Console.WriteLine("Price:{0}", b.Price);  
    Console.WriteLine("Title:{0}", b.Titie);  
}
```

这时问题来了，既然是排序，就免不了比较大小，那么现在请问：book1和book2谁比较大？张三可以说book1大，因为它的Price是30,；而李四可以说book2大，因为它的Title是“J”开头的，比book1的“H”靠后。说了半天，问题在于不确定按什么规则排序。
既然不知道，那我们就给Book定义一种排序规则（按价格排序），我们声明一个用于比较的接口：
  
```
public interface IComparable  
   {  
       int CompareTo(object obj);  
   }  
  让Book类型实现这个接口：
[csharp] view plain copy
public class Book : IComparable  
    {  
        //CODE：上面的实现略  
        public int CompareTo(object obj)  
        {  
            Book book2 = (Book)obj;  
            return this.Price.CompareTo(book2.Price);  
        }  
    }
```

既然我们现在已经让Book类实现了IComparable接口，那么泛型类应该可以工作了吧？不行的，还要记得，泛型类是一个模板类，它对在执行时传递的类型参数是一无所知的，也不会做任何的猜测，所以需要我们告诉泛型类SortHelper<T>，它所接受的T类型参数必须能够进行比较，也就是说必须实现IComparable接口，我们把对T进行约束这种行为称：泛型参数约束。
为了要求类型参数T必须实现IComparable接口，需要像下面这样重新定义：

```
public class SortHelper<T> where T : IComparable  
    {  
        //参数为T的冒泡排序  
        public void BubbleSort(T[] array)  
        {  
            int length = array.Length;  
  
            for (int i = 0; i <= length - 2; i++)  
            {  
                for (int j = length - 1; j >= 1; j--)  
                {  
                    //对两个元素进行交换  
                    if (array[j].CompareTo(array[j - 1]) < 0)  
                    {  
                        T temp = array[j];  
                        array[j] = array[j - 1];  
                        array[j - 1] = temp;  
                    }  
                }  
            }  
        }  
    }
```

上面的定义说明了类型参数T必须实现IComparable接口，否则将无法通过编译。因为现在T已经实现了IComparable接口，而数组array中的成员是T的实例，所以当在array[i]后面点击小数点“.”时，VS可以智能提醒出T是IComparable的成员，也就是CompareTo()方法。

原文地址：http://blog.csdn.net/quwenzhe/article/details/47105603