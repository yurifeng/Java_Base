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

   	/**
   	 * 该类Itr是ArrayList的内部类,Itr被称为ArrayList的成员内部类
   	 */
   	private class Itr implements Iterator<E> {
   	    int cursor;       // index of next element to return
   	    int lastRet = -1; // index of last element returned; -1 if no such
   	    int expectedModCount = modCount;
   	    Itr() {}



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







