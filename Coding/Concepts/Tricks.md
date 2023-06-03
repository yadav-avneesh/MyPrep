# Learnings

## Coding

### General
- Increment while loop
- Check corner cases

### Graph
- Check if all nodes covered.
  - [Valid Tree Problem](https://leetcode.com/problems/graph-valid-tree/)
  - Recall that a graph, G, is a tree iff the following two conditions are met:
    - G is fully connected. In other words, for every pair of nodes in G, there
      is a path between them. => [len(edges) == n-1]
    - G contains no cycles. In other words, there is exactly one path between
      each pair of nodes in G.
   - Approaches
     - DFS --> To find connectivity and check for cycle by keeping track of
       visited.
     - Can use BFS too
     - Union Find

### String encoding
- len of str to bytes (number of bytes = 4 in this case)
```python
    def len_to_str(x):
        """
        Encodes length of string to 4-bytes string
        """
        x = len(x)
        bytes = [chr(x >> (i * 8) & 0xff) for i in range(4)]
        bytes.reverse()
        bytes_str = ''.join(bytes)
        return bytes_str
```

- Understanding bytes = [chr(x >> (i * 8) & 0xff) for i in range(4)] all you
  need to do is encode the length of x into 4 bytes
  - why 4 bytes - integer size - 4 bytes = [8bits, 8bits, 8bits, 8bits]
- ok so how do you get a X(length of str) total size into chunks of 8 bits ?
  - >> is right shift
    - which means if you have 101111 >> 2
      - this right shift moves 101111, 2 times to the right - which becomes 1011
  - if you go to python terminal and type 0xff you get 8 1's which represents
    11111111 = a BYTE
  - if you AND(&) any number with 0xff = it gives you the right most 8 bits of
    the number, example:
    - bin(291) (binary of 291) is '0b100100011'
    - oxff binary is '0b11111111'
    - now if you 291 & 0xff = you get last 8 bits of 291 == 00100011
- If you understand this - you understood the solution.

Now as explained in the 2 point. The python solution line 6 we take the whole
length of the string (len(str)) - we have to encode that into
[8bits, 8 bits, 8bits, 8bits] example:
- lets say our total length is 291 ( in binary its bin(291) = '0b100100011')
- what you have to do now ? we grab the right most 8 bits - how do you grab
  right most 8 bits ?
  - as mentioned above you do 291 & 0xff = you get last 8 bits
- Now you already have right most 8 bits - in next iteration you are interested
  in NEXT 8 bits - how do you get that ?
  - we move 291 >> 8 ( which removes the last 8 bits we already computed)
    - which means if you do (291 >> 8 ) & 0xff = it gives you the next right
      most 8 bits
- As already mentioned we need 4 chunks of 8 bits [8bits, 8bits, 8bits, 8bits]
  - [ for i in range(4)] thats why the range is 4 ( 0, 1, 2, 3)
- Once you compute all the 8bits we need to convert to char hence its
  [chr((x >> (i * 8)) & 0xff) for i in range(4)]



# Tricks
## Math Ones

### GCD

#### Euclidean Algorithm

##### What?
- If we subtract a smaller number from a larger one (we reduce a larger number),
GCD doesn’t change. So if we keep subtracting repeatedly the larger of two, we
end up with GCD.
- Instead of subtraction, if we divide the smaller number, the algorithm stops
    when we find the remainder 0.

##### Code
```python
# TC = O(log(min(a, b)))
def gcd(x, y):
    while(y):
        x, y = y, x % y
    return abs(x)
```

#### Extended Euclidean Algorightm:

##### What?
Extended Euclidean algorithm also finds integer coefficients x and y such
that: ax + by = gcd(a, b)

Examples:
- Input: a = 30, b = 20
  - Output: gcd = 10, x = 1, y = -1
  - (Note that 30*1 + 20*(-1) = 10)
- Input: a = 35, b = 15
  - Output: gcd = 5, x = 1, y = -2
  - (Note that 35*1 + 15*(-2) = 5)

The extended Euclidean algorithm updates the results of gcd(a, b) using the
results calculated by the recursive call gcd(b%a, a). Let values of x and y
calculated by the recursive call be x1 and y1. x and y are updated using the
below expressions.

##### How does the algo work?

As seen above, x and y are results for inputs a and b,
a.x + b.y = gcd                      —-(1)
And x1 and y1 are results for inputs b%a and a
(b%a).x1 + a.y1 = gcd
When we put b%a = (b – (⌊b/a⌋).a) in above, we get following.
Note that ⌊b/a⌋ is floor(b/a)

(b – (⌊b/a⌋).a).x1 + a.y1  = gcd

Above equation can also be written as below

b.x1 + a.(y1 – (⌊b/a⌋).x1) = gcd      —(2)

After comparing coefficients of ‘a’ and ‘b’ in (1) and (2), we get following,

x = y1 – ⌊b/a⌋ * x1
y = x1

```text
ax + by = gcd(a, b)
gcd(a, b) = gcd(b%a, a)
gcd(b%a, a) = (b%a)x1 + ay1
ax + by = (b%a)x1 + ay1
ax + by = (b – [b/a] * a)x1 + ay1
ax + by = a(y1 – [b/a] * x1) + bx1

# Comparing LHS and RHS,
x = y1 – ⌊b/a⌋ * x1
y = x1
```

##### Code
```python
# TC = O(logN)

def gcdExtended(a, b):

    # Base Case
    if a == 0:
        return b, 0, 1

    gcd, x1, y1 = gcdExtended(b % a, a)

    # Update x and y using results of recursive
    # call
    x = y1 - (b//a) * x1
    y = x1

    return gcd, x, y


# Driver code
a, b = 35, 15
g, x, y = gcdExtended(a, b)
print("gcd(", a, ",", b, ") = ", g)
```

##### Application
The extended Euclidean algorithm is particularly useful when a and b are coprime
(or gcd is 1). Since x is the modular multiplicative inverse of “a modulo b”,
and y is the modular multiplicative inverse of “b modulo a”. In particular, the
computation of the modular multiplicative inverse is an essential step in RSA
public-key encryption method.


### nCr Computations

#### Binomial Coefficient

A binomial coefficient C(n, k) can be defined as the coefficient of x^k in the
expansion of (1 + x)^n.

A binomial coefficient C(n, k) also gives the number of ways, disregarding
order, that k objects can be chosen from among n objects more formally, the
number of k-element subsets (or k-combinations) of a n-element set.

##### The Problem
Write a function that takes two parameters n and k and returns the value of
Binomial Coefficient C(n, k). For example, your function should return 6 for
n = 4 and k = 2, and it should return 10 for n = 5 and k = 2.

##### Solutions

- Optimal Substructure
The value of C(n, k) can be recursively calculated using the following standard
formula for Binomial Coefficients.

   C(n, k) = C(n-1, k-1) + C(n-1, k)
   C(n, 0) = C(n, n) = 1
- [See here for detailed](https://www.geeksforgeeks.org/binomial-coefficient-dp-9/)


### Euler Totient Function

Euler’s Totient function Φ (n) for an input n is the count of numbers in
{1, 2, 3, …, n-1} that are relatively prime to n, i.e., the numbers whose GCD
(Greatest Common Divisor) with n is 1.

Examples :

Φ(1) = 1
gcd(1, 1) is 1

Φ(2) = 1
gcd(1, 2) is 1, but gcd(2, 2) is 2.

Φ(3) = 2
gcd(1, 3) is 1 and gcd(2, 3) is 1

Φ(4) = 2
gcd(1, 4) is 1 and gcd(3, 4) is 1

Φ(5) = 4
gcd(1, 5) is 1, gcd(2, 5) is 1,
gcd(3, 5) is 1 and gcd(4, 5) is 1

Φ(6) = 2
gcd(1, 6) is 1 and gcd(5, 6) is 1,

- [See here for Details](https://www.geeksforgeeks.org/eulers-totient-function/)