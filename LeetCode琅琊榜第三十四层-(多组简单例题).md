# LeetCode琅琊榜第三十四层-(多组简单例题)

## 题目

### 题目1

[1185. 一周中的第几天](https://leetcode.cn/problems/day-of-the-week/)

### 题目2

[1184. 公交站间的距离](https://leetcode.cn/problems/distance-between-bus-stops/)

### 题目3

[169. 多数元素](https://leetcode.cn/problems/majority-element/)

## 算法

### 1.数组替代条件判断

1. 遵循一个原则,即前面的快速解决,当前的仔细解决,即如果是2022-2-22日,从2021以及之前的年份都可以用365天快速去算,而2022年需要仔细去算,2月之前的月份都可以用对应天数去算,而2月仔细算

### 2.一次遍历算法

### 3.对冲算法

## 代码实现

### 题目1

```Java
    public String dayOfTheWeek(int day, int month, int year) {
        // 将所有的星期放入数组中
        String[] week = new String[]{"Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"};
        // 将1-11月的月份日期数放入数组中
        // 这里不需要写12月份的31,因为当我们是12月份的时候,根据算法思想,当前的仔细解决,用不上31,如果用上了说明12月过去了,即这一年就过去了,没有意义了
        int[] months = new int[]{31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30};
        // 以上两个数组都体现了利用数组替代if条件的思想
        // ----------------------------------------------------------------------------
        // (year - 1971)   即自今年到1971年一共过去了多少年,假设全是平年,所以都乘365,符合快速解决
        // (year - 1969)/4  的作用是将闰年多出来的一天全部加回去,比如1973,前面有1972是一个闰年,刚好+1,符合
        // 题目中的有效日期是1971-1-1开始的,所以参考对象就是它
        int days = 365 * (year - 1971) + (year - 1969) / 4;
        // 快速算,将前面的月份总和加起来
        for (int i = 0; i < month - 1; i++) {
            days += months[i];
        }
        // 如果是闰年,而且该月大于等于三所以过了二月,应该加1,符合快速算
        if ((year % 4 == 0 && year % 100 != 0) || year % 400 == 0 && month >= 3) {
            days += 1;
        }
        // 将剩下的日子全部加起来
        days += day;
        // days得到了自1971-1-1到如今的总天数,要想算出现在是星期几,必须知道当时是星期几(星期五)
        // 所以,我们的初始日期是星期五
        // (days % 7 + 3) % 7的意义是将总日期对7取模,可以算出多出了多少天
        // 因为1971-1-1算出来的结果是多了一天,而星期五在下标为4的地方,所以+3
        // 结果有可能大于7,所以对7继续取模
        // (days + 3) % 7 = (days % 7 + 3) % 7 是等效的
        return week[(days + 3) % 7];
    }
```

### 题目2

```java
    public int distanceBetweenBusStops(int[] distance, int start, int destination) {
        // 若起点终点一样,不用判断,直接返回0
        if (start == destination) {
            return 0;
        }
        // point1记录顺时针的总路程,point2记录逆时针总路程
        int point1 = 0;
        int point2 = 0;
        // n记录数组长度
        int n = distance.length;
        // 为了算法可行性,我们需要让start永远小于destination
        // 而且是可行的,因为A -> B 和 B -> A是等效的
        if (start > destination) {
            int tmp = start;
            start = destination;
            destination = tmp;
        }
        // 根据题意不难发现,从start 到 destination有如下规律
        for (int i = 0; i < n; i++) {
            // 顺时针是从start下标加到destination下标之前
            if (i >= start && i < destination) {
                point1 += distance[i];
            // 其他下标相加就是逆时针    
            }else {
                point2 += distance[i];
            }
        }
        // 返回较小值即可
        return Math.min(point1,point2);
    }
```



### 题目3

```Java
    public int majorityElement(int[] nums) {
        // 设置最多那个元素出现的次数是maxSize,初始化为0
        int maxSize = 0;
        // 假设出现最多的元素就是maxNum,初始化为题目不可能取到的数
        int maxNum = Integer.MIN_VALUE;
        // 由题目可知,多数元素出现的次数大于n/2次,即一定会比数组的其他元素的总和还多
        // 我们其他元素来抵消他出现的次数也不可能让该次数变为0,所以有以下代码
        // -------------------------------------------------------------------------
        for (int num : nums) {
            // 如果最多出现的次数是0了,那么就假设当前元素是出现最多的元素,并让数值加1
            if (maxSize == 0) {
                maxNum = num;
                maxSize++;
            // 如果当前值和出现最多值一致,继续加1
            } else if (maxNum == num) {
                maxSize++;
            // 否则对冲
            } else {
                maxSize--;
            }
        }
        // 最后就是我们想要的结果
        return maxNum;
    }
```

## 算法总结

> ​	算法总体比较简单,还是要注意找规律,对冲,还有零或替代条件等新思想