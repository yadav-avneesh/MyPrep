
# Principles

- Base case
- Subproblems
- Reccurance relation

## Example

Pascal's triangle are a series of numbers arranged in the shape of triangle.
In Pascal's triangle, the leftmost and the rightmost numbers of each row are
always 1. For the rest, each number is the sum of the two numbers directly
above it in the previous row.

Let's start with the recurrence relation within the Pascal's Triangle.
First of all, we define a function f(i,j) which returns the number in the
Pascal's Triangle in the i-th row and j-th column.

We then can represent the recurrence relation with the following formula:
f(i,j)=f(i−1,j−1)+f(i−1,j)

Base Case

As one can see, the leftmost and rightmost numbers of each row are the base
cases in this problem, which are always equal to 1. As a result, we can define
the base case as follows:
f(i,j)=1 where j=1 or j=i

### Optimization

In the above example, you might have noticed that the recursive solution can
incur some duplicate calculations, i.e. we compute the same intermediate numbers
repeatedly in order to obtain numbers in the last row. For instance, in order to
obtain the result for the number f(5,3), we calculate the number f(3,2) twice
both in the calls of f(4,2) and f(4,3).

Recursion is often an intuitive and powerful way to implement an algorithm.
However, it might bring some undesired penalty to the performance, e.g.
duplicate calculations, if we do not use it wisely. For instance, at the end of
the previous chapter, we have encountered the duplicate calculations problem in
Pascal's Triangle, where some intermediate results are calculated multiple times.

To demonstrate another problem with duplicate calculations, let's look at an
example that most people might be familiar with, the Fibonacci number. If we
define the function F(n) to represent the Fibonacci number at the index of n,
then you can derive the following recurrence relation:

F(n) = F(n - 1) + F(n - 2)

with the base cases: F(0) = 0, F(1) = 1

Now, if you would like to know the number of F(4), you can apply and extend the
above formulas as follows:
    F(4) = F(3) + F(2) = (F(2) + F(1)) + F(2)

As you can see, in order to obtain the result for F(4), we would need to
calculate the number F(2) twice following the above deduction: the first time in
the first extension of F(4) and the second time for the intermediate result F(3).

#### Memoization

To eliminate the duplicate calculation in the above case, as many of you would
have figured out, one of the ideas would be to store the intermediate results in
the cache so that we could reuse them later without re-calculation.

This idea is also known as memoization, which is a technique that is frequently
used together with recursion.

Memoization is an optimization technique used primarily to speed up computer
programs by storing the results of expensive function calls and returning the
cached result when the same inputs occur again. (Source: wikipedia)

Back to our Fibonacci function F(n). We could use a hash table to keep track of
the result of each F(n) with n as the key. The hash table serves as a cache that
saves us from duplicate calculations. The memoization technique is a good
example that demonstrates how one can reduce compute time in exchange for some
additional space.

For the sake of comparison, we provide the implementation of Fibonacci number
solution with memoization below.

As an exercise, you could try to make memoization more general and
non-intrusive, i.e. applying memoization without changing the original function.
(Hint: one can refer to a design pattern called decorator).

```python
def fib(self, N):
    """
    :type N: int
    :rtype: int
    """
    cache = {}
    def recur_fib(N):
        if N in cache:
            return cache[N]

        if N < 2:
            result = N
        else:
            result = recur_fib(N-1) + recur_fib(N-2)

        # put result in cache for later reference.
        cache[N] = result
        return result

    return recur_fib(N)
```

## Complexity

```
Given a recursion algorithm, its time complexity O(T) is typically the product
of the number of recursion invocations (denoted as R) and the time complexity
of calculation (denoted as O(s)) that incurs along with each recursion call:
    O(T)=R∗O(s)
```

As you might recall, in the problem of printReverse, we are asked to print the
string in the reverse order. A recurrence relation to solve the problem can be
expressed as follows:
    printReverse(str) = printReverse(str[1...n]) + print(str[0])
    where str[1...n] is the substring of the input string str, without the
    leading character str[0].

As you can see, the function would be recursively invoked n times, where n is
the size of the input string. At the end of each recursion, we simply print the
leading character, therefore the time complexity of this particular operation is
constant, i.e. O(1).

To sum up, the overall time complexity of our recursive function
printReverse(str) would be
    O(printReverse)=n∗O(1)=O(n).

## Conclusion

Now, you might be convinced that recursion is indeed a powerful technique that
allows us to solve many problems in an elegant and efficient way. But still, it
is no silver bullet. Not every problem can be solved with recursion, due to the
time or space constraints. And recursion itself might come with some undesired
side effects such as stack overflow.

In this chapter we would like to share a few more tips on how to better apply
recursion to solve problems in the real world.

> When in doubt, write down the recurrence relationship.

Sometimes, at a first glance it is not evident that a recursion algorithm can be
 applied to solve a problem. However, it is always helpful to deduct some
 relationships with the help of mathematical formulas, since the recurrence
 nature in recursion is quite close to the mathematics that we are familiar
 with. Often, they can clarify the ideas and uncover the hidden recurrence
 relationship. Within this chapter, you can find a fun example named Unique
 Binary Search Trees II, which can be solved by recursion, with the help of
 mathematical formulas.

> Whenever possible, apply memoization.

When drafting a recursion algorithm, one could start with the most naive
strategy. Sometimes, one might end up with the situation where there might be
duplicate calculation during the recursion, e.g. Fibonacci numbers. In this
case, you can try to apply the memoization technique, which stores the
intermediate results in cache for later reuse. Memoization could greatly
improve the time complexity with a bit of trade on space complexity, since
it could avoid the expensive duplicate calculation.

> When stack overflows, tail recursion might come to help.

There are often several ways to implement an algorithm with recursion. Tail
recursion is a specific form of recursion that we could implement. Different
from the memoization technique, tail recursion could optimize the space
complexity of the algorithm, by eliminating the stack overhead incurred by
recursion. More importantly, with tail recursion, one could avoid the problem
of stack overflow that comes often with recursion. Another advantage about
tail recursion is that often times it is easier to read and understand,
compared to non-tail-recursion. Because there is no post-call dependency in
tail recursion (i.e. the recursive call is the final action in the function),
unlike non-tail-recursion. Therefore, whenever possible, one should strive to
apply the tail recursion.