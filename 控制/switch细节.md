# 细节

* 表达式数据类型，应和case后的常量类型一致，或者是可以自动转成可以相互比较的类型，比如输入的是字符，而常量是int

* switch（表达式）中返回值必须是：（byte,short,char,int,Enum,String）

* case子句中的值必须是常（1,'1',"1"），而不是变量

* default子句是可选的，当没有匹配case时，执行default

* break语句用来在执行完一个case分支后使程序跳出switch语句块；如果没有写break，程序会按顺序执行到switch结尾，除非遇到break；

  ```java
  char c = 'a';
  switch(c){
      case 'a':
          System.out.println('ok1');
      default:
          System.out.println('ok2');
  }
  // ok1
  // ok2
  ```

  

# 穿透练习

```java
// 3,4,5春季 6,7,8 夏季 9,10,11 秋季 12,1,2 冬季
import java.util.*;
public class SwitchExercise {
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		int month = sc.nextInt();
		switch(month){
			case 3:
			case 4:
			case 5:
				System.out.println("春");
				break;
			case 6:
			case 7:
			case 8:
				System.out.println("夏");
				break;
			case 9:
			case 10:
			case 11:
				System.out.println("秋");
				break;
			case 12:
			case 1:
			case 2:
				System.out.println("冬");
				break;
		}
	}
}
```

