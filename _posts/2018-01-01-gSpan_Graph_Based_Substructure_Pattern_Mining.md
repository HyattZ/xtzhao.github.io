---
layout: post
---

**Abstract**

提出了一个不需要做候选生成的频繁子结构发现算法gSpan。gSpan算法在图上面建立一个新的字典序，并且将每个图映射到一个唯一的最小化DFS编码作为他的规范化编码。基于这种字典序，gSpan算法采用深度优先策略去有效的挖掘连通子图。有时候可以比之前的其他算法表现提高一个量级。

**Introduction**

频繁子结构挖掘是一个很重要问题。标记的图作为一个通用的数据结构可以用来建模很多数据中复杂的子结构模式。给定一个图数据集$D=\{G_0,G_1,...,G_n\}$，$support(g)$表示在数据集$D$中的子图$g$出现的数目。问题是要来找$support(g)\ge minSup$，为了减少问题的复杂度，只考虑频繁连通子图。

频繁子图挖掘的核心问题在于子图的同构测试。很多知名的pair-wise的同构测试算法被发明，但是，子图挖掘问题没有被很好的探索。AGM算法，发现所有的频繁子结构（包括连通子图和非连通子图）。FSG算法，使用图的邻接矩阵表示和边增长算法去发现连通频繁子图从化合物的数据集中，在6.5%的情况下，使用了10分钟。对于同样的任务，本文的算法只要10秒钟。

AGM和FSG都利用了Apriori的level-wise方法。在频繁子图挖掘问题中，基于Apriori的算法主要两大挑战：

+ 候选生成：从k的频繁子图生成k+1的子图候选更加的复杂和消耗更多的资源跟项集比例来的话
+ 剪枝那些假积极的候选（pruning false positive）：子图同构的判断是一个NP-complete问题

本文的贡献：

+ gSpan可以减少上述的提出的消耗。
+ 提出了DFS 字典序和最小DFS编码。第一次将深度优先搜索应用在频繁子图挖掘中。不需要去做候选生成和剪枝假积极的候选。

**DFS Lexicographic Order**

这部分介绍一些gSpan中的技巧，包括：

- 把一张图映射到DFS编码
- 建立一个字典序在这些编码中
- 基于字典序构建一棵搜索树

*DFS Subscripting* 在一张图中使用DFS，可以构建一棵DFS树。一张图可以有几个不同的DFS树。深度优先搜索可以将节点组成一个线性顺序。我们根据被发现时间的早晚，使用这种脚注标记这种顺序。$i < j$意味着$v_i$比$v_j$发现的要早。$v_0$表示的是根节点，$v_n$表示最右节点。从$v_0$到$v_n$的路径称为最右路径。

*Forward Edge and Backward Edge* 前向边包含所有的DFS树中的边，反向边包含所有不在DFS树中的边。使用$(i,j)$表示一条边，如果$i<j$是前向边，否则是反向边。一个线性序的定义$<_T$,假设$e_1 = (i_1,j_1),e2=(i_2,j_2)$：

- 如果$i_1= i_2 ,j_1 \lt j_2$，则$e_1\lt e_2$
- 如果$i_1\lt j_1 , j_1 = i_2$，则$e_1\lt e_2$
- 如果$e_1 \lt e_2, e_2 < e_3$，则$e_1 < e_3$

*Definition 1(DFS Code)* 给定一个树T可以用上述定义的线性序来声明一个编码$code(G,F)$，使用五元组来表示一条边$(i,j,l_i,l_{(i,j)},l_j)$

*Definition 2 (DFS Lexicographic Order)* 假设$Z=\{Code(G,T) \mid T \;is\;a\;DFS\;tree\;of\;G\}$，Z是一个包含所有连通标记图的DFS编码的集合。假设有一个线性序的关系$<_L$在标记集合L中，字典序是$<_T$和$<_L$的组合，定义在一个图的所有同构图的所有DFS的编码集合上面。定义如下，假设编码是$\alpha=Code(G_{\alpha},T_{\alpha})=(a_0,a_1,...,a_m),\quad\beta=Code(G_{\beta},T_{\beta})=(b_0,b_1,...,b_m) ,then \quad {\alpha} \le {\beta}$

- $\exists\;0 \le t \le min(m,n),a_k=b_k\;for\;k \lt t,a_t \lt_{e} b_t$
- $\quad a_k=b_k\;for\; 0 \le k \le m,and\;n\ge m$

*Definition 3 (Minimum DFS Code)*  $Z=\{Code(G,T) \mid T \;is\;a\;DFS\;tree\;of\;G\}$在这个集合中一句上述定义的DFS词典序，最小的一个被称为最小DFS编码。它是G的一个标准化标记。

*Theorem 1* 给定两个图，如果两个图的最小DFS编码是一致的，那么这两个图是同构的。

挖掘频繁连通子图等价于去挖掘他们的最小DFS编码。问题变成了一个序列模式挖掘，除了一些轻微的差别之外，但是从概念上来说可以通过现有的序列模式挖掘算法来解决。

给定一个DFS编码$\alpha=(a_0,a_1,...,a_m)$,任何有效的DFS编码是$\beta=(a_0,a_1,...,a_m,b) $被称为$\alpha$的一个子节点。事实上，要构建一个有效的DFS编码，必须从最右路径上面的节点长出一条边。**反向边必须从最右节点生长出来，前向边可以从最右路径的任何节点生长出来。**

**DFS Code Tree**

一棵DFS编码树，每个节点便是一个DFS编码，父子关系如上所述。兄弟关系和DFS字典序是一直的。因此，DFS编码树的前序搜索跟在DFS编码字典序之后。

给定一个标记集L，一个DFS编码树包含图的数目是无限的，因为只考虑频繁子图在有限的数据集里面，所有DFS树的大小是有限的。第n层的节点包含的DFS编码有n-1条边。通过深度优先算法搜索这棵编码树，所有的频繁子图的最小DFS编码可以被发现。因此，所有的频繁子图模式可以被通过这种方式发现。

**The gSpan Algorithm**

这部分形式化gSpan算法。gSpan使用稀疏邻接表表示法来存储图。D用来表示图的数据集，S包含所有的挖掘结果。

> Algorithm 1 GraphSet_Project(D,S):
>
> sort the labels in D by their frequency;
>
> remove infrequent vertices and edges;
>
> relabel the remaining verttices and edges;
>
> $S^1 \leftarrow$ all frequent 1-edge graphs in D;
>
> sort $S^1$ in DFS lexicographic order;
>
> $S\leftarrow S^1$
>
> for each edge in $e \in S^1$ do
>
> ​	initialize s with e ,set s.D by graph which contains e;
>
> ​	Subgraph_Mining(D,S,s)
>
> ​	$D \leftarrow D - e$
>
> ​	if $|D|$ < minSup:
>
> ​		break;



> Subprocedure 1 Subgraph_Mining(D,S,s)
>
> if $s \ne min(s)$
>
> ​	return ;
>
> $S \leftarrow S\cup\{s\}$;
>
> enumerate s in each graph in D and count its children;
>
> for each c ,c is s' child do:
>
> ​	if support(c) $\ge$ minSup:
>
> ​		$s \leftarrow c$;
>
> ​		Subgraph(D,S,s)



