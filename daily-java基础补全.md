

### **01.04-01.06，重温java基础理论**以及java基础补全：

1.idea常用操作
sout 即System.out.println
psvm 即public static void main()
alt+insert(鼠标要位于类内) ：可以快捷设置构造方法和setget
创建对象后可以在分号后 alt+回车 ，来快速补全左侧对象

2.重载：方法名相同，但是参数个数，参数类型可能不同

super用来在子类中调用父类的构造方法(放在第一行)或者普通方法super.xxx()
this是本类的构造或者，一般是当前对象

3.封装，
用private修饰，经过封装的话不用setget是无法调用的,比如对象.setName("wang");

4.重写（override）：
重写需要类与类有继承关系，比如A extends B，而且是针对非静态的方法

也就是说new对象时方法是来自右侧类）
字面意思，子类可以对父类的方法进行修改（不修改还想用就用super
可以有A a = new A（）；
也可以有B b = new A（）；//这个就是用A的方法实例化父类B的一个对象，也叫向上转型
不过方法不能是静态的，因为静态与类有关（如果方法为静态，只会表现为子类）

5.多态
比如A extends B
可以有A a = new A（）；
也可以有B b = new A（）；//父类的引用指向子类的类型
对象能执行哪些方法和左边类型有关

6.static
如果static修饰的属性，是可以直接 类名.属性名，来调用的.
static范围是类内使用的，所以非静态时必须用new对象，用对象来调用。

7.接口interface
接口内均为抽象，
类 xxx 实现（implements） 接口xx
接口可以多继承

java集合，hashmap



**注解：**更方便，现在框架除了mybatis外一般都使用注解引入而非xml。

后续笔记在对应框架笔记中

**反射：**

可以在运行时创建类的对象！获取类的内部信息还能直接操作对象的内部属性和方法

java是静态语言，利用反射是可以变为伪动态，同时增加了一些不安全性

Reflection(反射)

正常方式：引入包名-->实例化(new对象)--->取得实例化的对象

与正常方式不同：

反射模式：利用反射机制创建实例化对象-->

getClass方法（获得class类型对象）--->

获得完整的包名；



反射相关的API：

java.lang.Class;

java.lang.reflect.Field（代表类的成员变量）

java.lang.reflect.Method

java.lang.reflect.Constructor（类的构造器）

一个类只有一个Class对象，且包含其内部所有方法，属性；

如何获取Class类的实例：

1.Class clazz = Person.class；通过类的class属性获取

2.Class clazz = Person.getClass();调用某个已知类的实例的getClass方法

3.已知全类名或者路径，使用Class.forName方法

Class对象有很多类型：map，基本类型等等



#### **继承和组合：**

这两个都属于java复用；

继承不去多说，组合简单来说就是在A类中将B类的对象作为成员变量

比如

```
public class Proxy {
    private Host host;
}
```

Host是另一个类，

其实所谓的组合就是创建一个新类去调用已经创建并且调试好的类，那么这个新类就可以把它叫做是一个组合





java的键盘输入存入数组，可判断是否是数字
        

```java
import java.util.Scanner;

public class Check {
    public static void main(String[] args){
        Scanner s = new Scanner(System.in);
        //把pre和str在外面定义，如果在循环中定义会出错
       // String[] two = new String[6];
        //String pre;
        int[] str = new int[6];
        int count = 0;//用于退出循环
   while(true){
        System.out.println("请依次输入6个数");
        count = 0;//如果输入字符不是数字时，从if中出来，重新计数
        for(int i=0;i<6;i++){
           String pre = s.next();//接收字符串
            if (!(pre.matches("\\d+"))) {//非（pre全为数字）
                System.out.println("输入的" + pre + "不是数字，请重新输入");
                break;//这个退出的是for循环，退出后会再次执行while循环，重新输入数字
            }
            else {
                str[i] = Integer.parseInt(pre);//把数字型字符串转换为int型数字
            }
            count++;//每执行一次数据输入后，count才会+1
        }
        if(count==6) break;//这个退出的是while循环
    }
    for(int i=0;i<6;i++){//输出打印数字
        if(i==0){
            System.out.print(str[i]);
        }
        else{
            System.out.print(","+str[i]);
        }
    }
}
    }
```
9x9乘法表

```java
public class Nine_Time_Nine {

    public static void main(String[] args) {
        timedown();
        up();
    }

    public static void timedown() {
//9x9乘法表（下三角形）
        for(int i = 1;i < 10;i ++){
            for(int j = 1; j <= i;j ++){
                if(j == i){
                    System.out.println(j +" * " + i + "="+ (j * i) + " ");
                    //System.out.println();如果上一句p是rint而不是println要加上这句来换行
                }else{
                    System.out.print(j +" * " + i + "="+ (j * i) + " ");
                }
            }
        }


    }
//乘法表的倒三角
    public static void up(){
            for (int i = 1; i <= 9; i++) {
                for (int j = i; j <= 9; j++) {
                    System.out.print(i + " * " + j + " = " + i * j + " ");
                }
                System.out.println();
            }
        }
    }









```

