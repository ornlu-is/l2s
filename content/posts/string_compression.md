---
title: "Leetcode 5: String compression"
date: "2025-01-27T12:51:10Z"
Description: ""
Categories: ["Leetcode"]
Author: "Lmbf"
draft: true
toc:
  enable: false
---

**Problem statement**: Implement a method to perform basic string compression using the counts of repeated characters. For example, the string aabcccccaaa would become a2b1c5a3. If the compressed string would not become smaller than the original string, your method should return the original string. You can assume the string has only uppercase and lowercase letters.

To solve this we need to iterate over each character in the string and, for each character we are currently looking at, we need to check if the previous character is the same while keeping count of how many equal characters we have already seen. Then we just need to verify if our compression is smaller than the given input and we are done.

```python
import unittest
from dataclasses import dataclass

class Compressor():
    def run(self, text: str) -> str:
        compressed = list()
        count = 0

        for i in range(len(text)):
            if i != 0 and text[i] != text[i-1]:
                compressed.append(f"{text[i-1]}{count}")
                count = 0
            count += 1 

        if text != "":
            compressed.append(f"{text[-1]}{count}")

        return min(''.join(compressed), text, key=len)


class TestCompressor(unittest.TestCase):
    def test_run(self):
        @dataclass
        class TestCase:
            name: str
            input: str
            expected: str

        test_cases = [
            TestCase(name="compresses large string", input="aaabbcaaaa", expected="a3b2c1a4"),
            TestCase(name="returns original if original is smaller", input="abcdefg", expected="abcdefg"),
            TestCase(name="works for empty string", input="", expected="")
        ]

        compressor = Compressor()
        for tc in test_cases:
            res = compressor.run(tc.input)
            self.assertEqual(tc.expected, res, f"[{tc.name}] - expected {tc.expected}, but got {res}")


if __name__ == "__main__":
    unittest.main()
```