# LeetCode琅琊榜第二十六层-路径总和

## 题目简述

### 题目



### 题目要点

- `根节点到叶子节点的路径`

  - [^叶子节点]:说明要遍历完整棵树,结束条件是叶子节点

- `该路径上所有节点值相加等于targetSum`

- `树中节点的数目在范围[0,5000]内` 

  - [^[0,5000\]]:说明可能为空树,要做出对应的布尔判断

## 示例分析

### 示例一

![image-20220701102339678](暂存图片/image-20220701102339678.png)

```Java
输入：root = [5,4,8,11,null,13,4,7,2,null,null,null,1], targetSum = 22
输出：true
```

### 示例二

![image-20220701102356722](暂存图片/image-20220701102356722.png)

```Java
输入：root = [1,2,3], targetSum = 5
输出：false
```

### 示例三

```Java
输入：root = [], targetSum = 0
输出：false
```

## 算法概述

### 递归回溯算法

#### 算法思想

- 通过递归回溯,依次纵向遍历完所有可能的路径

### 广度优先算法

#### 算法思想

- 通过广度优先,横向遍历所有可能出现的结果,并将结果记录下来,参考广度优先搜索算法

## 代码实现

### 递归算法

```Java
public boolean hasPathSum(TreeNode root, int targetSum) {
    // 如果根节点为null,则一个节点都没有,不可能等于targetSum,直接返回false即可
    if (root == null) {
        return false;
    }
    // 详情请看解析1
    if (root.left == null && root.right == null) {
        return root.val == targetSum;
    }
    // 详情请看解析2
    return hasPathSum(root.left,targetSum - root.val) || hasPathSum(root.right,targetSum - root.val);
}
```

#### 解析1

- 这里的`root`有两层含义
  1. `root`第一次判断该`if语句`时,`root`表示根节点,即如果只有一个根节点,则判断根节点的值是否是目标值,直接返回即可
  2. `root`第二次判断该`if语句`时,`root`表示当前节点,即`root`的某一个子节点

#### 解析2

- 如果执行到了这里,说明当前节点并不是`叶子节点`,则我们需要对其进行递归搜索

  - [^搜索策略]:先深度搜索左子树,再搜索右子树

- `hasPathSum(root.left,targetSum - root.val)`代表:向**左子树**递归搜索,同时,当我们向左子树递归时,目标值会减少,因为当前节点填补了目标值

- ` hasPathSum(root.right,targetSum - root.val)`同理

- `||`很妙

  - 如果在左子树的叶子节点中返回`true`,由于短路现象后面那部分不需要执行即不需要递归,减少递归次数
  - 即使返回了`false`,也会继续执行后面那部分,实现遍历全树

### 广度优先算法

```Java
public boolean hasPathSum2(TreeNode root, int targetSum) {
    // 如果根节点为null,则一个节点都没有,不可能等于targetSum,直接返回false即可
    if (root == null)
        return false;
    // 队列nodes,用于专门存放待遍历的节点,算法思想请回顾广度优先算法
    Queue<TreeNode> nodes = new LinkedList<>();
    // 队列nodeSum,用于专门存放到当前这一层的所有路径的路径和(创新点)
    Queue<Integer> nodesSum = new LinkedList<>();
    // 将根节点及其值分别放入两个队列中
    nodes.offer(root);
    nodesSum.offer(root.val);
    // 当队列不为空时,说明节点还没有遍历完
    while (!nodes.isEmpty()) {
        // 取出待遍历的节点及其值
        TreeNode now = nodes.poll();
        int curSum = nodesSum.poll();
        // 如果该节点是叶子节点,则进入以下代码进行判断
        if (now.left == null && now.right == null) {
            // 如果等于目标值,说明该路径可以完成目的,直接返回true,不需要再去广度遍历了
            if (curSum == targetSum) {
                return true;
            }
            // 否则,继续去遍历节点
            continue;
        }
        // 执行到这里,说明不是叶子节点,应该取出其左右子节点放入队列中,表示待遍历
        if (now.left != null) {
            nodes.offer(now.left);
            // 表示到now.left时,一共的路径和
            nodesSum.offer(now.left.val + curSum);
        }
        if (now.right != null) {
            nodes.offer(now.right);
            nodesSum.offer(now.right.val + curSum);
        }
    }
    // 如果执行到这里,说明遍历完都没有完成目标,返回false
    return false;
}
```

# 算法总结

> ​	针对于树常用的方法有:
>
>  	1. 深度优先
>  	2. 广度优先
>  	3. 递归回溯
