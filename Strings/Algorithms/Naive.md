```
def naive(T[1..n],P[1..m]):
	for s in range(1, n - m + 1 + 1):
		equal = True
		i = 1
		while equal and i <= m:
			if T[s + i - 1] != P[i]:
				equal = False
			else:
				i += 1
		if equal:
			return s
	return None
```

## Worst case running time:
$O((n-m)m)=O(nm)$