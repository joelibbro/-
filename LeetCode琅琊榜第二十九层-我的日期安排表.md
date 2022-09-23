# LeetCode琅琊榜第二十九层-我的日期安排表

## 题目分析

### 题目

### 要点分析

- 日期安排不可以**重复预订**,当我们的区间有相交的时候,就是**重复预订**的情况

- ```Java
  // 固定的调用格式
  MyCalendar myCalendar = new MyCalendar();
  myCanendar.book(start,end);
  ```

## 示例分析

### 示例一

```Java
输入：
["MyCalendar", "book", "book", "book"]
[[], [10, 20], [15, 25], [20, 30]]
输出：
[null, true, false, true]
解释:
	第一次先将对象的MyCalendar对象实例化出来,构造方法不传任何参数,说明包含无参构造,也不需要有参构造
	调用 book()方法,将区间[10,20)添加,无任何冲突,返回 true
	调用 book()方法,将区间[15,25)添加,与[10,20)冲突,返回 false
	调用 book()方法,将区间[20,30)添加,无任何冲突,返回 true                    
```

## 算法分析

### 直接遍历法

#### 算法思想

- 该题目的目的就是想让我们插入的区间没有任何的重叠,我们不妨以某一个区间作为参考面
- 利用循环去遍历每一个参考面,如果发现不合适的情况直接返回false
- 遍历一圈后都没有错误,说明可以插入

#### 算法图解

![image-20220707104650168](../暂存图片/image-20220707104650168.png)

### TreeSet集合法

#### 算法思想

- 利用TreeSet可排序的特性,我们可以令其根据某一个区间值进行排序
- 通过考虑可插入情况完成

#### 算法图解

![image-20220707105443256](../暂存图片/image-20220707105443256.png)

## 代码实现

### 直接遍历法

```java
// list集合用于存放区间,区间用一个数组表示,可以利用自动扩容的特性
List<int[]> list;
public MyCalendarS() {
    list = new ArrayList<>();
}
public boolean book(int start, int end) {
    // 用增强for循环遍历
    for (int[] ints : list) {
        // 根据算法图解写出
        if (start < ints[1] && end > ints[0]) {
            return false;
        }
    }
    // 执行到这说明都合法,直接加入即可
    list.add(new int[] {start, end});
    return true;
}
```

### TreeSet集合法

```Java
TreeSet<int[]> set;
public MyCalendarS() {
    // 实例化一个TreeSet对象,特别的是,这里面利用了Lambda表达式,排序的规则按照start排序,与图解相似
    set = new TreeSet<>((o1,o2) -> o1[0] - o2[0]);
}
public boolean book(int start, int end) {
    // 如果集合为空,说明一个元素都没有,大胆加入即可
    if (set.isEmpty()) {
        set.add(new int[] {start, end});
        return true;
    }
    // 因为按照索引为0进行排序,所以,如果我们想要用end和start比较的时候,需要额外有一个数组,end放在索引为0处
    int[] endCom = new int[] {end,0};
    // 解析1
    int[] bigger = set.ceiling(endCom);
    // 解析2
    if (set.first() == bigger || set.lower(endCom)[1] <= start) {
        set.add(new int[] {start, end});
        return true;
    }
    // 执行到了这,说明不成立的都没直接返回false
    return false;
}
```

#### 解析1

- 这里主要考虑了图解中的情况1和情况2,要想判断,我们需要得到后面的一个区间,所以利用ceiling()函数去获取

[^ceiling()]:天花板函数,根据索引为0,返回一个比他大的且在比他大的元素中最小的数组

#### 解析2

- **前提:end比bigger[0]小**

- 如果`bigger == set.first()`成立,说明就是情况1,该情况一定成立

[^first()]:返回根据索引为0,最小的数组

- 如果`set.lower(endCom)[1] <= start`成立,说明就是情况2,该情况一定成立

[^lower()]:返回的是根据索引为0比这个区间小的且是比他小的区间中最大的区间

- 问题:这里为什么不能用bigger找一个比lower呢?
  - 按理来说,两个都是等效的,但是待插入的区间可能是比最大的区间还要大,`bigger = null`,会导致空指针异常,所以不可以用

## 算法总结

> ​	这道题主要由两个层面出发,即**有一个情况不满足就是不满足**,或者是**找出满足的情况**两种思路,分别对应着两种算法
