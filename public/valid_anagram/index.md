# Leetcode 9: Valid anagram


**Problem statement**: given two strings `s` and `t`, return `true` if the two strings are anagrams of each other, otherwise return `false`. An anagram is a string that contains the exact same characters as another string, but the order of the characters can be different.

Not much to say here, this is immediate using `Counter`s, just create one for each string and compare them directly with `==`.

```python
import unittest
from collections import Counter
from dataclasses import dataclass

class AnagramChecker():
    def check(self, s: str, t: str) -> bool:
        return Counter(s) == Counter(t)

class TestAnagramChecker(unittest.TestCase):
    def test_check(self):
        @dataclass
        class TestCase:
            name: str
            s: str
            t: str
            expected: bool

        test_cases = [
            TestCase(name="not an anagram", s="racecar", t="carrace", expected=True),
            TestCase(name="is an anagram", s="mouse", t="house", expected=False),
            TestCase(name="works for empty string", s="", t="", expected=True)
        ]

        checker = AnagramChecker()
        for tc in test_cases:
            res = checker.check(tc.s, tc.t)
            self.assertEqual(tc.expected, res, f"[{tc.name}] - expected {tc.expected} but got {res}")


if __name__ == "__main__":
    unittest.main()
```
