---
date: 2020-01-11
tag: 
  - JavaScript
  - DataSource
author: NeoYo
location: Shenzhen
---

### 最长回文子串

[5. 最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

```
输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。
```

> 判断是否回文

```js
// Array.prototype.reverse
const isPalindrome = (str) => (
    str.split('').reverse().join('') === str;
);
// 时间复杂度： O(n)
// 空间复杂度： O(n)


// 前后对称指针
const isPalindrome = (str) => {
    const mid = str.length >> 1;
    for (let i = 0; i < mid; i++) {
        if (str[i] !== str[str.length - 1 -i]) {
           return false;
        }
    }
    return true;    
}
// 时间复杂度： O(n)
// 空间复杂度： O(1)
```

#### 解一：暴力法

1. 时间复杂度：O(n^3)
2. 两个 for 循环 * reverse 字符串比较
3. 空间复杂度：O(n)

```
/**
 * @param {string} s
 * @return {string}
 */
var longestPalindrome = function(s) {
    let maxS = '';
    
    for (let i = 0; i < s.length; i++) {
        for (let j = i; j < s.length; j++) {
            const cur = s.slice(i, j + 1);
            if (cur.split().reverse().join() !== cur) {
                break;
            }            
            if (cur.length > maxS.length) {
                maxS = cur;
            }
        }
    }
    return maxS;
};
```

> 不用 reverse，用前后指针

#### 解二：中心扩展算法

1. O(n^2)
2. 奇数的回文中心 n 个，偶数的回文中心 n - 1 个，即 2n - 1, 乘以 i 扩散就是 O(n^2)

```
/**
 * @param {string} s
 * @return {string}
 */
var longestPalindrome = function(s) {
    let maxSub = '';
    for (let i = 0; i < s.length; i++) {
        // 奇数回文 'babad'
        const oddSpreadLength = Math.min(
            s.length - 1 - i,
            i
        );
        for (let spread = 0; spread <= oddSpreadLength; spread++) {
            if (s[i + spread] !== s[i - spread]) {
                break;
            }
            if ((1 + spread * 2) > maxSub.length) {
                maxSub = s.slice(i - spread, i + spread + 1);
            }
        }
        // 偶数回文 'cbbd'
        const evenSpreadLength = Math.min(
            s.length - i,
            i
        );
        for (let spread = 0; spread <= evenSpreadLength; spread++) {
            if (s[i + 1 + spread] !== s[i - spread]) {
                break;
            }
            if ((2 + spread * 2) > maxSub.length) {
                maxSub = s.slice(i - spread, i + spread + 2);
            }
        }
    }
    return maxSub;
};
```

#### 解三：最长公共子串

1. 每个 `char` 遍历 一次 ，在 reverse 的 string indexOf
2. for O(n) * indexOf O(n) = O(n^2)
3. 检查子串的索引与反向子串的原始索引相同

#### 解四：动态规划

![](img.yuweixi.cn/Xnip2019-06-08_12-27-52.png)

#### 解五：Manacher算法

[【面试现场】如何找到字符串中的最长回文子串？  | 漫画](https://mp.weixin.qq.com/s?__biz=MzIzMTE1ODkyNQ==&mid=2649410225&idx=1&sn=ed045e8edc3c49a436a328e5f0f37a55&chksm=f0b60f53c7c18645b4c04a69ad314723cce94ed56994d6f963c2275a2db8d85f973f15f508e4&mpshare=1&scene=23&srcid=1001JCsBlpxgUWjgixasChNQ#rd)

每个扩散都有意义