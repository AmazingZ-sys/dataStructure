# 数据结构

## 前言

上大学的时候老师讲数据结构总是不以为然，不知道数据机构和算法到底能对我们的程序造成什么影响，工作一年了也没有对这方面的知识有点清晰的理解和感触。前端时间看到大佬公众号的文章，才对算法这种虚无缥缈的东西有了个清晰的认知。

文章链接：https://mp.weixin.qq.com/s/Sj4mB0V9TXHB5XpQLsrTEw

本文学习记录自修言大佬的掘金小册：《前端算法与数据结构面试：底层逻辑解读与大厂真题训练》

推荐大家也去看看，还有修言大佬的设计模式小册也不错！！！

对于一个斐波那契数列的求解，你能想到多少种方法？

最简单的---**递归**：

```javascript
function fib(n){
  if(n == 1 || n == 2){
    return 1;
  }
  return fib(n - 2) + fib(n - 1);
}
```

运行`fib(50)`的时候，在我笔记本上跑了将近107秒，如图：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg0wm4pwfbj30ug0bcgmz.jpg)

运用算法进行优化---**备忘录解法**：

核心：去除很多不必要的重复运算

```javascript
let fib = (function(){
  let memo = new Map();
  return function (n){
    // 查找记忆中有无这个计算
    // 没有就继续递归
    let memorized = memo.get(n);
    if(memorized){
      return memorized;
    }
    if(n == 1 || n == 2){
      return 1;
    }
    let f1 = fib(n - 1);
    let f2 = fib(n - 2);
    
    // 将计算记忆到Map中
    memo.set(n - 1,f1);
    memo.set(n - 2,f2);
    
    return f1 + f2;
  };
})();
```

运算时长如图：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg0wwhwsb5j30ug0pan0t.jpg)

只是去除不必要的重复计算就得到了这么大的性能提升，这就是算法的魅力所在。

算法再优化---**动态规划**

看似上面的备忘录解法已经很完美了，实际上不是，虽然备忘录解法把无用的重复求解都优化了，在速度上达到了比较优的程度。

但是对于第一次求解，未被记忆化的值来说，还是会进入递归调用逻辑。

比如 f(10000)，那么必然会递归调用 f(9999)、f(9998) ...... f(0)，而在递归的过程中，这些调用栈是不断叠加的，当函数调用的深处，栈已经达到了成千上万层。

此时它就会报出一个我们熟悉的错误：

```
RangeError: Maximum call stack size exceeded
    at c:\codes\leetcode-javascript\动态规划\斐波那契数列-509.js:20:19
    at c:\codes\leetcode-javascript\动态规划\斐波那契数列-509.js:32:14
```

我们回过头来思考一下，备忘录的思路下我们的解法路径是「自顶向下」的，如果我们把思路倒置一下，变成「自底向上」的求解呢？

也就是说，对于 fib(10000)，我们从 fib(1), fib(2), fib(3) ...... fib(10000)，

从最小的值开始求解，并且把每次求解的值保存在“记忆”（可以是一个数组，下标正好用来对应 n 的解答值）里，下面的值都由记忆中的值直接得出。

这样等到算到 10000 的时候，我们想要求解的值自然也就得到了，直接从 `记忆[10000]` 里取到值返回即可。

那么这种解法其实只需要一个 for 循环，而不需要任何递归的逻辑。

```javascript
let fib = function (n){
	let dp = [];
	
  // 初始条件
  dp[0] = 0;
  dp[1] = 1;
  
  // 从2开始，不断用之前记忆的值
  // 填充数组
  for(let i = 2; i <= n; i++){
    dp[i] = dp[i - 1] + dp[i - 2];
  }
  return dp[n];
};
```

运行时长如图：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg26jyk6apj30um0pudjj.jpg)

我们可以看到，堆栈超出问题已经解决，这就是算法的魅力和和对应使用场景的必要性。

## 数组

### 数组的创建

直接赋值：

```javascript
const arr = [1,2,3,4];
```

构造函数创建：

```javascript
const arr = new Array();
const arr1 = Array();
// 创建空数组，此时等价为：
const arr = [];
```

创建固定长度的空数组：

```javascript
const arr = new Array(4);
```

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg111y8wfvj30fw08cdgi.jpg)

创建固定长度的数组并每个值都确定：

```javascript
// 初始化一个长度为7的数组，全部填充为1
const arr = (new Array(7)).fill(1);
```

### 数组的遍历

- #### `for`循环

  通过数组的下标，依次访问每个值

  ```javascript
  const len = arr.length;
  for(let i=0;i<len;i++){
  	console.log(arr[i],i);
  }
  ```

- #### `forEach`方法

  通过传入函数中的参数获取值和下标

  ```javascript
  forEach((item,index)=>{
    console.log(item,index);
  })
  ```

- #### `map`方法

  `map`的循环和`forEach`差不多，只是`map`返回一个新数组，常常用于对原数组的批量修改等

  ```javascript
  const newArr = arr.map((item,index)=>{
    console.log(item,index);
    // 每个元素在原有基础上加一，返回值的必需的，否则新数组为空
    return item +1
  })
  ```

个人建议如果没有特殊需要，统一使用`for循环`执行遍历，因为**从性能上看，`for循环`遍历是最快的**。

### 二维数组

二维数组就是数组中嵌套数组（每个元素都是数组）

正常的一位数组：

```javascript
const arr = [1,2,3,4,5];
```

数组结构分布：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg11hrypn4j30oi07eq3a.jpg)

二维数组：

```javascript
const arr = [
  [1,2,3,4,5],
  [1,2,3,4,5],
  [1,2,3,4,5],
  [1,2,3,4,5],
  [1,2,3,4,5]
]
```

二维数组的结构分布：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg11jpg8sej30nm0lwdhb.jpg)

在数学中，形如这样**长方阵列排列的复数或实数集合**，被称为“矩阵”。因此**二维数组的别名就叫“矩阵”**

### 二维数组的初始化

#### `fill`的局限性：

使用`fill`填充二维数组

```
const arr = (new Array(7)).fill([]);
```

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg11vd5l3nj30ug0geta6.jpg)

乍一看没毛病，只是在你修改数组中的某一个位置的时候，问题出现了

```javascript
arr[0][0] = 1;
```

会出现：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg11xdf16qj30uc0gggmv.jpg)

出现这个是因为`fill`的工作机制，在你传入一个参数给`fill`的时候，**如果这个入参的类型是引用类型，那么 fill 在填充坑位时填充的其实就是入参的引用**。也就是说下图中虽然看似我们在7个位置初始化了一个数组，其实这7个数组对应了同一个引用、指向的是同一块内存空间，**它们本质上是同一个数组**。因此当你修改第0行第0个元素的值时，第1-6行的第0个元素的值也都会跟着发生改变。

#### 正确初始化

本着安全的原则，这里我推荐大家采纳的二维数组初始化方法非常简单（而且性能也不错）。直接用一个 `for` 循环来解决：

```javascript
const len = arr.length
for(let i=0;i<len;i++) {
    // 将数组的每一个坑位初始化为数组
    arr[i] = []
}
```

### 二维数组的遍历和访问

我们要访问二维数组，需要进行两层遍历

```javascript
// 缓存外部数组的长度
const outerLen = arr.length
for(let i=0;i<outerLen;i++) {
    // 缓存内部数组的长度
    const innerLen = arr[i].length
    for(let j=0;j<innerLen;j++) {
        // 输出数组的值，输出数组的索引
        console.log(arr[i][j],i,j)
    }
}
```

一维数组用 for 循环遍历只需一层循环，二维数组是两层，三维数组就是三层。依次类推，**N 维数组需要 N 层循环来完成遍历**。

## 栈和队列

在 `JavaScript` 中，**栈和队列的实现一般都要依赖于数组**，大家完全可以把栈和队列都看作是“特别的数组”。

### 添加元素的数组方法：

- `unshift`添加元素到数组头部

  ```javascript
  const arr = [1,2]
  arr.unshift(0) // [0,1,2]
  ```

- `push`添加元素到数组尾部

  `push`方法返回的是`push`进数组的元素个数

  ```javascript
  const arr = [1,2]
  arr.push(3) // [1,2,3]
  ```

- `splice`任意位置添加元素

  ```javascript
  const arr = [1,2] 
  arr.splice(1,0,3) // [1,3,2]
  ```

### 删除元素的数组方法：

- `shift`删除头部

  ```javascript
  const arr = [1,2,3]
  arr.shift() // [2,3]
  ```

- `pop`删除尾部

  `pop`方法返回的是从数组中弹出的那一个元素

  ```javascript
  const arr = [1,2,3]
  arr.pop() // [1,2]
  ```

- `splice`删除任意位置

  ```javascript
  const arr = [1,2,3]
  arr.splice(2,1) // [1,2]
  ```

### 栈----先进后出

栈是一种后进先出(LIFO，Last In First Out)的数据结构。

操作元素都只从尾部开始，就实现了先进后出。

```javascript
// 初始状态，栈空
const stack = []  
// 入栈过程
stack.push('1')
stack.push('2')
stack.push('3')
stack.push('4')
stack.push('5')

// 出栈过程，栈不为空时才执行
while(stack.length) {
    // 单纯访问栈顶元素（不出栈）
    const top = stack[stack.length-1]
    console.log('现在为', top)  
    // 将栈顶元素出栈
    stack.pop()
}

// 栈空
stack // []
```

### 队列----先进先出

队列是一种先进先出（FIFO，First In First Out）的数据结构。

操作元素时添加从尾部操作，删除从头部操作，类似排队。

```
const queue = []  
queue.push('1')
queue.push('2')
queue.push('3')  
  
while(queue.length) {
    // 单纯访问队头元素（不出队）
    const top = queue[0]
    console.log(top,'取餐')
    // 将队头元素出队
    queue.shift()
}

// 队空
queue // []
```

## 链表

链表和数组类似，它们都是有序的列表、都是线性结构（有且仅有一个前驱、有且仅有一个后继）。不同点在于，链表中，数据单位的名称叫做“结点”，而结点和结点的分布，在内存中可以是**离散**的。

链表可分为**单向链表**和**双向链表**：

- 单向链表：只可单向访问，只能访问到下一个结点的位置
- 双向链表：每个结点都有指向下一结点位置的指针和指向上一结点位置的指针，通过访问指针来访问到上个结点/下个结点

这个“离散”是相对于数组的“连续”来说的：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg11hrypn4j30oi07eq3a.jpg)

数组在内存中最为关键的一个特征，就是它一般是对应一段位于自己上界和下界之间的、一段**连续**的内存空间。

而链表中的结点，内存空间是不连续的。一个内容为1->2->3->4->5的链表，在内存中的形态可以是散乱如下的：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg13yu1byuj30os0lkwfl.jpg)

在数组中，我们可以通过遍历循环去获取每个元素的值和下标，而这在链表中是行不通的，但是在链表的每一个结点中，都包括了数据和指针。JS 中的链表，是以嵌套的对象的形式来实现的：

```javascript
{
    // 数据域
    val: 1,
    // 指针域，指向下一个结点
    next: {
        val:2,
        next: ...
    }
}   
```

数据即这个结点的值，指针指向下一个结点的内存地址（引用类型），现在知道某一个结点的值和位置，也就知道了下一个结点的位置，通过位置获取到下一个结点的值。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg1492twqkj30zk0ciq3x.jpg)

要想访问链表中的任何一个元素，我们都得从头开始逐个访问下一个结点的位置，知道访问到目标结点为止。为了确保起点结点是可抵达的，我们有时还会设定一个 head 指针来专门指向链表的开始位置：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg14ak8w89j30z60fmdh6.jpg)

### 链表结点的创建

创建链表结点，我们需要一个构造函数

```javascript
function ListNode(val){
  this.val = val;
  this.next = null;
}
```

然后我们通过构造函数创建结点，多个结点组成了链表：

```javascript
const node = new ListNode(1)  
node.next = new ListNode(2)
```

### 链表结点的添加

#### 在尾部添加结点

创建一个新结点，将链表中尾部结点的`next`指向新结点

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg14z56wurj30ps080gm5.jpg)

#### 在链表任意两结点之间添加新结点

创建一个新结点，要插入位置的上个结点的`next`指向新结点，新结点的`next`指向要插入位置的结点

插入前：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg1546f9ayj30ni0e274u.jpg)

插入后：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg154dvi84j30m60fawf7.jpg)

代码表述：

```javascript
// 如果目标结点本来不存在，那么记得手动创建
const node3 = new ListNode(3)     
// 把node3的 next 指针指向 node2（即 node1.next）
node3.next = node1.next
// 把node1的 next 指针指向 node3
node1.next = node3
```

### 链表元素的删除

要想删除链表中的某个结点，我们只需要将那个结点的上个位置的结点的`next`直接指向他下个结点（直接跳过本结点）：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg157to49jj30l40gmwf9.jpg)

举个栗子，将上面新加的`node3`删除：

```javascript
node1.next = node3.next 
```

在涉及链表的操作中，重点不是定位目标结点，而是**定位目标结点的前驱结点**。

## 树结构

一个根结点，任意多个子结点：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg168j1g6zj30wa0lkgna.jpg)

计算机中的树：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg16iusktcj30h90b5wez.jpg)

关于树结构：

- 树的层次计算规则：根结点所在的那一层记为第一层，其子结点所在的就是第二层，以此类推。
- 结点和树的“高度”计算规则：叶子结点高度记为1，每向上一层高度就加1，逐层向上累加至目标结点时，所得到的的值就是目标结点的高度。树中结点的最大高度，称为“树的高度”。
- “度”的概念：一个结点开叉出去多少个子树，被记为结点的“度”。比如我们上图中，根结点的“度”就是3。
- “叶子结点”：叶子结点就是度为0的结点。在上图中，最后一层的结点的度全部为0，所以这一层的结点都是叶子结点。

### 二叉树

二叉树就是满足一下条件的树：

- 可以没有根结点，作为一棵空的树

- 如果它不是空树，那么**必须由根结点、左子树和右子树组成，且左右子树都是二叉树**。如下图：

  ![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg16od8py0j30h90b5dg9.jpg)

  注意，**二叉树不能被简单定义为每个结点的度都是2的树**。普通的树并不会区分左子树和右子树，但在二叉树中，左右子树的位置是严格约定、不能交换的。对应到图上来看，也就意味着 B 和 C、D 和 E、F 和 G 是不能互换的。

#### 二叉树的编码实现

在`JavaScript`中，二叉树可以使用对象来定义，结构分为三部分：

- 数据域：这个结点的值
- 左侧子结点（左子树根结点）的引用
- 右侧子结点（右子树根结点）的引用

在定义二叉树构造函数时，我们需要把左侧子结点和右侧子结点都预置为空：

```javascript
// 二叉树结点的构造函数
function TreeNode(val) {
    this.val = val;
    this.left = this.right = null;
}
```

创建一个值为1的二叉树结点：

```javascript
const node  = new TreeNode(1);
```

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg16sbgd0sj30dw0683yo.jpg)

以这个结点为根结点，我们可以通过给 left/right 赋值拓展其子树信息，延展出一棵二叉树。因此从更加细化的角度来看，一棵二叉树的形态实际是这样的：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg16srp3kjj30vy0mcwg7.jpg)

#### 二叉树的遍历

以一定的顺序规则，逐个访问二叉树的所有结点，这就是遍历。根据顺序规则的不同，遍历方式有一下四种：

- 先序遍历
- 中序遍历
- 后序遍历
- 层次遍历

按照实现方式不同，遍历方式又可以分为以下两种：

- 递归遍历（先、中、后序遍历）
- 迭代遍历（层次遍历）

#### 递归遍历

我们都知道，在一个函数中，如果出现了在函数体中调用了自身，这就是一个递归函数。简单来说，一个函数在反复调用自己的时候，递归也就产生了。一个递归函数需要两个必需条件：

- 递归式（函数体---你要重复做什么）
- 递归边界（出口---达到什么条件时停止递归）

现在我们再来看二叉树，一个完整的二叉树应该由这三部分组成：

- 根结点
- 左子树
- 右子树

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg3gj79jfxj30iq0co74r.jpg)

对于二叉树的遍历，就可以看做是对这三个部分的遍历，在保证“左子树一定先于右子树遍历”的前提下，有三种遍历顺序：

- 根结点=>左子树=>右子树
- 左子树=>根结点=>右子树
- 左子树=>右子树=>根结点

上面的三个遍历顺序，就对应了二叉树的先、中、后序遍历。在这三种遍历顺序中，根结点的遍历分别被安排在了开始位置、中间位置和末尾位置，所谓的“先序”、”中序“和“后序”遍历也就是指的根结点的遍历时机。

#### 遍历方法和编码实现

##### 先序遍历

遍历顺序如图

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg3gwfsgfjj30lg0dgaal.jpg)

如果有 N 多个子树，那么我们在每一棵子树中都要进行的遍历如下：

![](https://user-gold-cdn.xitu.io/2020/4/6/1714ec42acc57e04?imageslim)

上面的二叉树结构为：

```javascript
const root = {
  val: "A",
  left: {
    val: "B",
    left: {
      val: "D"
    },
    right: {
      val: "E"
    }
  },
  right: {
    val: "C",
    right: {
      val: "F"
    }
  }
};
```

编码实现：

```javascript
// 所有遍历函数的入参都是树的根结点对象
function preorder(root) {
    // 递归边界，root 为空
    if(!root) {
        return 
    }
     
    // 输出当前遍历的结点值
    console.log('当前遍历的结点值是：', root.val)  
    // 递归遍历左子树 
    preorder(root.left)  
    // 递归遍历右子树  
    preorder(root.right)
}
```

##### 中序遍历

遍历顺序如图

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg3h8gehxij30ju0csaal.jpg)

动图解释如下：

![](https://user-gold-cdn.xitu.io/2020/4/6/1714f098b2bd1f9a?imageslim)

编码实现：

```javascript
// 所有遍历函数的入参都是树的根结点对象
function inorder(root) {
    // 递归边界，root 为空
    if(!root) {
        return 
    }
     
    // 递归遍历左子树 
    inorder(root.left)  
    // 输出当前遍历的结点值
    console.log('当前遍历的结点值是：', root.val)  
    // 递归遍历右子树  
    inorder(root.right)
}
```

##### 后序遍历

遍历顺序如图：

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gg3habiukkj30le0dgdge.jpg)

动图解释如下：

![](https://user-gold-cdn.xitu.io/2020/4/6/1714efce7db2cdff?imageslim)

编码实现：

```javascript
function postorder(root) {
    // 递归边界，root 为空
    if(!root) {
        return 
    }
     
    // 递归遍历左子树 
    postorder(root.left)  
    // 递归遍历右子树  
    postorder(root.right)
    // 输出当前遍历的结点值
    console.log('当前遍历的结点值是：', root.val)  
}
```

### 复杂度分析

复杂度是评价一个算法或者一段程序运行时效性的重要参考。

#### 时间复杂度

> 时间复杂度，即运行一段程序代码要花多长时间

我们先看这一段代码，一共会执行多少次？

```javascript
function traverse(arr){
  const len = arr.length;
  for(let i=0;i<len;i++){
    console.log(arr[i]);
  }
}
```

首先，函数体里的第一行代码，它只会被执行一次，然后是循环体里的输出函数，`arr`数组有多长，它就会执行多少次