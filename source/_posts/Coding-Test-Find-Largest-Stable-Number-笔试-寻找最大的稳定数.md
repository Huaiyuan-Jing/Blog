---
title: 'Coding Test: Find the Largest Stable Number | 笔试： 寻找最大的稳定数'
tags:
  - Algorithm
index_img: https://github.com/user-attachments/assets/da2f8b15-9df0-4bc4-a555-083795b63aeb 
banner_img: https://github.com/user-attachments/assets/da2f8b15-9df0-4bc4-a555-083795b63aeb
math: true
abbrlink: 49b62a13
date: 2025-08-31
author: HackThink
---

<!-- @format -->
## Problem 题面

Give a number $N$, find the largest number $k$ satisfy the following requirements smaller than $N$:

1. Sum of all digits of $k$ is prime
2. Each digits of $k$ is larger than or equals to the previous digit (e.g. 123456, 111111)

给定一个上限$N$, 寻找最大的小于$N$的数字$k$，要求满足以下条件：

1. $k$的各个数位之和是素数
2. $k$的每一位都比前一位大 (比如 123455, 11111)

### Input 输入

One line with one number $N$

一行，一个数字$N$

### Output 输出

One line with one number $k$ satisfy the requirements or $-1$ if there is no solution

一行，一个满足条件的数字$k$，如果没有符合条件的答案输出$-1$

### Size and Limits 数据规模和限制

$N \leq 10^{18}$

Time Limits 时间限制: 200ms

## Solution 题解

Obviously, the simplest approach is to try each number from $N$ down to $1$; if it satisfies the condition, return it, otherwise return $-1$. However, given the data scale up to $10^{18}$, this approach will definitely time out. By observing that the number of satisfying numbers is much smaller than the total range, we use a reverse thinking: generate numbers that meet the conditions via a search algorithm.

显然，最简单的做法就是从$N$到$1$一个个试过去，找到了就返回答案，没找到就返回$-1$。但考虑到$10^{18}$的数据规模，这个做法一定会超时。通过观察可以简单得到，满足条件的数的规模远小于上限内所有数的规模，所以采取逆向思维，从条件入手用搜索算法生成满足条件的数字。

First list all possible primes via the sieve method

首先，通过筛法列出所有可能的素数：

```python
num = int(input().strip())

def primes(n):
    sieve = [True] * (n + 1)
    sieve[0] = sieve[1] = False
    for i in range(2, int(math.sqrt(n)) + 1):
        if sieve[i]:
            for j in range(i * i, n + 1, i):
                sieve[j] = False
    return [i for i in range(n + 1) if sieve[i]]

primes_list = primes(len(str(num)) * 9 + 1)
```

For each prime, find the largest number with non-decreasing digits and digit-sum equal to that prime

然后，对于每一个素数，我们要找到最大的满足条件2且和为当前这个素数的数字。然后再这些数字里面找到最大的值进行返回

```python
ans = -1
for p in primes_list:
    digits = [0 for _ in range(len(str(num)))]
    upper_limit = [int(d) for d in str(num)]
    left = p
    res = largest_stable_number(0, digits, left, upper_limit)
    if res != -1:
        ans = max(ans, res)
print(ans)
```

Implement the DFS function to build the largest valid number

1. Convert the number to a digit array for easy generation.
2. If the sum of digits so far exceeds the target prime, prune this branch.
3. If all digits are assigned and the remaining sum is zero, return the constructed number.
4. Generate digits from most significant to least significant, iterating from the maximum allowable digit down to the minimum to ensure the first found solution is the largest.
5. Determine the upper bound for the current digit: if all previous digits match the corresponding digits of $N$, the upper bound is that digit of N; otherwise it is $9$.
6. Determine the lower bound for the current digit: it must be at least the previous digit to maintain non-decreasing order (or $0$ if it’s the first digit).
7. For the current position, try all digits in [lower_bound, upper_bound] in descending order; if a recursive call returns a valid number, return it immediately.
8. If no digit works, return -1.

最后，我们来具体实现对于每一个素数，找到最大的满足条件的数字的dfs函数：

1. 首先把数字转化为对应的数位数组，方便生成数字。
2. 如果当前数位数组之和已经超过指定的素数，说明当前的dfs方向已经不存在解，退回到上一层。
3. 如果已经完成了所有位的生成，且和刚好等于指定的素数，那么则找到答案并返回解。
4. 从高位到低位一位位生成，对于每一位，从范围内最大的数字往范围内最小的数字遍历，保证找到的第一个结果就是最大的结果。
5. 判断当前位数字的上界：如果之前所有位的数字都等于$N$的对应位，那么如果当前位大于了$N$的对应位，就会超过范围，所以这种情况下上限为$N$的对应位的数字；如果之前已经有某一位的数字小于$N$的对应位，那么无论当前位的数字大小是多少，该数字都不可能超过范围，所以上限为单个位的最大值$9$。
6. 判断当前位数字的下界：题目要求当前位大于等于前一位，所以下界就是前一位的数字。
7. 对于当前位从大到小遍历范围内所有值，如果找到答案直接返回值退出。
8. 如果没有找到解，返回-1

```python
def largest_stable_number(cur: int, digits, left: int, upper_limit) -> int:
    if left < 0:
        return -1
    if cur == len(digits):
        if left != 0:
            return -1
        return int(''.join(map(str, digits)))
    with_limit = True
    for i in range(cur):
        if digits[i] < upper_limit[i]:
            with_limit = False
            break
    top = upper_limit[cur] if with_limit else 9
    for i in range(top, (0 if cur == 0 else digits[cur - 1])-1, -1):
        digits[cur] = i
        res = largest_stable_number(cur + 1, digits, left - i, upper_limit)
        if res != -1:
            return res
    return -1
```

## Code 完整代码

```python
import math

num = int(input().strip())

def primes(n):
    sieve = [True] * (n + 1)
    sieve[0] = sieve[1] = False
    for i in range(2, int(math.sqrt(n)) + 1):
        if sieve[i]:
            for j in range(i * i, n + 1, i):
                sieve[j] = False
    return [i for i in range(n + 1) if sieve[i]]

primes_list = primes(len(str(num)) * 9 + 1)

def largest_stable_number(cur: int, digits, left: int, upper_limit) -> int:
    if left < 0:
        return -1
    if cur == len(digits):
        if left != 0:
            return -1
        return int(''.join(map(str, digits)))
    with_limit = True
    for i in range(cur):
        if digits[i] < upper_limit[i]:
            with_limit = False
            break
    top = upper_limit[cur] if with_limit else 9
    for i in range(top, (0 if cur == 0 else digits[cur - 1])-1, -1):
        digits[cur] = i
        res = largest_stable_number(cur + 1, digits, left - i, upper_limit)
        if res != -1:
            return res
    return -1

ans = -1
for p in primes_list:
    digits = [0 for _ in range(len(str(num)))]
    upper_limit = [int(d) for d in str(num)]
    left = p
    res = largest_stable_number(0, digits, left, upper_limit)
    if res != -1:
        ans = max(ans, res)
print(ans)
```

And for Rust Lovers, here is the Rust version of code:

对于像我一样的Rust爱好者来说，下面是Rust的版本：

```rust
fn main() {
    let n = read_i64();
    let num_digits = n.abs().to_string().len();
    let primes = primes(num_digits as i64 * 9);
    let mut ans = -1 as i64;
    let limit = n
        .abs()
        .to_string()
        .chars()
        .map(|c| c.to_digit(10).unwrap() as i32)
        .collect();
    for &prime in primes.iter() {
        let result = largest_stable_number(0, &mut vec![0; num_digits], &limit, prime, true);
        ans = ans.max(result);
    }
    println!("{}", ans);
}
fn read_i64() -> i64 {
    use std::io::{self, Read, Write};
    let mut number = String::new();
    io::stdout().flush().unwrap();
    let mut stdin = io::stdin();
    let mut buffer = [0; 1];
    let mut started = false;
    while let Ok(()) = stdin.read_exact(buffer.as_mut()) {
        let c = buffer[0] as char;
        if !started {
            if c == '-' || c.is_numeric() {
                started = true;
                number.push(c);
            }
        } else {
            if c.is_numeric() {
                number.push(c);
            } else {
                break;
            }
        }
    }
    number.parse::<i64>().unwrap_or(0)
}
fn primes(upper_limit: i64) -> Vec<i64> {
    let mut sieve = vec![true; (upper_limit + 1) as usize];
    sieve[0] = false;
    sieve[1] = false;
    for i in 2..=((upper_limit as f64).sqrt() as i64) {
        if !sieve[i as usize] {
            continue;
        }
        for j in (i * i..=upper_limit).step_by(i as usize) {
            sieve[j as usize] = false;
        }
    }
    sieve
        .iter()
        .enumerate()
        .filter_map(|(i, &is_prime)| if is_prime { Some(i as i64) } else { None })
        .collect()
}
fn largest_stable_number(
    cur: usize,
    digits: &mut Vec<i32>,
    limit: &Vec<i32>,
    leftover: i64,
    with_limit: bool,
) -> i64 {
    if leftover < 0 {
        return -1;
    }
    if cur == digits.len() {
        if leftover != 0 {
            return -1;
        }
        let mut result = 0 as i64;
        for &digit in digits.iter() {
            result = result * 10 + digit as i64;
        }
        return result;
    }
    let upper_bound = if with_limit { limit[cur] } else { 9 };
    let lower_bound = if cur == 0 { 0 } else { digits[cur - 1] };
    for i in (lower_bound..=upper_bound).rev() {
        digits[cur] = i;
        let mut new_with_limit = with_limit;
        let mut new_leftover = leftover;
        if i < limit[cur] {
            new_with_limit = false;
        }
        new_leftover -= i as i64;
        let result = largest_stable_number(cur + 1, digits, limit, new_leftover, new_with_limit);
        if result != -1 {
            return result;
        }
    }
    -1
}
```

## Conclusion 总结

The main focus of this problem is search pruning; a solid foundation in search algorithms is sufficient to solve it easily.

这题的考察点主要是搜索剪枝，有扎实的搜索算法基础即可轻松拿下本题。
