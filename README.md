# EXP.NO.6-Simulation-of-Hamming-and-Shannon-Fano-Code

# AIM
Simulation of Hamming and Shannon-Fano Code

# SOFTWARE REQUIRED
Google Collab

# ALGORITHMS
1.Input the probability of each symbol/message (e.g., P₁, P₂, ..., P₇). 2.Create leaf nodes
for each symbol, sorted by increasing probability. 3.Repeat until there's only one node
left: 4.Pick the two nodes with the lowest probability. 5.Combine them into a new node
with their summed probability. 6.Assign 0 and 1 to branches from the new node.
7.Assign codes to each symbol by tracing the path from the root to the leaf (left → 0,
right → 1).
1. Input the probabilities Pi for each symbol.
2. Sort the symbols in descending order of their probability. 3.Divide the list into two
parts such that the total probabilities are as equal as possible. 4.Assign 0 to the
upper group and 1 to the lower group. 5.Recursively apply the procedure to each
group, appending the bit to the existing code

# PROGRAM
```
import heapq
import math

# Node class for Huffman Tree
class Node:
    def _init_(self, symbol, prob):
        self.symbol = symbol
        self.prob = prob
        self.left = None
        self.right = None

    def _lt_(self, other):
        return self.prob < other.prob

# Generate Huffman Codes
def huffman_coding(probabilities):
    heap = [Node(symbol, prob) for symbol, prob in probabilities]
    heapq.heapify(heap)

    while len(heap) > 1:
        left = heapq.heappop(heap)
        right = heapq.heappop(heap)
        merged = Node(None, left.prob + right.prob)
        merged.left = left
        merged.right = right
        heapq.heappush(heap, merged)

    def generate_codes(node, code=""):
        if node is None:
            return {}
        if node.symbol is not None:
            return {node.symbol: code}
        left_codes = generate_codes(node.left, code + "0")
        right_codes = generate_codes(node.right, code + "1")
        return {**left_codes, **right_codes}

    return generate_codes(heap[0])

# Generate Shannon-Fano Codes
def shannon_fano(probabilities):
    probabilities.sort(key=lambda x: x[1], reverse=True)

    def recursive_sf(pairs):
        if len(pairs) == 1:
            return {pairs[0][0]: ""}
        total = sum(prob for _, prob in pairs)
        acc = 0
        for i in range(len(pairs)):
            acc += pairs[i][1]
            if acc >= total / 2:
                break
        left = pairs[:i + 1]
        right = pairs[i + 1:]
        left_codes = recursive_sf(left)
        right_codes = recursive_sf(right)
        for k in left_codes:
            left_codes[k] = "0" + left_codes[k]
        for k in right_codes:
            right_codes[k] = "1" + right_codes[k]
        return {**left_codes, **right_codes}

    return recursive_sf(probabilities)

# Entropy
def calculate_entropy(p_list):
    return round(sum(p * math.log2(1 / p) for p in p_list), 3)

# Average length, Efficiency, Redundancy, Variance
def calculate_metrics(prob_dict, code_dict, entropy):
    lengths = [len(code_dict[sym]) for sym in prob_dict]
    probs = [prob_dict[sym] for sym in prob_dict]
    avg_len = sum(p * l for p, l in zip(probs, lengths))
    efficiency = round(entropy / avg_len, 3)
    redundancy = round(1 - efficiency, 3)
    variance = round(sum(p * (len(code_dict[sym]) - avg_len) ** 2 for sym, p in prob_dict.items()), 3)
    return avg_len, efficiency, redundancy, variance

# Sample probabilities (7 symbols)
prob_dict = {
    "S1": 0.2,
    "S2": 0.15,
    "S3": 0.1,
    "S4": 0.2,
    "S5": 0.1,
    "S6": 0.15,
    "S7": 0.1
}

# Normalize if not summing to 1
total_prob = sum(prob_dict.values())
if round(total_prob, 2) != 1.0:
    print("Warning: Normalizing probabilities...")
    prob_dict = {k: v / total_prob for k, v in prob_dict.items()}

symbol_prob_list = list(prob_dict.items())
entropy_val = calculate_entropy([p for _, p in symbol_prob_list])

# Huffman
huff_codes = huffman_coding(symbol_prob_list)
L_huff, eff_huff, red_huff, var_huff = calculate_metrics(prob_dict, huff_codes, entropy_val)

# Shannon-Fano
shannon_codes = shannon_fano(symbol_prob_list)
L_shan, eff_shan, red_shan, var_shan = calculate_metrics(prob_dict, shannon_codes, entropy_val)

# Output
print("\n--- Huffman Coding ---")
for sym in sorted(huff_codes): print(f"{sym}: {huff_codes[sym]}")
print(f"Average Codeword Length: {round(L_huff, 3)}")
print(f"Entropy: {entropy_val}")
print(f"Efficiency: {eff_huff}")
print(f"Redundancy: {red_huff}")
print(f"Variance: {var_huff}")

print("\n--- Shannon-Fano Coding ---")
for sym in sorted(shannon_codes): print(f"{sym}: {shannon_codes[sym]}")
print(f"Average Codeword Length: {round(L_shan, 3)}")
print(f"Entropy: {entropy_val}")
print(f"Efficiency: {eff_shan}")
print(f"Redundancy: {red_shan}")
print(f"Variance: {var_shan}")
```

# OUTPUT
![Screenshot 2025-04-08 234039](https://github.com/user-attachments/assets/4df1bfe1-7333-45ff-9125-9903c669929c)

 
# RESULT
Simulation of Hamming and Shannon-Fano Code was verified successfully
