
如下是一个If语句的文法

```
Stmt -> If | Other 
If ->
	'if' Expr Stmt |
	'if' Expr Stmt 'else' Stmt
Expr -> 'Expr' 
Other -> 'Other' 
```

显然,对于 `if xxx` 或 `if xxx else xxx`的情况,可直接由 `<If> ` 的两个产生式推导得到. 

但是,对于嵌套if,和 连续`else if` 的情况

**嵌套if**

If 文法应当能保证`else`的归属遵循就近原则.

```
if xxx
	if xxx
	else xxx
```

对于上面的句型,在某个时刻,必然存在如下两个项:

```
If -> 'if' Expr Stmt
If -> 'if' Expr Stmt else Stmt
```

由于后续输入是 `if` ,将 `If`代入`Stmt`得到:

```
If -> 'if' Expr· Stmt
If -> 'if' Expr· Stmt else Stmt
If -> ·'if' Expr Stmt
If -> ·'if' Expr Stmt else Stmt
```

在消费`if`(即 `Shift if`)后, 进入下一个状态

```
If -> 'if' · Expr Stmt
If -> 'if' · Expr Stmt else Stmt
```

这里我们按一下「快进」, 因为后面有一段相同的「路」, 而我们关心的是最终的「交叉口」

```
If -> 'if' Expr Stmt ·
If -> 'if' Expr Stmt · else Stmt
```

显然, 在后续输入为 `else` 的情况, 我们应该选择移入(走第二条路), 那么什么时候应该选择第一条路呢?

这涉及到不同LR算法对展望符的确定策略,描述起来内容过多这里不作展开.  仅仅给一个提示,回想一下,上一次代入`If`是什么时候?

上一次代入时,我们用 `If` 去替换了 `if ... <Stmt> else ...` 中的`Stmt`, 显然的, 在遇到 `else` 时,也可选择归约.

这是典型的移入归约冲突, 更具体的讲, 如果选择规约,`else`将属于外层if

```
(	
	if Expr 
		(if Expr Other) 
	else Other
)
```

如选择移入,`else`将属于内层if, 这是我们想要的


```
(	
	if Expr 
	( if Expr Other else Other ) 
)
```

因此, 我们应该将 `If` 设置为右结合.