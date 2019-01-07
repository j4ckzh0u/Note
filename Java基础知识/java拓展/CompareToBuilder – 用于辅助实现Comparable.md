- CompareToBuilder – 用于辅助实现Comparable.compareTo(Object)方法；

- EqualsBuilder – 用于辅助实现Object.equals()方法；

  ```
   public boolean equals(Object obj) {
     if (obj == null) { return false; }
     if (obj == this) { return true; }
     if (obj.getClass() != getClass()) {
       return false;
     }
     MyClass rhs = (MyClass) obj;
     return new EqualsBuilder()
                   .appendSuper(super.equals(obj))
                   .append(field1, rhs.field1)
                   .append(field2, rhs.field2)
                   .append(field3, rhs.field3)
                   .isEquals();
    }
  ```

  或者是通过反射机制进行处理，仅仅比较id和time两个域

  ```
  return  EqualsBuilder.reflectionEquals(this,obj,"id","time");
  ```

- HashCodeBuilder – 用于辅助实现Object.hashCode()方法；

  ```
  HashCodeBuilder.reflectionHashCode(this,"id","name");
  ```

- ToStringBuilder – 用于辅助实现Object.toString()方法；

- ReflectionToStringBuilder – 使用反射机制辅助实现Object.toString()方法；


