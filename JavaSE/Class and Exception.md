## 一.类

#### 1.内部类

``内部类的访问权限有4种``

​	1). private

​	2). public

​	3). protected

​	4). default



- 自定义内部类:

   ````java
   /*外部类*/
   public class Outer {
       private String username = "外面";//外部类的成员变量
       public void addOuter() {}//外部类的方法
       /*内部类*/
       class InnerClass {
           private String username = "里面";//内部类的私有成员变量
           public void addInner() {//内部类中的方法
               System.out.println("外部名字:---->" + Outer.this.username);//必须加this
               System.out.println("内部名字:---->" + username);//可以加可以不加this
               System.out.println("内部类..");
           }
       }
   }
   
   /*调用测试*/
   public class man {
       public static void main(String[] args) {
           Outer o = new Outer();//创建外部类对象
           Outer.InnerClass innerClass = o.new InnerClass();//创建内部类对象
   	}
   }
   ````




- API实例:

    ````java
    /**	该类Itr是ArrayList的内部类,Itr被称为ArrayList的成员内部类 **/
       	private class Itr implements Iterator<E> {
       	    int cursor;       // index of next element to return
       	    int lastRet = -1; // index of last element returned; -1 if no such
       	    int expectedModCount = modCount;
       	    Itr() {}
    ````


#### 2.静态内部类

``在成员内部类前面加上static,称为静态内部类``

- API实例:

   ````java
    	/*该静态类位于Integer类中,属于Integer类的静态内部类*/
   	private static class IntegerCache {
           static final int low = -128;
           static final int high;
           static final Integer cache[];
   ````



- 自定义静态内部类:

   ````java
   public class Outer {
       public static int age = 10;//静态变量
       private String name = "jsd001";
       static class Inner {//static的内部类中可以有非static方法
           public void show() {
               /*System.out.println("name:---->" + name);*///静态的方法不能调用动态的成员
               System.out.println("age:---->" + age);
           }
       }
   
       /*内部类中的方法如果是static,则内部类也必须要static修饰*/
       static class Inner2{//含有static方法的内部类,内部类必须是static的
           static void add(){
           }
       }
   }
   ````

   ``注意:``

   - 静态的内部类中可以含有非静态的方法
   - 含有static方法的内部类,内部类必须是static的
   - 访问静态内部类的静态方法是:外部类.内部类.方法

#### 3.方法中的内部类

````Java
/*定义一个方法中的内部类*/
public class Outer {
    public void show(int number) {
        //内部类被定义在了方法中,不能有访问修饰符
        //Inner是show方法的局部变量
        class Inner {
            //该类中仍然可以定义方法
            public void add() {
                System.out.println("number---->" + number);//jdk1.8之前该处会显示错误
            }
        }
        Inner inner = new Inner();//创建Inner类对象
        inner.add();
    }
}

/*测试类*/
public class test002 {
    public static void main(String[] args) {
        Outer o = new Outer();
        o.show(10);//成功调用方法中方的内部类中的方法
    }
}
````

- 注意:

   - 1). 在 JDK1.8之前,方法中的内部类在访问 [方法的参数] 和 [局部变量] 的时候要求变量必须是final类型

      ````java
       public void show(final int number) {.....}
      ````



#### 4.匿名内部类

``俗称:没有名称的内部类``

- 要求:
   - 必须是存在继承或者实现的关系(必须继承一个父类或者实现一个接口)
   - 如果该类只是用一次,可以考虑使用匿名内部类
- 自定义实例:

````java
/*普通方法*/
abstract class Person {
    public abstract void eat();
}
 
class Child extends Person {
    public void eat() {
        System.out.println("eat something");
    }
}
 
public class Demo {
    public static void main(String[] args) {
        Person p = new Child();
        p.eat();
    }
}

/*使用匿名内部类方法*/
public class test003 {
    public static void main(String[] args) {
        Person p = new Person(){//new接口,后面跟大括号,里面写实现
            @Override
            public void eat() {
                System.out.println("吃饭...");
            }
        };
        p.eat();//接口.方法
    }

    //定义一个接口
    abstract static class Person {
        public abstract void eat();
    }
}
````



## 二.类关系

|           关系           |                        描述                        |        图形表示        |
| :----------------------: | :------------------------------------------------: | :--------------------: |
|     继承 ( extends )     |           子类继承父类/子接口继承父接口            |   实线 + 空心三角形    |
|   实现 ( realization )   |                     类实现接口                     |   虚线 + 空心三角形    |
|   依赖 ( dependency )    |    一个类需要另一个类 ( 临时性,偶然性,非常弱 )     |      虚线 + 箭头       |
|   关联 ( association )   |    一个类需要另一个类 ( 必然的,长期的,强烈的 )     |      实线 + 箭头       |
| 聚合关系 ( aggregation ) |    关联关系的特例 ( 整体和部分的关系 )  has - a    | 空心菱形 + 实线 + 箭头 |
| 组合关系 ( composition ) | 关联关系的特例 ( 整体和部分不可分离 ) contains - a | 实心菱形 + 实线 + 箭头 |

- 依赖关系中:
  - A类依赖B类, B类以参数的形式存在于A类中
- 关联关系中:
  - A类关联B类, B类以成员变量的形式存在于A类中
- 聚合关系:
  - A类聚合B类, 2者的生命周期不同, 2者可以分离, 关系和关联关系相同
- 组合关系:
  - A类组合B类, 2者是整体与部分关系,且不可分离,生命周期相同, 关系和关联关系相同



## 三.异常

``>> 程序运行过程中发生的不正常情况``

- 运行时异常
- 编译时异常



``>> 异常关键字``

|                捕获异常                 |        声明异常        |   异常抛出   |
| :-------------------------------------: | :--------------------: | :----------: |
|     try ( 执行可能产生的异常代码 )      |         throws         |    throw     |
|           catch ( 捕获异常 )            | 声明方法可能要抛出异常 | 手动抛出异常 |
| finally ( 无论是否发生异常,最重要执行 ) |                        |              |



``>> 异常情况解析``

|        是否产生异常        |                       执行情况                        |
| :------------------------: | :---------------------------------------------------: |
|          没有异常          |                 try-finally-正常结束                  |
|   产生异常,匹配捕获异常    |              try-catch-finally-正常结束               |
|  产生异常,不匹配捕获异常   |                 try-finally-程序中断                  |
|                            |                                                       |
| 在catch中使用System.exit() | try-程序终止,直接退出 JVM ( 唯一不执行finally的情况 ) |
| 在catch中存在return/throw  |               try-finally-return/throw                |



``>> 异常中catch块如何处理异常``

|       输出用户自定义异常信息       |         调用异常的方法输出异常信息          | 继续向上抛出异常 |
| :--------------------------------: | :-----------------------------------------: | :--------------: |
| System.err.println("除数不能为0"); |       toString(),显示异常的类名和原因       |     throw e      |
|                                    |   void printStackTrace(),输出异常堆栈信息   |                  |
|                                    | String getMessage(), 返回异常信息描述字符串 |                  |



``>> 常见的异常类型``

|                          异常方法名                          |      说明      |
| :----------------------------------------------------------: | :------------: |
|  public class ArithmeticException extends RuntimeException   |    算术异常    |
| public class ArrayIndexOutOfBoundsException extends IndexOutOfBoundsException |  数组下标越界  |
|  public class NullPointerException extends RuntimeException  |   空指针异常   |
| public class ClassNotFoundException extends ReflectiveOperationException | 无法找到所需类 |
| public class InputMismatchException extends NoSuchElementException | 输入类型不匹配 |
| public class IllegalArgumentException extends RuntimeException |  参数非法异常  |
|   public class ClassCastException extends RuntimeException   |  类型转换异常  |
| public class NumberFormatException extends IllegalArgumentException |  数字转换异常  |



``>> 异常的分类``

	-  a) Error: Java程序本身不无法恢复的错误

- b) Exception: 所有异常的根类
  - 运行时异常 ( 可处理也可以不处理 ) : RuntimeException
  - 编译 ( 检查 ) 时异常 ( 要求必须处理 ) : 检查时异常是N多个必须做处理的异常的总称,不是具体的类名称

​	

``>> 子类声明的异常范围不能超过父类声明范围``

- 父类没有异常声明,那么子类也不能声明异常 
  - 如果子类有异常,则必须在子类中进行处理 ( try-catch ) 
- 父类中声明了异常, 但是子类在重写时, 异常的类型只能小于等于父类的异常



``注意:``

​	1). 异常与方法的重写有关,跟方法的重载无关

``方法的重写:``

- 发生在子类
- 方法的名称相同,参数的类型,个数,顺序相同
- 返回值类型 <= 父类方法返回值类型
- 访问权限 >= 父类的访问权限
- 声明异常 <= 父类方法声明的异常

``自定义异常``

````java
		/*使用自定义异常类*/
		Scanner sc = new Scanner(System.in);
        String name = sc.next();
        if (!"jsd".equals(name) && !"yty".equals(name)) {
            try {
                throw new CustomException("输入的都不是想要的....");
            } catch (Exception e) {
                System.out.println(e.getMessage());
            }
        }else{
            System.out.println("name:---->" + name);
        }

		/*创建自定义异常*/
        public class CustomException extends Exception {
            public CustomException() {
                super();
            }
            public CustomException(String message) {
                super(message);
            }
        }
````



