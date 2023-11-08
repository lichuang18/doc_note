# fio使用记录
## 参数
+ 负载分布 例如random_distribution=zipf:0.5  
+ 随机读写的起始范围从offset（起始lba）到offset+size（终止lba）
+ io_size：在这段size中产生多少io，如果不指定io_size，io_size默认等于size
+ norandommap



+ 其他不做过多介绍， 可查看fio的官方文档说明https://fio.readthedocs.io/en/latest/fio_doc.html#job-file-parameters 
+ 


```
```


### 参考资料：
+ https://www.jianshu.com/p/94ac980ed29e 
+ 