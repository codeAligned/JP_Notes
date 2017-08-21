## Arithmetic Sums
In counting the number of steps executed during the evaluation of loops or nested sequence of loops, various arithmetic sums will sometimes arise. Here is a quick overview of the notation for arithmetic sums and closed form solutions for some common sums.

### Notation
An arithmetic sum is the operation of adding a sequence of numbers. If we index the numbers in the sequence by the subscript i, the arithmetic sum can be expressed mathematically in the form

∑<sub>i</sub> = 0<sub>nai</sub> = a<sub>0</sub> + a<sub>1</sub> + a<sub>2</sub> + ...a<sub>n</sub>.
Note that, as opposed to Python, both the lower bound (zero) and the upper bound (n) are also included in the sum.

#### Common arithmetic sums and their solutions
Closed form expressions for almost all of the various arithmetic sums that you will encounter during this class are listed below. It's not particularly important to memorize these expressions, just be aware that they exists so you know that you can look them up when necessary.

+ ∑<sub>i=0</sub><sup>n</sup> 1 = 1 + 1 + ... + 1 = n + 1  
The sum of a constant expression is a linear polynomial in _n_.

+ ∑<sub>i=0</sub><sup>n</sup> n = n + n + n + ... + n = (n + 1)n   
The sum of a linear expression in _n_ is a quadratic polynomial in _n_.

+ ∑<sub>i=0</sub><sup>n</sup> i = 0 + 1 + 2 + ... + n = 1/2 \* (n + 1)n   
This sum is known as a triangular sum. The sum of a linear expression in _i_ is also a quadratic polynomial in _n_.

+ ∑<sub>i=0</sub><sup>n</sup> 2<sup>i</sup> = 2<sup>0</sup> + 2<sup>1</sup> + 2<sup>2</sup> + ... + 2<sup>n</sup> = 2<sup>n+1</sup>−1   
This sum is known as a geometric sum. In most cases, the sum of a sequence of exponential expressions is again exponential.

+ ∑<sub>i=0</sub><sup>n</sup> αi = α<sup>0</sup> + α<sup>1</sup> + α<sup>2</sup> + ... + α<sup>n</sup> = α<sup>n+1</sup>−1 / (α − 1)   
Note that this relation holds as long as α≠1. If 0<α<1 and the sum is infinite, i.e. n=∞, the sum reduces to 1 / (1−α).

+ ∑<sub>i=1</sub><sup>n</sup> 1/i = 1 + 1/2 + 1/3 + ... + 1/n ≈ log(n) + γ   
This sum is called a harmonic sum and has only an approximate solution (indicated by the symbol ≈). Here, γ is a [small constant](http://en.wikipedia.org/wiki/Euler%E2%80%93Mascheroni_constant).
