# LeetCode琅琊榜第三十一层-矩阵置零

## 题目简述

### 题目

### 要点

> - 如果有一个元素为**0**,将其所在的行和列的所有元素都**设为0**,请`使原地算法`
> - `1 <= m,n <= 200`,不存在数组为空的情况,不需要额外的考虑

## 算法简述

### 1. 稀疏数组算法

#### 算法思想

- 一次遍历,将所有0的位置通过稀疏数组全部记录下来
- 再一次遍历,将对应位置的行与列都设置为0



### 2. 数组标记算法

#### 算法思想

- 通过建立两个布尔数组,长度分别是`rowLength`和`colLength`,分别记录对应下标的行与列是否需要变成0
- 一次遍历,填写布尔数组
- 再一次遍历,将对应的位置都设置为0



### 3. 两个变量标记算法

#### 算法思想

- 建立两个变量,`rowIsZero`和`colIsZero`分别记录第一行和第一列是否有0
- 从第二行第二列开始一次遍历,如果发现有0的情况,把对应行和对应列的第一行第一列位置设置为0
- 一次遍历,如果发现对应下标的第一行,第一列有0的情况,直接赋值为0
- 一次遍历第一行和一次遍历第一列,如果第一点真的有的话,将第一行或者是第一列全部赋值为0



### 4. 一个变量标记算法

#### 算法思想

- 建立一个变量,`colIsZero`用于记录第一列是否有0
- 一次遍历,对第一列的元素进行判断,如果是就将`colIsZero`置为`true`,如果发现对应下标的元素是0,则将对应行和列的第一行和第一列的元素置为0
- 一次遍历,从后面往前面遍历,如果发现对应第一行和第一列的元素是0,直接赋值为0 
- 一次遍历第一列,如果第一列有0的话,将第一列全部赋值为0

## 算法实现

### 1. 稀疏数组算法

```Java
public void setZeroes(int[][] matrix) {
    // 分别记录行和列的长度
    int m = matrix.length;
    int n = matrix[0].length;
    // List集合充当稀疏数组的作用
    // 不过稀疏数组不需要记录长度,因为我们不需要通过稀疏数组重构数组
    ArrayList<int[]> list  = new ArrayList<>();
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            // 记录对应的行与列的下标
            if (matrix[i][j] == 0) {
                list.add(new int[] {i, j});
            }
        }
    }
    // 如果该集合不为空,说明某个为0的元素对应的行和列没有被全部置为0
    while (!list.isEmpty()) {
        // 通过remove()方法获取对应需要被置为0的行列下标,并将其销毁
        int[] ints = list.remove(0);
        // 根据取出的数据进行赋值0
        for (int k = 0; k < n; k++) {
            matrix[ints[0]][k] = 0;
        }
        for (int k = 0; k < m; k++) {
            matrix[k][ints[1]] = 0;
        }
    }
}
```

### 2. 数组标记算法

```Java
public void setZeroes(int[][] matrix) {
    // 分别记录行和列的长度
    int m = matrix.length;
    int n = matrix[0].length;
    // 算法思想
    boolean[] rowIsZero = new boolean[m];
    boolean[] colIsZero = new boolean[n];
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (matrix[i][j] == 0) {
                rowIsZero[i] = true;
                colIsZero[j] = true;
            }
        }
    }
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            // 如果对应下标被标记为true,说明对应的元素需要被置为0
            if (rowIsZero[i] || colIsZero[j]) {
                matrix[i][j] = 0;
            }
        }
    }
}
```

### 3. 两个变量标记算法

```java
public void setZeroes(int[][] matrix) {
    int m = matrix.length;
    int n = matrix[0].length;
    boolean rowIsZero = false;
    boolean colIsZero = false;
    for (int i = 0; i < n; i++) {
        if (matrix[0][i] == 0){
            rowIsZero = true;
            break;
        }
    }
    for (int i = 0; i < m; i++) {
        if (matrix[i][0] == 0) {
            colIsZero = true;
            break;
        }
    }
    for (int i = 1; i < m; i++) {
        for (int j = 1; j < n; j++) {
            if (matrix[i][j] == 0) {
                matrix[i][0] = 0;
                matrix[0][j] = 0;
            }
        }
    }
    for (int i = 1; i < m; i++) {
        for (int j = 1; j < n; j++) {
            if (matrix[0][j] == 0 || matrix[i][0] == 0) {
                matrix[i][j] = 0;
            }
        }
    }
    if (rowIsZero) {
        for (int i = 0; i < n; i++) {
            matrix[0][i] = 0;
        }
    }
    if (colIsZero) {
        for (int i = 0; i < m; i++) {
            matrix[i][0] = 0;
        }
    }
}
```

### 4. 一个变量标记算法

```Java
public void setZeroes(int[][] matrix) {
    int m = matrix.length;
    int n = matrix[0].length;
    boolean isZero = false;
    for (int i = 0; i < m; i++) {
        if (matrix[i][0] == 0) {
            isZero = true;
        }
        for (int j = 1; j < n; j++) {
            if (matrix[i][j] == 0) {
                matrix[i][0] = 0;
                matrix[0][j] = 0;
            }
        }
    }
    for (int i = m - 1; i >= 0; i--) {
        for (int j = 1; j < n; j++) {
            if (matrix[i][0] == 0 || matrix[0][j] == 0) {
                matrix[i][j] = 0;
            }
        }
    }
    if (isZero) {
        for (int i = 0; i < m; i++) {
            matrix[i][0] = 0;
        }
    }
}
```

## 算法总结

> ​	该算法主要是对0的对应行列的统计,衍生出了不同的统计算法,主要最优的,最简单的还是数组标记算法,一个变量算法和两个变量标记算法没必要这么苛责去追求