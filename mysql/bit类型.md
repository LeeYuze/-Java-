# 代码

```mysql
CREATE TABLE t1 (
	num bit(8) # 1～64
)
INSERT INTO t1 VALUES (8)
SELECT * from t1

// 00001000
```



# 细节

* bit 字段显示时，按照位的方式显示
* 查询的时候仍然可以使用 添加的数值
* 如果一个值只有0，1可以考虑使用 bit(1)，可以节约空间
* 位类型，M指定位数，默认值1，范围1～64
* 使用不多