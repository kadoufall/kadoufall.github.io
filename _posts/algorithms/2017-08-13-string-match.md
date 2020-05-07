---
title: 字符串匹配
date: 2017-08-13 17:24:51
tags:
    - String
    - Algorithms
---

<!-- more -->

```java
String string = "2359023141526739921";    // T
String pattern = "31415";                 // S
```

## Naive
- 从头到尾扫描`string`，对于每一个偏移，依次扫描m个连续的字符看是否都相同
- 偏移是从`0`到`n-m`，共有`n-m+1`次
- 时间复杂度为`O(m*(n-m+1))`
```java
public static void naive(String string, String pattern) {
        int n = string.length();
        int m = pattern.length();
        for (int i = 0; i <= n - m; i++) {
            boolean equal = true;
            for (int j = 0; j < m; j++) {
                if (string.charAt(i + j) != pattern.charAt(j)) {
                    equal = false;
                    break;
                }
            }
            if (equal) {
                System.out.println("Pattern occurs with shift " + i);
            }
        }
}
```

---

## Rabin-Karp算法
- 该算法的思想是用十进制数来表示每个字符串。则对于`pattern`，可用`p`表示其相应的十进制数；对于`string`，可用 t<sub>s</sub> 表示偏移`s`处连续`m`个字符对应的十进制数。这样如果能在`O(m)`时间内计算`p`，在总时间`O(n-m+1)`内计算所有的 t<sub>s</sub>，就能在总时间`O(n)`内找到所有偏移`s`
- 运用霍纳规则在`O(m)`时间内计算`p`，
```
p = P[m] + 10(P[m-1] + 10(P[m-2] + ... + 10(P[2] + P[1]) ...))
```
- 同样可以在`O(m)`时间内计算 t<sub>0</sub>，而通过t<sub>0</sub>计算后续`t`只需要常数时间
  - 若`m = 5`、`t[s] = 31415`、`T[s+5+1] = 2`， 则
  ```
  t[s+1] = 10(31415 - 3 * 10^4) +2 = 14152
  ```
```
t[s+1] = 10(t[s] - T[s+1] * 10^(m-1)) + T[s + m +1]
```
- 所以可以在总时间`O(n-m+1)`内计算所有的 t<sub>s</sub>，这里用`n-m+1`不用`n-m`是因为凸显`m=n`时继续单计算`t[s]`需要`O(1)`
- 算法有个问题是没有考虑`p`和`t[s]`的值太大的问题，所以改进的算法是，计算这些值的时候进行取模运算，
```
t[s+1] = (d(t[s] - T[s+1] * h) + T[s + m + 1]) mod q  ,  h = d*(m-1) (mod q), d进制
```
- 由于是取模运算，所以可能会出现相同值的伪命中情况，所以最坏的情况进行验证的时间为`O(m*(n-m+1))`
```
  t[s+1] = (10(7 - 3 * (10^4 % 13)))%13 +2%13 = 8
```
- 理想的情况下假设只有`c`个值是有效偏移，则匹配时间为`O((n-m+1) + cm) = O(n+m)`
- 代码实现：
```java
  rabinKarp("235902314152673992131415", "31415", 128, 6999997);
  rabinKarp("nadsuifhksfygifh", "ifh", 128, 6999997);

  public static void rabinKarp(String string, String pattern, int d, int q) {
        int n = string.length();
        int m = pattern.length();
        int h = (int) Math.pow(d, m - 1) % q;
        int p = 0;
        int t = 0;

        for (int i = 0; i < m; i++) {
            p = (d * p + pattern.charAt(i)) % q;
            t = (d * t + string.charAt(i)) % q;
        }

        for (int i = 0; i <= n - m; i++) {
            if (p == t) {
                boolean equal = true;
                for (int j = 0; j < m; j++) {
                    if (string.charAt(i + j) != pattern.charAt(j)) {
                        equal = false;
                        break;
                    }
                }
                if (equal) {
                    System.out.println("Pattern occurs with shift " + i);
                }
            }
            if (i < n - m) {
                t = (d * (t - h * string.charAt(i) % q + q) + string.charAt(i + m)) % q;
            }
        }
    }
```