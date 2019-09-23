# MySQL
## 索引(index)
+ 主键索引
+ 唯一索引unique
  + 唯一索引列的值必须唯一,但允许为空值 
+ 普通索引 index
  + 最基本的索引
+ 组合索引
  + 一个索引包含多个列
  + index_group(id,name,age)
  + 索引顺序: id,(id,name),(id,name,age)
+ 全文索引 fulltext

## 连接 join
+ 