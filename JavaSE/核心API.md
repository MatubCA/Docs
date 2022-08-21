# 核心API

## 枚举类

### 为什么使用枚举？

枚举用于定义一些固定的值。当然也可以使用常量，但是常量在某些场景中并不适用，或者可能并不那么好用，所有我们将枚举这个概念抽象出来，形成了枚举类。

常量能做的，枚举可以做，常量不能做的，枚举也能做。

特点：

- 枚举类被`final`修饰，因此枚举类不能被继承；
- 枚举类默认继承了`Enum`类，java不支持多继承，因此枚举类不能继承其他类；
- **⭐每一个枚举项都是该枚举类的一个对象**
- 枚举类的构造器是`private`修饰的，因此其他类不能通过构造器来获取对象；
- 枚举类的成员变量是`static`修饰的，可以用类名.变量来获取对象；

- 枚举项必须在第一行
- 枚举类中可以定义抽象方法，有抽象方法必须重写



### 定义枚举

在枚举项中没有书写get、set方法

```java
public enum Season_Enum {
    SPRING("春"){
        @Override
        void show() {
            System.out.println("Spring");
        }
    },SUMMER("夏"){
        @Override
        void show() {
            System.out.println("Summer");
        }
    },AUTUMN("秋"){
        @Override
        void show() {
            System.out.println("Autumn");
        }
    },WRITE("冬"){
        @Override
        void show() {
            System.out.println("Write");
        }
    };

    private String name;

    Season_Enum(String name) {
        this.name = name;
    }

    abstract void show();
}
```



