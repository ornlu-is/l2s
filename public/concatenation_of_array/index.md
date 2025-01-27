# Leetcode 7: Concatenation of array


**Problem statement**: given an integer array `nums` of length `n`, you want to create an array `ans` of length `2n` where `ans[i] == nums[i]` and `ans[i+n] == nums[i]` for `0 <= i < n (0-indexed)`. Specifically, `ans` is the concatenation of two `nums` array. Return the array `ans`.

I am sure that this exercise is challenging in some programming languages. However, python is not one of them, since we can simply use `+` to concatenate two arrays.

```python
import unittest
from dataclasses import dataclass

class Concatenator():
    def run(self, nums):
        return nums + nums

class TestCase(unittest.TestCase):
    def test_run(self):
        @dataclass
        class TestCase:
            name: str
            nums: list
            expected: list

        test_cases = [
            TestCase(name="works as expected", nums=[1, 2, 3], expected=[1, 2, 3, 1, 2, 3])
        ]

        concatenator = Concatenator()
        for tc in test_cases:
            res = concatenator.run(tc.nums)
            self.assertEqual(tc.expected, res, f"[{tc.name}] - expected {tc.expected}, but got {res}")

if __name__ == "__main__":
    unittest.main()
```
