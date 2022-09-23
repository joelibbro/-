# LeetCode琅琊榜第三十七层-插入区间

## 题目链接

#### [57. 插入区间](https://leetcode.cn/problems/insert-interval/)

## 算法思想-模拟算法(分类讨论算法)

> ​	对于插入问题,我们可以采用分类讨论的方法,我们可以以第一个数字和全部的数字作为两个参考的比较对象!

| 研究对象         | 判断方法                                                  |
| ---------------- | --------------------------------------------------------- |
| 第一个数字或区域 | 我们以该数字作为参考,考虑待插入的数字是否在第一个数字之前 |
| 全部的数字或区域 | 我们以该数字作为参考,考虑待插入的数字是否在该数字之后     |

### 本题题解

| 研究对象                                     | 判断方式                                                     | 结论                             |
| -------------------------------------------- | ------------------------------------------------------------ | -------------------------------- |
| 第一个区间[L<sub>1</sub>,L<sub>2</sub>]/并集 | 判断新区间[left,right]中的right是否小于L<sub>1</sub>,若小于说明新区间在第一个区间之前 | 第一个区间与新区间不可能产生交集 |
| 第一个区间[L<sub>1</sub>,L<sub>2</sub>]      | 若大于L<sub>1</sub>,则可能产生交集或者不会产生交集,需要下一步判断 | 不可判断                         |
| 全部区间[L<sub>i</sub>,L<sub>j</sub>]        | 判断新区间[left,right]中的left是否大于L<sub>j</sub>,若大于说明新区间在该区间后面 | 该区间与新区间不可能产生交集     |
| 全部区间[L<sub>i</sub>,L<sub>j</sub>]        | 否则                                                         | 该区间与新区间产生交集           |

## 代码实现

```java
    public int[][] Insert(int[][] intervals, int[] newInterval) {
        // 分别记录新区间的左端点和右端点
        int left = newInterval[0];
        int right = newInterval[1];
        // 用于记录结果的集合
        // 思想来自`合并区间`
        ArrayList<int[]> resList = new ArrayList<>();
        // 新区间默认没插入,初始化为false
        boolean isAdd = false;
        // 遍历题目所给的数组
        for (int[] curEle : intervals) {
            // 对应思想的第一行
            // 注意的是,产生交集后,需要再到这里判断
            if (right < curEle[0]) {
                // 如果成立,说明对应的结论,应该把新区间插入结果集合中
                if (!isAdd) {
                    resList.add(new int[] {left, right});
                    // 初始化为true,如果执行到了此处,说明以后的判断都会成立,我们要避免后面再添加newInterval,所以要初始化为true
                    isAdd = true;
                }
                // 所以对应的区间也应该加入
                resList.add(curEle);
            }else if (left > curEle[1]) {
                // 如果成立,说明对应的结论,应该把区间放入结果集合中
                resList.add(curEle);
            }else {
                // 说明最后一种情况,产生了交集,所以我们需要找出最小值和最大值,取得并集把交集抵消掉
                // 注意:不可以直接将结果放入结果集合中,因为有可能后面的区间与该区间继续有交集,且产生不必要的错误
                left = Math.min(curEle[0], left);
                right = Math.max(curEle[1], right);
            }
        }
        // 如果始终都没加进去,说明新区间的left比最大区间的right都大,所以,最后判断加入
        if (!isAdd) {
            resList.add(new int[] {left, right});
        }
        // 返回结果
        return resList.toArray(new int[resList.size()][2]);
    }
```



## 相似题目

#### [56. 合并区间](https://leetcode.cn/problems/merge-intervals/)
