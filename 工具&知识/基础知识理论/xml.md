XML---Extensible Markdown Language
- 标记语言   
通过添加标签的饭是钢hi为数据赋予意义
- 可拓展性  
xml与html不同，并没有规定标签的种类，使用者可以自由的创建标签
- xml是元语言，通过xml只要遵循xml的规范，就能够产生新的语言  
- 为了避免不同机构对于同一个标签的意义的定义不同，可以给xml的标签指定命名空间  
`<cat xmlns="http://www.cat.com">it a cat</cat>`
- 严格规定xml文件的格式  
xml文件使用的时候，还不不希望能够随心所遇的写，可以使用dtd文件或者xml schema进行格式的指定  
比如某一个标签下面一定要有某些其他的标签等等。  
```
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
```
- 解析XML的组件——DOM、SAX
- 本质上xml就是一种通用的数据交换的格式，现在用的比较多的是JSON