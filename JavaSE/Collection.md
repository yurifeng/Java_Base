## 一.Collection接口

``>>集合的优点:``

- 大小可以不确定.
- 删除和添加效率比数组高
- 可以保存映射关系
- 具备封装,操作简单

``>> Collection接口的特点:``

- 元素不唯一,无顺序

``>> 图解:``

![Collection接口](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1546416741893.png)

``>> Collection接口的公用方法``

````java
//add方法
	boolean add(E e);
//clear方法
	void clear();
//remove方法
	boolean remove(Object o);
//iterator方法
	Iterator<E> iterator();
//size方法
	int size();
//contains方法
	boolean contains(Object o);
//isEmpty方法
	boolean isEmpty();
````



## 二.List接口

##### ``List接口的方法``

```java
//add方法
	void add(int index,E element);
//remove方法
	E remove(int index);
//get方法
	E get(int index);
//indexOf方法
	int indexOf(Object o);
```

##### ``ArrayList源码分析(add方法功能源码)``

````java
	/**
     * Shared empty array instance used for default sized empty instances. We
     * distinguish this from EMPTY_ELEMENTDATA to know how much to inflate when
     * first element is added.
     */
//声明Object数组,已开空间,长度为0;
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

	/**
     * The array buffer into which the elements of the ArrayList are stored.
     * The capacity of the ArrayList is the length of this array buffer. Any
     * empty ArrayList with elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
     * will be expanded to DEFAULT_CAPACITY when the first element is added.
     */
//声明数组,没开空间;
    transient Object[] elementData;

	/**
     * The size of the ArrayList (the number of elements it contains).
     *
     * @serial
     */
//声明size;
    private int size;

 	/**
     * Default initial capacity.
     */
//默认初始大小
    private static final int DEFAULT_CAPACITY = 10;

	/**
     * Constructs an empty list with an initial capacity of ten.
     */
//ArrayList构造方法,用到了DEFAULTCAPACITY_EMPTY_ELEMENTDATA和elementData
    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
        //this.elementData长度为0,有地址
    }

	/**
     * Appends the specified element to the end of this list.
     */
//ArrayList的add方法,返回值boolean类型
    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // 所需要的最小容量
        elementData[size++] = e;
        return true;
    }

	private static int calculateCapacity(Object[] elementData, int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            //第一次调用add方法时,结果为true
            return Math.max(DEFAULT_CAPACITY, minCapacity);//Math.max(10,1);结果为10
        }
        return minCapacity;
    }

//检测容量是否够用
    private void ensureExplicitCapacity(int minCapacity) { //minCapacity==10;
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)//elementData.length初始化为0;
            grow(minCapacity);//开始扩容,minCapacity==10;
    }

	/**
     * Increases the capacity to ensure that it can hold at least the
     * number of elements specified by the minimum capacity argument.
     *
     * @param minCapacity the desired minimum capacity
     */
//ArrayList中增长容量的方法
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }

     private void ensureCapacityInternal(int minCapacity) {
        ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
    }
````

``>>实例:``

````Java
 public static void main(String[] args) {
        /**调用无参构造,Object类型数组的长度为0*/
        /**ArrayList的底层是Object类型的数组*/
        ArrayList list = new ArrayList();
        /**第一次添加元素时,初始化容量为10*/
        list.add("000");
        /**当add的元素达到11个,需要再次扩充容量*/
        list.add("111");
        list.add("222");
        list.add("333");
 }
````

- ##### ArrayList源码分析:

  - 1.调用无参构造方法创建ArrayList对象,底层创建了一个长度为Object类型的数组对象
  - 2.第一次调用add方法时,进行初始化容量为10
  - 3.添加第十一个元素时,需要重新扩容,约为容量的1.5倍

##### `` ArrayList其他API源码``

````java
	/**
     * Removes the first occurrence of the specified element from this list,
     * if it is present.  If the list does not contain the element, it is
     * unchanged.  More formally, removes the element with the lowest index
     * <tt>i</tt> such that
     * <tt>(o==null&nbsp;?&nbsp;get(i)==null&nbsp;:&nbsp;o.equals(get(i)))</tt>
     * (if such an element exists).  Returns <tt>true</tt> if this list
     * contained the specified element (or equivalently, if this list
     * changed as a result of the call).
     */
//1.根据元素删除
    public boolean remove方法调用(Object o) {
        if (o == null) {
            for (int index = 0; index < size; index++)
                if (elementData[index] == null) {
                    fastRemove(index);
                    return true;
                }
        } else {
            for (int index = 0; index < size; index++)
                if (o.equals(elementData[index])) {//如果参数和数组里面的元素一样
                    fastRemove(index);//调用fastRemove()方法删除元素
                    return true;
                }
        }
        return false;
    }
	/*
     * Private remove method that skips bounds checking and does not
     * return the value removed.
     */
//私有方法,删除index所在元素,并且给remove方法调用
    private void fastRemove(int index) {
        modCount++;
        int numMoved = size - index - 1;//移动元素的个数
        if (numMoved > 0)//如果长度合法
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);//数组复制
        elementData[--size] = null; // clear to let GC do its work;
        //将最后一个元素置为null;
    }


	/**
     * Removes the element at the specified position in this list.
     * Shifts any subsequent elements to the left (subtracts one from their
     * indices).
     */
//2.根据index删除元素
    public E remove(int index) {
        rangeCheck(index);//调用rangeCheck方法校验index是否合法
        
        modCount++;
        E oldValue = elementData(index);

        int numMoved = size - index - 1;//移动元素的个数
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);//数组copy
        elementData[--size] = null; // clear to let GC do its work; 
        //将最后一个元素置为null;

        return oldValue;//返回被删除的元素
    }
	/**
     * Checks if the given index is in range.  If not, throws an appropriate
     * runtime exception.  This method does *not* check if the index is
     * negative: It is always used immediately prior to an array access,
     * which throws an ArrayIndexOutOfBoundsException if index is negative.
     */
    private void rangeCheck(int index) {
        if (index >= size)
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
	@SuppressWarnings("unchecked")
    E elementData(int index) {
        return (E) elementData[index];
    }

	/**
     * Returns the element at the specified position in this list.
     *
     * @param  index index of the element to return
     * @return the element at the specified position in this list
     * @throws IndexOutOfBoundsException {@inheritDoc}
     */
//3.通过index查找元素
    public E get(int index) {
        rangeCheck(index);//验证index的合法性
        return elementData(index);//返回index所在的元素
    }
	@SuppressWarnings("unchecked")
    E elementData(int index) {
        return (E) elementData[index];
    }
````

##### ``手写实现ArrayList部分功能``

````java
package org.test.test02.entity;

import java.util.Collection;
import java.util.Iterator;
import java.util.List;
import java.util.ListIterator;

/**
 * @author yurifeng
 * @create 2019-01-02 15:02
 * @package_name org.test.test02.entity
 * @project_name test02
 */
@SuppressWarnings("all")
public class MyArrayList implements List {

    /** 声明一个Object类型的数组 */
    private Object[] objects;

    /** 声明集合中元素的个数 */
    private int size;

    /** 空参构造 */
    @SuppressWarnings("all")
    public MyArrayList() {
        this(10);
    }

    /** 带参构造,初始化大小为10 */
    @SuppressWarnings("all")
    public MyArrayList(int capacity) {
        objects = new Object[10];
    }

    /** 返回list大小 */
    @Override
    public int size() {
        return size;
    }

    /** list是否为空 */
    @Override
    public boolean isEmpty() {
        return size == 0;
    }

    /** 是否包含元素的方法 */
    @Override
    public boolean contains(Object item) {
        /**遍历数组*/
        for (Object object : objects) {
            //元素不为null;且与传入的参数相同的
            if (object != null && object.equals(item)) {
                return true;
            }
        }
        return false;
    }
    
    /** list添加元素的方法 */
    @Override
    public boolean add(Object o) {
        //将添加的元素下标置为0
        objects[size] = o;
        //元素个数+1
        size++;
        return true;
    }

    /** 根据元素删除 */
    @Override
    public boolean remove(Object item) {
        int index = -1;
        boolean flag = false;
        //根据元素找到了下标
        for (int i = 0; i < objects.length; i++) {
            if (objects[i] != null && objects[i].equals(item)) {
                index = i;
                break;
            }
        }

        //判断下标是否合法
        if (index != -1) {
            //元素移动
            int numMoved = size - index - 1;
            System.arraycopy(objects, index + 1, objects, index, numMoved);
            //将最后一位废弃的元素置为null
            size--;
            objects[size] = null;
            return true;
        } else {
            //下标不合法,返回false
            return false;
        }
    }

    /** 清空元素集合 */
    @Override
    public void clear() {
        //遍历置空
        for (int i = 0; i < objects.length; i++) {
            objects[i] = null;
        }
        //大小置0
        size = 0;
    }

    @SuppressWarnings("unchecked")
    @Override
    public Object get(int index) {
        return objects[index];
    }
}
````



## 三.LinkedList类

##### ``LinkedList源码``

````Java
//LinkedList定义的成员变量
	transient int size = 0;

    /**
     * Pointer to first node.
     * Invariant: (first == null && last == null) ||
     *            (first.prev == null && first.item != null)
     */
    transient Node<E> first;

    /**
     * Pointer to last node.
     * Invariant: (first == null && last == null) ||
     *            (last.next == null && last.item != null)
     */
    transient Node<E> last;

	/**
     * Appends the specified element to the end of this list.
     */
//add方法
    public boolean add(E e) {
        linkLast(e);
        return true;
    }

	/**
     * Links e as last element.
     */
//LinkedList底层操作基于链表的数据结构
    void linkLast(E e) {
        final Node<E> l = last;//last是在LinkedList定义的成员变量,目前为null;
        final Node<E> newNode = new Node<>(l, e, null);//pre是null;元素是e;next是null;
        last = newNode;
        if (l == null)
            first = newNode;
        else
            l.next = newNode;
        size++;
        modCount++;
    }
//Node节点类
	private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
````

##### ``LinkedList图实例``

![1546421068085](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1546421068085.png)

``>> pre和next存的都是内存地址``



##### `` 其他API方法``

````java
//clear方法
	public void clear();
//addFirst方法
	public void addFirst(E e);
//addLast方法
	public void addLast(E e)
//getFirst方法
	public E getFirst()
//getLast方法
    public E getLast()
````



## 四.数据结构

![1546478810308](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1546482851433.png)

##### `` >> 数据结构``

- 线性结构 
  - 顺序存储结构
    - 数组
  - 链式存储结构
    - LinkedList (双向链表)
- 非线性结构
  - 图
  - 数



##### ``>> 对LinkedList的所有操作``

- 实际上是对Node节点的操作;
- Node是LinkedList中的私有静态内部类;
- 每次调用add方法,底层是在创建Node节点对象



## 五.Iterator接口

`` >>等级划分:``

```java
public interface Collection<E> extends Iterable<E> /**Collection接口是Iterable接口子类*/
		public interface Iterable<T>
				 /**
                 * Returns an iterator over elements of type {@code T}.
                 *
                 * @return an Iterator.
                 */
                Iterator<T> iterator();//Iterable中有个方法是iterator
```

`` >>注意:``

- 不同的集合的数据结构不同, 判断元素和取出元素的方式不同,功能一致,将功能封装成一个接口,取名Iterator;
- 接口的实现交由集合自己使用内部类来完成,内部类作为外部类的属性,操作起来更加方便和安全;

`` >>图示接口``

![1546431098698](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1546479064074.png)

``ArrayList中Iterator源码``

````java
	/**
     * Returns an iterator over the elements in this list in proper sequence.
     *
     * <p>The returned iterator is <a href="#fail-fast"><i>fail-fast</i></a>.
     *
     * @return an iterator over the elements in this list in proper sequence
     */
    public Iterator<E> iterator() {
        return new Itr();
    }

    /**
     * An optimized version of AbstractList.Itr
     */
    private class Itr implements Iterator<E> {
        int cursor;       // index of next element to return
        int lastRet = -1; // index of last element returned; -1 if no such
        int expectedModCount = modCount;

        Itr() {}

        public boolean hasNext() {
            return cursor != size;
        }

        @SuppressWarnings("unchecked")
        public E next() {
            checkForComodification();
            int i = cursor;
            if (i >= size)
                throw new NoSuchElementException();
            Object[] elementData = ArrayList.this.elementData;
            if (i >= elementData.length)
                throw new ConcurrentModificationException();
            cursor = i + 1;
            return (E) elementData[lastRet = i];
        }

        final void checkForComodification() {
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
        }
    }
````

##### ``ListIterator``

```java
/**ListIterator接口继承了Iterator接口*/
public interface ListIterator<E> extends Iterator<E>{ 
    boolean hasNext();
    E next();
    boolean hasPrevious();
    E previous();
    int nextIndex();
    int previousIndex();
    void remove();
    void set(E e);
    void add(E e);
}
//ListIterator接口中不仅有遍历方法,还有修改方法;

```



## 六.泛型

##### `` >> 定义``

- 泛型能大大保证输入类型的正确性;
- 泛型的作用仅在于代码编译时期,编译过后没有意义;

##### ``>> 分类``

- 泛型类

  - ````java
    public class MethodAbc<T>{} //T表示占位符,数据类型在创建对象时才会明确;
    ````

- 泛型接口

  - ````java
    package org.test.test02.method000;
    
    /**
     * @author yurifeng
     * @create 2019-01-03 15:28
     * @package_name org.test.test02.method000
     * @project_name test02
     */
    public class MethodZzz {
        public static void main(String[] args) {
            //T的类型现在明确
            MyInt<String> my = new MyInt<>();
            //
            MyInt2 myInt2 = new MyInt2();
        }
    }
    
    interface MyInterface<T> {//泛型接口,1.数据类型可以在实现类中明确;2.也可以在创建实现类的对象中明确
    }
    
    class MyInt<T> implements MyInterface<T> {//实现类也不知道类型,2.也可以在创建实现类的对象中明确
    }
    
    class MyInt2 implements MyInterface<String>{//已确定T的类型
    
    }
    ````

- 泛型方法

  - ````java
    package org.test.test02.method000;
    
    /**
     * @author yurifeng
     * @create 2019-01-03 13:48
     * @package_name org.test.test02
     * @project_name test02
     */
    @SuppressWarnings("all")
    public class MethodAbc<T> {
        public static void main(String[] args) {
            People<String> p = new People<>();
            p.add("String");
            //Q代表String类型
            p.show("hello java");
            //Q代表Integer类型
            p.show(123456798);
    
            //可变参数(泛型方法的可变参数,解决了方法重载问题)
            p.fun(123, 465, 789, 987, 654, 321);
            p.fun("function", "html");
            p.fun(new Dog());
    
        }
    }
    
    /** 泛型类 */
    class People<T> {//泛型类,T在创建对象的时候明确
    
        public void add(T t) {//泛型方法,T在创建类的对象时候明确
            System.out.println(t);
        }
    
        public <Q> void show(Q q) {//Q也代表数据类型,在调用方法时确定
        }
    
        public <P> void fun(P... pp) {//泛型的可变参数的方法
            for (P ppp : pp) {
                System.out.println(ppp);
            }
        }
    }
    
    /** 其他类 */
    class Dog {
    
    }
    ````

##### ``>> 泛型的高级应用``

- 泛型的上限: 确定父类 (已经捅到最高了)

  - ```java
    ArrayList<? extends 父类> //确定类型为E或者E的子类
    ```

- 泛型的下限: 确定子类 (超级表示还有更高)

  - ```java
    ArrayList<? super 子类> //确定类型为E或者E的所有的父类
    ```

##### ``>> 详细代码``

- ```java
  package org.method000;
  import java.util.StringJoiner;
  /**
   * Pseron父类
   */
  public class Person {
      private String name;
      private Integer age;
      private Gender gender;
      public Person(String name, Integer age, Gender gender) {
          this.name = name;
          this.age = age;
          this.gender = gender;
      }
      public Person() {
      }
      /**getter,setter省略*/
      @Override
      public String toString() {
          return new StringJoiner(", ", Person.class.getSimpleName() + "[", "]")
                  .add("name='" + name + "'")
                  .add("age=" + age)
                  .add("gender=" + gender)
                  .toString();
      }
  }
  
  package org.method000;
  /**
   * Student子类
   */
  public class Student extends Person {
      private String stuNo;
      public Student(String name, Integer age, Gender gender, String stuNo) {
          super(name, age, gender);
          this.stuNo = stuNo;
      }
      public Student() {
          super();
      }
      @Override
      public String toString() {
          return super.toString() + "学号:" + this.stuNo;
      }
  }
  
  package org.method000;
  /**
   * Teacher子类
   */
  public class Teacher extends Person {
      private Integer teachOfYear;
      public Integer getTeachOfYear() {
          return teachOfYear;
      }
      public void setTeachOfYear(Integer teachOfYear) {
          this.teachOfYear = teachOfYear;
      }
      public Teacher(String name, Integer age, Gender gender, Integer teachOfYear) {
          super(name, age, gender);
          this.teachOfYear = teachOfYear;
      }
      public Teacher() {
          super();
      }
      @Override
      public String toString() {
          return super.toString() + "教龄:" + this.teachOfYear;
      }
  }
  
  package org.method000;
  import java.util.ArrayList;
  /**
   * 测试类
   */
  public class test000000 {
      public static void main(String[] args) {
  
          /**Person*/
          ArrayList<Person> listPerson = new ArrayList<>();
          //创建person类型的对象
          listPerson.add(new Person("jsd", 12, Gender.Male));
          listPerson.add(new Person("yty", 22, Gender.Female));
          listPerson.add(new Person("htt", 32, Gender.Male));
  
          /**Student*/
          ArrayList<Student> listStudent = new ArrayList<>();
          //创建Student类型的对象
          listStudent.add(new Student("jack", 111, Gender.Male, "stu001"));
          listStudent.add(new Student("mark", 222, Gender.Female, "stu002"));
  
          /**Teacher*/
          ArrayList<Teacher> listTeacher = new ArrayList<>();
          //创建Teacher类型的对象
          listTeacher.add(new Teacher("mahadon", 999, Gender.Male, 333));
          listTeacher.add(new Teacher("catalinna", 1000, Gender.Female, 444));
  
          /**调用上限方法**/
          print(listPerson);
          print(listStudent);
          print(listTeacher);
  
          /**调用下限方法*/
          show(listPerson);
          show(listStudent);
          //show(listTeacher);
      }
  
      /** Person 以及其子类可以用的方法 */
      public static void print(ArrayList<? extends Person> p) {
          for (Person person : p) {
              System.out.println(person);
          }
          System.out.println("*******************************************");
      }
  
      /** 子类Student,以及其所有的父类可以用的方法 */
      public static void show(ArrayList<? super Student> p) {
          for (Object object : p) {
              System.out.println(object);
          }
          System.out.println("*******************************************");
      }
  }
  ```

##### ``>> 图例``

![1546505583958](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1546505583958.png)



## 七.Set 接口

##### ``>> 定义``

Set接口特点:	(1)唯一,无序;

​			(2)底层数据结构是hash表+链表;

![1546506925307](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1546506925307.png)

- HashSet
  - 不允许添加重复元素;添加时会判断元素是否存在,若存在,要添加的元素自动忽略
  - 元素添加完毕,打印出来是乱序的,不规则打印

##### ``>> hash表原理``

- 原理涉及到2个方法, Object中的 hashCode(), equals() 

![1546506944306](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1546506944306.png)

- Integer类型的hashCode()就是Integer本身

  - ```Java
    public static int hashCode(int value) {
        return value;
    }
    ```

- ![1546566938983](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1546566938983.png)

  - 存储步骤:
    - 1.计算对象的hashCode; ( 结果都是Integer类型 )
    - 2.根据规定的函数计算存储位置; ( 根据Integer类型得出位置 ) 
    - 3.得到位置,便存放对象; ( 检查位置是否有相同的元素,无相同元素,链表添加对象 )

  - 删除步骤:
    - 1.计算hash码;
    - 2.计算存储位置;
    - 3.查找是否有该删除的对象,有就删除;

``>> 当单个链表中的存储数超过8个,则使用了二叉树存储``



##### ``>> HashSet``

````java
/**People实体类**/
package org.set;
import java.util.Objects;
import java.util.StringJoiner;
/**
 * @author yurifeng
 * @create 2019-01-04 10:09
 * @package_name org.set
 * @project_name test02
 */
public class People {
    private String name;
    private Integer age;
    private Gender gender;
    public People(String name, Integer age, Gender gender) {
        this.name = name;
        this.age = age;
        this.gender = gender;
    }
    public People() {
    }
    /**省略setter和getter*/
    @Override
    public String toString() {
        return new StringJoiner(", ", People.class.getSimpleName() + "[", "]")
                .add("name='" + name + "'")
                .add("age=" + age)
                .add("gender=" + gender)
                .toString();
    }
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof People)) return false;
        People people = (People) o;
        return Objects.equals(getName(), people.getName()) &&
                Objects.equals(getAge(), people.getAge()) &&
                getGender() == people.getGender();
    }
    @Override
    public int hashCode() {
        return Objects.hash(getName(), getAge(), getGender());
    }
}
/**枚举类**/
enum Gender {
    Female, Male
}

/**测试类**/
package org.set;
import java.util.HashSet;
/**
 * @author yurifeng
 * @create 2019-01-03 17:04
 * @package_name org.set
 * @project_name test02
 */
public class MySet {
    public static void main(String[] args) {
        HashSet<People> hs = new HashSet<>();
        People p1 = new People("jsd",200,Gender.Male);
        People p2 = new People("yty",300,Gender.Female);
        People p3 = new People("jst",400,Gender.Male);
        People p4 = new People("jsd",200,Gender.Male);
		
        /**add()方法执行时,自动调用hashCode**/
        hs.add(p1);
        hs.add(p2);
        hs.add(p3);
        hs.add(p4);

        System.out.println(hs.size());//size:3
        System.out.println(hs);//内容:前三个对象,不包括第四个
    }
}
//注意:对象类型的HashSet添加时,实体类一定要重写hashCode()和equals()方法,这样才能不重复;
````

##### ``>> LinkedHashSet``

![1546571010039](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1546571010039.png)



##### ``>> TreeSet``

- 特点:	(1)升序,按照英文字母排序

  ​		(2)唯一,不能有重复的元素

  ​		(3)底层数据结构是:红黑树

![1546576336058](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1546576336058.png)



- TreeSet的使用

  ````java
  package org.set;
  import java.util.TreeSet;
  /**
   * @author yurifeng
   * @create 2019-01-04 11:04
   * @package_name org.set
   * @project_name test02
   */
  public class TestTreeSet {
      public static void main(String[] args) {
          TreeSet<String> treeSet = new TreeSet<>();
          treeSet.add("jsd");
          treeSet.add("jxt");
          treeSet.add("hst");
          treeSet.add("yty");
          treeSet.add("catalina");
          treeSet.add("jsd");
          System.out.println(treeSet.size());//size:5
          System.out.println(treeSet);//元素:[catalina, hst, jsd, jxt, yty]
      }
  }
  ````

- TreeSet存储自定义对象

  - ![1546582799654](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1546582799654.png)

  - ````java
    package org.set;
    import java.util.Objects;
    import java.util.StringJoiner;
    /**
     * @author yurifeng
     * @create 2019-01-04 10:09
     * @package_name org.set
     * @project_name test02
     */
    public class People implements Comparable<People> {
        private String name;
        private Integer age;
        private Gender gender;
    		
        /**省略了setter和getter方法**/
        /**省略了toString()方法,hashCode()方法,equals()方法**/
        
        /** 重写Comparable接口比较大小的方法 */
        /** 此处制定自定义的比较方法 */
        @Override
        public int compareTo(People val1) {
            /*return this.age - val1.age;*/
            if (this.age == val1.age) {//如果年龄相等,则再次比较name
                if (this.name.compareTo(val1.getName()) == 0) {//如果name相等
                    return this.getGender().compareTo(val1.getGender());//则比较性别
                } else if (this.name.compareTo(val1.getName()) > 0) {
                    return 1;
                } else {
                    return -1;
                }
            } else if (this.age > val1.age) {
                return 1;
            } else {
                return -1;
            }
        }
    }
    /**枚举类*/
    enum Gender {
        Female, Male
    }
    
    /**测试类*/
    package org.set;
    import java.util.TreeSet;
    /**
     * @author yurifeng
     * @create 2019-01-04 11:04
     * @package_name org.set
     * @project_name test02
     */
    public class TestTreeSet {
        public static void main(String[] args) {
            TreeSet<People> ts = new TreeSet<>();
            People p1 =new People("jsd", 20, Gender.Female);
            People p4 =new People("jtd", 44, Gender.Male);
            People p3 =new People("jsa", 33, Gender.Female);
            People p2 =new People("jat", 20, Gender.Male);
            ts.add(p1);
            ts.add(p2);
            ts.add(p3);
            ts.add(p4);
            System.out.println(ts);
            /**People[name='jat', age=20, gender=Male], 
               People[name='jsd', age=20, gender=Female], 
               People[name='jsa', age=33, gender=Female],
               People[name='jtd', age=44, gender=Male]]**/
        }
    }
    ````



##### ``>> Set接口总结``

- Set接口的实现类

  - HashSet ( 底层是hash表 ) [ 特点:唯一,无序 ] {必须重写hashCode()和equals方法}
  - LinkedHashSet ( 底层链表+hash表 ) [ 特点:唯一, 有序(添加顺序) ] {必须重写hashCode()和equals方法}
  - TreeSet ( 红黑树 ) [ 特点:唯一,有序(升序) ] {要求实体类实现Comparable(内部比较器)接口,具备比较大小的能力;也可以实现Comparator(外部比较器)接口}

- 注意:
  - 如果实体类未实现Comparable(内部比较器)接口, 则可以自定义一个其他类, 去实现Comparator(外部比较器)接口, 里面写比较规则.然后在TreeSet构造方法中传入比较规则

````java
//手写实现Comparator接口的比较方法
/**同样是以上的People实体类和测试类,只是实体类没有实现Comparable接口**/
package org.set;
import java.util.Comparator;
/**
 * @author yurifeng
 * @create 2019-01-04 15:17
 * @package_name org.set
 * @project_name test02
 */
public class ComparatorPeople implements Comparator<People> {
    /**编写自定义的比较规则方法*/
    @Override
    public int compare(People people1, People people2) {
        return people1.getName().length() - people2.getName().length();
    }
}

//使用规则对People对象进行排序(name的长度)
/**测试类*/
package org.set;
import java.util.TreeSet;
/**
 * @author yurifeng
 * @create 2019-01-04 11:04
 * @package_name org.set
 * @project_name test02
 */
public class TestTreeSet {
    public static void main(String[] args) {
        /** 接口new实现类*/
        Comparator<People> comparator = new ComparatorPeople();

        /**将Comparator比较器当作TreeSet构造参数*/
        TreeSet<People> ts = new TreeSet<>(comparator);
        People p1 = new People("001", 20, Gender.Female);
        People p4 = new People("002", 44, Gender.Male);//根据比较规则,length相同;
        People p3 = new People("0003", 33, Gender.Female);
        People p2 = new People("00004", 20, Gender.Male);
        ts.add(p1);
        ts.add(p2);//根据TreeSet的唯一性,p1和p2,只能添加一个;
        ts.add(p3);
        ts.add(p4);
        System.out.println(ts);
        /**
        [People[name='001', age=20, gender=Female],
            People[name='0003', age=33, gender=Female],
            People[name='00004', age=20, gender=Male]]
        */
    }
}

````

``>> 图列:``

![1546587511093](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1546587511093.png)

## 八.Map 接口

##### ``>> Map接口常用方法``

- 增
  - `put(K key,V value)` 添加键值对, 如果遇到相同的key,则后者的value覆盖前者value;
- 删
  - `clear()` 移除集合中所有的key-value,结合的length是0;
  - `remove(Object key)`  
  - `remove(Object key, Object value)` 
- 查
  - `get(Object key)` 根据key,找到value,获取到是key对应的value;
  - `keySet()` 获取集合中所有key,结果就是都是key的Set集合;
  - `size()`
  - `values()` 获取集合中额所有value,结果就是都是value的Collection集合;
  - `entrySet()` 获取全部完整的key-value对应内容,map特性的方法;
- 判断
  - `containsKey(Object key)` 判断集合中是否存在key
  - `containsValue(Object value)` 判断集合中是否存在value
  - `isEmpty()` 判断集合中是否有key-value键值对



##### ``>> Map图结构``![1546826172685](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1546826172685.png)





##### ``>> 注意``

- HashSet底层使用了HashMap的结构

  ```java
  /**
   * Constructs a new, empty set; the backing <tt>HashMap</tt> instance has
   * default initial capacity (16) and load factor (0.75).
   */
  //HashSet的
      public HashSet() {
          map = new HashMap<>();
      }
  
  /**
       * Adds the specified element to this set if it is not already present.
       * More formally, adds the specified element <tt>e</tt> to this set if
       * this set contains no element <tt>e2</tt> such that
       * <tt>(e==null&nbsp;?&nbsp;e2==null&nbsp;:&nbsp;e.equals(e2))</tt>.
       * If this set already contains the element, the call leaves the set
       * unchanged and returns <tt>false</tt>.
       *
       * @param e element to be added to this set
       * @return <tt>true</tt> if this set did not already contain the specified
       * element
       */
  //HashSet的add()方法底层引用的map的put()方法
      public boolean add(E e) {
          return map.put(e, PRESENT)==null;
      }
  ```

- `Map.Entry<K,V>` 

  - Map.Entry类型 (key, value关系的类型)
    - entrySet();

  ````java
  package org.map;
  import java.util.Collection;
  import java.util.HashMap;
  import java.util.Map;
  import java.util.Set;
  /**
   * @author yurifeng
   * @create 2019-01-04 15:45
   * @package_name org.map
   * @project_name test02
   */
  public class TestMap {
      public static void main(String[] args) {
          HashMap<String, Integer> hm = new HashMap<>();
          hm.put("jsd", 111);
          hm.put("jat", 222);
          hm.put("jst", 333);
          hm.put("yty", 444);
          //foreach循环遍历Map.Entry,得到完整映射
          for (Map.Entry<String, Integer> item : hm.entrySet()) {
              System.out.println(item);
          }
          //foreach循环遍历,单独取出key,value
          Set<Map.Entry<String, Integer>> items = hm.entrySet();
          for (Map.Entry<String, Integer> item : items) {
              String key = item.getKey();
              Integer value = item.getValue();
              System.out.println(key + "--->" + value);
          }
          //以Set集合的方式打印maps集合
          Set<Map.Entry<String, Integer>> maps = hm.entrySet();
          System.out.println(maps);
      }
  }
  ````

- Map通俗解释图

  ![1546592773325](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1546592773325.png)



##### ``>> 案例``

- 使用LinkedHashSet, key有序,唯一,升序
- 使用TreeMap,key有序,唯一,升序

````java
package org.mapcollection;
import java.util.StringJoiner;
/**
 * @author yurifeng
 * @create 2019-01-07 10:05
 * @package_name org.treemap
 * @project_name test02
 */
/**Product实体类*/
public class Product {
    private String name;
    private double price;
    private String proNo;
    private String brand;
    private Category category;
    public Product() {
        super();
    }
    public Product(String name, double price, String proNo, String brand, Category category) {
        this.name = name;
        this.price = price;
        this.proNo = proNo;
        this.brand = brand;
        this.category = category;
    }
    /**setter和getter省略**/
    @Override
    public String toString() {
        return new StringJoiner(", ", Product.class.getSimpleName() + "[", "]")
                .add("name='" + name + "'")
                .add("price=" + price)
                .add("proNo='" + proNo + "'")
                .add("brand='" + brand + "'")
                .add("category=" + category)
                .toString();
    }
}

/**枚举类*/
enum Category {
    酒水, 粮油, 生活用品, 副食品
}

package org.mapcollection;
import java.util.LinkedHashMap;
import java.util.Map;
/**
 * @author yurifeng
 * @create 2019-01-07 9:41
 * @package_name org.treemap
 * @project_name test02
 */
/**LinkedHashMap测试类*/
public class LinkedHashMap01 {
    public static void main(String[] args) {
        //key为String类型;value是Product类型
        LinkedHashMap<String, Product> hashMap = new LinkedHashMap<>();
        //创建Product对象
        Product p1 = new Product("茅台", 9000, "1001", "贵州茅台", Category.酒水);
        Product p2 = new Product("牛奶", 28, "1002", "蒙牛", Category.副食品);
        Product p3 = new Product("金龙鱼", 100, "1003", "中粮集团", Category.粮油);
        //添加到集合中
        hashMap.put(p1.getProNo(), p1);
        hashMap.put(p2.getProNo(), p2);
        hashMap.put(p3.getProNo(), p3);
        printPro(hashMap);
    }
    private static void printPro(LinkedHashMap<String, Product> hashMap) {
        for (Map.Entry<String, Product> stringProductEntry : hashMap.entrySet()) {
            System.out.println(stringProductEntry);
/**
   1001=Product[name='茅台', price=9000.0, proNo='1001', brand='贵州茅台', category=酒水]
   1002=Product[name='牛奶', price=28.0, proNo='1002', brand='蒙牛', category=副食品]
   1003=Product[name='金龙鱼', price=100.0, proNo='1003', brand='中粮集团', category=粮油]
            */
        }
    }
}


package org.treemap;
import java.util.Map;
import java.util.TreeMap;
/**
 * @author yurifeng
 * @create 2019-01-07 11:31
 * @package_name org.treemap
 * @project_name test02
 */
//TreeMap测试类
//String为key,商品为value
public class TreeMapTest001 {
    public static void main(String[] args) {
        //声明key为String类型,value为Product类型的TreeMap
        TreeMap<String, Product> treeMap = new TreeMap();
        //添加对象
        Product p2 = new Product("牛奶", 28, "b1002", "蒙牛", Category.副食品);
        Product p1 = new Product("茅台", 9000, "a1001", "贵州茅台", Category.酒水);
        Product p3 = new Product("金龙鱼", 100, "b1003", "中粮集团", Category.粮油);
        //添加到TreeMap中
        treeMap.put(p1.getProNo(), p1);
        treeMap.put(p2.getProNo(), p2);
        treeMap.put(p3.getProNo(), p3);
        //遍历集合
        for (Map.Entry<String, Product> stringProductEntry : treeMap.entrySet()) {
            System.out.println(stringProductEntry);
        }
/**
a1001=Product[name='茅台', price=9000.0, proNo='a1001', brand='贵州茅台', category=酒水]
b1002=Product[name='牛奶', price=28.0, proNo='b1002', brand='蒙牛', category=副食品]
b1003=Product[name='金龙鱼', price=100.0, proNo='b1003', brand='中粮集团', category=粮油]
*/
    }
}

/**Comparator匿名内部类使用比较**/
//以对象类型为key,String类型为value
        TreeMap<Product, String> tm = new TreeMap(
                new Comparator<Product>() {
                    @Override
                    public int compare(Product o1, Product o2) {
                        return o1.getProNo().compareTo(o2.getProNo());
                    }
                }
        );
		//添加对象
        Product p2 = new Product("牛奶", 28, "b1002", "蒙牛", Category.副食品);
        Product p1 = new Product("茅台", 9000, "a1001", "贵州茅台", Category.酒水);
        Product p3 = new Product("金龙鱼", 100, "b1003", "中粮集团", Category.粮油);

        //添加到TreeMap中;
        tm.put(p1, p1.getName());
        tm.put(p2, p2.getName());
        tm.put(p3, p3.getName());

````



## 九.Collection 集合框架总结

![1546844581096](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1546844581096.png)



![1546844848028](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1546844848028.png)



![1546825907977](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1546825907977.png)



## 十.Collections 集合工具类

##### ``>> 定义``

-  Collections是集合的工具类
- 关系代码

```java
public class Collections
extends Object
```



##### ``>> 常用方法``

````java
	/**addAll()方法**/
	List<String> coll = new ArrayList<>();
        //添加元素
        Collections.addAll(coll, "jsd001", "jsd002", "jsd003");
        for (String item : coll) {
            System.out.println(item);/**按照添加顺序添加*/
        }

	/**sort()方法**/
        Collections.sort(coll);
        for (String str : coll) {
            System.out.println(str);/**将集合内容按照升序排序*/
        }

	/**binarySearch()方法**/
		//二分搜索
	 	System.out.println(Collections.binarySearch(coll, "haha"));//结果等于(-插入点-1);
        System.out.println(Collections.binarySearch(coll, "jsd002"));//返回0

	//找打元素中的max和min
        System.out.println(Collections.max(coll) + "---->" + Collections.min(coll));

	//fill填充
        Collections.fill(coll, "00000");
        System.out.println(coll);
	
	//reverse反转(不是降序)
		Collections.reverse(coll);
        System.out.println(coll);

	//swap()方法
		//按照指定的位置交换
		Collections.swap(coll, 1, 0);
        System.out.println(coll);
````















