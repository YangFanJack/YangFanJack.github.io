# java-注解


## 什么是注解

* 可以被其他程序读取
* 可以附加在package,class,method,field上都可以添加注解
* 可通过反射实现对注解中信息的访问
* 注解是jdk1.5后的新特性

## 注解作用

1. 编写文档：通过代码里标识的注解生成doc文档
2. 代码分析：通过代码里标识的注解堆代码进行分析（结合反射）
3. 编译检查：通过代码里标识的注解让编译器能实现基本的编译检查（@Override）

## 内置注解

* @Override：必须重写父类的方法
* @Deprecated：已过时的，不推荐使用，存在更好的方式
* @SuppressWarning：镇压警告

## 元注解

* 负责解释其他注解的注解：Java定义了4个标准的meta-annotation类型，以供其他的annotation类型做说明
* @target：描述注解使用范围
* @Retention：描述什么级别保存该注释信息，用于描述注解的生命周期(SOURCE<CLASS<RUNTIME)
* @Documented：说明该注解将被包含在javadoc中
* @Inherited：说明子类可以继承父类中的该注解

```java
@Target(value={ElementType.METHOD,ElementType.TYPE})
@Retention(value=RetentionPolicy=RUNTIME)
@Documented
@Inherited
@interface MyAnnotation(){
    
}
```

## 自定义注解

* @interface用来声明一个注解
* 注解体中的每一个方法实际就是一个配置参数，方法名就是参数名，返回值类型就是参数的类型（返回值只能是Class,String,enum）
* 可以通过default来声明参数的默认值
* 只有一个参数，参数名是value，赋值时可省略参数名
* 注解元素必须要有值，我们定义注解元素时，经常使用空字符串0作为默认值

```java
public class Test{
    //注解可以显式赋值，如果没有默认值必须给注解赋值
    @MyAnnotation(name = "JackYang",school={"西部开源","清华大学"})
    public void test(){}
}


@Target(value={ElementType.METHOD,ElementType.TYPE})
@Retention(value=RetentionPolicy=RUNTIME)
@interface MyAnnotation(){
   	//注解的参数：参数类型+参数名
    String name() default "";
    int age() default 0;
    int id() default -1;//默认值是-1代表不存在
    String[] school();
}
```

