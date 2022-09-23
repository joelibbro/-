# LeetCode琅琊榜第二十五层-移除元素(双指针---快慢与前后)

## 题目

### 要点分析

- `0 <= nums.length <= 100`长度可能为**0**
- 函数的返回值是**新数组的长度**
- 对原数组要**就地修改**

## 示例分析

### 示例一

```Java
输入: nums = [3,2,2,3] val = 3
输出: 2 nums = [2,2]    
```

### 示例二

```Java
输入: nums = [] val = 0
输出: 0 nums = []
```

### 示例三

```Java
输入: nums[1] val = 1
输出: 0 nums = []
```

## 算法分析

### 快慢双指针算法

#### 算法回顾

- 快慢双指针算法中,双指针的定义分别是
  - 快指针:用于遍历元素,主要起扫描作用
  - 慢指针:用于遍历已解决元素,慢指针的移动说明遍历过的元素已经解决

#### 算法思想

- 在本道题中,我们可以通过移动快指针,扫描数组,可能会遇见以下两种情况
  1. fast下标对应的元素等于`val`,继续移动快指针
  2. fast下标对应的元素不等于`val`,将对应的元素赋值给slow,slow和fast分别向前移动
- 解读:
  - slow指向的已解决元素的后一个位置,如果fast指向的是不等于`val`的元素,赋值给slow,则该slow指向的位置一定被解决了,slow可以移动,fast正常移动
  - 如果fast执行的是等于`val`的元素,赋值给slow是不合理的,slow这个位置依然没有解决,所以fast移动即可,不需要赋值

### 前后双指针算法(快慢升级版)

### 快慢双指针的弊端

- 慢指针要移动,快指针也要移动,一共加起来遍历了数组多于一次

### 算法思想

- 我们可以建立前后双指针分别是front和behind,分别初始化`front = 0 behind = nums.length`
  - 如果front指向的元素不等于`val`,front向后移动
  - 如果front指向的元素等于`val`,将behind的前面的元素赋予front,并将behind向前移动
- 算法妙处
  - front指针行使了slow指针的职责,即front也起到了元素解决才后退的作用
  - behind指针行使了fast指针的职责,即behind也起到了遍历元素的作用
  - 特别的是,behind和front一起遍历元素,减少了元素遍历总次数

## 代码实现

### 快慢双指针

```java
    public int removeElement(int[] nums, int val) {
        int slow = 0;
        int fast = 0;
        int n = nums.length;
        while (fast < n) {
            if (nums[fast] != val) {
                nums[slow] = nums[fast];
                slow++;
            }
            fast++;
        }
        // 因为slow指向被解决元素的下一个位置,即解决元素的长度,直接返回即可
        return slow;
    }
```

### 前后双指针

```Java
    public int removeElement(int[] nums, int val) {
        int front = 0;
        int behind = nums.length;
        // 详情请看解析1
        while (front < behind) {
            if (nums[front] == val) {
                nums[front] = nums[behind - 1];
                behind--;
            }else {
                front++;
            }
        }
        return front;
    }
```

#### 解析1

- `front < behind` 的原因
  - 因为behind被初始化为`nums.length`,当`front + 1 = behind`的时候本质上说明了,front指向的元素是最后一个待遍历和判断的元素,所以布尔条件这样写

#### 说明

- 该算法的妙处是,slow和front的一起遍历元素,本质是behind遍历过的要么给front赋值了,要么是`val`不能要的,所以behind遍历过的front没必要去遍历

# 算法总结

> ​	不论是快慢双指针还是前后双指针,**慢指针,前指针的移动一定要考虑清楚,判断是否真的解决了才移动,否则会造成很多不必要的麻烦**.

