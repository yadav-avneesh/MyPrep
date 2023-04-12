# Links

## Articlese
- [Huffman Encoding](https://www.techiedelight.com/huffman-coding/)

## Youtube
- [William - Graph](https://www.youtube.com/watch?v=09_LlHjoEiY)
- [Algorithms](https://www.youtube.com/watch?v=8hly31xKli0)


## Questions
- [Coding Questions LC](https://leetcode.com/discuss/interview-question/2069641/The-Only-Lists-You-Need-For-Your-Interview-Preparation/1406307)
- [Kenny's List](https://www.reddit.com/r/csMajors/comments/pu9tyk/kenny_talks_code_list_of_leetcode_problems/)
- [Kenny's List 1](https://archive.ph/kMKcB)
- [Kenny's List extended](https://www.reddit.com/r/csMajors/comments/pu9tyk/comment/he5g00d/?context=3)


## Python
- [Helpful Syntax](https://towardsdatascience.com/19-helpful-python-syntax-patterns-for-coding-interviews-3704c15b758f)


## Blogs
- [Techie Delight](https://www.techiedelight.com/Category/Dynamic-Programming/)
- [CP-algorithms](https://cp-algorithms.com/)
- [The algorist](https://www.thealgorists.com/)

## Code Patterns
- https://seanprashad.com/leetcode-patterns/
- https://hackernoon.com/14-patterns-to-ace-any-coding-interview-question-c5bb3357f6ed
- https://www.educative.io/courses/grokking-coding-interview-patterns-python
- https://www.youtube.com/watch?v=cpgAULF6Vpw&list=PL7g1jYj15RUOjoeZAJsWjwV8XUo9r0hwc
- https://github.com/dennyzhang/cheatsheet-python-A4

## Repo
- [Company Code Problem](https://github.com/xizhengszhang/Leetcode_company_frequency)
- [Neetcode LC](https://github.com/neetcode-gh/leetcode/blob/main/python/)

# Notes

## Coding notes
- Practice back-tracking & DP questions ---> weak areas for me; Revise notes;
- while loop ---> always check for counter increase;
- LL or Tree problem; For corner cases, check if adding placeholder empty nodes
  can help in simplifying corner cases.
- Try iterative first if possible or when need to code;is faster. Do recursive
  if easier to code
- When you handle corner cases, do check if it is already handled by logic, see
  versions of reorganized-string solutions.
- Sometime its better to work on indices than actual values; Specially for
  arrays ---: Tree (from in and post, k closest to X in sorted array)
- [Prefix code - Wikipedia](https://en.wikipedia.org/wiki/Prefix_code)
- Backtrack DFS ---> mark as visited then dfs, then unmark on completion -- backtrack;
```python
class Solution:
    def exist(self, board: List[List[str]], word: str) -> bool:
        ROWS, COLS = len(board), len(board[0])
        path = set()

        def dfs(r, c, i):
            if i == len(word):
                return True
            if (
                min(r, c) < 0
                or r >= ROWS
                or c >= COLS
                or word[i] != board[r][c]
                or (r, c) in path
            ):
                return False
            path.add((r, c))
            res = (
                dfs(r + 1, c, i + 1)
                or dfs(r - 1, c, i + 1)
                or dfs(r, c + 1, i + 1)
                or dfs(r, c - 1, i + 1)
            )
            path.remove((r, c))
            return res

        # To prevent TLE,reverse the word if frequency of the first letter is more than the last letter's
        count = defaultdict(int, sum(map(Counter, board), Counter()))
        if count[word[0]] > count[word[-1]]:
            word = word[::-1]

        for r in range(ROWS):
            for c in range(COLS):
                if dfs(r, c, 0):
                    return True
        return False

    # O(n * m * 4^n)
```


## Random in Python
```python
from random import random
x = random.choice(iterable) # --> gives a random value from iterable.
```

## Collection in Python
```python
from collections import Counter ---> pretty helpful
s = "asasasasafddfsf"
x = Counter(s) # ---> gives_char_freq_map
y = x.most_common(n) # --> gives char_freq_map of n elements in most to least common order
```

## Priority Queue (heap)
- From heapq import heappify, heappush, heappop ---> good for heap, PQ
- Default is min, to get max for PQ --> or heap, add invert values, multiply by -1
- There is also a threadsafe version ---> https://pythonguides.com/priority-queue-in-python/
- NOTE: If using heapq as PQ then put only (primitive_int, primitive_key),
  putting obj directly as second argument leads to error in heapq; why?
  may be because in case priorities are same then it'll compare second argument,
  so either second argument should have implemented __lt__, __gt__, __eq__ or
  second argument should be a primitive
-

## For char mapping
```python
ord(char) - ord('a') # ==> number from 0 to 25 --> char array index
chr(ord('a')+n) # ==> a character that maps n (n is between 0 to 25 or a to z, small case)
```

## Str - uppler/lower other bitwise
```python
# Bitwise & with '_' or 95 ascii code will convert alphabets to upper case
chr(ord('a') & 95) ---> 'A'
chr(ord('A') & 95) ---> 'A'
# Bitwise | with ' ' or 32 ascii code will convert alphabets to lower case
chr(ord('a') | 32) ---> 'a'
chr(ord('A') | 32) ---> 'a'
# Bitwise ^ with ' ' or 32 ascii code will toggle alphabets  case
chr(ord('a') ^ 32) --> 'A'
# first index of right most occurrence of x
str.rfind(x)
# ---> first index of left most occurrence of x
str.find(x)
```
- The ASCII table is constructed in such way that the binary representation of
  lowercase letters is almost identical of binary representation of uppercase
  letters. Character ‘A’ is integer 65 = (0100 0001)2, while character ‘a’ is
  integer 97 = (0110 0001)2. The difference between the ASCII values of ‘a’ and
  ‘A’ is 32. So we can easily change the case of the letters either from Upper
  to lower or lower to upper by adding or subtracting the difference from the
  letters using bitwise operators as shown above.
- Or just use, str.lower, str.upper, str.isalpha, str.isalphanum, str.strip,
  str.rstrip, str.lstrip
- str.isalnum() ---> True if alphanumeric

## Binary search
```python
from bisect import bisect, bisect_left, bisect_right
#---> right most index for inserting 3 into arr (bisect_right)
x = bisect([1,2,3,4,5], x=3, low=0, high=arr_len-1)
# bisect_left gives left most position
```

## Deque
```python
from collections import deque # (for head-tail operations in O(1))
x = deque([])
x.append
x.appendleft
x.pop
x.popleft
x.extend
x.extendleft
```

## Comparator function
```python
def cmp_func(x, y):
    if x > y:
        return 1
    elif x < y:
        return -1
    else:
        return 0

Sorted(iterable, key=cmp_to_key(cmp_func)) # ; default = ASC

def my_cmp(x, y):
    if x[0] > y[0]:
        return 1
    elif x[0] < y[0]:
        return -1
    else:
        if x[1] > y[1]:
            return 1
        elif x[1] < y[1]:
            return -1
        else:
            return 0
l1 = [[3, 10], [1, 3], [2, 5],[5, 9],[5, 11]]
l2 = [[0,2],[1,3],[1,3],[2,4],[3,5],[3,5],[4,6]]
from functools import cmp_to_key
print(sorted(l1, key=cmp_to_key(my_cmp)))
print(sorted(l2, key=cmp_to_key(my_cmp)))
```

## Some tricks
- If input array is sorted then
    - Binary search
    - Two pointers

- If asked for all permutations/subsets then
    - Backtracking

- If given a tree then
    - DFS
    - BFS

- If given a graph then
    - DFS
    - BFS

- If given a linked list then
    - Two pointers

- If recursion is banned then
    - Stack

- If must solve in-place then
    - Swap corresponding values
    - Store one or more different values in the same pointer

- If asked for maximum/minimum subarray/subset/options then
  - Dynamic programming

- If asked for top/least K items then
  - Heap
  - QuickSelect

- If asked for common strings then
  - Map
  - Trie
- Else
  - Map/Set for O(1) time & O(n) space
  - Sort input for O(nlogn) time and O(1) space
