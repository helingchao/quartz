---
tags: TIL
author: lche
alias: 
---

#### 理解优化器
对于一个数据库查询 (Query), 可以有不同的执行路径 (每种路径的查询结果是相同的), 类似从出发从地点 A 到 地点 B, 可以规划不同的路径.

不同的查询执行路径, 执行的效率 (例如执行时间) 是不同的.

##### 什么是优化器
优化器的输入是一个 Query 语句, 输出是一个最优的 (almost)、可执行的查询计划.

优化器会对 Query 语句做逻辑优化和物理优化, 前者遵循 [[关系代数]] 的 [[关系代数等价变换规则对（查询）优化的指导意义|等价变换规则]] 对查询语句进行重写实现逻辑优化, 后者依据代价估算模型找到最优的查询执行路径.

##### 为什么需要优化器
优化器的作用是生成最优的查询执行计划, 随后执行器只需按照计划即可完成查询任务.

优化器的作用类似于高级语言编译器, 高级语言编译器的作用是将源代码文件 (高级语言命令) 编译成可执行程序, 优化器是将 SQL 命令编译成执行计划.

相比于手工编写的执行计划, 优化器可以更高效的生成、更优的查询计划.

##### 如何实现优化器 (优化器是如何工作的)
优化器的两个主要工作:[[逻辑优化]],[[物理优化]].

逻辑优化过程, 是一个 transform、rewrite SQL 语句的过程.

物理优化的过程, 使用代价估算模型、启发式规则, DP 算法、遗传算法, 找到最优的查询路径.

#### Innodb Insert 基本流程

##### Cursor 搜索
- dtuple
- rec_t
- 4 种 search mode:
	- PAGE_CUR_G: > 查询，查询第一个大于 dtuple 的 rec_t 
	- PAGE_CUR_GE: >=，> 查询，查询第一个大于等于 dtuple 的 rec_t 
	- PAGE_CUR_L: < 查询，查询最后一个小于 dtuple 的 rec_t 
	- PAGE_CUR_LE: <= 查询，查询最后一个小于等于 dtuple 的 rec_t
- cursor 定位流程
![[../../attachments/Pasted image 20230704173045.png]]

- btr_cur_search_to_nth_level(cursor 搜索实现)
	- page_cur_search_with_match

##### 并发控制
- mtr(mini-transaction)
- 用户线程, 在操作 btree 前开启一个 mtr, 操作 btree 过程中使用的资源 (latch, redo log, page ptr) 都挂载在 mtr 上管理.

> [!summary] mtr 思路类似于 shadow page ?

- life cycle of mtr
![](https://pic1.zhimg.com/80/v2-0a0034314587d9bb13221357f9831bc4_1440w.webp)
- 8.0 latch 避免死锁的策略.
	- 自顶向下, 自左向右
	- 如果需要访问上层的、左边的 page, 都需要从 root 开始重新开始.
- 修改数据时, 乐观、悲观两种 latch 策略, 复习 [[../../15-445@Lecture 09  Index Concurrency Control|15-445@Lecture 09  Index Concurrency Control]]
![](https://pic2.zhimg.com/80/v2-2e8f951cbb2ca71080bdd8ecdfa5e7b5_1440w.webp)

##### Insert 路径
insert 一条元组的基本流程:
- 构建元组的内存版本 (row, dtuple type)
- 构建 page 存储的版本 (entry, dtuple type)
- 使用 cursor 找到合适的位置,写入 page.

##### Insert 路径,主键索引
- row_ins_clust_index_entry
- 先乐观插入
	- 插入失败, 悲观插入.
- 分裂处理.
	- btr_page_split_and_insert
	- 分裂策略
		- 50%.
		- 顺序插入优化.

##### Insert 路径, 二级索引
基本流程类似于主键索引.

## Related
- [[../../数据库优化器|数据库优化器]]
- [[../../Innodb Insert 基本流程|Innodb Insert 基本流程]]