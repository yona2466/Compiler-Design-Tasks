# yona_code_assi_1: Compiler Design Assignment 1

## 1. Theory: Left Recursion and Parsing
**Definition:**
Left recursion occurs when a grammar has a non-terminal `A` that can derive a string starting with itself, like `A → Aα`. 

**Why it is problematic:**
* **Infinite Loops:** Top-down parsers (like Recursive Descent) keep expanding `A` without ever reading an input character.
* **Stack Overflow:** The program runs out of memory because the parser calls itself forever.
* **Deterministic Choice:** The parser cannot decide when to stop the recursion to match actual tokens.



---

## 2. C++: Arithmetic Expression Tokenizer
This program identifies **Integers**, **Plus (+)**, and **Multiply (*)** tokens.

```cpp
#include <iostream>
#include <string>
#include <cctype>

using namespace std;

void tokenize(string input) {
    for (int i = 0; i < input.length(); i++) {
        if (isspace(input[i])) continue;
        if (isdigit(input[i])) {
            string val = "";
            while (i < input.length() && isdigit(input[i])) {
                val += input[i++];
            }
            i--; 
            cout << "[Type: INTEGER, Value: " << val << "]" << endl;
        } 
        else if (input[i] == '+') cout << "[Type: PLUS,    Value: +]" << endl;
        else if (input[i] == '*') cout << "[Type: MULTIPLY, Value: *]" << endl;
    }
}

int main() {
    tokenize("15 + 3 * 9");
    return 0;
}
3. Problem Solving: Parse Tree Construction
Given Grammar:

S → AB

A → aA | ε

B → bB | b

Target String: aab

Derivation steps:
Step 1: S -> AB
Step 2: AB -> aAB       (Rule: A -> aA)
Step 3: aAB -> aaAB     (Rule: A -> aA)
Step 4: aaAB -> aaεB    (Rule: A -> ε)
Step 5: aaB -> aab      (Rule: B -> b)
Visual Parse Tree:
S
     /   \
    A     B
   / \    |
  a   A   b
     / \
    a   A
        |
        ε
