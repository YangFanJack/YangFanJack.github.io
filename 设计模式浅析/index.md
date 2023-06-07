# 设计模式浅析

# what is design pattern?
设计模式Design Pattern是一套代码设计经验的总结

代码中合理的运用设计模式可以解决很多问题

设计模式的三大类：
1. 创建型模式(Creational Pattern)：在软件设计中对象的创建和对象的使用是分开的，因为对象的创建会消耗掉系统的很多资源，所以单独对对象的创建进行研究，从而能够高效地创建对象就是创建型模式要探讨的问题。

   单例、工厂方法、抽象工厂、建造者、原型

2. 结构型模式(Structural Pattern)：在解决了对象的创建问题之后，对象的组成以及对象之间的依赖关系就成了开发人员关注的焦点，因为如何设计对象的结构、继承和依赖关系会影响到后续程序的维护性、代码的健壮性、耦合性等。

   适配器、桥接、组合、装饰、外观、享元、代理

3. 行为型模式(Behavioral Pattern)：类和对象如何交互&划分责任

   访问者、模板、策略、状态、观察者、备忘录、中介者、迭代器、解释器、命令、责任链

# 设计原则：

1. 开闭：开放扩展，关闭修改
2. 里氏替换：任何地方子类都能替换父类
3. 依赖倒置：高层低层都依赖于抽象
4. 单一职责：一个类只负责一项职责
5. 接口隔离：使用多个专门的接口比使用单一的总接口要好
6. 迪米特：将模块间的依赖和相互影响降到最低
7. 合成复用：能用关联就别用继承

# Creational Pattern
## Simple Factory
* 实质：由一个工厂类根据传入的参数，动态决定应该创建哪一个产品类（这些产品类继承自一个父类或接口）的实例
* 包含的角色&职责：
  1. 工厂(Creator)：负责实现创建所有实例的内部逻辑。工厂类的创建产品类的方法可以被外界直接调用，创建所需的产品对象
  2. 抽象产品(Product)：所有具体产品类的父类，它负责描述所有实例所共有的公共接口
  3. 具体产品(Concrete Product)：该模式的创建目标，创建的具体实例对象
* 代码实现：
  ```java
    public interface Digital {
        void use();
    }

    public class PadDigital implements Digital {
        @Override
        public void use() {
            System.out.println("product pad...");
        }
    }

    public class PhoneDigital implements Digital {
        @Override
        public void use() {
            System.out.println("product phone...");
        }
    }

    public class Factory {
        public Digital product(String name){
            switch (name){
                case "pad":
                    return new PadDigital();
                case "phone":
                    return new PhoneDigital();
                default:
                    return null;
            }
        }
    }

    public class TestMain {
        public static void main(String[] args) {
            Factory factory = new Factory();
            Digital pad = factory.product("pad");
            Digital phone = factory.product("phone");
            pad.use();
            phone.use();
        }
    }

  ```
* 适用场景：
  1. 工厂类创建的对象较少,种类比较固定的场景
  2. 客户端对具体创建的对象没有特殊要求,只需要使用对象的场景
  3. 对象创建仅需要一两个参数,依赖关系较简单的场景
  4. 存在一对一的对应关系,一个参数对应一个具体产品的场景
  5. 将来可能增加对象的概率较低,新增对象也较少的场景

## Factory
* 实质：定义一个创建产品对象的工厂接口，将实际创建工作推迟到子工厂类当中。核心工厂类不再负责产品的创建，这样核心类成为一个抽象工厂角色，仅负责具体工厂子类必须实现的接口，这样进一步抽象化的好处是使得工厂方法模式可以使系统不修改具体工厂角色的情况下引进新的产品。
* 包含的角色&职责：
  1. 抽象工厂(Creator)：核心，任何具体工厂类都必须实现这个接口
  2. 具体工厂(Concrete Creator)：抽象工厂的一个实现，负责实例化产品对象
  3. 抽象产品(Product)：所有具体产品类的父类，它负责描述所有实例所共有的公共接口
  4. 具体产品(Concrete Product)：创建的具体实例对象
* 代码实现；
  ```java
    public interface Digital {
        void use();
    }

    public class PadDigital implements Digital {
        @Override
        public void use() {
            System.out.println("product pad...");
        }
    }

    public class PhoneDigital implements Digital {
        @Override
        public void use() {
            System.out.println("product phone...");
        }
    }

    public interface Factory {
        Digital product();
    }

    public class PadFactory implements Factory {
        @Override
        public Digital product() {
            System.out.println("product pad...");
            return new PadDigital();
        }
    }

    public class PhoneFactory implements Factory{
        @Override
        public Digital product() {
            System.out.println("product phone...");
            return new PhoneDigital();
        }
    }

    public class TestMain {
        public static void main(String[] args) {
            Factory factory = new PadFactory();
            Digital pad = factory.product();
            factory = new PhoneFactory();
            Digital phone = factory.product();
            pad.use();
            phone.use();
        }
    }

  ```
* 适用场景：
  1. 适用于创建对象种类较多的场景。通过定义多个工厂子类来创建不同的对象,更易扩展
  2. 可以有更加复杂的判断逻辑来决定创建何种具体产品。可以根据复杂的参数或配置来决定具体创建的产品类型
  3. 通过定义抽象工厂类和多个具体工厂子类,很容易增加新的具体工厂和产品来扩展系统
* 对比：
  * 简单工厂模式适用于产品种类较少、创建逻辑简单、对扩展性要求不高的场景。用户可以直接使用,无需知晓具体产品信息
  * 工厂方法模式更加灵活和扩展性强,可以根据复杂的判断逻辑来创建不同的产品对象。适用于产品种类较多且需要灵活扩展的场景。用户一般需要知晓具体的产品和工厂类型

## Abstract Factory
* 实质：
* 包含的角色&职责：
* 代码实现：
* 适用场景：
* 对比：

## Singleton
* 实质：
* 包含的角色&职责：
* 代码实现：
* 适用场景：
* 对比：

## Builder
* 实质：
* 包含的角色&职责：
* 代码实现：
* 适用场景：
* 对比：

## ProtoType
* 实质：
* 包含的角色&职责：
* 代码实现：
* 适用场景：
* 对比：

# Structural Pattern
## Adapter

## Bridge

## Composite

## Decorator

##辅导费
