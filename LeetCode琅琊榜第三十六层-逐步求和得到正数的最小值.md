# LeetCode琅琊榜第三十六层-逐步求和得到正数的最小值

[1413. 逐步求和得到正数的最小值](https://leetcode.cn/problems/minimum-value-to-get-positive-step-by-step-sum/)

## 贪心算法

```java
// 题目给出条件,startValue为最小正数,注意,正数不包含0,且该正数在数组中不一定会出现,startValue任由我们发挥
// 不妨得出以下的规律: [curSum]:当前数值总和 [minValue]:最小数值总和,minValue是curSum的一种特殊情况,如果满足
// curSum + startValue永远大于等于1,即说明了minValue + startValue >= 1
// startValue + curSum >= 1 || startValue + minValue >= 1
// 题目又给出取出最小正数,所以minValue + startValue = 1就是取出最小正数的结果
// startValue = 1 - minValue
public int minStartValue(int[] nums) {
    // 初始化为0,当所有的数值都大于0时,结果毋庸置疑,肯定大于1,所以,我们可以给最小正数1,刚好初始化为0可以完成
    // 该任务
    int minValue = 0;
    int curSum = 0;
    // 统计curSum从而获取最小的minValue
    for (int num : nums) {
        curSum += num;
        minValue = Math.min(curSum, minValue);
    }
    // 上述所示
    return - minValue + 1;
}
```