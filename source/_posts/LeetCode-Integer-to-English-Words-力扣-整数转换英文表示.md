---
title: 'LeetCode: Integer to English Words | 力扣: 整数转换英文表示'
tags:
  - Algorithm
index_img: https://github.com/user-attachments/assets/7ab8204a-6598-428a-aea2-8fe20005da31
banner_img: https://github.com/user-attachments/assets/7ab8204a-6598-428a-aea2-8fe20005da31 
math: true
abbrlink: 3911b54c
date: 2025-08-31
---
## Problem 题面

<https://leetcode.cn/problems/integer-to-english-words/description/>

## Code 代码

```Rust
impl Solution {
    fn print_a_thousand(num: i32) -> String {
        use std::collections::HashMap;
        let mut ans = String::new();
        let nums = vec![
            (0, ""),
            (1, "One"),
            (2, "Two"),
            (3, "Three"),
            (4, "Four"),
            (5, "Five"),
            (6, "Six"),
            (7, "Seven"),
            (8, "Eight"),
            (9, "Nine"),
            (10, "Ten"),
            (11, "Eleven"),
            (12, "Twelve"),
            (13, "Thirteen"),
            (14, "Fourteen"),
            (15, "Fifteen"),
            (16, "Sixteen"),
            (17, "Seventeen"),
            (18, "Eighteen"),
            (19, "Nineteen"),
            (20, "Twenty"),
            (30, "Thirty"),
            (40, "Forty"),
            (50, "Fifty"),
            (60, "Sixty"),
            (70, "Seventy"),
            (80, "Eighty"),
            (90, "Ninety"),
        ];
        let num_to_string: HashMap<_, _> = nums.into_iter().collect();
        if num >= 100 {
            ans += num_to_string[&(num / 100)];
            ans += " Hundred ";
        }
        if num % 100 < 20 {
            ans += num_to_string[&(num % 100)];
            return ans.trim().to_string();
        }
        ans += num_to_string[&(num % 100 - num % 10)];
        ans += " ";
        ans += num_to_string[&(num % 10)];
        ans.trim().to_string()
    }
    pub fn number_to_words(num: i32) -> String {
        if num == 0 {
            return "Zero".to_string();
        }
        let out = num / 1000000000;
        let mut ans = String::new();
        if out > 0 {
            ans = format!("{} {} ", Solution::print_a_thousand(out), "Billion"); 
        }
        let out = num / 1000000 % 1000;
        if out > 0 {
            ans = format!("{}{} {} ", ans, Solution::print_a_thousand(out), "Million"); 
        }
        let out = num / 1000 % 1000;
        if out > 0 {
            ans = format!("{}{} {} ", ans, Solution::print_a_thousand(out), "Thousand"); 
        }
        let out = num % 1000;
        if out > 0 {
            ans = format!("{}{}", ans, Solution::print_a_thousand(out)); 
        }
        ans.trim().to_string()
    }
}
```

## Analysis 分析

This problem implements an interesting feature: converting a number to its English representation. This feature is often used when writing articles, so I specifically implemented this problem.

First, check the problem's data range. $2 ^ {31} - 1 = 2147483647$, so the English magnitude needs to be sufficient for billion. Then, according to the English number expression, in English, billion, million, and thousand are represented by a number every three digits. So the entire program can be divided into two steps:

1. Implement English representation of numbers within 1000
2. Call the above function every three digits to output, and connect all outputs with billion, million, and thousand.

这道题实现了一个有意思的小功能, 把一个数字转换为英文表示. 这个功能在我写文章的时候经常用到, 所以特地实现了一下本题.

首先查看题目的数据范围, $2 ^ {31} - 1 = 2147483647$, 所以英文上数量级到billion足以. 然后根据英文数字的表达方式, 英语中billion, million, thousand是以每三个位为一个数字来表示的. 所以整个程序可以分为两步:

1. 实现了1000以内数字的英文表示
2. 每三位调用上面的函数来输出, 并把所有的输出通过billion, million和thousand连接在一起

### 1. English Representation of Numbers smaller than 1000 一千以内数字的英文表示

First, initialize the numbers that can be represented by a single word from 1 to 90 using a dictionary. Then extract the hundreds digit and output it with "Hundred", and extract the ones and tens digits. If it's less than 20, output the corresponding word directly. Otherwise, output it by adding the tens digit and the ones digit.

首先, 把1-90能用一个单词表示的数字用字典初始化. 然后先提取百分位输出并加上Hundred, 再提取个位和十位的数字, 如果小于20就直接输出对应单词, 否则通过十位加个位的方法来输出.

```Rust
fn print_a_thousand(num: i32) -> String {
    use std::collections::HashMap;
    let mut ans = String::new();
    let nums = vec![
        (0, ""),
        (1, "One"),
        (2, "Two"),
        (3, "Three"),
        (4, "Four"),
        (5, "Five"),
        (6, "Six"),
        (7, "Seven"),
        (8, "Eight"),
        (9, "Nine"),
        (10, "Ten"),
        (11, "Eleven"),
        (12, "Twelve"),
        (13, "Thirteen"),
        (14, "Fourteen"),
        (15, "Fifteen"),
        (16, "Sixteen"),
        (17, "Seventeen"),
        (18, "Eighteen"),
        (19, "Nineteen"),
        (20, "Twenty"),
        (30, "Thirty"),
        (40, "Forty"),
        (50, "Fifty"),
        (60, "Sixty"),
        (70, "Seventy"),
        (80, "Eighty"),
        (90, "Ninety"),
    ];
    let num_to_string: HashMap<_, _> = nums.into_iter().collect();
    if num >= 100 {
        ans += num_to_string[&(num / 100)];
        ans += " Hundred ";
    }
    if num % 100 < 20 {
        ans += num_to_string[&(num % 100)];
        return ans.trim().to_string();
    }
    ans += num_to_string[&(num % 100 - num % 10)];
    ans += " ";
    ans += num_to_string[&(num % 10)];
    ans.trim().to_string()
}
```

### 2. Representation of the whole number 整个数字的表示

Extract the digits every three digits, call the `num_to_string` function, then add billion, million, and thousand according to the number of digits. And connect them together. Pay attention to handling spaces and trailing blanks.

每三位提取出数字调用 `num_to_string` 函数，然后根据位数来添加billion, million和thousand. 并将他们连接在一起. 注意处理空格和末尾空白.

```Rust
pub fn number_to_words(num: i32) -> String {
    if num == 0 {
        return "Zero".to_string();
    }
    let out = num / 1000000000;
    let mut ans = String::new();
    if out > 0 {
        ans = format!("{} {} ", Solution::print_a_thousand(out), "Billion"); 
    }
    let out = num / 1000000 % 1000;
    if out > 0 {
        ans = format!("{}{} {} ", ans, Solution::print_a_thousand(out), "Million"); 
    }
    let out = num / 1000 % 1000;
    if out > 0 {
        ans = format!("{}{} {} ", ans, Solution::print_a_thousand(out), "Thousand"); 
    }
    let out = num % 1000;
    if out > 0 {
        ans = format!("{}{}", ans, Solution::print_a_thousand(out)); 
    }
    ans.trim().to_string()
}
```

## Conclusion 总结

This problem is a simple string processing problem with a medium-easy difficulty. Regarding the fact that this problem is in the mathematics section on LeetCode and has a hard difficulty rating, I am confused, but it is indeed a pretty interesting problem.

这道题是一道简单的字符串处理题目, 难度在中等偏简单. 至于LeetCode上这道题是数学分区且难度分级是Hard我表示疑惑, 不过确实是挺有意思的一题.
