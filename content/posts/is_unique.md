---
title: "Leetcode 1: Is unique?"
date: 2025-01-25T18:05:26Z
Description: ""
Categories: ["Leetcode"]
Author: "Lmbf"
---

The company I work for decided to do layoffs and fortunately I was not impacted. However, it did get me thinking on whether or not I felt ready to interview with other companies in case I had been fired. And I honestly don't feel that ready. As such, I bought the *Cracking the Coding Interview* book and I'm going to be solving every single question that is in the book. Worst case scenario, I'll learn some new stuff, which isn't that bad. Plus, I'll get to refresh my Python knowledge, which is always nice.

Okay, first exercise here we go: **implement an algorithm to determine if a string has all unique characters.**

Side note: this is called a heterogram. This is fairly straightforward to solve, all I need to do is iterate over the characters in the string while keeping a ledger of the characters I've already seen. I'll also sprinkle in some nicely formatted unit tests just for good measure. To ensure that this ledger is efficient for lookups (since we'll have to check every character against it), we use a hashmap to have constant time access.

```python
import unittest
from dataclasses import dataclass

class HeterogramChecker():
    def check(self, text):
        seen = {}
        for c in text:
            if c in seen:
                return False
            else:
                seen[c] = True
        return True


class TestHeterogramChecker(unittest.TestCase):
    def test_check(self):
        @dataclass
        class TestCase:
            name: str
            input: str
            expected: bool

        test_cases = [
            TestCase(name="heterogram with just letters", input="abcd", expected=True),
            TestCase(name="heterogram with letters and number", input="s4fad", expected=True),
            TestCase(name="empty string", input="", expected=True),
            TestCase(name="repeated number in string", input="23ds2", expected=False),
            TestCase(name="several repeated characters in string", input="hb 627jh=j ()", expected=False)]
        
        checker = HeterogramChecker()
        for tc in test_cases:
            res = checker.check(text=tc.input)
            self.assertEqual(
                res, 
                tc.expected, 
                msg=f"[{tc.name}] expected {tc.expected}, but got {res}")


if __name__ == "__main__":
    unittest.main()
```
