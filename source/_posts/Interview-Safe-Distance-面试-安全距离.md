---
title: 'Interview: Safe Distance | 面试: 安全距离'
tags:
  - Data Structure
index_img: https://github.com/user-attachments/assets/b3aea179-9c53-4b9e-8819-76f7d3658c7c
banner_img: https://github.com/user-attachments/assets/b3aea179-9c53-4b9e-8819-76f7d3658c7c
math: true
abbrlink: 3dfae82
date: 2025-08-31
author: HackThink
---
## Problem 题面

In a classroom with n seats, numbered from 0 to n-1, a student must choose the seat with the largest minimum distance to any other occupied seat. If multiple seats satisfy this condition, choose the seat with the smallest number.

Implement a class Room with the following method:

在一个教室里有 n 个座位, 编号为 0,1,2,...,n−1.
当有同学进教室时，他必须选择距离最近的同学之间的距离最大的位置坐下.
如果有多个位置满足条件, 选择编号最小的那个.

实现一个类Room, 包含以下方法:

```python
class Room:
  def __init__(self, n: int):
    ...
  def seat(self) -> int:  # Return the index of the selected position 返回选中的位置
    ...
  def leave(self, pos: int):  # The person leave the classroom at position pos pos位置上的人离开教室
    ...
```

## Code 代码

```python
import heapq


class Interval:
    def __init__(self, start, end, N):
        self.start = start
        self.end = end
        self.N = N

    def dist(self):
        if self.start == -1:
            return self.end
        if self.end == self.N:
            return self.N - 1 - self.start
        return (self.end - self.start) // 2

    def __lt__(self, other):
        if self.dist() == other.dist():
            return self.start < other.start
        return self.dist() > other.dist()


class Room:
    def __init__(self, N):
        self.N = N
        self.heap = []
        self.start_map = {}
        self.end_map = {}
        self.seated = set()

        initial = Interval(-1, N, N)
        heapq.heappush(self.heap, initial)
        self.start_map[-1] = initial
        self.end_map[N] = initial

    def seat(self):
        while self.heap:
            interval = heapq.heappop(self.heap)
            start, end = interval.start, interval.end
            if start in self.start_map and self.start_map[start] == interval:
                break
        else:
            return -1
        if start == -1:
            seat = 0
        elif end == self.N:
            seat = self.N - 1
        else:
            seat = (start + end) // 2

        self.seated.add(seat)

        del self.start_map[start]
        del self.end_map[end]

        left = Interval(start, seat, self.N)
        right = Interval(seat, end, self.N)
        self.start_map[start] = left
        self.end_map[seat] = left
        self.start_map[seat] = right
        self.end_map[end] = right
        heapq.heappush(self.heap, left)
        heapq.heappush(self.heap, right)

        return seat

    def leave(self, p):
        self.seated.remove(p)

        left = self.end_map.get(p)
        right = self.start_map.get(p)

        if left:
            del self.start_map[left.start]
            del self.end_map[left.end]
        if right:
            del self.start_map[right.start]
            del self.end_map[right.end]

        new_start = left.start if left else p
        new_end = right.end if right else p
        merged = Interval(new_start, new_end, self.N)
        self.start_map[new_start] = merged
        self.end_map[new_end] = merged
        heapq.heappush(self.heap, merged)


def main():
    room = Room(10)
    print(room.seat())
    print(room.seat())
    print(room.seat())
    print(room.seat())
    room.leave(4)
    print(room.seat())


if __name__ == "__main__":
    main()
```

## Analysis 解析

The key data structures for this problem are a heap and a dictionary. Initially, analyze the problem. Each time the seat() function is called, a seat must be found that offers the "largest minimum distance between neighboring students." Although this constraint sounds complicated, it can be translated to: "Find the longest continuous unoccupied seat segment." For each continuous unoccupied segment, the middle position is the position within that segment that offers the "largest minimum distance between neighboring students." Thus, the distance is related only to the length of the current segment (specifically, half of the segment length, except for the initial and final segments, where the nearest distance is exactly twice the segment length).

这题考察的数据结构是堆+字典. 先简单分析一下题目, 每次调用seat()函数时要找到一个"距离最近的同学之间的距离最大"的位置, 尽管这个限制条件读起来相当拗口, 翻译成人话就是: "找出最大的一段连续空着的座位". 因为对于每个连续空着的座位段, 中间位置就是这个段里"距离最近的同学之间的距离最大"的位置. 所以实际上的距离只和当前段长度有关, 更具体的说是当前段长度的一半 (其中最开始的最后的段是特殊的, 因为两头没有人所以最近的距离不是段长度的一半, 而是刚好一倍).

Therefore, the problem's requirements become clear:

1. Each call to seat() finds and returns the middle position of the longest continuous unoccupied segment. After returning, the segment is split into two halves at that position.
2. Each call to leave() removes the two segments separated by the leave() position and re-merges them.

所以这题的需求就变得很清晰了:

1. 每次调用seat就把所有区间里最长的拿出来, 返回中点后把区间从那个位置掰成两半
2. 每次调用leave就把leave的位置所区分的两个区间都去掉, 重新拼接起来

The first function can be easily thought of using a heap data structure, but the second function cannot be implemented solely with a heap; it requires a dictionary for additional implementation.

第一个功能可以很容易想到堆的数据结构, 但是第二个功能只靠堆是无法实现的, 需要通过字典来额外实现

### Initialization 初始化

```python
class Interval:
    def __init__(self, start, end, N):
        self.start = start
        self.end = end
        self.N = N

    def dist(self):
        # No need need to divide by 2 because two heads nobody 因为两头没人所以长度不需要除以2
        if self.start == -1:
            return self.end
        if self.end == self.N:
            return self.N - 1 - self.start
        return (self.end - self.start) // 2

    def __lt__(self, other):
        if self.dist() == other.dist():
            return self.start < other.start
        return self.dist() > other.dist()

class Room:
    def __init__(self, N):
    self.N = N
    self.heap = []
    self.start_map = {}
    self.end_map = {}
    self.seated = set()

    initial = Interval(-1, N, N)
    heapq.heappush(self.heap, initial)
    self.start_map[-1] = initial
    self.end_map[N] = initial

```

First, design an `Interval` class to represent each continuous empty seat segment. The `dist()` function is used to calculate the maximum distance to the nearest classmate within the current segment. `__lt__(self, other)` is for calling the Python `heappush` and `heappop` functions because Python's heap operations are a min-heap by default, so the definition of `__lt__(self, other)` is actually the opposite of the actual definition.

Then, initialize the `Room` class, where `start_map` and `end_map` respectively store `Interval` objects starting/ending at key points. `seated` is used to record the set of occupied seats.

首先, 设计一个`Interval`类, 用于表示每个连续的空座位段. 其中`dist()`函数用来计算, 当前段内最大的到最近的同学的距离. `__lt__(self, other)`是为了调用后面python的`heappush`和`heappop`函数, 因为python的堆操作默认是小根堆, 所以这里`__lt__(self, other)`的定义其实是与实际定义相反的.

然后, 对`Room`类初始化, 其中`start_map`和`end_map`分别存储以key这个点开始/结束的`Interval`.`seated`则是用来记录已经有人的座位的集合.

### Seat() Function Seat() 函数

```python
def seat(self):
    while self.heap:
        interval = heapq.heappop(self.heap)
        start, end = interval.start, interval.end
        if start in self.start_map and self.start_map[start] == interval:  # Verify the interval is valid 验证这个区间是存在的
            return seat
        else:
            continue
            break
    else:
        return -1
    if start == -1:
        seat = 0
    elif end == self.N:
        seat = self.N - 1
    else:
        seat = (start + end) // 2

    self.seated.add(seat)

    del self.start_map[start]
    del self.end_map[end]

    left = Interval(start, seat, self.N)
    right = Interval(seat, end, self.N)
    self.start_map[start] = left
    self.end_map[seat] = left
    self.start_map[seat] = right
    self.end_map[end] = right
    heapq.heappush(self.heap, left)
    heapq.heappush(self.heap, right)
```

First, take the largest interval from the `heap`, and simultaneously use `start_map` and `end_map` to determine whether the current interval actually exists (explained in the `leave` function). After taking out the largest interval, determine the position where the seat should be placed, then split the interval according to the seat's position and update `start_map`, `end_map`, and `heap`.

首先从`heap`里拿出最大的区间, 同时通过`start_map`和`end_map`来判断当前取出来的区间是否实际存在 (`leave`函数中会解释为什么是这样做的). 取出最大区间后判断seat应该摆放的位置, 再把区间根据seat的位置分为两半并更新`start_map`, `end_map`与`heap`.

### Leave() Function Leave() 函数

```python
class Room:
    ...
    def leave(self, p):
        self.seated.remove(p)

        left = self.end_map.get(p)
        right = self.start_map.get(p)

        if left:
            del self.start_map[left.start]
            del self.end_map[left.end]
        if right:
            del self.start_map[right.start]
            del self.end_map[right.end]

        new_start = left.start if left else p
        new_end = right.end if right else p
        merged = Interval(new_start, new_end, self.N)
        self.start_map[new_start] = merged
        self.end_map[new_end] = merged
        heapq.heappush(self.heap, merged)
```

First, find the two segments to be merged through `start_map` and `end_map`. Then merge these two segments into a new interval and add it to the heap. Theoretically, the algorithm should remove the original two intervals from the heap and then add the new interval to the heap. However, the heap does not natively support this operation, so simply ignore it here because the `seat()` function implements a verification process that checks whether the current interval corresponds to the intervals in `start_map` and `end_map`. If it doesn't, it means that this interval no longer exists, so the algorithm will simply ignore it. This ensures the correctness of the answer even if the original interval is not deleted.

首先, 通过`start_map`和`end_map`找到需要拼接的两个段. 然后把这两个段合并成一个新的区间, 并将其加入到堆中. 理论上说, 算法应当把原来的两个区间从堆中弹出, 后将新的区间加入堆中。但是堆无法原生支持这种操作, 所以这里干脆直接不处理, 因为在之前的`seat()`函数中算法实现了一个验证处理, 查看当前取出来的区间是不是`start_map`和`end_map`对应的区间. 如果不是说明这个区间已经不存在了, 那么算法就会直接忽略它. 这样就算不删除原区间也不影响答案的正确性.

### Runtime Analysis 时间复杂度分析

算法里所有的堆操作时间复杂度都是`O(log n)`, 所以总时间复杂度为 `O(n log n)`, 这里`n`指的是操作数量.

All heap operations in the algorithm have a time complexity of `O(log n)`, so the total time complexity is `O(n log n)`. Here `n` is number of operations.

## Conclusion 总结

This question is a moderately difficult data structure problem that involves slight modifications and optimizations to the original heap operations. It requires a good grasp of basic data structure operations and implementations, and is the type of problem that is easy to come up with an idea for but requires paying attention to many details in the actual implementation.

这道题是一道难度适中的数据结构题目, 在原本堆操作的基础上稍加修改优化. 需要熟练掌握数据结构的基本操作和实现, 是容易想出思路但是实际写起来需要注意很多细节的类型.

## Rust

Don't forget about `RUST` time.

来点`RUST`

```rust
use std::{
    cmp::Reverse,
    collections::{BinaryHeap, HashMap},
};

fn main() {
    let mut room = Room::new(10);
    println!("{}", room.seat());
    println!("{}", room.seat());
    println!("{}", room.seat());
    println!("{}", room.seat());
    room.leave(4);
    println!("{}", room.seat());
}
struct Room {
    size: i32,
    heap: BinaryHeap<(i32, Reverse<i32>, Reverse<i32>)>,
    start_map: HashMap<i32, (i32, Reverse<i32>, Reverse<i32>)>,
    end_map: HashMap<i32, (i32, Reverse<i32>, Reverse<i32>)>,
}
impl Room {
    pub fn new(size: i32) -> Self {
        let mut heap = BinaryHeap::new();
        heap.push((size, Reverse(-1), Reverse(size)));
        let mut start_map = HashMap::new();
        start_map.insert(-1, (size, Reverse(-1), Reverse(size)));
        let mut end_map = HashMap::new();
        end_map.insert(size, (size, Reverse(-1), Reverse(size)));
        Room {
            size,
            heap,
            start_map,
            end_map,
        }
    }
    fn dist(&self, x: i32, y: i32) -> i32 {
        if x == -1 || y == self.size {
            return (y - x).abs() - 2;
        }
        ((y - x).abs() - 2) / 2
    }
    pub fn seat(&mut self) -> i32 {
        let mut x = -1;
        let mut y = self.size;
        while self.heap.len() > 1 {
            let interval = self.heap.pop().unwrap();
            if self.start_map.contains_key(&interval.1 .0)
                && *self.start_map.get(&interval.1 .0).unwrap() == interval
            {
                x = interval.1 .0;
                y = interval.2 .0;
                break;
            }
        }
        let seat = if x == -1 {
            0
        } else {
            if y == self.size {
                self.size - 1
            } else {
                (x + y) / 2
            }
        };
        let left = (self.dist(x, seat), Reverse(x), Reverse(seat));
        let right = (self.dist(seat, y), Reverse(seat), Reverse(y));
        self.start_map.insert(x, left);
        self.start_map.insert(seat, right);
        self.end_map.insert(seat, left);
        self.end_map.insert(y, right);
        self.heap.push(left);
        self.heap.push(right);
        seat
    }
    pub fn leave(&mut self, pos: i32) {
        let left = self.end_map.get(&pos).unwrap().1 .0;
        let right = self.start_map.get(&pos).unwrap().2 .0;
        self.start_map.remove(&pos);
        self.end_map.remove(&pos);
        self.start_map.remove(&left);
        self.end_map.remove(&right);
        let merge = (self.dist(left, right), Reverse(left), Reverse(right));
        self.start_map.insert(left, merge);
        self.end_map.insert(right, merge);
        self.heap.push(merge);
    }
}
```
