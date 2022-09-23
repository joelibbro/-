# LeetCode琅琊榜第二十六层-第三大的数



## 题目简述

[TOC]

### 题目

### 要点

- 返回数组`第三大的数`
  - 数组中的有效元素需大于等于3个,才会有第三大的数
- 如果该数组不存在,`则返回最大的数`
  - 数组中的有效元素小于3个,不会有第三大的数

## 实力分析

### 示例一

```java
输入: [3,2,1]
输出: [1]
解释: 数组中的有效元素为3个,返回第三大的数即为1
```

### 示例二

```
输入: [1,2,2]
输出: [2]
解释: 数组中的有效元素为2个,返回最大的数即为2
```

## 算法分析

### 普通排序算法

#### 算法思想

- 首先对数组进行排序,特别的是,我们需要从大到小的进行排序
- 排序后我们进行特殊的排重处理就可以得到第三大的元素

#### 算法缺陷

- 在传统的API中,我们是没有从大到小的排序API的,所以我们需要利用固有的API然后进行翻转
- 所以,我们需要额外实现翻转算法,代码量大

#### 算法优点

- 排重代码牛

### 集合排序算法

#### 算法思想

- 利用`TreeSet`无需不可重复且可排序的特性,直接获取即可

#### 算法优点

- 代码量少,思想简单

### 假设法(一次遍历)

#### 算法思想

- 通过假设三个大小,分别是`最大``次大``第三大`的三个数,通过一次遍历,找出我们想要的答案

#### 算法优点

- 该算法只用到了一次遍历,且没有多余的操作,算法效率高

## 代码实现

### 普通排序算法

```java
public int thirdMax(int[] nums) {
    // 利用API,先将数组从小到大进行排序
    Arrays.sort(nums);
    // 利用变量n,记录数组长度
    int n = nums.length;
    // reverse()实例方法用于翻转,当我们反转后就是从大到小
    reverse(nums,n);
    // 详情至解析1
    for (int i = 1,diff = 1; i < n; i++) {
        if (nums[i - 1] != nums[i] && ++diff == 3) {
            return nums[i];
        }
    // 如果执行到这,说明有效元素小于3,返回最大元素即可
    return nums[n - 1];
}
private void reverse(int[] nums, int n) {
    // 注意:这里的布尔条件是i < n/2 ,否则会换回去
    for (int i = 0; i < n/2; i++) {
        int temp = nums[i];
        nums[i] = nums[n - i - 1 ];
        nums[n - i - 1] = temp;
    }
}
```

#### 解析1

- 为什么要从大到小的进行排序而不是从小到大?

- 解析

  - 变量:

    - `diff`表示有效数字,默认初始化为`1`
    - `i`用于遍历数组的索引

  - 代码

    - ```java
      // 如果前面不等于后面,说明有效元素+1,即diff应该前置++
      // 前置++的原因是,如果判断后,diff前置++等于3,所以i指向的就是第三个有效元素
      // 因为从大到小拍排序,所以就是我们想要的第三大元素
      if (nums[i - 1] != nums[i] && ++diff == 3) {
          return nums[i];
      }
      ```

### 集合排序算法

```java
public int thirdMax(int[] nums) {
    TreeSet<Integer> treeSet = new TreeSet<>();
    for (int num : nums) {
        treeSet.add(num);
        // 如果发现元素大于3个,将第一个删去
        // 因为从小到大排序,当元素第三大的时候,第一个元素不可能是第三大,直接删去即可
        if (treeSet.size() > 3) {
            treeSet.remove(treeSet.first());
        }
    }
    // 如果集合中的有效元素等于3,说明其原有元素大于等于3,返回第一个即可,第一个因为前面删除元素的原因,就是最小的
    // 否则返回最后一个最大的
    return treeSet.size() == 3 ? treeSet.first() : treeSet.last();
}
```

### 假设法(一次遍历)

```java
public int thirdMax3(int[] nums) {
    // 利用包装类Integer,将三个值分别赋值为null
    Integer a = null,b = null,c = null;
    // 用forech遍历数组,详情请看解析
    for (int num : nums) {
        if (a == null || num > a) {
            c = b;
            b = a;
            a = num;
        }else if (num < a && (b == null || num > b)) {
            c = b;
            b = num;
        }else if ((b != null && num < b) && (c == null || num > c)) {
            c = num;
        }
    }
    return c == null ? a : c;
}
```

#### 解析

- ```Java
  // 如果a = null 或者说 num > a
  // 说明没有最大值或者比最大值还要大赋值给a
  if (a == null || num > a) {
      // a的变化,b,c也会变化
      c = b;
      b = a;
      a = num;
   // 如果num < a,b = null 或者 比次大大,
   // 应该给b赋值num,c也变化
   // 这里要加括号,如果不加括号,或发生空指针异常
      // 如[3,3]这种情况就会发生
  }else if (num < a && (b == null || num > b)) {
      c = b;
      b = num;
   // 同理,加括号以及不等于空都是因为重复元素的原因   
  }else if ((b != null && num < b) && (c == null || num > c)) {
      c = num;
  }
  ```

  ## 算法总结

  > ​	算法主要是针对排序展开的,灵活运用排序,是解决问题的利器

  