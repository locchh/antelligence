Building an **Abstract Syntax Tree (AST)** from natural language in real-time can be approached using a parsing algorithm. Here's an example implementation using a **Shift-Reduce Parser** for simplicity, with a predefined grammar for constructing an AST from natural language input.

---

### Example: Real-Time AST Construction for Simple English Sentences

#### Target Grammar:
The grammar is defined to parse simple subject-verb-object (SVO) sentences like:  
- `"The cat eats fish"`

#### Grammar Rules:
1. `S → NP VP`  (Sentence → Noun Phrase + Verb Phrase)  
2. `NP → DET N` (Noun Phrase → Determiner + Noun)  
3. `VP → V NP`  (Verb Phrase → Verb + Noun Phrase)  

---

#### Python Code for Real-Time AST Construction
```python
class Node:
    """Represents a node in the AST."""
    def __init__(self, value, children=None):
        self.value = value
        self.children = children if children else []

    def __repr__(self, level=0):
        """Pretty print the AST."""
        ret = "  " * level + repr(self.value) + "\n"
        for child in self.children:
            ret += child.__repr__(level + 1)
        return ret


class ShiftReduceParser:
    def __init__(self):
        # Grammar rules (left-hand side -> right-hand side)
        self.rules = {
            "S": [["NP", "VP"]],
            "NP": [["DET", "N"]],
            "VP": [["V", "NP"]],
        }

    def is_reduce_possible(self, stack):
        """Check if a reduction is possible."""
        for lhs, rhs_list in self.rules.items():
            for rhs in rhs_list:
                if stack[-len(rhs):] == rhs:
                    return lhs, rhs
        return None, None

    def parse(self, tokens):
        """Perform shift-reduce parsing to build an AST."""
        stack = []
        ast_stack = []

        index = 0
        while index < len(tokens) or len(stack) > 1:
            # Shift: Move a token to the stack
            if index < len(tokens):
                stack.append(tokens[index])
                ast_stack.append(Node(tokens[index]))
                index += 1

            # Reduce: Apply grammar rules
            lhs, rhs = self.is_reduce_possible(stack)
            if lhs:
                # Pop right-hand side elements
                children = []
                for _ in rhs:
                    stack.pop()
                    children.append(ast_stack.pop())

                # Reverse children for correct order
                children.reverse()

                # Push left-hand side
                stack.append(lhs)
                ast_stack.append(Node(lhs, children))
            elif index == len(tokens):
                # If reduction isn't possible and input is exhausted, fail
                raise SyntaxError("Parsing failed. Invalid syntax.")

        # The final AST should be the only node left
        if len(ast_stack) == 1:
            return ast_stack[0]
        else:
            raise SyntaxError("Parsing failed. Ambiguous or incomplete input.")


# Real-time example
if __name__ == "__main__":
    # Tokenized input sentence
    tokens = ["DET", "N", "V", "DET", "N"]  # Represents: "The cat eats the fish"

    parser = ShiftReduceParser()
    try:
        ast = parser.parse(tokens)
        print("Abstract Syntax Tree (AST):")
        print(ast)
    except SyntaxError as e:
        print(f"Error: {e}")
```

---

### Explanation of the Code:
1. **Tokens Input**:
   The sentence is tokenized into grammar components (`DET`, `N`, `V`, etc.). In a real-world scenario, you can use a **part-of-speech (POS) tagger** to automate this step.

2. **Shift-Reduce Parsing**:
   - **Shift**: Moves tokens from input to the stack.
   - **Reduce**: Matches sequences on the stack with grammar rules and replaces them with non-terminal symbols.

3. **AST Construction**:
   The `Node` class represents nodes in the AST, and its `children` list holds subtrees for recursive parsing.

---

### Example Run:
For the tokenized input: `["DET", "N", "V", "DET", "N"]`  
Representing the sentence: `"The cat eats the fish"`

The output will be:
```
Abstract Syntax Tree (AST):
'S'
  'NP'
    'DET'
    'N'
  'VP'
    'V'
    'NP'
      'DET'
      'N'
```

---

### Enhancements for Real-Time Parsing:
1. **Tokenization & POS Tagging**: Use a library like **spaCy** or **NLTK** to preprocess raw text into tokens and POS tags.
2. **Ambiguity Handling**: Extend grammar rules or use probabilistic parsers (e.g., with weights or neural models) to handle ambiguous sentences.

Would you like an example integrating **spaCy** for real-time POS tagging?
