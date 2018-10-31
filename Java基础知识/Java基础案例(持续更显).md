## 自动拆装箱中的比较

```Java
   		Integer a=111;
        Integer b=222;
        Integer c=333;
        Long g=333L;
        System.out.println(c==a+b);
        System.out.println(c.equals(a+b));
        System.out.println(g==a+b);
        System.out.println(g.equals(a+b));
输出结果：true/true/true/false
```

对于包装类型的`==`放遇到算数运算的时候会自动拆箱，所以例子中的两个`==`运算是纯粹比较数值

包装类的`equals()`方法，首先进行一次类型判断，然后比较数值大小，如果类型不同直接返回false**(这是个坑)**

例如：Integer.equals()

```java
public boolean equals(Object obj) {
    //比较的前提是类型完全一样    
    if (obj instanceof Integer) {
            return value == ((Integer)obj).intValue();
        }
        return false;
}
```





