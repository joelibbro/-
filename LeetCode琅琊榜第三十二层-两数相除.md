# LeetCode琅琊榜第三十二层-两数相除

## 题目解析

### 题目

### 要点分析

> - <font color = red>**要求不可以用乘法,除法和取模**</font >
> - **被除数**:`dividend `
> - **除数**:`divisor`
> - 如果除法结果大于`Integer.MAX_VALUE`,则返回`Integer.MAX_VALUE`
> - 被除数和除数均为32位整数,且除数不为0,所以不需要考虑除法出错的情况

## 算法分析

### 折半查找法+(折半)快速加算法

#### 算法前提

- 我们需要将除法先转换为乘法
  - 如果我们直接用除法去找某一个数,不用找了,直接违背了题意,所以不可行
  - 虽然说乘法也是不给用的,但是我们可以用(折半)快速加的算法来替代我们的乘法,从而实现乘法,找到最终的数
- 我们不妨将除数和被除数都设置为同号,这样的的的数一定大于0,不需要我们再去考虑有符号所带来的各种影响

#### 算法思想

##### 二分查找思想

- 由于算法前提可知,我们的的数只可能是大于或者等于0的数,所以,我们只需要从大于等于0的数字去二分查找即可
- .....

##### 快速加思想

- 快速加的思想本质上是让两个乘子发生变化,即折半变化
- 通过位运算法一个乘子进行折半运算,通过加法运算让另一个乘子进行增倍运算,所得结果依然不变

#### 算法图解

![image-20220717185129902](../暂存图片/image-20220717185129902.png)

## 代码分析

```java
public int divide(int dividend, int divisor) {
    // 特殊情况的判断可以利用提高算法效率
    // 若被除数是Integer.MIN_VALUE,即题目规定的最小值,会有两种直接得出答案的情况
    if (dividend == Integer.MIN_VALUE) {
        // 若除数为-1,理应为-Integer.MIN_VALUE,但是该值比Iteger.MAX_VALUE大,根据题目应该做出如下改变
        if (divisor == -1)
            return Integer.MAX_VALUE;
        // 若除数为-1,就是它本身
        else if (divisor == 1)
            return Integer.MIN_VALUE;
    }
    // 如果除数是Integer.MIN_VALUE,只会有两种情况
    if (divisor == Integer.MIN_VALUE) {
        // 如果被除数和除数一样则为1,否则都是0
        return dividend == Integer.MIN_VALUE ? 1 : 0;
        // 注意:除数是Integer.MAX_VALUE稍加判断也可以加
    }
    // 被除数为0,商一定为0
    if (dividend == 0) {
        return 0;
    }
    // ----------------------------------------------------------------------------------------
    // rev变量用于判断,最终的结果是否是负数
    boolean rev = false;
    // 解析1
    if (dividend > 0) {
        dividend = -dividend;
        rev = !rev;
    }
    if (divisor > 0) {
        divisor = -divisor;
        rev = !rev;
    }
    // 因为结果一定大于等于0,所以寻找的范围就在left与right之间.ans初始化为0超细节,不用找0了,默认为0
    int left = 1;
    int right = Integer.MAX_VALUE;
    int ans = 0;
    // 二分
    while (left <= right) {
        // 因为不可以用除法,所以用移位达到一半的效果
        int mid = left + ((right - left) >> 1);
        // 快速加算法,达到乘的效果
        boolean state = quickAdd(mid,dividend,divisor);
        if (state) {
            // 进来说明不够加或刚刚好,先记录下来
            ans = mid;
            // 主要是预防结果是当mid正好等于该值,left = mid + 1导致的越界死循环
            if (mid == Integer.MAX_VALUE)
                break;
            // 往右边找,会越来越接近
            left = mid + 1;
        }else {
            // 往左边找
            right = mid - 1;
        }
    }
    // 依据定义判断即可
    return rev ? -ans : ans;
}

private boolean quickAdd(int z, int x, int y) {
    // result的定义是z为偶数时的乘积
    int result = 0;
    // 待相乘的算子
    int add = y;
    // 如果z != 0,说明还没有加完
    while (z != 0) {
        // 如果该条件成立,说明z是奇数,不可以折半,故要执行以下代码
        if ((z & 1) != 0) {
            // 如果result < x - add
            // 即result + add < x,就是负的比目标值还多,加过头了,返回false
            if (result < x - add)
                return false;
            // 否则,将add提出来,就是result += add(算法图解)
            // 来达成分离的目的
            result += add;
        }
        // 如果z != 1,说明还不是 1 * 某个数,即这个数还能被拆一半,另一个数加一倍,所以执行以下代码
        if (z != 1) {
            // 同理
            if (add < x - add)
                return false;
            // 加一倍
            add += add;
        }
        // 完成拆一半的目的
        z >>= 1;
    }
    //执行到了这只有两种结果,要么不够加,要么刚刚好
    return true;
}
```

### 解析1

> 1. 很容易看出,该算法的目的是将除数和被除数都变成负数**(前提,也可以都为正,效果一致)**
> 2. 两个`!rev`彰显算法的妙,如果有一个为正,则`rev = true`,如果有两个为正,则`rev = false`,和两个为负的结果一致,既变号,又不改变

## 算法总结

> - **只要把算法图解看懂就全部都会了!**