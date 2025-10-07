---
title: codelib
date: 2025-09-17 11:46:38
banner_img: https://github.com/user-attachments/assets/bcf154a6-768d-42e3-81ba-b1108af0ec53
banner_img_height: 50
banner_mask_alpha: 0.5
comment: True
---
## Readline From Keyboard
```Rust
fn _readline<T>() -> Vec<T>
where
    T: std::str::FromStr,
    <T as std::str::FromStr>::Err: std::fmt::Debug,
{
    let mut input = String::new();
    std::io::stdin().read_line(&mut input).unwrap();
    input
        .split_whitespace()
        .filter_map(|word| word.parse().ok())
        .collect()
}
```

## Prim
```Rust
fn prim(adj_list: &Vec<Vec<(usize, i32)>>) -> (i32, Vec<(usize, usize, i32)>) 
/* output: <cost of mst, Vec<(from, to, cost of edge)>> */
{
    use std::{
        cmp::Reverse,
        collections::{BinaryHeap, HashSet},
    };
    let mut total_cost = 0;
    let mut mst_edges: Vec<(usize, usize, i32)> = Vec::new();
    let mut visited: HashSet<usize> = HashSet::new();
    let mut pq: BinaryHeap<(Reverse<i32>, usize, usize)> = BinaryHeap::new();

    // Start from node 0
    visited.insert(0);

    // Add initial edges to the priority queue
    for &(neighbor, cost) in &adj_list[0] {
        pq.push((Reverse(cost), neighbor, 0));
    }

    while visited.len() < adj_list.len() {
        if pq.is_empty() {
            // Graph is not connected
            return (-1, Vec::new());
        }

        let (Reverse(cost), to, from) = pq.pop().unwrap();

        if visited.contains(&to) {
            continue;
        }

        visited.insert(to);
        total_cost += cost;
        mst_edges.push((from, to, cost));

        // Add new edges to the priority queue
        for &(neighbor, edge_cost) in &adj_list[to] {
            if !visited.contains(&neighbor) {
                pq.push((Reverse(edge_cost), neighbor, to));
            }
        }
    }

    (total_cost, mst_edges)
}
```

## Binary Indexed Tree
```Rust
struct BinaryIndexTree {
    tree: Vec<i32>,
    n: usize,
}
impl BinaryIndexTree {
    fn new(n: usize) -> Self {
        BinaryIndexTree {
            tree: vec![0; n + 1],
            n,
        }
    }
    fn update(&mut self, i: usize, k: i32) {
        let mut i = i as i32 + 1;
        while i <= self.n as i32 {
            self.tree[i as usize] += k;
            i += i & -i;
        }
    }
    fn query(&self, i: usize) -> i32 {
        let mut i = i as i32 + 1;
        let mut s = 0;
        while i > 0 {
            s += self.tree[i as usize];
            i -= i & -i;
        }
        s
    }
}
```
