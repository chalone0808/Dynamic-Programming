# Dynamic-Programming
Dynamic Programming

## Example 1:

找到一个数组中的一个和，其每个元素不相邻，求和最大
        
    OPT(6) = { OPT(4)+arr[6]   -> 选择6
          { OPT(5)       -> 不选
    .......
    OPT(i) = max { OPT(i-2) + arr[i] -> chosen
             { OPT(i-1)       -> not chosen
    终止条件：
    OPT(0) = arr[0]
    OPT(1) = max(arr[0], arr[1])
    
``` Python
arr = [1,2,4,1,7,8,3]
arr1 = [1]

# 递归做法
def rec_opt(arr, i):
    if i == 0:
        return arr[0]
    elif i == 1:
        return max(arr[0],arr[1])
    else:
        A = rec_opt(arr, i-2) + arr[i]
        B = rec_opt(arr, i-1)
        return max(A, B)
    
# 动态规划做法
def dp_opt(arr):
    if len(arr) == 1:
        return arr[0]
    opt = [0] * len(arr)
    opt[0] = arr[0]
    opt[1] = max(arr[0], arr[1]) 
    for i in range(2, len(arr)):
        A = opt[i-2] + arr[i]
        B = opt[i-1]
        opt[i] = max(A, B)
    return opt[len(arr) - 1]
```

## Example 2

找一堆数组中是否有几个数相加能得到S

            0  1  2  3  4  5
    arr = {3, 34, 4, 12, 4, 2}
    S = 9
    对每个数组都有两种可能，选/不选
    在 arr 里选一个子集 Subset(i, s) -> i是位置，s是最终要选的
                 
    Subset(arr[5], 9) ↗ Subset(arr[4], 7) -> chosen
                      ↘ Subset(arr[4], 9) -> not chosen
    用or连接，出口：
``` Python
if s == 0:
    return True
if i == 0:
    return arr[0] == s
if arr[i] > s: # 剪枝
    return Subset(arr, i-1, s)
```
        Subset(arr, i, s)
    选  Subset(arr, i-1, s-arr[i])
    不选Subset(arr, i-1, s)
    
对于动态规划解法， 创建一个二维数组， 有len(arr)行，有s+1列
则， i行j列表示的就是， Subset(arr[i], j)的子问题

            0 1 2 3 4 5 6 7 8 9
    3  |0   F F F T F F F F F F
    34 |1   T
    4  |2   T
    12 |3   T
    5  |4   T
    2  |5   T
    
``` Python
arr = [3, 34, 4, 12, 5, 2]
S = 9

def rec_subset(arr, i, s):
    if s == 0:
        return True
    elif i == 0:
        return arr[0] == s
    elif arr[0] > s:
        return rec_subset(arr, i-1, s)
    else:
        A = rec_subset(arr, i-1, s-arr[i])
        B = rec_subset(arr, i-1, s)
        return A or B
    
def dp_subset(arr, S):
    subset = [[False] * (S + 1) for _ in range(len(arr))]
    for i in range(len(subset)):
        for s in range(S+1):
            if s == 0:
                subset[i][s] = True
            if i == 0:
                if arr[i] == s:
                    subset[i][s] = True
    for i in range(1, len(arr)):
        for s in range(1, S+1):
            if arr[i] > s:
                subset[i][s] = subset[i-1][s]
            else:
                A = subset[i-1][s-arr[i]]
                B = subset[i-1][s]
                subset[i][s] = A or B
    return subset[len(arr) - 1][S]
dp_subset(arr, 13)
```

### 注意有一个陷阱，如果不用numpy的话

``` Python
subset = [[False] * 5] * 2
subset[1][1]=True
for x in subset:
    print(x)
# >>> [False, True, False, False, False]
# >>> [False, True, False, False, False]
```
注意到第1列（从0开始）所有的元素都变成True， 是因为 
[[0] * 5] * 2 中的 [0] * 5 是对列表的引用，改变一个值，其他的值都变了
