# </center>**中科大《编译原理》视频学习**</center>
<center>视频作者 华保健</center>

## **词法分析**

### **字母表**
 $Alphabet$: A finite set of symbols  
 字母表即是一个有限的符号的集合  
例如: $\{0,\,1\}$, $\{a,\,b,\,c,\cdots,z\}$, ASCII 码的全体符号集, Unicode 码的全体符号集合, 等等.

### **字符串**
$String$: A finite sequence of symbols from an alphabet  
字符串即是字母表上的字符的序列.  
例如: $abc$, $\alpha\beta\gamma$, $Stephen$ 等等. 

### **语言**
A set of strings over an alphabet
语言是字母表上字符串的集合.  
例如: $\Phi$ (空集), $\{ 1, 11, 111, 1111 \}$, 所有的英文单词组成的集合, 所有的中文字符串组成的集合等等.

### **语言的运算**

假设两个语言 $L = \{\epsilon, wo\}$, 和 $M = man,\,men$
- 连接($Concatenation$)运算: 一个字符串紧接着另一个字符串. 例如: $LM = \{man, men, woman, women\}$.
- 并($Union$)运算: 将两个语言的所有字符串做并运算. 例如: $L \cup M = \{\epsilon,\,wo,\,man,\,men\}$.
- 克林闭包(Kleene Closure)运算: $M^∗ = \{\epsilon,\,M,\,MM,\,MMM,\,\cdots\} =\{\epsilon,\,man,\,men,\,manman,\,manmen,\,menman,\,menmen,\,manmanman,\,manmanmen,\,manmenman,\,\cdots\}$

### **有限状态自动机**

以下内容摘抄自 《Engineering a compiler》  
有限状态自动机 $(finite\,automata)$ 是一个五元组:  $(S, \Sigma, \delta, s_0, S_A)$, 其中:  
- $S$: 有限的状态的集合, 包括一个错误状态 $s_e$.
- $\Sigma$: 字母表, 也就是做状态转移时读入的字符集.
- $\delta(s, c)$, $\delta :S\times \Sigma \rightarrow S$: 状态转移函数, 它将 $\forall s \in S$ 和 $\forall c \in \Sigma$ 映射为 $S$ 中的一个状态. 对于状态 $s_i$ 的输入 $c$, 有限状态自动机 $FA$ 做状态转移: $s_i \stackrel{c}{\rightarrow} \delta(s_i,c)$.
- $s_0$: $s_0 \in S$, 是一个指定的开始状态.
- $S_A$: $S_A \subseteq S$, 接受状态的集合. 在状态转移图中, $\forall s_a \in S_A$ 任意一个接受状态以双圆圈的形式出现.

例子:

```graphviz

digraph finite_state_machine {
  rankdir = LR;
  size = "8,5"

  node [shape = doublecircle, color = red]
  S_0

  node [shape = doublecircle, color = green];

  S_3 S_5 S_10;

  node [shape = circle, color = blue];

  S_0 -> S_1 [ label = "n" ];
  S_0 -> S_6 [ label = "w" ];
  S_1 -> S_2 [ label = "e" ];
  S_1 -> S_4 [ label = "o" ];
  S_2 -> S_3 [ label = "w" ];
  S_4 -> S_5 [ label = "t" ];
  S_6 -> S_7 [ label = "h" ];
  S_7 -> S_8 [ label = "i" ];
  S_8 -> S_9 [ label = "l" ];
  S_9 -> S_10 [ label = "e"];
}

```

上图是一个识别new($S_0 \stackrel{n}{\rightarrow} S_1, S_1 \stackrel{e}{\rightarrow} S_2, S_2 \stackrel{w}{\rightarrow} S_3$), not($S_0 \stackrel{n}{\rightarrow} S_1, S_1 \stackrel{o}{\rightarrow} S_4, S_4 \stackrel{t}{\rightarrow} S_5$), while($S_0 \stackrel{w}{\rightarrow} S_6, S_6 \stackrel{h}{\rightarrow} S_7, S_7 \stackrel{i}{\rightarrow} S_8, S_8 \stackrel{l}{\rightarrow} S_9, S_9 \stackrel{e}{\rightarrow} S_{10}$)三个单词的有限状态自动机, 
其中: 
- 字母表 $\Sigma = \{e,h,i,l,n,o,t,w\}$, 
- 状态集 $S = \{s_0, s_1, s_2, s_3, s_4, s_5, s_6, s_7, s_8, s_9, s_{10}, s_e\}$
- 状态转移函数集 $\delta = \begin{Bmatrix} s_0 \stackrel{n}{\rightarrow} s_1,\,&s_1 \stackrel{e}{\rightarrow} s_2,\,&s_2 \stackrel{w}{\rightarrow} s_3,\,&s_1 \stackrel{o}{\rightarrow} s_4,\,&s_4 \stackrel{t}{\rightarrow} s_5 \\ s_0 \stackrel{w}{\rightarrow} s_6,\,&s_6 \stackrel{h}{\rightarrow} s_7,\,&s_7 \stackrel{i}{\rightarrow} s_8,\,&s_8 \stackrel{l}{\rightarrow} s_9,\,&s_9 \stackrel{e}{\rightarrow} s_{10} \\ \end{Bmatrix}$
- 开始状态 $s_0$
- 接受状态集 $S_A = \{s_3, s_5, s_{10} \}$

### **正则表达式**

正则表达式($Regular Expression$, 简写为$RE$)是递归定义的:
1. 空字符串 $\epsilon$ 是$RE$, 用 $\{\epsilon\}$ 表示.
2. 一个字符 $c \in \Sigma$ 是 $RE$, 用 $\{c\}$ 表示.
3. 如果 $E_1$ 和 $E_2$ 都是 $RE$, 那么他们的连接也是 $RE$, 记为 $E_1E_2$.
4. 如果 $E_1$ 和 $E_2$ 都是 $RE$, 那么他们的选择也是 $RE$, 记为 $E_1|E_2$.
5. 如果 $E$是 $RE$, 那么他的闭包也是 $RE$, 记为 $(E)^*$.

例子, 假如有一个表达式 $a(b|c)^*$, 递归的使用上述定义来确定他是否是一个正则表达式:
- 首先 $a$ 是一个单独的字符串, 根据规则2, $a$ 是一个 $RE$, 同理 $b$ 和 $c$ 都是 $RE$;
- 由于 $b$ 和 $c$ 都是 $RE$, 那么根据规则4, 他们的选择 $(b|c)$ 也是 $RE$;
- 由于 $(b|c)$ 是 $RE$, 那么根据规则5, 他的闭包 $(b|c)^*$ 也是 $RE$;
- 由于 $a$ 和 $(b|c)^*$ 都是$RE$, 那么根据规则3, 他们的连接 $a(b|c)^*$ 也是 $RE$.  

由此可见, 递归的使用规则1~5, 可以判断一个表达式是否是正则表达式.

### **DFA**
确定性有限状态自动机, Deterministic Finite Automaton 

### **NFA**
非确定性有限状态自动机, Nondeterministic Finite Automaton

### **正则表达式转化为NFA - Thompson 算法**

### **NFA转化为DFA - 子集构造算法(Powerset Construction Algorithm)**

### **DFA的最小化-Hopcroft算法**