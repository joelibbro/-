# LeetCode琅琊榜第三十五层-在二叉树中增加一行

#### [623. 在二叉树中增加一行](https://leetcode.cn/problems/add-one-row-to-tree/)

## 解法1:DFS

```java
public TreeNode dfs(TreeNode root, int val, int depth) {
    // 该条件必须要写,可能遇到如下情况,假设我们要在第三层增加一样,也第二层的右子树为空,但是符合下面的条件,贸然判断root.right会造成空指针异常,所以要加这个条件,如图所示
    if (root == null) {
        return null;
    }
    // 如果depth是1,结果很明了,就是要就根节点的位置替换掉
    if (depth == 1) {
        // 直接new一个新的节点,其左子节点或右子节点是以前的root即可,当它取代完成后,新的节点就是root了,所以赋值给root
        root = new TreeNode(val, root, null);
        // 如果depth是2,根据图示可以得出以下的代码
    } else if (depth == 2) {
        root.left = new TreeNode(val, root.left, null);
        root.right = new TreeNode(val, null, root.right);
    } else {
        // 说明depth大于2,我们不妨进行深度优先
        // 如果深度优先,为了简化代码,我们不妨假设深度优先的位置为根节点,所以,随着递归,depth会减少,所以depth-1
        // 因为是二叉树,所有深度优先的方向有左子树和右子树
        // 因为该方法返回值应该是root,所以,当我们深度优先的时候,root是动态的,所以,当我们到左子树时,root.left就是最新的跟节点,所以用root.left接受
        root.left = dfs(root.left, val, depth - 1);
        root.right = dfs(root.right, val, depth - 1);
    }
    // 返回root,以至于返回整棵树
    return root;
}
```

![image-20220806113259032](E:\暂存\博客\暂存图片\image-20220806113259032.png)

## 解法2:BFS

```Java
public TreeNode bfs(TreeNode root, int val, int depth) {
   	// 处理特殊情况,同DFS
    if (depth == 1) {
        return new TreeNode(val, root, null);
    }
    // 用于记录当前层的所有节点
    List<TreeNode> curNodes = new ArrayList<>();
    // 当前层是第一层,所以只有一个根节点,把root加入即可
    curNodes.add(root);
    // 当我们遍历到depth - 2时,我们已经将depth - 1层的所有节点记录下来了,所以循环条件这样写
    for (int i = 1; i < depth - 1; i++) {
        // 用于动态记录当前层的节点,必须写在此处,否则记不全
        List<TreeNode> tmp = new ArrayList<>();
        // 遍历当前层的节点,获取下一层的所有节点
        for (TreeNode node : curNodes) {
            if (node.left != null)
                tmp.add(node.left);
            if (node.right != null)
                tmp.add(node.right);
        }
        // 将tmp的结果给curNodes,可以有效将前面的结果覆盖,让里面的元素不可能出现多层的情况
        // 必须斜这,否则基本全
        curNodes = tmp;
    }
    // 我们得到上一层后,直接在下一层加即可,同DFS
    for (TreeNode curNode : curNodes) {
        curNode.left = new TreeNode(val, curNode.left, null);
        curNode.right = new TreeNode(val, null, curNode.right);
    }
    // 同理
    return root;
}
```