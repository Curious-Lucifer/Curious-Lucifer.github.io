---
author: Curious
title: "Note : RSA"
date: 2023-08-04
math: true
categories:
    - Note
tags:
    - Crypto
---

## Bézout's Identity
if $a, b, m \in \mathbb Z$, and $a \neq 0 \wedge b \neq 0$, then

$$
\exist(x, y) \in \mathbb Z^2 \text{ that } ax + by = m \Leftrightarrow \operatorname{gcd}(a, b)|m
$$

### Proof
let 

$$
A = \lbrace(ax + by)|(x, y) \in \mathbb Z^2\rbrace
$$

because $A \cup \mathbb N \neq \emptyset$, so $\exist d_0 = ax_0 + by_0$ is $A$’s smallest positive element.

for any $p = ax_1 + by_1 \in A$, let $p$ perform integer division on $d_0$

$$
p = qd_0 + r \text{ and } 0 \le r \lt d_0
$$

$$
r = p - qd_0 = a(x_1 - qx_0) + b(y_1 - qy_0) \in A
$$

because $0 \le r \lt d_0$ and $d_0$ is the smallest positive element in $A$, so $r = 0$ $\Rightarrow$ $p = qd_0$ $\Rightarrow$ $d_0 | p$, this represent any element $p \in A$ is $d_0$’s multiple

we know that $a \in A \text{ and } b \in A$, so $d_0$ is $(a,b)$’s common factor

for any $(a,b)$’s common factor $d$, let $a = kd$ and $b = ld$

$$
d_0 = ax_0 + by_0 = d(kx_0 + ly_0)
$$

so $d|d_0$, $d_0 = \operatorname{gcd}(a,b)$

> the solve set of $ax + by = \operatorname{gcd}(a,b)$ is $\lbrace(x_0 + k \cdot \frac{b}{d_0},y_0 - k \cdot \frac{a}{d_0})|k \in \mathbb Z\rbrace$

---
## Extended Euclidean Algorithm
the algorithm that calculate a solve $(x, y)$ of $ax + by = \operatorname{gcd}(a, b)$

Ex. $a = 240, b = 46$ :

```
5 | 240 |  46 | 4         5 |        a |        b | 4
  | 230 |  40 |             |       5b | 4a - 20b |
  |-----|-----|             |----------|----------|
1 |  10 |   6 | 1         1 |   a - 5b | 21b - 4a | 1
  |   6 |   4 |             | 21b - 4a | 5a - 26b |
  |-----|-----|             |----------|----------|
2 |   4 |   2 |           2 | 5a - 26b | 47b - 9a |
  |   4 |     |             |          |          |
  |-----|     |
  |   0 |     |
```

### Code

```python
def egcd(a: int, b: int):
    """
    - input : a(int), b(int) , (a != 0 and b != 0)
    - output : (x, y) (int, int) that satisfy ax + by = gcd(a, b)
    """

    assert (a != 0) and (b != 0)

    a, coe_a =  (a, (1, 0)) if (a > 0) else (-a, (-1, 0))
    b, coe_b =  (b, (0, 1)) if (b > 0) else (-b, (0, -1))
    q, r = a // b, a % b
    while r:
        a, b, coe_a, coe_b = b, r, coe_b, (coe_a[0] - q * coe_b[0], coe_a[1] - q * coe_b[1])
        q, r = a // b, a % b
    
    return coe_b
```

**Modular Multiplicative Inverse** : if $\operatorname{gcd}(a, n) = 1$, then exist unique modular multiplicative inverse $b$ let $ab \equiv 1 \pmod n$. $b$ can written as $a ^ {-1} \pmod n$

> use Extended Euclidean Algorithm calculates $ax + ny = 1$’s solve, 
($x \pmod n$) will be $a$’s modular multiplicative inverse under $n$

---
## Euler’s Theorem
if $a \gt 1$, $n \gt 1$, $a,n \in \mathbb N$, and $\operatorname{gcd}(a,n) = 1$, then $a^{\varphi(n)} \equiv 1 \pmod n$, where $\varphi(n)$ is the number of integer that less than $n$ and coprime with $n$

### Proof

> the operation in this proof will be performed with modulo $n$

let $\lbrace\alpha_1,\alpha_2,...,\alpha_{\varphi(n)}\rbrace$ be a set of $\varphi(n)$ different digits that are coprime with $n$, we know $\operatorname{gcd}(a,n) = 1$, so for $i,j \in \lbrace1,2,...,\varphi(n)\rbrace$ and $i \neq j$, $\operatorname{gcd}(a\alpha_i,n) = 1$, and $a\alpha_i \not\equiv a\alpha_j$

$\Rightarrow \lbrace a\alpha_1,a\alpha_2,...,a\alpha_{\varphi(n)}\rbrace$ is another set of of $\varphi(n)$ different digits that are coprime with $n$

$$
\alpha_1 \cdot \alpha_2 \cdot ...  \cdot \alpha_{\varphi(n)} \equiv a\alpha_1 \cdot a\alpha_2 \cdot ... \cdot a\alpha_{\varphi(n)} \pmod n
$$

it's easy to see $a^{\varphi(n)} \equiv 1 \pmod n$

> **Primitive Root :** if $\operatorname{gcd}(a, n) = 1$, for the smallest $\delta$ satisfy $a ^ {\delta} \equiv 1 \pmod n$ that $\delta = \varphi(n)$, then we call $a$ is a primitive root modulo $n$

---
## Chinese Remainder Theorem

$$
\text{if } \space
\begin{cases}
x \equiv a_1 \pmod {m_1}\\\\
x \equiv a_2 \pmod {m_2}\\\\
x \equiv a_3 \pmod {m_3}\\\\
...\\\\
x \equiv a_n \pmod {m_n}
\end{cases}
\quad\text{ let }\quad
\begin{cases}
M = m1 \cdot m2 \cdot ... \cdot m_n\\\\
M_i = \frac{M}{m_i}\\\\
t_i \equiv M_i^{-1} \pmod {m_i}
\end{cases}
$$

$$
\Rightarrow \quad x \equiv a_1t_1M_1 + a_2t_2M_2 + ... + a_nt_nM_n \pmod M
$$

### Code

```python
def crt(ai_list: list[int], mi_list: list[int]):
    """
    - input : `ai_list (list[int])`, `mi_list (list[int])` , and assume `ai_list = [a1, a2, ...]`, `mi_list = [m1 ,m2, ...]`
        - `x ≡ a1 (mod m1)`
        - `x ≡ a2 (mod m2)`
        - ...
    - output : `x % M (int)` , `M = m1 * m2 * ...`
    """
    assert len(ai_list) == len(mi_list)

    M = reduce(lambda x, y: x * y, mi_list)
    Mi_list = [M // mi for mi in mi_list]
    ti_list = [pow(Mi, -1, mi) for Mi, mi in zip(Mi_list, mi_list)]
    return sum(ai * ti * Mi for ai, ti, Mi in zip(ai_list, ti_list, Mi_list)) % M
```

---
## RSA
generate two prime $p, q$ and an integer $e$ that $\operatorname{gcd}(e, \varphi(n)) = 1$, then calculate $n = pq$ and $\varphi(n) = (p - 1)(q - 1)$, $d \equiv e^{-1} \pmod {\varphi(n)}$

$$
\text{public key : } (e,n) \qquad \text{private key : } d
$$

**Encryption :** $c \equiv m^e \pmod n$

**Decryption :** $m \equiv c^d \pmod n$

### Proof

$d \equiv e^{-1} \pmod {\varphi(n)}$, so $ed = 1 + k\varphi(n)$

if $\operatorname{gcd}(m, p) = 1$

$$
m^{ed} \equiv m^{1 + k(p-1)(q-1)} \equiv m \cdot (m^{p-1})^{k(q-1)} \equiv m \pmod p
$$

if $\operatorname{gcd}(m, p) = p$

$$
m^{ed} \equiv 0 \equiv m \pmod p
$$

same as $q$, so 

$$
\begin{cases}
m^{ed} \equiv m \pmod p \\\\
m^{ed} \equiv m \pmod q
\end{cases}
$$

we know exist $(x, y)$ that satisfy $px + qy = 1$, and $x \equiv p^{-1} \pmod q$ and $y \equiv q^{-1} \pmod p$

use CRT

$$
\begin{aligned}
m^{ed} & \equiv m \cdot q \cdot (q^{-1} \pmod p) + m \cdot p \cdot (p^{-1} \pmod q) &\pmod n\\\\
& \equiv m \cdot (q \cdot (y + kp) + p \cdot (x + lq)) & \pmod n \\\\
&\equiv m &\pmod n
\end{aligned}
$$

---
## Factor n with d
for

$$
\begin{cases}
x^2 \equiv 1 \pmod p\\\\
x^2 \equiv 1 \pmod q
\end{cases}
\space\Rightarrow\space
x^2 \equiv 1 \pmod n
$$

there will be at least 4 solves

$$
\begin{cases}
x \equiv 1 \pmod p\\\\
x \equiv 1 \pmod q
\end{cases}
\Rightarrow x \equiv 1 \pmod n
\quad,\quad
\begin{cases}
x \equiv -1 \pmod p\\\\
x \equiv -1 \pmod q
\end{cases}
\Rightarrow x \equiv -1 \pmod n
$$

$$
\begin{cases}
x \equiv 1 &\pmod p\\\\
x \equiv -1 &\pmod q
\end{cases}
\quad,\quad
\begin{cases}
x \equiv -1 &\pmod p\\\\
x \equiv 1 &\pmod q
\end{cases}
$$

if we can find a solve of $x^2 \equiv 1 \pmod n$ that $x \not\equiv \pm 1 \pmod n$, then

$$
(x + 1)(x - 1) \equiv 0 \pmod n
\space\Rightarrow\space
\begin{cases}
1 \lt \operatorname{gcd}(n, x + 1) \lt n\\\\
1 \lt \operatorname{gcd}(n, x - 1) \lt n
\end{cases}
$$

for any $g$, we know that $g^{ed - 1} \equiv 1 \pmod n$ and $ed - 1 = k\varphi(n) = 2^tr$, so $g^{2^{t - 1}r} \pmod n$ is a root of $x^2 \equiv 1 \pmod n$. if $g^{2^{t - 1}r} \not\equiv \pm 1 \pmod n$, then we can calculate $\operatorname{gcd}(g^{2^{t - 1}r} - 1, n)$ to factor $n$

else if $g^{2^{t - 1}r} \equiv 1 \pmod n$, then calculate if $g^{{2^{t-2}}r} \not\equiv \pm 1 \pmod n$, so on and so forth.

if none of them $\not\equiv \pm 1 \pmod n$, then choose another $g$.

### Code

```python
def factor_n_with_d(n: int, e: int, d: int):
    """
    - input : `n (int)`, `e (int)`, `d (int)`
    - output : `(p, q) (int, int)` , `p * q = n` and p, q is prime
    """

    for g in range(2, n):
        init_pow = e * d - 1
        while ((init_pow % 2) == 0):
            init_pow //= 2
            root = pow(g, init_pow, n)
            if 1 < gcd(root - 1, n) < n:
                p = gcd(root - 1, n)
                q = n // p
                return p, q
            if root == (n - 1):
                break
```

---
## Fermat Factorization
assume $p \gt q$ (well $p,q$ is changable), if $p-q$ is really small, let

$$
\begin{cases}
p = a + b\\\\
q = a - b
\end{cases}
\space\Rightarrow\space
n = pq = a^2 - b^2
\space\Rightarrow\space
n + b^2 = a^2
$$

because $p-q$ is really small, so $b$ is really small too $\Rightarrow$ try $a$ let $\sqrt{a^2 - n} \in \mathbb N$

### Code

```python
def fermat_factor(n: int):
    """
    - input : `n (int)`
    - output : `(p, q) (int, int)`
    """

    a = int(isqrt(n)) + 1
    b = iroot(a ** 2 - n, 2)
    while not b[1]:
        a += 1
        b = iroot(a ** 2 - n, 2)
        print(a)
    b = int(b[0])
    return (a - b), (a + b)
```

---
## Pollard's p-1 Algorithm
if $(p-1)$’s max prime factor $B$ is really small, $(p-1)|1 \times 2 \times ...\times B$ , so

$$
2^{1 \times 2 \times ... \times B} = 2^{k(p-1)} \equiv 1 \pmod p
$$

$\Rightarrow$ $\operatorname{gcd}(2^{1 \times 2 \times 3 \times ... \times B} - 1,n) \gt 1$

### Code

```python
def pollard_algorithm(n: int):
    """
    - input : `n (int)` , n has a factor p that (p - 1)'s large prime factor is really small
    - output : `(p, q) (int, int)` , n's factors
    """

    a = 2
    b = 2
    while True:
        a = int(pow(a, b, n))
        p = int(gcd(a - 1, n))
        if 1 < p < n:
            return p, n // p
        b += 1
```

---
## Legendre Symbol

$$
\left\(\frac{a}{p}\right\) = 
\begin{cases}
1\qquad &\text{if } \exist x \text{ that } x^2 \equiv a \pmod p \text{ and } a \not\equiv 0 \pmod p \\\\
-1\qquad &\text{if } \forall x \text{ that } x^2 \not\equiv a \pmod p \text{ and } a \not\equiv 0 \pmod p \\\\
0\qquad &\text{if } a \equiv 0 \pmod p
\end{cases}
$$

we can write the Legendre Symbol like (if $p$ is prime)

$$
\left\(\frac{a}{p}\right\) = a^{\frac{p-1}{2}} \pmod p
$$

### Proof
we know that $a^{p-1} \equiv 1 \pmod p$,

$$
(a^{\frac{p-1}{2}} + 1) \cdot (a^{\frac{p-1}{2}} - 1) \equiv 0 \pmod p
$$

so $a^{\frac{p-1}{2}}$ will be $1$ or $-1$

if $a$ is $p$’s quadratic residue, then $\exist x$ that $x^2 \equiv a \pmod p$, then

$$
a^{\frac{p-1}{2}} \equiv x^{p-1} \equiv 1 \pmod p
$$

if $a^{\frac{p-1}{2}} \equiv 1 \pmod p$, because $p$ is a prime number, so $p$’s primitive root exist. assume $d$ is $p$’s primitive root, then $\exist j$ that $1 \le j \le p-1$ let $a \equiv d^j \pmod p$, so

$$
a^{\frac{p-1}{2}} \equiv d^{j \cdot \frac{p-1}{2}} \equiv 1 \pmod p
$$

because $d$ is $p$’s primitive root, so $(p - 1) | j \cdot \frac{p-1}{2}$ $\Rightarrow$ $j$ is even $\Rightarrow$ $a$ is $p$’s quadratic residue

### Code

```python
def legendre_symbol(a: int, p: int):
    """
    - input : `a (int)`, `p (int)`
    - output : `ls (int)` , value of (a/p) (legendre symbol)
    """

    ls = pow(a, (p - 1) // 2, p)
    return -1 if ls == (p - 1) else ls
```

> **Jacobi Symbol :** for any integer $a$ and any positive odd integer $n = {p_1}^{\alpha_1}{p_2}^{\alpha_2}...{p_k}^{\alpha_k}$
> $$
> \left\(\frac{a}{n}\right\) = \left\(\frac{a}{p_1}\right\)^{\alpha_1} \left\(\frac{a}{p_2}\right\)^{\alpha_2} ... \left\(\frac{a}{p_k}\right\)^{\alpha_k} = 
> \begin{cases}
> 1\qquad &\text{if } \exist x \text{ that } x^2 \equiv a \pmod n \text{ and } a \not\equiv 0 \pmod n \\\\
> -1\qquad &\text{if } \forall x \text{ that } x^2 \not\equiv a \pmod n \text{ and } a \not\equiv 0 \pmod n \\\\
> 0\qquad &\text{if } \operatorname{gcd}(a, n) \not = 1
> \end{cases}
> $$

---
## William's p+1 Algorithm

> **Lucas Sequence** : Given two integer parameters $P$ and $Q$, then Lucas Sequences $U_n(P, Q)$ and $V_n(P, Q)$ is defined by
> 
> 
> $$
> U_0(P, Q) = 0,\space U_1(P, Q) = 1,\space U_n(P, Q) = P \cdot U_{n-1} - Q \cdot U_{n-2}
> $$
> 
> $$
> V_0(P, Q) = 2,\space V_1(P, Q) = P,\space U_n(P, Q) = P \cdot U_{n-1} - Q \cdot U_{n-2}
> $$

> **Lucas Sequence's Properties** :
> 1. $V_{m+n} = V_mV_n - Q^nV_{m-n}$
> 2. $V_{2n} = {V_n}^2 - 2Q^n$
> 3. $V_{mn}(P, Q) = V_m(V_n(P, Q), Q^n)$
> 4. for $P = A > 2$ and $Q = 1$, and every operations are performed modulo $N$
> 
> $$
> V_0 = 2,\space V_1 = A,\space V_j = AV_{j-1} - V_{j-2}
> $$
> 
> then any odd prime $p$ divides $\operatorname{gcd}(N,V_M−2)$ whenever $M$ is a multiple of $p - \left\(\frac{D}{p}\right\)$, where $D = A^2 - 4$ and $\left\(\frac{D}{p}\right\)$ is Legendre Symbol

if $(p + 1)$’s max prime factor $B$ is really small, then we can randomly choose $A$. if $(\frac{A^2 - 4}{p}) = -1$, let $M = 1 \times 2 \times … \times B$, then $\operatorname{gcd}(n, V_M-2) > 1$

### Code

```python
def william_algorithm(n: int, B: int=None):
    """
    - input : `n (int)`, `B (int, default None)` , B is the upper bound of (p + 1)'s max prime factor
    - output : `(p, q) (int, int)` , `p * q = n`
    """

    def calc_lucas(a: int, k: int):
        """
        - input : `a (int)`, `k (int)`
        - output : `v1 (int)` , return V_k(a, 1)
        """

        # Init : v1, v2 = V[1], V[2]
        # General : v1, v2 = V[i], V[i + 1]
        v1, v2 = a % n, (a ** 2 - 2) % n
        for bit in bin(k)[3:]:
            if bit == '1':
                # v1, v2 = V[2i + 1], V[2i + 2]
                v1, v2 = (v1 * v2 - a) % n, (v2 ** 2 - 2) % n
            else:
                # v1, v2 = V[2i], V[2i + 1]
                v1, v2 = (v1 ** 2 - 2) % n, (v1 * v2 - a) % n
        return v1

    prime_list = [3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97, 101]
    
    B = B or int(isqrt(n))
    for A in prime_list:
        v = A
        for i in trange(1, B + 1, desc=f'A = {A}'):
            v = calc_lucas(v, i)
            p = gcd(v - 2, n)
            if n > p > 1:
                return p, n // p
```

---
## Wiener Attack

> **Continued Fraction** : for example, $\frac{13}{17}$’s continued fraction is $[c_0,c_1,c_2,c_3]$, and  
> 
> $c_0 = \lfloor \frac{13}{17} \rfloor = 0$  
> $c_1 = 0 + \frac{1}{\lfloor \frac{17}{13} \rfloor} = 0 + \frac{1}{1} = 1$  
> $c_2 = 0 + \frac{1}{1 + \frac{1}{\lfloor \frac{13}{4} \rfloor}} = 0 + \frac{1}{1 + \frac{1}{3}} = \frac{3}{4}$  
> $c_3 = 0 + \frac{1}{1 + \frac{1}{3 + \frac{1}{\lfloor 4 \rfloor}}} = 0 + \frac{1}{1 + \frac{1}{3 + \frac{1}{4}}} = \frac{13}{17}$  

> **Legendre’s Theorem in Diophantine Approximations** : for $\alpha \in \mathbb R$, 
$\frac{a}{b} \in \mathbb Q$, and satisfy $| \alpha - \frac{a}{b}| \lt \frac{1}{2b^2}$, then $\frac{a}{b}$ will be in $\alpha$’s continued fraction

if $d \lt \frac{1}{3}n^{\frac{1}{4}}$, $|p - q| \lt \operatorname{min}(p,q)$, and $ed = k\varphi(n) + 1$, we can proof that $\frac{k}{d}$ will be in $\frac{e}{n}$’s continued fraction. because $\operatorname{gcd}(k,d) = 1$, so we can try all $\frac{e}{n}$’s continued fraction and because

$$
\frac{ed - 1}{k} = \varphi(n) = (p-1)(q-1) = n - p - \frac{n}{p} + 1
$$

so

$$
p^2 + p(\frac{ed-1}{k} -  n - 1) + n = 0
$$

we can test if $p \in \mathbb N$ and if $p$ is $n$’s factor to check if $k$ and $d$ is correct

### Proof

we know that $|p-q| \lt \operatorname{min}(p,q)$, we can set $p \gt q$, then $2q \gt p \gt q$, set $p = q + a$ and $a \lt q$, so

$$
n - \varphi(n) = n - (p-1)(q-1) = p + q - 1 = 2q + a - 1 < 3q
$$

and

$$
3\sqrt{n} = 3\sqrt{q^2 + qa} \gt 3\sqrt{q^2} = 3q
$$

so $3\sqrt{n} \gt (n - \varphi(n))$

$$
\left\|\frac{e}{n} - \frac{k}{d}\right\| = \left\|\frac{ed - nk}{nd}\right\| = \left\|\frac{1 + k\varphi(n) - kn}{nd}\right\| = \frac{k(n - \varphi(n)) - 1}{nd}
$$

then

$$
\left\|\frac{e}{n} - \frac{k}{d}\right\| = \frac{k(n - \varphi(n)) - 1}{nd} \lt  \frac{3k\sqrt{n} - 1}{nd} \lt \frac{3k\sqrt{n}}{nd}
$$

we also know $d \lt \frac{1}{3}n^{\frac{1}{4}}$ 

$$
k\varphi(n) = ed - 1 \lt ed \lt \varphi(n)d
$$

$$
k \lt d \lt \frac{1}{3}n^{\frac{1}{4}}
$$

so

$$
\left\|\frac{e}{n} - \frac{k}{d}\right\| \lt \frac{3k\sqrt{n}}{nd} \lt \frac{1}{n^{\frac{1}{4}}d}
$$

and $d \lt \frac{1}{3}n^{\frac{1}{4}} \quad\Rightarrow\quad 2d \lt 3d \lt n^{\frac{1}{4}} \quad\Rightarrow\quad \frac{1}{2d} \gt \frac{1}{n^{\frac{1}{4}}}$, so

$$
\left\|\frac{e}{n} - \frac{k}{d}\right| \lt \frac{1}{n^{\frac{1}{4}}d} \lt \frac{1}{2d^2}
$$

because $ed - k\varphi(n) = 1$, so $\operatorname{gcd}(k,d) = 1$, and because Legendre’s Theorem in Diophantine Approximations

$$
\left\|\frac{e}{n} - \frac{k}{d}\right\| \lt \frac{1}{2d^2}
$$

so $\frac{k}{d}$ will be in $\frac{e}{n}$’s continued fraction

### Code

```python
def wiener_attack(n: int, e: int):
    """
    - input : `n (int)`, `e (int)`
    - output : `(p, q, d) (int, int, int)`
    """

    continued_fraction_list = (Integer(e) / Integer(n)).continued_fraction()
    for i in range(2, len(continued_fraction_list)):
        cf = continued_fraction_list.convergent(i)
        k = cf.numerator()
        d = cf.denominator()
        if ((e * d - 1) % k) != 0:
            continue
        b = (e * d - 1) // k - n - 1
        if (b ** 2 - 4 * n) <= 0:
            continue
        D = iroot(int(b ** 2 - 4 * n), 2)
        if not D[1]:
            continue
        p, q = ((-int(b) + int(D[0])) // 2), ((-int(b) - int(D[0])) // 2)
        if p * q == n:
            return p, q, int(d)
```

---
## LSB Oracle Attack
if there’s an oracle that give it $c$, it will give us the less bit of corresponding $m$. we can write the oracle like

$$
c \longrightarrow m \longrightarrow \lfloor m \rfloor_2
$$

let $m = x_0 + 2 \cdot y_1$, $x_0 \in \lbrace 0, 1\rbrace$, then we can get $x_0$ by

$$
c \longrightarrow m \longrightarrow \lfloor m \rfloor_2 = x_0
$$

let $y_1 = x_1 + 2 \cdot y_2$, $x \in \lbrace 0, 1\rbrace$, then we can get $x_1$ by

$$
(2^{-1})^e \cdot c \longrightarrow 2^{-1} \cdot m \pmod n \longrightarrow \lfloor 2^{-1} \cdot x_0 \rfloor_n + x_1 \pmod 2
$$

and then so on and so forth, we can find every bits of $m$ for ($n$’s bits length) time.

### Code

```python
def LSB_oracle_attack(n: int, e: int, c: int, oracle, m_bitlength: int = None):
    """
    - input : `n (int)`, `e (int)`, `c (int)`, `oracle (func)`, `m_bitlength (int, default = None)`
    - output : `m (int)`
    - oracle func : 
        - input : `c (int)`
        - output : `lbit (int)` , `{0, 1}` last bit of `m` (`c`'s plain)
    """

    m_bitlength = m_bitlength or n.bit_length()
    multiple_const = pow(2, -e, n)
    m_bitlist = []
    for i in trange(m_bitlength):
        new_c = (pow(multiple_const, i, n) * c) % n
        bit = (oracle(new_c) - (sum((pow(2, - i + j, n) * m_bitlist[j] % n) for j in range(i)) % n)) % 2
        m_bitlist.append(bit)

    return int(''.join(str(bit) for bit in reversed(m_bitlist)), base=2)
```

---
## Bleichenbacher 1998
if there’s an oracle that give it $c$, it will tells you if the $m$’s highest two bytes is `0x0002` (PKCS#1). 

let $2^{8(k-1)} \le n \lt 2^{8k}$, $B = 2^{8(k-2)}$, if the oracle tells $m$ is PKCS conforming, that means 

$$
2 \cdot B \le m \lt3 \cdot B
$$

### Algorithm

> let $M_i$ be a set of intervals that $m_0 \in M_i$

step 1

given an cipher $c$, randomly choose $s_0$ to let $(c{s_0}^e \pmod n)$’s plain is PKCS conforming, then set $c_0 = c(s_0)^e \pmod n$, $M_0 = [2B, 3B-1]$, $i = 1$

step 2

if $i = 1$, because for any $s_1 \le \frac{n}{3B}$, $s_1m_0$ will not be PKCS conforming, so search for the smallest integer $s_1 \gt \frac{n}{3B}$, let $s_1m_0$ is PKCS conforming.

if $i \gt 1$ and the number of intervals in $M_{i-1}$ is at least 2, then search for the smallest integer $s_i \gt s_{i-1}$ let $s_im_0$ is PKCS conforming.

if $i \gt 1$ and $M_{i-1} = [a, b]$, then choose small integer $r_i, s_i$ such that

$$
r_i \ge 2 \frac{bs_{i-1} - 2B}{n}
$$

and 

$$
\frac{2B + r_in}{b} \le s_i \lt \frac{3B + r_in}{a}
$$

because $a \le m_0 \le b$, so

$$
\frac{2B + r_in}{b} \le \frac{2B + r_in}{m_0} \le s_i \le \frac{3B - 1 + r_in}{m_0} \le \frac{3B-1+r_in}{a}
$$

use these $s_i$ until $s_im_0$ is PKCS conforming

step 3

after $s_i$ has been found, set

$$
M_i = \bigcup_{[a,b] \in M_{i-1},\space r_i} \{[\space\operatorname{max}(a,\space\lceil \frac{2B + r_in}{s_i} \rceil),\space\operatorname{min}(b, \space\lfloor \frac{3B - 1 + r_in}{s_i} \rfloor)\space]\}
$$

and for $[a, b] \in M_{i-1}$

$$
\frac{as_i - 3B + 1}{n} \le r_i \le \frac{bs_i - 2B}{n}
$$

because

$$
\begin{aligned}
& 2B \le s_im_0 \pmod n \le 3B - 1\\\\
\Rightarrow \space & 2B \le s_im_0-r_in \le 3B - 1 \\\\
\Rightarrow \space & 2B + r_in \le s_im_0 \le 3B - 1 + r_in \\\\
\Rightarrow \space & \lceil \frac{2B + r_in}{s_i} \rceil \le m_0 \le \lfloor \frac{3B - 1 + r_in}{s_i} \rfloor
\end{aligned}
$$

and for every $[a, b] \in M_{i-1}$, we know that $2B \le s_im_0 - r_in \le 3B - 1$, so 

$$
as_i - (3B - 1) \le r_in \le bs_i - 2B
$$

$$
M_i = \bigcup_{[a,b] \in M_{i-1},\space r_i} \{[\space\operatorname{max}(a,\space\lceil \frac{2B + r_in}{s_i} \rceil),\space\operatorname{min}(b, \space\lfloor \frac{3B - 1 + kn}{s_i} \rfloor)\space]\}
$$

step 4

if $M_i$ contains only one interval and $M_i = [a,a]$, then $m_0 = a$, $m \equiv {s_0}^{-1}a \pmod n$, else go back to step 2

### Code

```python
def bleichenbacher_1998(n: int, e: int, c: int, oracle):
    """
    - input : `n (int)`, `e (int)`, `c (int)`, `oracle (func)` , `c` is PKCS#1 conforming
    - output : `m (int)` , `e`'s plain
    - oracle func : 
        - input : `c (int)`
        - output : `PKCS_conforming (bool)` , is `c` PKCS#1 conforming
    """

    assert oracle(c)
    B = 1 << (n.bit_length() // 8 - 1) * 8

    def bleichenbacher_orifind_s(lower_bound: int):
        si = lower_bound
        while True:
            new_c = (pow(si, e, n) * c) % n
            if oracle(new_c):
                return si
            si += 1

    def bleichenbacher_optfind_s(prev_si: int, a: int, b: int):
        ri = ceil_int(2 * (b * prev_si - 2 * B), n)
        while True:
            low_bound = ceil_int(2 * B + ri * n, b)
            high_bound = ceil_int(3 * B + ri * n, a)
            for si in range(low_bound, high_bound):
                new_c = (pow(si, e, n) * c) % n
                if oracle(new_c):
                    return si
            ri += 1

    def bleichenbacher_merge_M(si: int, M: list):
        new_M = []
        for [a, b] in M:
            r_low = ceil_int(a * si - 3 * B + 1, n)
            r_high = floor_int(b * si - 2 * B, n) + 1
            for ri in range(r_low, r_high):
                interval_low = max(a, ceil_int(2 * B + ri * n, si))
                interval_high = min(b, floor_int(3 * B + ri * n - 1, si))
                if interval_high >= interval_low:
                    new_M.append([interval_low, interval_high])
        return new_M

    s = bleichenbacher_orifind_s(ceil_int(n, 3 * B))
    M = bleichenbacher_merge_M(s, [[2 * B, 3 * B - 1]])
    print(s, M)

    while True:
        if len(M) > 1:
            s = bleichenbacher_orifind_s(s + 1)
        else:
            if M[0][0] == M[0][1]:
                return M[0][0]
            s = bleichenbacher_optfind_s(s, M[0][0], M[0][1])
        M = bleichenbacher_merge_M(s, M)
        print(s, M)
```

---
## Coppersmith Method
if given a monic polynomial $f(x) = x^\delta + ...$, a integer of unknown factorization $N$, a upper bound $X$, a real number $\beta$, our goal is to find out all $|x_0| \le X$ that satisfy

$$
f(x_0) \equiv 0 \pmod b \quad\text{ , where }\space b \ge N^\beta \space\text{ and }\space b|N
$$

let 

$$
X_0 = \lbrace x_0 \space|\space f(x_0) \equiv 0 \pmod b ,\space |x_0| \le X \rbrace
$$

if we can find a polynomial $g$ that for all $x_0 \in X_0$, $g(x_0) = 0$, then we can just solve $g(x) = 0$ to know the root of $f$

now pick two integers $m$ and $t$, let

$$
f_i(x) = \begin{cases}
x^k \cdot N^{m-j} \cdot [f(x)]^j & \text{for } 0 \le j \lt m,\space 0 \le k \lt \delta & \text{ , and } i = j\delta + k \\\\
x^l \cdot [f(x)]^m & \text{for } 0 \le l \lt t & \text{ , and } i = m\delta + l
\end{cases}
$$

for all $i \in [0,m\delta + t)$, $f_i(x_0) \equiv 0 \pmod {b^m}$ , let

$$
g(x) = \sum_{i=0}^{m\delta + t - 1} a_if_i(x) \space\text{ , for all }\space a_i \in \mathbb Z
$$

we know that $g(x_0) \equiv 0 \pmod {b^m}$, if $|g(x_0)| \lt b^m$, then $g(x_0) = 0$

if ($n = m\delta + t$)

$$
g(x) = \sum_{i = 0}^{n - 1}g_i \cdot x^i
$$

then let

$$
\textbf{g(x)} = (g_0,\space g_1x,\space  g_2x^2 ,\space  ... ,\space g_{n-2}x^{n-2}, \space g_{n-1}x^{n-1})
$$

if 

$$
\left\|\textbf{g(X)}\right\| = \sqrt {\sum_{i=0}^{n-1} (g_i \cdot X^i)^2} \lt \frac{b^m}{\sqrt n}
$$

then

$$
\begin{aligned}
\left\|g(x_0)\right\| & = \left\| \sum_{i=0}^{n-1} g_i \cdot {x_0}^i \right\| \le \sum_{i=0}^{n-1} \left\| g_i \cdot {x_0}^i \right\| \\\\
& \le \sum_{i=0}^{n-1} \left\| g_i \cdot X^i \right\| = \sqrt {(\sum_{i=0}^{n-1} \left\| g_i \cdot X^i \right\| )^2} \\\\
& \le \sqrt {n \cdot \sum_{i=0}^{n-1} \left\| g_i \cdot X^i \right\| ^2} \quad \text{ , Cauchy–Schwarz inequality} \\\\
& = \sqrt n \cdot \|\textbf{g(X)}\| \lt b^m
\end{aligned}
$$

so now, if we have a $g(x) = \sum_{i=0}^{m\delta + t - 1} a_if_i(x)$, and choose $a_i$ let $\|\textbf{g(X)}\| \lt \frac{b^m}{\sqrt n}$. then we can solve $g(x) = 0$ to get it's roots and some of them will be ($f(x) \equiv 0 \pmod m$)’s roots

let $(\boldsymbol{f_0(X)},\space\boldsymbol{f_1(X)},\space ..., \boldsymbol{f_{n-1}(X)})$ be the basis of lattice $L$, we can use LLL-algorithm to find some $\textbf{g(X)}$ that

$$
\|\textbf{g(X)}\| \le 2^{\frac{n-1}{4}} \cdot \operatorname{det}(L)^{\frac{1}{n}}
$$

we know that $\operatorname{det}(L) = N^{\frac{1}{2}\delta m(m+1)} \cdot X^{\frac{1}{2}n(n-1)}$, if we choose $0 \lt \epsilon \le \frac{\beta}{7}$ , and

$$
m = \lceil \frac{\beta^2}{\delta \epsilon} \rceil ,\space t = \lfloor \delta m(\frac{1}{\beta} - 1) \rfloor ,\space X = \lceil \frac{1}{2}N^{\frac{\beta^2}{\delta} - \epsilon} \rceil
$$

after some calculation, then $2^{\frac{n-1}{4}} \cdot \operatorname{det}(L)^{\frac{1}{n}} \lt \frac{N^{\beta m}}{\sqrt n}$

> Implementation : [Sage Implement](https://doc.sagemath.org/html/en/reference/polynomial_rings/sage/rings/polynomial/polynomial_modn_dense_ntl.html#sage.rings.polynomial.polynomial_modn_dense_ntl.small_roots)

---
## Stereotyped Messages
if we know $m = \bar{m} + x_0$ and $x_0 \le N^{\frac{1}{e}}$, the cipher $c \equiv m^e \equiv (\bar{m} + x_0)^e \pmod N$, then $x_0$ is the small root of $f(x) \equiv (\bar{m} + x)^e - c \pmod N$

### Code

```python
def stereotyped_message(n: int, e: int, c: int, m0: int, epsilon=None):
    """
    - input : `n (int)`, `e (int)`, `c (int)`, `m0 (int)`, `epsilon (default=None)` , `0 < epsilon <= 1/7`
    - output : `m (int)` , `c`'s plain. if there's no solve, return `-1`
    """
    P = PolynomialRing(Zmod(n), implementation='NTL', names=('x',))
    x = P._first_ngens(1)[0]

    f = (m0 + x) ** e - c
    small_roots = f.small_roots(epsilon=epsilon)
    if len(small_roots) > 0:
        return int(small_roots[0]) + m0
    else:
        return -1
```

---
## Known High Bits Of p
if $p = \bar{p} + x_0$ and we know $\bar{p}$, $|x_0| \lt N^{\frac{1}{4}}$, then $x_0$ is $f(x) \equiv \bar{p} + x \pmod p$’s small root (assume that $p \gt q$)

### Code

```python
def known_high_bits_of_p(n: int, p0: int, epsilon=None):
    """
    - input : `n (int)`, `p0 (int)`, `epsilon (default=None)` , `0 < epsilon <= 0.5/7`
    - output : `(p, q) (int, int)`
    """
    P = PolynomialRing(Zmod(n), implementation='NTL', names=('x',))
    x = P._first_ngens(1)[0]
    
    f = p0 + x
    small_roots = f.small_roots(beta=0.5, epsilon=epsilon)
    if len(small_roots) > 0:
        p = p0 + int(small_roots[0])
        q = n // p
        assert p * q == n
        return p, q
    else:
        return -1
```

---
## Franklin-Reiter Related Message Attack
if we know $m_1, m_2$’s cipher $c_1, c_2$ is encrypted by public key $(n,e)$, and $m_1, m_2$ satisfy $m_2 = f(m_1)$ such $f$ is a polynomial on modulo $n$, consider

$$
\begin{aligned}
g_1(x) & \equiv x^e - c1 \pmod n \\\\
g_2(x) & \equiv {f(x)}^e - c2 \pmod n
\end{aligned}
$$

then $m_1$ is both $g_1(x)$ and $g_2(x)$’s root, so $\operatorname{gcd}(g_1,g_2) = x - m_1$

### Code

```python
def polynomialgcd(f1, f2):
    if f2 == 0:
        return f1.monic()

    if f2.degree() > f1.degree():
        f1, f2 = f2, f1

    while f2 != 0:
        f1, f2 = f2, f1 % f2
    
    return f1.monic()


def franklin_reiter(e: int, c1: int, c2: int, f, x):
    """
    - input : `e (int)`, `c1 (int)`, `c2 (int)`, `f (polynomial of x mod n)`, `x (symbol of polynomial mod n)`
    - output : `m1 (int)` , `f(m1) = m2`
    """

    f1 = x ** e - c1
    f2 = f ** e - c2
    return int(-polynomialgcd(f1, f2)[0])
```

---
## Coppersmith Short-Pad Attack
if $m_1 = 2^kM + r_1$ and $m_2 = 2^kM + r_2$ ($r_1$ and $r_2$ is padding, $M$ is plaintext), and we know $c_1, c_2$ is $m_1, m_2$’s cipher encrypted by $(n,c)$, consider

$$
\begin{aligned}
f_1(x,y) & \equiv x^e - c_1 \pmod n \\\\
f_2(x,y) & \equiv (x + y)^e - c_2 \pmod n
\end{aligned}
$$

if $\bar y = r_2 - r_1$, then $f_1(x,\bar y)$ and $f_2(x,\bar y)$ has common factor $x - m_1$, so $\operatorname{res}(f_1(x,\bar y),\space f_2(x,\bar y)) \equiv 0 \pmod n$. let $h(y) = \operatorname{res}_x(f_1,f_2)$, $\bar y$ will be a small root of $h(y)$

> **Resultant :** if $a$, $b$ is $f$, $g$’s leading coefficient, then
> 
> $$
> \operatorname{res}(f,g) = a^{\operatorname{deg}(f)} \cdot b^{\operatorname{deg}(g)} \cdot \prod_{\forall (x,y) , f(x) = g(y) = 0} (x - y)
> $$

then bring $\bar y$ back to $f_1$ and $f_2$ , this well simplify problem to Franklin-Reiter related message attack

### Code

```python
def coppersmith_short_pad_attack(n: int, e: int, c1: int, c2: int, epsilon=None):
    """
    - input : `n (int)`, `e (int)`, `c1 (int)`, `c2 (int)`, `epsilon (default=None)` , `0 < epsilon <= 1/7`
    - output : `m1 (int)` , `c1`'s plain
    """

    P2 = PolynomialRing(IntegerRing(), names=('x', 'y'))
    (x, y) = P2._first_ngens(2)

    f1 = x ** e - c1
    f2 = (x + y) ** e - c2
    h = f1.resultant(f2, x).univariate_polynomial().change_ring(Zmod(n))
    small_roots = h.small_roots(epsilon=epsilon)
    if len(small_roots) > 0:
        diff = small_roots[0]
    else:
        return -1

    P = PolynomialRing(Zmod(n), implementation='NTL', names=('x',))
    x = P._first_ngens(1)[0]

    f = x + diff
    return franklin_reiter(e, c1, c2, f, x)
```