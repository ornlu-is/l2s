---
title: "Leetcode 2: Check permutation"
date: 2025-01-26T12:17:34Z
Description: ""
Categories: ["Leetcode"]
Author: "Lmbf"
draft: true
toc:
  enable: false
---

The first problem I did was pretty simple so lets see if the second one is a bit more interesting.

**Problem statement**: given two strings, write a method to decide if one is a permutation of the other.

The first thing to understand is what would classify the strings as being a permutation of one another. Sure we could generate all permutations and check them all, but that would be horribly inefficient and not particularly easier to code. For a string to be a permutation of another they both must have the same characters and they must show up in equal number. This means that all we have to do is get the frequency counts of each string and check if these match. 

For that purpose we can use a dictionary. However, since this is such a common thing, Python already provides a class designed for such situations, the `Counter`. We use it to get the frequency counts of the first string, then we use its method `subtract` to subtract the frequency counts of the second string. Now, if there are any new keys after subtracting, then the strings had different characters and are not palindromes. If the total sum on frequency counts isn't zero, then it means the strings have the same characters but they show up in different numbers. 

Putting this together into code and throwing some unit tests in there, we get the following script.

```python
import unittest
from collections import Counter
from dataclasses import dataclass


class PermutationChecker():
    def check(self, string_1: str, string_2: str) -> bool:
        character_counts = Counter(string_1)
        pre_num_chars = len(character_counts)

        character_counts.subtract(string_2)
        post_num_chars = len(character_counts)

        has_same_characters = pre_num_chars == post_num_chars
        has_same_character_count = character_counts.total() == 0

        return True if (has_same_characters and has_same_character_count) else False


class TestPermutationChecker(unittest.TestCase):
    def test_check(self):
        @dataclass
        class TestCase:
            name: str
            str1: str
            str2: str
            expected: bool

        test_cases = [TestCase(name="permutation with just letters", str1="abcd", str2="bacd", expected=True),
                      TestCase(name="permutation with just numbers", str1="3563476", str2="7334566", expected=True),
                      TestCase(name="permutation with alphanumeric", str1="wef34f", str2="wffe34", expected=True),
                      TestCase(name="not a permutation because of different length", str1="abcd", str2="d2cba", expected=False),
                      TestCase(name="not a permutation because of different characters", str1="2354", str2="1234", expected=False)]
        
        checker = PermutationChecker()
        for tc in test_cases:
            res = checker.check(tc.str1, tc.str2)
            self.assertEqual(tc.expected, res, f"[{tc.name}] - expected {tc.expected} but got {res}")


if __name__ == '__main__':
    unittest.main()
```