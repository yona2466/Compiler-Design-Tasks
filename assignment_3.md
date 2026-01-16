# yona_code_assi_1: Compiler Design Assignment 3
## Topic #64: Static Detection of Infinite Recursion

**Student:** [yonas_birhanie]  
**Course:** Principles of Compiler Design  
**Date:** 

---

## 1. Introduction to Static Analysis
**Static Analysis** is the process of inspecting source code without actually executing it. In this assignment, we design a **heuristic system** (a rule-based approximation) to detect "Infinite Recursion" before the program even runs. This helps prevent **Stack Overflow** errors.

---

## 2. The Heuristic: How to Detect Infinite Recursion
Since the "Halting Problem" proves we cannot detect *every* infinite loop perfectly, compilers use **Heuristics** (basic checks) to flag suspicious code.

### Heuristic Rule 1: Self-Call without Condition
If a function `foo()` calls `foo()` and there is no `if` statement or `return` branch before the call, it is 100% infinite.

### Heuristic Rule 2: Lack of Parameter Change
If a function `foo(int n)` calls `foo(n)` without changing the value of `n`, it is likely to never reach a base case.

### Heuristic Rule 3: Unreachable Base Case
If the base case exists but the recursive call happens *before* the check, the check is never reached.



---

## 3. Technical Example: Safe vs. Dangerous

**Dangerous Code (Infinite):**
```cpp

void count(int n)
{
    // No 'if' check here!
    count(n - 1); // Infinite Recursion
}
```
## Safe Code (Terminating):

void count(int n)
{
    if (n <= 0) return; // Base Case
    
 count(n - 1);       // Progress towards base case
}

## 4. Visual Logic Flow

graph TD

    Start[Analyze Function] --> CheckCall{Does it call itself?}
    
    CheckCall -- No --> Safe[Mark as Safe]

    CheckCall -- Yes --> CheckBase{Is there a Base Case?}
    
    CheckBase -- No --> Error[FLAG: Infinite Recursion]
    
    CheckBase -- Yes --> CheckProgress{Does argument change?}
    
    CheckProgress -- No --> Warning[FLAG: Possible Infinite Loop]
    
    CheckProgress -- Yes --> ProbableSafe[Mark as Likely Safe]

   ## 5. C++ Implementation: The "Heuristic Detector"
This program simulates how a compiler's static analyzer would check a function for the "Missing Base Case" rule.
```
#include <iostream>
#include <string>

using namespace std;

// Structure to hold function metadata for static analysis
struct FunctionInfo {
    string name;
    bool hasBaseCase;
    bool callsSelf;
};

// The Heuristic Detection Logic
void analyzeRecursion(FunctionInfo func) {
    cout << "Analyzing: " << func.name << endl;
    
    if (func.callsSelf && !func.hasBaseCase) {
        cout << "STATUS: [!!] CRITICAL ERROR detected." << endl;
        cout << "REASON: Infinite recursion (No Base Case)." << endl;
    } else if (func.callsSelf && func.hasBaseCase) {
        cout << "STATUS: [OK] Recursive but has base case." << endl;
    } else {
        cout << "STATUS: [OK] Function is non-recursive." << endl;
    }
    cout << "------------------------------------------" << endl;
}

int main() {
    cout << "--- Static Recursion Analysis ---" << endl << endl;

    FunctionInfo f1 = {"infiniteCounter", false, true};
    FunctionInfo f2 = {"safeFactorial", true, true};

    analyzeRecursion(f1);
    analyzeRecursion(f2);

    return 0;
}
```

## 6. Importance & Conclusion
## Importance
Resource Management: Prevents the CPU from wasting cycles on code that will never finish.

Reliability: Helps developers catch "off-by-one" errors in recursive logic during the coding phase.

Optimization: Compilers can use these checks to decide whether to perform Tail Call Optimization.

## Conclusion
Static detection of infinite recursion is a vital part of modern compiler safety. While it is impossible to detect 
every case due to the complexity of logic, basic heuristics catch the most common "Missing Base Case" errors, saving time and preventing system crashes.
