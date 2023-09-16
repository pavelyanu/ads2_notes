- Assume that the alphabet consists of ten digits 0 through 9.
- Let $p$ be the numerical value of the pattern $P$, and for any shift $s$, let $t_s$ be the numerical value of $T_s$:
$$
p = \sum_{i=1}^m10^{m-i}\cdot P[i]
$$
$$
t_s=\sum_{i=1}^m10^{m-i}\cdot T[s+i-1]
$$
Clearly we can rephrase our problem as follows: Find the smallest $s$, if any, such that $p=t_s$. To compute $p$ in $O(m)$ we can use Horner's Rule:
$$
 p=P[m]+10(P[m−1]+10(P[m−2]+···+10(P[2]+10 \cdot P[1])...))
$$