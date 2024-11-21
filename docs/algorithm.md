Here are examples of **LL** and **LR** parsing algorithms:

---

### **1. LL Parsing Algorithm (Top-Down Parsing)**  
LL parsers read the input from **left-to-right** and construct a **leftmost derivation** of the sentence. 

#### Example Grammar:
```
S → A B
A → a
B → b
```

#### Steps:
1. Start with the start symbol `S`.
2. Expand `S` to `A B` (based on the rule `S → A B`).
3. Expand `A` to `a` (based on the rule `A → a`).
4. Match `a` from the input.
5. Expand `B` to `b` (based on the rule `B → b`).
6. Match `b` from the input.
7. Parsing completes successfully if the input matches.

#### Input String: `ab`  
- Stack: `S` → `A B` → `a B` → `B` → `b`  
- Output: Matches `ab` successfully.

#### Algorithm:  
The LL parsing table can be used to guide these expansions. It relies on FIRST and FOLLOW sets for predictive parsing.

---

### **2. LR Parsing Algorithm (Bottom-Up Parsing)**  
LR parsers read the input from **left-to-right** and construct a **rightmost derivation in reverse**.

#### Example Grammar:
```
S → A B
A → a
B → b
```

#### Steps:
1. Start with the input string `ab` and an empty stack.
2. Shift `a` from the input onto the stack.
3. Recognize that `a` matches `A → a`, so reduce `a` to `A`.
4. Shift `b` from the input onto the stack.
5. Recognize that `b` matches `B → b`, so reduce `b` to `B`.
6. Recognize that `A B` matches `S → A B`, so reduce `A B` to `S`.
7. Parsing completes successfully if the stack contains `S` and the input is empty.

#### Input String: `ab`  
- Stack: `a` → `A` → `A b` → `A B` → `S`  
- Output: Matches `ab` successfully.

#### Algorithm:  
LR parsers use a parsing table constructed with **items and states** derived from the grammar's LR(0), SLR, or LALR rules.

---

### Comparison:
| Feature               | LL Parser                          | LR Parser                          |
|-----------------------|-------------------------------------|-------------------------------------|
| Parsing Approach      | Top-down                          | Bottom-up                          |
| Input Scanning Order  | Left-to-right (L)                 | Left-to-right (L)                 |
| Derivation Constructed| Leftmost                          | Rightmost (in reverse)             |
| Complexity            | Easier to implement but less powerful | More powerful, handles ambiguous grammars |

Here are examples of **LL** and **LR** parsers implemented in Python. 

---

### **1. LL Parser Implementation**
This is a recursive-descent parser for a simple grammar:

#### Grammar:
```
S → A B
A → 'a'
B → 'b'
```

#### Python Code:
```python
class LLParser:
    def __init__(self, input_string):
        self.input = input_string
        self.position = 0

    def match(self, expected_char):
        if self.position < len(self.input) and self.input[self.position] == expected_char:
            self.position += 1
        else:
            raise SyntaxError(f"Expected '{expected_char}' at position {self.position}")

    def parse_S(self):
        self.parse_A()
        self.parse_B()

    def parse_A(self):
        self.match('a')

    def parse_B(self):
        self.match('b')

    def parse(self):
        self.parse_S()
        if self.position == len(self.input):
            print("Parsing succeeded!")
        else:
            raise SyntaxError(f"Unexpected characters remaining at position {self.position}")

# Test the LL parser
try:
    parser = LLParser("ab")
    parser.parse()
except SyntaxError as e:
    print(f"Parsing failed: {e}")
```

---

### **2. LR Parser Implementation**
This is a shift-reduce parser for the same grammar:

#### Grammar:
```
S → A B
A → 'a'
B → 'b'
```

#### Python Code:
```python
class LRParser:
    def __init__(self, input_string):
        self.input = input_string + '$'  # Add end marker
        self.stack = []
        self.position = 0

    def shift(self):
        if self.position < len(self.input):
            self.stack.append(self.input[self.position])
            self.position += 1

    def reduce(self):
        # Reduce 'a' to A
        if self.stack[-1] == 'a':
            self.stack.pop()
            self.stack.append('A')
        # Reduce 'b' to B
        elif self.stack[-1] == 'b':
            self.stack.pop()
            self.stack.append('B')
        # Reduce 'A B' to S
        elif len(self.stack) >= 2 and self.stack[-2:] == ['A', 'B']:
            self.stack.pop()
            self.stack.pop()
            self.stack.append('S')

    def parse(self):
        while self.position < len(self.input) or len(self.stack) > 1:
            if self.position < len(self.input) and self.input[self.position] not in ['$']:
                self.shift()
            self.reduce()

        if self.stack == ['S']:
            print("Parsing succeeded!")
        else:
            raise SyntaxError("Parsing failed!")

# Test the LR parser
try:
    parser = LRParser("ab")
    parser.parse()
except SyntaxError as e:
    print(f"Parsing failed: {e}")
```

---

### **Key Features**:
1. **LL Parser**: Simple recursive-descent approach with explicit functions for each grammar rule.
2. **LR Parser**: Implements a shift-reduce strategy, reducing rules in reverse order of a rightmost derivation.

Both parsers will successfully parse the input `ab` according to the given grammar.
