## Automaton

- Trie of patterns
- Success edges
- Failure edges (also acting as shortcuts)

Given a text `T` and patterns `P_1, P_2, ..., P_k`, Aho-Corasick will identify all occurrences of any of the patterns in the text.

## Implementation

### Algorithm

```
def AhoCorasick(T[1..n], patterns):
    construct_trie_and_shortcuts(patterns)
    j = root of trie
    for i in range(1, n+1):
        while j is not root and T[i] is not a child of j:
            j = shortcut[j]
        if T[i] is a child of j:
            j = child node of j for T[i]
        if j is an end of pattern node:
            print matched pattern
```

### Constructing Trie and Shortcuts

```
def construct_trie_and_shortcuts(patterns):
    root = new node
    for each pattern in patterns:
        insert_into_trie(pattern, root)

    shortcut[root] = root
    for each node v in level order from root:
        for each character c such that v has a child u by c:
            w = shortcut[v]
            while w is not root and w does not have a child by c:
                w = shortcut[w]
            if w has a child by c:
                shortcut[u] = child of w by c
            else:
                shortcut[u] = root
            if shortcut[u] is an end of pattern node:
                add that pattern to u's matched patterns list
    return root
```

### Insert Pattern into Trie

```
def insert_into_trie(pattern, root):
    current = root
    for char in pattern:
        if char not in current.children:
            current.children[char] = new node
        current = current.children[char]
    current.is_end_of_pattern = True
```

### Notes

- **Shortcut**: This combines the functionality of failure and output links. If a node `v` corresponds to the string `x`, then the shortcut from `v` not only points to the node representing the longest proper suffix of `x` that's also a prefix of some pattern, but also checks if that node is an end of pattern node and if so, adds that pattern to `v`'s matched patterns list.

Sure. Let's delve deep into the correctness of the Aho-Corasick algorithm.

## Correctness of Failure Links

### Definition:

For each node $v$ that represents a string $s$, the failure link from $v$ points to the longest proper suffix of $s$ which is also a prefix of some pattern.

### Proof:

1. **Base Case**: For the root, the failure link always points to itself, representing an empty string, which is a proper suffix for any string.
  
2. **Inductive Hypothesis**: Assume the failure links are correct up to level $L$ of the trie.

3. **Inductive Step**: For a node $v$ at level $L+1$ which represents a string $s$ ending in character $c$, and let its parent be $u$ (representing string $s'$), the failure link of $v$ is found by extending the failure link of $u$. 

    - If the failure link of $u$ has a direct edge by character $c$, then it's the longest proper suffix of $s$ which is also a prefix of some pattern. 
    - If not, the algorithm keeps following the failure links of the failure links until it reaches the root or finds a valid edge by $c$. In this manner, it finds the longest such suffix. If it reaches the root, it means no such suffix exists and the failure link of $v$ points to the root.

By induction, the correctness of failure links is proven.

## Correctness of Shortcuts

### Definition:

For each node $v$, the shortcut either points to the root (if no pattern is a suffix of the string represented by $v$) or the node that represents a pattern which is the longest suffix of the string represented by $v$.

### Proof:

Given the correctness of failure links, we can determine the shortcut of a node $v$:

1. Follow the failure link. If it's an end of a pattern node, it's the desired node.
  
2. If not, keep following the failure links of the failure links until an end of pattern node is found or the root is reached.

3. If root is reached, no pattern is a suffix, so shortcut points to root.

## Correctness of the Whole Algorithm

Given the correctness of failure links and shortcuts:

1. **Invariant**: At any point during the search in the text, if the current node is $v $ which represents string $s $, then the latest characters of the text represent a string which is a suffix of $s $.

2. **Initialization**: The algorithm starts at the root, representing an empty string, which is trivially a suffix of any string.

3. **Maintenance**: For every character $c $ in the text, the algorithm tries to transition using $c $. If the transition is unavailable, it follows the failure link and tries again, ensuring that it's checking for the longest suffix of the current string which is also a prefix of some pattern and has a transition by $c $.

    - If no such transition exists all the way to the root, it means the current character doesn't start any pattern. Move to the next character and reset to root.
    
    - If a transition is found, it moves to the respective node.

4. **Termination**: At the end of the text, given our invariant, if the current node is an end of pattern node, it indicates a match ending at the last character of the text.

By maintaining our invariant, the Aho-Corasick algorithm correctly identifies all occurrences of any pattern in the text.

**Side Note**: While this proof provides a logical reasoning for the correctness of the algorithm, in more rigorous settings, more formal proofs with detailed lemmas and arguments would be necessary.

## Time analysis

Alright, let's get into the time complexity analysis of the Aho-Corasick algorithm.

### Trie Construction

1. **Insertion of Patterns**:
   - Each pattern insertion into the trie will take $O(m)$ time, where $m$ is the length of the pattern.
   - Since there are $k$ patterns, the total time for insertion is $O(k \times m$. However, if the patterns are of varying lengths, it becomes $O(\sum_{i=1}^{k} m_i)$, where $m_i$ is the length of the $i^{th}$ pattern.

2. **Building Failure Links**:
   - For each node, it involves moving up the trie until the root is reached or a match is found.
   - In the worst case, this process will take $O(m)$ time per node.
   - Given that there are $n$ nodes in the trie, the worst-case time for building failure links for all nodes is $O(n \times m)$. This is a pessimistic upper bound, and in practice, it's much faster as not every node would require $m$ steps.

3. **Building Shortcuts**:
   - Since shortcuts are built using failure links which are already constructed, the complexity is $O(n)$ for all nodes.

### Text Processing

1. **For each character in the text**:
   - You either move to the next node in the trie or follow the failure links to find the next node.
   - In the worst case, for each character, you might have to follow failure links up to the root. However, once you follow a failure link, you essentially skip some characters in the text.
   - Thus, for a text of length $p$, the worst-case time is $O(p)$.

### Total Time Complexity

Considering all the steps:
$$ O(\sum_{i=1}^{k} m_i) + O(n \times m) + O(n) + O(p) $$

However, the dominating factors here would be the construction of the trie and the text processing. In practice, the time for building failure links is much less than $O(n \times m)$. Thus, the time complexity can often be represented more succinctly as:

 $$O(\sum_{i=1}^{k} m_i) + O(p)$$

Where:
- $k$ is the number of patterns
- $m_i$ is the length of the$i^{th}$ pattern
- $n$ is the total number of nodes in the trie (essentially the sum of lengths of all patterns)
- $p$ is the length of the text.

**Space Complexity**:
The space complexity is primarily determined by the trie, which has $n$ nodes and, in the worst case, can have up to 26 (or more, depending on the alphabet) children pointers per node. Thus, the space complexity is $O(n \times \text{ALPHABET SIZE})$. Again, in practice, most nodes won't have children for every possible character, so the actual memory usage is often much less.