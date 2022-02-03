# 细节

* break，continue语句出现在多层嵌套语句块中，可以通过**标签**指明要终止是哪个一层语句块

  ```java
  label1:
  for(int i = 0;i <= 10;i++){
      label2:
      for(int j = 0;j <= 10;j++){
          break;// 等价于 break label2;
          continue; // 等价于 continue label2;
      }
  }
  ```