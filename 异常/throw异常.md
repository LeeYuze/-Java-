# 介绍

> 手动生成异常对象的关键字

```java
// 连上自定义异常
public class throw01 {
    public static void main(String[] args) {
        int age = 120;
        if (!(age >= 18 && age <= 120)) {
            throw new AgeException("年龄错误");
        }
        System.out.println("年龄正确");
    }
}

class AgeException extends RuntimeException {
    public AgeException(String message) {
        super(message);
    }
}
```

