## Automaton

- Finite-state machine
- Success edges
- Failure edges

At all times we have text character $T[i]$ and the current node in the graph $P[j]$. We iterate the following rules:

- If $T[i]=P[j]$, or if the current label is 0, follow the success edge to the next node and increment $i$.
- If $T[i] \neq P[j]$, follow the failure edge back, but do not change $i$.
If we reach the final node, then we have found an instance, if not and $i>n$ and we are not in the final node there is no match.

## Implementation

- Encode failure function in an array `fail[1..n]`, where for each index $j$, the failure edge from node j leads to node `fail[j]`. 
- Success edges are just moving from $P[j]$ to $P[j+1]$ (or the final state).

### Algorithm

```
def KMP(T[1..n], P[1..m]):
	j = 1
	for j in range(1, n + 1):
		while j > 0 and T[i] != P[j]:
			j = fail[j]
		if j = m:
			 return i - m + 1
		j += 1
	return None
```

### Constructing failure function

We follow the rule:

- `P[1..fail[j] - 1]` is the longest proper prefix of `P[1..j-1]` that is also a suffix of `P[1..j-1]`

```
def compute_failure(P[1..m]):
	j = 0
    fail = [0] * m
	for i in range(1, m + 1):
		fail[i] = j (&)
		while j > 0 and P[i] != P[j]:
			j = fail[j]
		j += 1
    return fail 
```

### Analysis

#### KMP Correctness (assuming correct fail)
##### Background

While searching for pattern `P` in text `T`, the KMP algorithm maintains a pointer `i` for the text and a pointer `j` for the pattern. At each step, it tries to match the character `T[i]` with `P[j]`.

##### Invariant

At the start of each iteration of the `for` loop (with current index `i`), the substring `T[i-j..i-1]` matches the prefix `P[1..j]`.

_Proof_:

1. **Initialization**: At the start, `j=1`, and there's no matched substring in `T`, so the invariant holds.
2. **Maintenance**: If `T[i] == P[j]`, the algorithm moves to the next character in both `T` and `P`, maintaining the invariant. If `T[i] != P[j]`, the algorithm adjusts `j` using `fail[j]` to the longest border of the current prefix of `P`. This ensures that the already matched part of `T` aligns with a border of `P`, which is identical to its prefix, thus maintaining the invariant.
3. **Termination**: The loop terminates when we've processed every character in `T`. If a match is found, the function returns the start position of the match in `T`. If no match is found, it returns `None`.

Given our invariant and the assumption that `compute_failure` is correct, we can conclude that the KMP algorithm correctly finds a match of pattern `P` in text `T` or determines that no such match exists.

#### KMP Time (without computing fail)

We can increment `i` at most `n − 1` times before we run out of text, so there are at most `n − 1` successful comparisons. Similarly, there can be at most `n − 1` failed comparisons, since the number of times we decrease `j` cannot exceed the number of times we increment `j`. In other words, we can amortize character mismatches against earlier character matches. Thus, the total number of character comparisons performed by KMP in the worst case is $O(n)$.
#### Compute Fail Correctness

`compute_failure` is actually a dynamic programming implementation of the following recursive definition of `fail[i]`:
$$
fail[i] = \begin{cases} 0 &\text{if } i = 0,\\ \max_{c\geq1}\{fail^c[i-1]+1|P[i-1]=P[fail^c[i-1]]\} &\text{otherwise.}\end{cases}
$$

Proof by induction that `compute_failure` correctly computes the failure function:
- Base case `fail[1] = 0`
- Assume correct for `fail[1]` through `fail[i - 1]`
- We need to show that when we reach line (&) `fail[i]` is correct.
- Just after `i`th iteration of line (&), we have `j=fail[i]`, so `P[1..j-1]` is the longest proper prefix of `P[1..i-1]` that is also its suffix.
- Let's define the iterated failure functions $fail^c[j]$ inductively as follows: $fail^0[j]=j$ and $fail^c[j]=fail[fail^{c-1}[j]]$. In particular, if $fail^{c-1}[j]=0$, then $fail^c[j]$ is undefined. We can easily show by induction that every string of the form $P[1..fail^c[j]-1]$ is both a proper prefix and a proper suffix of $P[1..i-1]$, and in fact, these are the only examples. Thus, the longest proper prefix/suffix of $P[1..i]$ must be the longest string of the form $P[1..fail^c[j]]$ - the one with smallest $c$ - such that $P[fail^c[j]=P[i]]$. This is exactly what the while loop in `compute_failure` computes. The $(c+1)$th iteration compares $P[fail^c[j]]=P[fail^{c+1}[j]]$ against $P[i]$.

Alternative proof:
##### Background

The idea behind the Knuth-Morris-Pratt (KMP) algorithm is to efficiently utilize previously matched portions of the pattern `P` to skip unnecessary comparisons in the future.

The `compute_failure` function builds the `fail` array. For each position `i` in `P`, `fail[i]` represents the length of the longest proper prefix of the substring `P[1..i]` that is also a suffix of `P[1..i]`. This prefix is also known as the _border_ of the substring.

##### Proof

**Lemma**: After the execution of the `while` loop for a given `i` in `compute_failure`, `j` represents the length of the longest border of the substring `P[1..i]`.

_Proof_:

1. **Base Case**:
    - For `i=1`, `j=0`, which is correct since there's no border for a single character substring.
2. **Inductive Hypothesis**:
    - Suppose the lemma holds for `i-1`, i.e., at the end of the loop for `i-1`, `j` holds the length of the longest border for substring `P[1..i-1]`.
3. **Inductive Step**:
    - Consider iteration `i`. If `P[i] == P[j]`, then the border length for `P[1..i]` is `j+1`. This follows since the substring `P[1..j]` matches the prefix of `P[1..i-1]` and `P[j] == P[i]`.
    - If `P[i] != P[j]`, we set `j` to `fail[j]`. This action essentially tries to find a shorter border for the substring `P[1..i-1]` that matches a prefix of `P[1..i]`. We continue this until `j=0` (no border) or `P[i] == P[j]`.

From the lemma, we can conclude that `compute_failure` computes the correct values for the `fail` array for each position in `P`.
#### Compute Fail Time

Same amortization as KMP. Runs in $O(m)$.

# Anki notes:

---



