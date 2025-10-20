---
title: 'ICPC: Mob Grinder'
tags:
  - Algorithm
math: true
abbrlink: b3ee6a2b
date: 2025-09-19 17:55:20
index_img: https://github.com/user-attachments/assets/35f82151-a90c-4aa0-bbdf-804383675d38
banner_img: https://github.com/user-attachments/assets/35f82151-a90c-4aa0-bbdf-804383675d38
author: HackThink
---
## Problem

[Link of Problem](https://open.kattis.com/problems/mobgrinder)

## Solution

This is an interesting constructive problem.

### Step 1: Analysis Illegal Situation

First we need to figure out in what kind of situation, it is an impossible task. Our target is to move all possible monsters on any position to the top-right corner. Obviously, the position takes most distance to move is the bottom-left corner. If we don't have enough "Up" and "Right" belts to move monster on that position to the top-right corner, we cannot get legal solution. So if R < M - 1 or U < N - 1, print "impossible"

### Step 2: Get the Main Path

Now we know, there must exist a path from bottom-left corner to top-right corner. And to simplify our construciton, we can assume this path only contains "Up" and "Right" because moving "Left" or "Down" is unnecessary and wasty. For 4*3 map, one of such example is like the following:

|_|R|*|
| :----------: | :--------:| :----------: |
|_|U|_|
|_|U|_|
|R|U|_|

We consider this path as "main path"

### Step 3: Guide to Main Path

Now we have the "Main Path". For positions main path covered we don't need to worry about them. For the others, we need to try to move monsters generate on them to the main path. We can see the main path cut the whole graph into two halfs: top-left half and bottom-right half, we call it Zone1 and Zone2. 

|1|R|*|
| :----------: | :--------:| :----------: |
|1|U|2|
|1|U|2|
|R|U|2|

If we spend some time, we will find if we only put "Right" and "Down" in Zone1, no matter how we put it, monsters in Zone1 will eventually get on the main path and will not stuck in loop or move out of the graph. And this theorem works for Zone2 as well:

|R|R|*|
| :----------: | :--------:| :----------: |
|D|U|U|
|D|U|L|
|R|U|L|

### Step 4: Generate Solution

Now we can easily generate the solution based on these properties:

1. There must exist a path from bottom-left and top-right
2. Zone1 only contains "Right" and "Down"
3. Zone2 only contains "Left" and "Up"

And here is the exact steps:

1. Calculate the number of "R" "L" "D" "U" left after we build the main path
2. Put all "R" and "D" on the top-left part, and all "L" and "U" on the bottom-right part, and make sure Zone1 and Zone2 don't touch each other
3. After we built Zone1 and Zone2, the grids still empty form the main path.

## Code

```Rust
fn main() {
    for _ in 0.._readline_usize()[0] {
        let [n, m, u, r, d, l] = _readline_usize().try_into().unwrap();
        if u < n - 1 || r < m - 1 {
            println!("impossible\n");
            continue;
        }
        let mut u = u - (n - 1);
        let mut d = d;
        let mut l = l;
        let mut r = r - (m - 1);
        let mut g = vec![vec!['*'; m]; n];
        for i in 0..n - 1 {
            for j in 0..m - 1 {
                if r > 0 {
                    g[i][j] = 'R';
                    r -= 1;
                    continue;
                }
                if d > 0 {
                    g[i][j] = 'D';
                    d -= 1;
                    continue;
                }
                break;
            }
        }
        for i in (1..n).rev() {
            for j in (1..m).rev() {
                if u > 0 {
                    g[i][j] = 'U';
                    u -= 1;
                    continue;
                }
                if l > 0 {
                    g[i][j] = 'L';
                    l -= 1;
                    continue;
                }
                break;
            }
        }
        let mut x = n - 1;
        let mut y = 0 as usize;
        while !(x == 0 && y == m - 1) {
            if x > 0 && g[x - 1][y] == '*' {
                g[x][y] = 'U';
                x -= 1;
                continue;
            }
            if y < m - 1 && g[x][y + 1] == '*' {
                g[x][y] = 'R';
                y += 1;
                continue;
            }
        }
        for i in 0..n {
            for j in 0..m {
                print!("{}", g[i][j]);
            }
            println!();
        }
        println!();
    }
}
fn _readline_usize() -> Vec<usize> {
    let mut input = String::new();
    std::io::stdin()
        .read_line(&mut input)
        .expect("Failed to get input");
    input
        .split_whitespace()
        .filter_map(|word| word.parse().ok())
        .collect()
}
```