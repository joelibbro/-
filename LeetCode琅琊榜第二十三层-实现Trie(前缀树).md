# LeetCode琅琊榜第二十三层-实现Trie(前缀树)

[TOC]

## 题目

### 题目浏览

![image-20220604170059708](E:\暂存\博客\暂存图片\image-20220604170059708.png)

### 题目信息解读

- `前缀树`是一种树形结构
- 前缀树对象,需要有`serach`,`insert`,`startWith`的功能
- `word`和`prefix`仅由**小写英文字母**组成

### 题目疑点

- `前缀树`是传统的二叉树还是多叉树?
- `前缀树`是否需要`节点`与`树结构`分离,减少耦合
  - 参考之前的数据结构博客,分离有利于减少耦合性,而且是代码更具`面向对象编程`的特性
- 如何实现题目中所给出的`高效`二字
- 底层是否`真实的存储每一个字符串`?

### 题目隐藏内容

- 根据示例,第一个`trie`对象,是我们前缀树的`根节点`,其实已经说明了不需要节点与树结构不需要分离
- 不论是`insert`操作,还是`search`以及`startWith`操作,都是以`根节点`开始的
  - 这决定了我们如何去编写底层的代码
- `所有的字符都不可能大写`

### 示例分析

#### 示例

```java
输入
["Trie", "insert", "search", "search", "startsWith", "insert", "search"]
[[], ["apple"], ["apple"], ["app"], ["app"], ["app"], ["app"]]
输出
[null, null, true, false, true, null, true]

Trie trie = new Trie();
trie.insert("apple");
trie.search("apple");   // 返回 True,原因是该前缀树中已经存在"apple"
trie.search("app");     // 返回 False,原因是该前缀树中只有"apple",没有"app"
trie.startsWith("app"); // 返回 True,原因是"apple"的前缀是"app"
trie.insert("app");
trie.search("app");     // 返回 True,原因是"app"已经被插入到前缀树
```

## 算法概述

### 前缀树创建思想的引导

1. 说到树结构,浮现在我们脑中的无非是这两种
   1. 在`DBA`眼里,`树`就是`索引`,`聚簇不聚簇`的那种`B+树`,`B+树可以完成前缀树的实现`
   2. 在`学完数据结构`的人眼里,`树`就是`二叉树`,`二叉树也能完成前缀树的实现,主要运用BST树`
2. 但是,上述两种树,如果拿来实现我们的`前缀树`,底层难免需要真实地`存储字符串`
3. 如果字符串一旦多起来,一定会出现`冗余`而且`难比较`的情况,间接导致我们的`层数过多`,`降低效率`
4. 所以,是否有方式可以让底层不真实的存储`字符串呢`

### 前缀树创建思想的浮现

1. 题目中给出所有的字符都是<font color = red>**小写**</font><font color = green>**英文字母**</font>组成,所以,最多有`26种情况`
2. 我们不妨创建一颗`类似于哈希表的树形结构`,来间接存储我们的`字符`
3. 如图所示

![image-20220604174624032](暂存图片/image-20220604174624032.png)

![image-20220604194311951](暂存图片/image-20220604194311951.png)

- 注意:上面的表述不准确,因为每一个地方都可能为`end`,应该有4种
  - `"a" , "aa" , "aaa" , "aab"`

### 前缀树创建思想的最终形成

1. 每一个节点对象都可以创建一个长度为26的数组,用于标记每一个字母,即`伪存储`
   1. 如果某一个位置上有`Trie`对象,说明有这个`字母`

2. 通过这种添加思路可以有效`减少冗余`
3. 此外,我们还需要一个重要的属性`end`,用于记录结束的位置
4. 从`根节点`中数组的元素,随着绿线到`end`位置就是我们存储的字符串

## 代码实现与分析

### 代码实现

```Java
class Trie {
    // 该数组就是思想中伪存储字母的数组,详情请看解析1
    public Trie[] children;
    // isEnd用于记录是否字符串达到了最后
    public boolean isEnd;
    // 构造方法
    public Trie() {
        // 二十六个英文字母,所以是26
        this.children = new Trie[26];
        // 默认没有到达最后
        isEnd = false;
    }

    // 插入方法
    public void insert(String word) {
        // 用临时引用node指向头节点,详情请看解析2
        var node = this;
        // 用于遍历字符串中所有的字符
        for (int i = 0; i < word.length(); i++) {
            // 获取对应的下标,详情请看解析3
            var index = word.charAt(i) - 'a';
            // 如果对应下标没有元素,实例化对应的对象
            if (node.children[index] == null) {
                node.children[index] = new Trie();
            }
            // 进入到该对象中,所以,必须要把this给node,否则无法跳入下一个节点中
            node = node.children[index];
        }
        // 如果循环结束,说明字符全部记录完毕,记录end
        node.isEnd = true;
    }

    // 判断是否有该字符串的方法
    public boolean search(String word) {
        // 调用查找方法
        var res = searchPre(word);
        // 详情请看解析5
        return res != null && res.isEnd;
    }

    public boolean startsWith(String prefix) {
        // 详情请看解析5
        return searchPre(prefix) != null;
    }

    // 查找方法(辅助方法,非常的重要)
    public Trie searchPre(String str) {
        // 同理
        var node = this;
        for (int i = 0; i < str.length(); i++) {
            var index = str.charAt(i) - 'a';
            // 如果该下标下没有对象,说明改下标的元素不存在,即该字符串在前缀树中不存在
            if (node.children[index] == null) {
                // 若不存在,返回NULL即可
                return null;
            }
            node = node.children[index];
        }
        // 返回最后的节点对象,详情请看解析4
        return node;
    }
}

/**
 * Your Trie object will be instantiated and called as such:
 * Trie obj = new Trie();
 * obj.insert(word);
 * boolean param_2 = obj.search(word);
 * boolean param_3 = obj.startsWith(prefix);
 */
```

#### 解析1

- 问题:为什么要用`Trie[]`数组,而不用`String[]`或者是`byte[]`之类的呢?
  - 由我们的示意图可以看出,每一个元素都对应着一个数组,即`HAS A`关系,所以,只能`Tire[]`数组能完成

#### 解析2

- 问题:`this`为什么是头节点呢?
  - 由示例可知,都是头节点`trie`去调用这些方法的,所以`this`表示头节点
- 问题:为什么要赋值给`node`呢?
  - 先按下不表

#### 解析3

- 我们创建数组的时候,因为`有26个字母`,所以`大小设置为26`
- 所以,不难得出`a`字符应该在`下标为0`的位置
- 所以,`获取的字符 - 'a'`就是对应的下标,如`'b' - 'a' = 1`,是正确的 

#### 解析4

- 返回的结果只有两种
  - `node`对象中的属性`isEnd = false`
  - `node`对象中的属性`isEnd = true`
- 作用先按下不表

#### 解析5

- 如果返回的对象为`not null`,而且其`isEnd = true`,说明该字符串存在
- 如果返回的对象为`not null`,但是其`isEnd = false`,说明该字符串只是一个某一个字符串的前缀
  - 这种情况出现的原因是,在辅助查找方法中,当前缀字符串查找完之后,没有到达原字符串的末尾,却把节点传回去了,所以为`null`

- 如果返回的对象为`null`,说明没有遍历完,一定不存在,说明前缀什么的都不可能

## 算法结论

> 前缀树运用到的算法思想还是很牛的,通过等效替代的方法完美的将字符串的没有给字符进行了伪存储,这位我们的琅琊榜第二十二层前缀解法定下了基础
>
> 如果有讲的不好或者不懂的地方欢迎指出
>
> 本篇是刷题第一阶段的最后的一期,下一期在一个月后!
>
> 下一篇是对本期的大总结,会有刷题思维导图,限时免费.