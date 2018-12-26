## 一.常用类



#### 1.全局概览图

![包装类](C:\Users\Administrator\Desktop\xmid文档\JavaSE\包装类.jpg)

![1545735130254](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1545735130254.png)

Java不是纯面向对象的语言,基本类型还是需要进行转换成对象



#### Integer类:

```java
public final class Integer
extends Number
implements Comparable<Integer>
```



Integer中的方法:

​	`compareTo(Integer anotherInteger)`

```java
	/**
     * Compares two {@code Integer} objects numerically.
     */
    public int compareTo(Integer anotherInteger) {
        return compare(this.value, anotherInteger.value);
    }

    /**
     * Compares two {@code int} values numerically.
     */
    public static int compare(int x, int y) {
        return (x < y) ? -1 : ((x == y) ? 0 : 1);
    }
```



​	`equals(Object obj)`

```java
	/**
     * Compares this object to the specified object.
     */
    public boolean equals(Object obj) {
        if (obj instanceof Integer) {
            return value == ((Integer)obj).intValue();
        }
        return false;
    }
```



​	`toString()`

```java
 	/**
     * Returns a {@code String} object representing this {@code Integer}'s value.
     */
    public String toString() {
        return toString(value);
    }
```



##### 常用方法:

int---->Integer: 

​	构造方法

```java
Integer integer = new Integer(132);
```

 	静态方法

```java
Integer integer = Integer.valueOf(132);
```



Integer---->int:

```java
Integer integer = Integer.valueOf(132);
int number = integer.intValue();
```

​	

Integer---->String:

```java
Integer integer = Integer.valueOf(132);
String string = integer.toString();
```



String---->Integer:

```java
Integer integer = Integer.valueOf("132");
```



String----->int:

```java
int integer = Integer.parseInt("132");
```



int----->String:

````java
String str = String.valueOf(132);
````



#### 注意: 

```java
Integer integer = 127;
/*如果数字在 -128~127 之间, Integer 对象不重新开空间,因为已经有缓存*/
/*超出缓存范围 Integer 对象就绪重新开空间*/
```



Integer的内部类源码解释了 Integer 自动装箱缓存:

```java
	/**
     * Cache to support the object identity
     * semantics of autoboxing for values between
     * -128 and 127 (inclusive) as required by JLS.
     */

    private static class IntegerCache {
        static final int low = -128;
        static final int high;
        static final Integer cache[];

        static {
            // high value may be configured by property
            int h = 127;
            String integerCacheHighPropValue =
                sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
            if (integerCacheHighPropValue != null) {
                try {
                    int i = parseInt(integerCacheHighPropValue);
                    i = Math.max(i, 127);
                    // Maximum array size is Integer.MAX_VALUE
                    h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
                } catch( NumberFormatException nfe) {
                    // If the property cannot be parsed into an int, ignore it.
                }
            }
            high = h;

            cache = new Integer[(high - low) + 1];
            int j = low;
            for(int k = 0; k < cache.length; k++)
                cache[k] = new Integer(j++);

            // range [-128, 127] must be interned (JLS7 5.1.7)
            assert IntegerCache.high >= 127;
        }

        private IntegerCache() {}
    }
```

所以:

```Java
Integer integer1 = 127;
Integer integer2 = 127;
/*以上两者的的内存地址相同*/

Integer integer3 = 128;
Integer integer4 = 128;
/*下面2者的内存地址不同,缓存超出,Integer重新建立空间*/
```



#### Character类:

```java
public final class Character
extends Object
implements Serializable, Comparable<Character>
```



character常用方法:

````java
/*
	 	isLetter()
	 	isDigit()
	 	isSpace()
*/

public class mianClass {
    public static void main(String[] args) {      
        String str = "Welcome to US 57 sector...";
        int count = 0;
        int digitCount = 0;
        int spaceCount = 0;
        for (char s : str.toCharArray()) {
            if (Character.isLetter(s)) {
                count++;
            } else if (Character.isDigit(s)) {
                digitCount++;
            } else if (Character.isSpaceChar(s)) {
                spaceCount++;
            }
        }
        System.out.println("count:" + count);
         System.out.println("digitCount:" + digitCount);
         System.out.println("spaceCount:" + spaceCount);
    }
}

````



#### String类:

```java
public final class String
extends Object
implements Serializable, Comparable<String>, CharSequence
```

构造方法:

```Java
//1.空参构造
	String()
//2.byte类型数组构造方法(通过Unicode码转换)
    String(byte[] bytes, int offset, int length)
//3.char类型数组构造方法
    String(char[] value, int offset, int count)
//4.String参数构造方法
    String(String string)
```

String类型底层的实现基于char类型的数组,String类是对char类型数组的封装

​	``a)数组一旦赋值不可更改内容``

​	``b)使用final修饰,表示其引用不能更改``

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];
}
```

##### charAt(int index)方法:

```java
	/**
     * Returns the {@code char} value at the
     * specified index. 
     */
    public char charAt(int index) {
        if ((index < 0) || (index >= value.length)) {
            throw new StringIndexOutOfBoundsException(index);
        }
        return value[index];
    }
```

String的charAt(int index)方法实现自CharSequence接口:

```java
public interface CharSequence {
    char charAt(int index);
   	CharSequence subSequence(int start, int end);
```

##### compareTo(String anotherString)方法:

```java
 public int compareTo(String anotherString) {
        int len1 = value.length;
        int len2 = anotherString.value.length;
        int lim = Math.min(len1, len2);
        char v1[] = value;
        char v2[] = anotherString.value;

        int k = 0;
        while (k < lim) {
            char c1 = v1[k];
            char c2 = v2[k];
            if (c1 != c2) {
                return c1 - c2;
            }
            k++;
        }
        return len1 - len2;
    }
```

##### contains(CharSequence charSequence)方法(方法中引用了诸多其他的方法):

​	a)``其中的参数是实现了CharSequence接口的String/StringBuilder/StringBuffer``

​	b)``contains查找的必须是连续的``

```java
	/**
     * Returns true if and only if this string contains the specified
     * sequence of char values.
     *
     * @param s the sequence to search for
     * @return true if this string contains {@code s}, false otherwise
     * @since 1.5
     */
    public boolean contains(CharSequence s) {
        return indexOf(s.toString()) > -1;
    }
```



##### format()方法:

​	``使用了可变参数(...),jdk1.5的新特性``

```java
 	public static String format(String format, Object... args) {
        return new Formatter().format(format, args).toString();
    }
```

 	实例:

```java
int inta = 10, intb = 20;
System.out.println(String.format("%d+%d=%d", inta, intb, inta + intb));
```



##### getBytes()方法:

​                 ``将String转换成byte类型的数组``

```java
/*得到的aByte是Unicode码*/
String str00 = "Hello Earth";
  byte[] bytes = str00.getBytes();
  for (byte aByte : bytes) {
       System.out.println(aByte);
  }

/*接收数据流*/
 String str = new String(bytes, 0, bytes.length);
   System.out.println(str);
```



##### indexOf()方法:

​	``注意:a)参数可接受char(char会自动类型转换成int),int,string类型 ``

```java
// 返回指定字符在此字符串中第一次出现处的索引
	indexOf(int ch)
// 返回在此字符串中第一次出现指定字符处的索引，从指定的索引开始搜索
	indexOf(int ch, int fromIndex)
// 返回指定子字符串在此字符串中第一次出现处的索引
    indexOf(String str)
// 返回指定子字符串在此字符串中第一次出现处的索引，从指定的索引开始
    indexOf(String str, int fromIndex)
```



##### lastIndexOf()方法:

​	``注意:lastIndexOf的查询是 从右往左 进行``

```Java
 	/*查找ch最后一次出现处的索引*/
		lastIndexOf(int ch)
    /*从fromIndex下标处开始查找,最后一次出现ch的索引*/
    	lastIndexOf(int ch, int fromIndex)
    /*查找str最后一次出现处的索引*/
        lastIndexOf(String str)
    /*从fromIndex下标处开始查找,最后一次出现str的索引*/
        lastIndexOf(String str, int fromIndex)       
```

> >indexOf()源码:
> >
> >````java
> > /**
> >     * Returns the index within this string of the first occurrence of the
> >     * specified character, starting the search at the specified index.
> >     */
> >    public int indexOf(int ch, int fromIndex) {
> >        final int max = value.length;
> >        if (fromIndex < 0) {
> >            fromIndex = 0;
> >        } else if (fromIndex >= max) {
> >            // Note: fromIndex might be near -1>>>1.
> >            return -1;
> >        }
> >
> >        if (ch < Character.MIN_SUPPLEMENTARY_CODE_POINT) {
> >            // handle most cases here (ch is a BMP code point or a
> >            // negative value (invalid code point))
> >            final char[] value = this.value;
> >            for (int i = fromIndex; i < max; i++) {
> >                if (value[i] == ch) {
> >                    return i;
> >                }
> >            }
> >            return -1;
> >        } else {
> >            return indexOfSupplementary(ch, fromIndex);
> >        }
> >    }
> >````



> >  lastIndexOf()源码:
> >
> > ````java
> > /**
> >      * Returns the index within this string of the last occurrence of
> >      * the specified character, searching backward starting at the
> >      * specified index. 
> >      */
> >     public int lastIndexOf(int ch, int fromIndex) {
> >         if (ch < Character.MIN_SUPPLEMENTARY_CODE_POINT) {
> >             // handle most cases here (ch is a BMP code point or a
> >             // negative value (invalid code point))
> >             final char[] value = this.value;
> >             int i = Math.min(fromIndex, value.length - 1);
> >             for (; i >= 0; i--) {
> >                 if (value[i] == ch) {
> >                     return i;
> >                 }
> >             }
> >             return -1;
> >         } else {
> >             return lastIndexOfSupplementary(ch, fromIndex);
> >         }
> >     }
> > ````



##### length()方法:

````java
	/**
     * Returns the length of this string.
     * The length is equal to the number of <a href="Character.html#unicode">Unicode
     * code units</a> in the string.
     *
     * @return  the length of the sequence of characters represented by this
     *          object.
     */
    public int length() {
        return value.length;
    }
````



##### replace()方法:

​	``a) replace(char oldChar, char newChar) 将后者替换前者``

​	``b) replace(CharSequence target, CharSequence replacement) 将后者替换前者``

> > 源码:
> >
> > ````java
> > 	/**
> >      * Returns a string resulting from replacing all occurrences of
> >      * {@code oldChar} in this string with {@code newChar}.
> >      */
> >     public String replace(char oldChar, char newChar) {
> >         if (oldChar != newChar) {
> >             int len = value.length;
> >             int i = -1;
> >             char[] val = value; /* avoid getfield opcode */
> > 
> >             while (++i < len) {
> >                 if (val[i] == oldChar) {
> >                     break;
> >                 }
> >             }
> >             if (i < len) {
> >                 char buf[] = new char[len];
> >                 for (int j = 0; j < i; j++) {
> >                     buf[j] = val[j];
> >                 }
> >                 while (i < len) {
> >                     char c = val[i];
> >                     buf[i] = (c == oldChar) ? newChar : c;
> >                     i++;
> >                 }
> >                 return new String(buf, true);
> >             }
> >         }
> >         return this;
> >     } 	
> > ````



##### replaceAll()方法:

````java
public String replaceAll(String regex, String replacement) {
        return Pattern.compile(regex).matcher(this).replaceAll(replacement);
    }
````



##### substring()方法:

​	``a) substring(int beginIndex) 从指定位置截取直到结束``

​	``b) substring(int beginIndex, int endIndex)从指定的位置截取,到指定位置结束 ``

````java
public String substring(int beginIndex, int endIndex) {
    	/*判断开始下标*/
        if (beginIndex < 0) {
            throw new StringIndexOutOfBoundsException(beginIndex);
        }
    	/*判断结束下标*/
        if (endIndex > value.length) {
            throw new StringIndexOutOfBoundsException(endIndex);
        }
    	/*设置变量*/
        int subLen = endIndex - beginIndex;
    	/*判断参数输入是否合法*/
        if (subLen < 0) {
            throw new StringIndexOutOfBoundsException(subLen);
        }
    	/*如果截取的是原数组,返回this;否则返回新数组,长度是subLen*/
        return ((beginIndex == 0) && (endIndex == value.length)) ? this
                : new String(value, beginIndex, subLen);
    }
````

##### toCharArray()方法:

> > 源码:
> >
> > ```Java
> > /**
> >  * Converts this string to a new character array.
> >  */
> > public char[] toCharArray() {
> >     // Cannot use Arrays.copyOf because of class initialization order issues
> >     char result[] = new char[value.length];
> >     System.arraycopy(value, 0, result, 0, value.length);
> >     return result;
> > }
> > ```



##### String类型总结:

````注意
注意:
	a) 如果等号右侧有=号,则开辟了堆内存空间
	b) 如果等号右侧有变量参与运算,则开辟了堆内存空间
````





#### StringBuffer类型:

```Java
public final class StringBuffer
extends Object
implements Serializable, CharSequence

/*
	1.JDK1.0版线程安全,可变的字符序列.字符串缓冲区就像一个可以更改的String
	2.重安全,轻效率
*/
```

> > StringBuffer源码:
> >
> > ````java
> > /**
> >      * Constructs a string buffer initialized to the contents of the
> >      * specified string. The initial capacity of the string buffer is
> >      * {@code 16} plus the length of the string argument.
> >      */
> >     public StringBuffer(String str) {
> >         super(str.length() + 16);
> >         append(str);
> >     }
> > 
> >  /**
> >      * Constructs a string buffer that contains the same characters
> >      * as the specified {@code CharSequence}. The initial capacity of
> >      * the string buffer is {@code 16} plus the length of the
> >      * {@code CharSequence} argument.
> >      */
> >     public StringBuffer(CharSequence seq) {
> >         this(seq.length() + 16);
> >         append(seq);
> >     }
> > 
> > /**
> > 	StringBuffer的构造方法调用了父类的带参构造:
> > 		//因为value没有final修饰,所以字类的引用时可以改变
> > 	    char[] value;
> > 		AbstractStringBuilder(int capacity) {
> >         value = new char[capacity];
> >     	}
> > **/
> > ````



##### StringBuffer的扩容原理:

````java
	/*判断存放字符所需要的最小容量*/
	public void ensureCapacity(int minimumCapacity) {
        if (minimumCapacity > 0)
            ensureCapacityInternal(minimumCapacity);
    }

    /*通过数组copy,创建一个新的char类型的数组,将引用赋值给value*/
    private void ensureCapacityInternal(int minimumCapacity) {
        // overflow-conscious code
        if (minimumCapacity - value.length > 0) {
            value = Arrays.copyOf(value,
                    newCapacity(minimumCapacity));
        }
    }

   	/*计算新容量的方法*/
    private int newCapacity(int minCapacity) {
        // overflow-conscious code
        int newCapacity = (value.length << 1) + 2;//新容量为数组长度的2倍+2
        if (newCapacity - minCapacity < 0) {
            newCapacity = minCapacity;
        }
        return (newCapacity <= 0 || MAX_ARRAY_SIZE - newCapacity < 0)
            ? hugeCapacity(minCapacity)
            : newCapacity;
    }
````



#### StringBuilder类型:

```java
public final class StringBuilder
extends Object
implements Serializable, CharSequence
```

##### 1.底层原理:

​	a) StringBuffer和StringBuilder底层的数据结构也是char类型的数组

​	

## 二. 数组

#### 1.数组的插入元素

> > ###### 基础的插入(具有数字大小排序)
> >
> > ````java
> > 	/*原数组*/
> >         int[] arr1 = {1,3,5,7,9,11};
> >         /*新数组*/
> >         int[] arr2 = new int[arr1.length + 1];
> >         /*复制填充新数组*/
> >         for (int i = 0; i < arr1.length; i++) {
> >             arr2[i] = arr1[i];
> >         }
> > 
> >         Scanner sc = new Scanner(System.in);
> >         System.out.println("输入任意数字:-->");
> >         /*输入数字*/
> >         int number = sc.nextInt();
> >         int index = arr2.length - 1;
> > 
> >         /*按照元素的大小插入*/
> >         for (int i = 0; i < arr2.length; i++) {
> >             if (arr2[i] > number) {
> >                 /*找到元素中第一个大于输入的数*/
> >                 index = i;
> >                 break;
> >             }
> >         }
> > 
> >         /*移动位置:从旧数组的最后一位开始,往前看到大数位置为止*/
> >         for (int i = arr1.length - 1; i >= index; i--) {
> >             /*从大数开始依次往后移位*/
> >             arr2[i + 1] = arr2[i];
> >         }
> > 
> >         /*将输入的数赋值给原大数位置*/
> >         arr2[index] = number;
> >         
> >         System.out.println(Arrays.toString(arr2));
> > ````

``添加元素:``

​	``a) 需要大量的移动元素``

​	``b) 元素后移``

#### 2.数组的删除元素

> > 基础删除:
> >
> > ````java
> > 	private static void DeleteArray() {
> >         /*旧数组*/
> >         int[] arr1 = {465, 7, 867, 9854, 658, 898, 97, 3};
> >         /*新数组*/
> >         int[] arr2 = new int[arr1.length - 1];
> >         Scanner sc = new Scanner(System.in);
> >         System.out.println("输入要找的数字:--->");
> >         int number = sc.nextInt();
> >         int index = -1;
> >         
> >         /*遍历查找需要的数字,并且记录下标*/
> >         for (int i = 0; i < arr1.length; i++) {
> >             if (number == arr1[i]) {
> >                 index = i;
> >                 break;
> >             }
> >         }
> > 
> >         /*如果下标合法,从最后一位开始,往前移动,直到要删除的数字下标*/
> >         if (index != -1) {
> >             for (int i = index; i < arr1.length - 1; i++) {
> >                 arr1[i] = arr1[i + 1];
> >             }
> >             /*赋值新数组*/
> >             for (int i = 0; i < arr1.length - 1; i++) {
> >                 arr2[i] = arr1[i];
> >             }
> >             System.out.println(Arrays.toString(arr2));
> >         } else {
> >             System.out.println("元素不存在.....");
> >         }
> >     }
> > ````
> >
> > ``1) 先查询元素存不存在``
> >
> > ``2)删除数字``
> >
> > ``3) 重新赋值``



#### 3.排序

> > ``比较排序:``
> >
> > ​	插入排序	:	直接插入排序/希尔(Shell)排序
> >
> > ​	选择排序:	直接选择排序/堆排序
> >
> > ​	交换排序:	冒泡排序/快速排序
> >
> > ​	归并排序:	归并排序
> >
> > ``非比较排序:``
> >
> > ​	计数排序
> >
> > ​	基数排序

​	

* 一般称为八大排序类型:

​	![1545801759896](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1545801759896.png)

`` 最坏情况:``

​	a).原数组本身就是有序

``最坏情况:``

​	a).原素组是倒序

``平均情况:``

​	a).平均运行时间也就是从概率的角度看，这个数字在每一个位置的可能性是相同的



**优先选择算法:**  

``直接插入,简单选择,冒泡排序,快速排序``

*冒泡排序:*

​	特点: 1).俩俩相比,小靠前;

​		 2).第一轮比较之后,最大值已经在最后;

​	         3).一共比较 Arrays.length-1 次	;

​		 4).无论是否有序,都要进行比较(缺点);

**冒泡代码:**

````java
	int[] arr = {99, 88, 77, 66, 55, 44, 33, 22, 11};
        for (int i = 0; i < arr.length - 1; i++) {
            for (int j = 0; j < arr.length - 1 - i; j++) {
                int temp;
                if (arr[j] > arr[j + 1]) {
                    temp = arr[j + 1];
                    arr[j + 1] = arr[j];
                    arr[j] = temp;
                }
            }
        }
````



#### 4.Arrays工具类

##### toString():

````java
	/**
     * Returns a string representation of the contents of the specified array.
     */
    public static String toString(int[] a) {
        if (a == null)
            return "null";
        int iMax = a.length - 1;
        if (iMax == -1)
            return "[]";

        StringBuilder b = new StringBuilder();
        b.append('[');
        for (int i = 0; ; i++) {
            b.append(a[i]);
            if (i == iMax)
                return b.append(']').toString();
            b.append(", ");
        }
    }
````

> > ``返回封装好的toString,结果是带[],例如:[1, 5, 9, 7, 6, 3, 5, 4, 2]``



**binarySearch()方法[二分查找]:**

> > 源码:
> >
> > ````Java
> >  /**
> >      * Searches the specified array of ints for the specified value using the
> >      * binary search algorithm.  The array must be sorted (as
> >      * by the {@link #sort(int[])} method) prior to making this call.  If it
> >      * is not sorted, the results are undefined.  If the array contains
> >      * multiple elements with the specified value, there is no guarantee which
> >      * one will be found.
> >      */
> >     public static int binarySearch(int[] a, int key) {
> >         return binarySearch0(a, 0, a.length, key);
> >     }
> > 
> > /*二分法查找,取中间值比较*/
> > 	// Like public version, but without range checks.
> >     private static int binarySearch0(int[] a, int fromIndex, int toIndex,
> >                                      int key) {
> >         int low = fromIndex;//起始index
> >         int high = toIndex - 1;//结束index
> >         
> >         while (low <= high) {
> >             int mid = (low + high) >>> 1;/*无符号向右移动1位*/
> >             int midVal = a[mid];//取值中间index
> >             if (midVal < key)//如果要查找的数字大
> >                 low = mid + 1; //则中间index+1表示最小index
> >             else if (midVal > key)//若要查找的数字小
> >                 high = mid - 1;//则index-1表示最大的index
> >             else
> >                 return mid; // key found
> >         }
> >         return -(low + 1);  // key not found.
> >     }
> > ````



##### copyOf()方法:

```java
	//参数1是int数组,参数2是新数组的长度
	//若newArr的长度大于oldArr,后面的位置填充0;否则oldArr多出的部分,将会被截断,再填充至newArr中
	public static int[] copyOf(int[] original, int newLength) {
        int[] copy = new int[newLength];
        System.arraycopy(original, 0, copy, 0, Math.min(original.length, newLength));
        return copy;
    }

	/*arraycopy来自于System类,native表示该arraycopy方法实现来自于非Java语言*/
	public static native void arraycopy(Object src,  int  srcPos,
                                        Object dest, int destPos,
                                        int length);
```



##### equals()方法:

> > 注意:
> >
> > ​	1).比较的是堆中的值是否相等;
> >
> > ​	2).元素和顺序完全一样,才会返回true

````Java
    /**
     * Returns <tt>true</tt> if the two specified arrays of ints are
     * <i>equal</i> to one another.  Two arrays are considered equal if both
     * arrays contain the same number of elements, and all corresponding pairs
     * of elements in the two arrays are equal.  In other words, two arrays
     * are equal if they contain the same elements in the same order.  Also,
     * two array references are considered equal if both are <tt>null</tt>.<p>
     */
    public static boolean equals(int[] a, int[] a2) {
        /*地址相等,值一定相等*/
        if (a==a2)
            return true;
        /*如果有个数组为null,不相等*/
        if (a==null || a2==null)
            return false;
		/*判断数组长度是否相等*/
        int length = a.length;
        if (a2.length != length)
            return false;
		/*判断数组中的元素是否相等*/
        for (int i=0; i<length; i++)
            if (a[i] != a2[i])
                return false;
        return true;
    }
````



##### fill()方法:

``数组元素的覆盖方法``

````Java
	/**
     * Assigns the specified int value to each element of the specified array
     * of ints.
     *
     * @param a the array to be filled
     * @param val the value to be stored in all elements of the array
     */
    public static void fill(int[] a, int val) {
        //int len = a.length; 
        for (int i = 0, len = a.length; i < len; i++)
            a[i] = val;//全部以第二个参数填充
    }
````



#### 5.二维数组

> > 定义:
> >
> > ````Java
> > /*数组中包含数组*/	
> > int[] arr = {11,22,33,44};
> >         int[] arr2 = {55,666,777,8888,9999};
> >         int[][] arr3 = {arr,arr2};
> >         for (int[] ints : arr3) {
> >             System.out.println(Arrays.toString(ints));
> >         }
> > ````
> >
> > ``内存结构``
> >
> > ![1545814260214](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1545814260214.png)



