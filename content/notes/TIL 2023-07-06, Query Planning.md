---
tags: TIL
author: lche
alias: 
---

#### Notes on Query Planning
- Background Info
	- SQL is declarative, 用户不指定执行查询的处理细节, 优化器决策/给出一个最优的执行计划.
	- IBM SYSTEM R 第一个实现了 query optimizer in 1970s.
	- Query Optimization is NP-HARD
- [[../../Heuristics Rules|Heuristics Rules]]
	- 基本思路是, rewrite query to remove stupid/inefficient things.
	- 实现方式是, pattern matching.
	- VS [[Cost-Based Search]].
- Query Planner Architecture
	- SQL Rewrite. (SQL 层面的 Rewrite) 可以是 view 的 rewrite、distribute table rewrite.
	- Query Rewrite. (Relation Algebra 层面的 Rewrite), Rewrite a inefficient Relation Algebra Expression(in short RAE) to a efficient RAE using [[Relational Algebra Equivalences]] Rules.
	- Logical Plan. logical RAE
	- Physical Plan, Operator. (一种理解)executable operator (in DBMS)with access path based on RAE.
	![[CleanShot 2023-04-11 at 10.28.49@2x.png]]
- [[Relational Algebra Equivalences]]
	- 2 [[Relational Algebra Expression]] are equivalent if they generate the same set of tuples. (关系表达式不是函数, 而是结果集)
	- Query Rewrite 应用其找到逻辑最优的 RAE.
- Query Rewrite
	- predicate pushdown
	- projection pushdown
	- Merging Predicates
	- Impossible / Unnecessary Predicates
	- Join .....
- pushdown 的本质
	- 尽量减少执行过程中产生的中间结果的数量和大小.
		- predicate pushdown 是提前过滤不需要的 tuples, 从而降低需要计算的中间结果的个数.
		- projection pushdown 是提前裁剪 tuples 的大小, 从而减小处理的中间结果的大小.

## Related
- [[../../15-445@Lecture 14 Query Planning & Optimization I|15-445@Lecture 14 Query Planning & Optimization I]]