# Leetcode 8: Contains duplicate


**Problem statement**: given an integer array `nums`, return `true` if any value appears more than once in the array, otherwise return `false`.

We have several ways we can solve this. We can use a standard Python dictionary, where we iteratively store the integers of the array as keys associated with any value and, in case we find a key that already exists, we can adequately flag the array as containing duplicates. We can also use a `Counter`, give it he full array and compare the total count with the number of keys in the `Counter`: if these differ, then there are duplicates. Similarly, we can create a set from the array and, in case the set has less values than the array, it contains duplicates. Finally, we can sort the array and iterate over it, checking the current number against the one that follows it and, in case these match, then there is a duplicate. This last option is the least efficient of the bunch since it requires sorting the array.

```python
import unittest
from collections import Counter
from dataclasses import dataclass

class DuplicateChecker():
    def check_with_dict(self, nums):
        d = dict()
        for num in nums:
            if num in d:
                return True
            d[num] = True

        return False

    def check_with_counter(self, nums):
        c = Counter(nums)
        return True if c.total() != len(c) else False

    def check_with_set(self, nums):
        return True if len(set(nums)) != len(nums) else False
    
    def check_with_sort(self, nums):
        nums = sorted(nums)
        for i in range(len(nums)-1):
            if nums[i] == nums[i+1]:
                return True
        return False

class TestDuplicateChecker(unittest.TestCase):
    def test_check(self):
        @dataclass
        class TestCase:
            name: str
            input: list
            expected: bool

        test_cases = [
            TestCase(name="empty list has no dups", input=[], expected=False),
            TestCase(name="single item list has no dups", input=[1], expected=False),
            TestCase(name="list has no dups", input=[5, 3, 8, 9], expected=False),
            TestCase(name="list has dups", input=[5, 3, 8, 9, 0, 9], expected=True)
        ]

        checker = DuplicateChecker()
        for tc in test_cases:
            res = checker.check_with_counter(tc.input)
            self.assertEqual(res, tc.expected, f"[{tc.name}] - with_counter, expected {tc.expected}, but got {res}")

            res = checker.check_with_dict(tc.input)
            self.assertEqual(res, tc.expected, f"[{tc.name}] - with_dict, expected {tc.expected}, but got {res}")

            res = checker.check_with_set(tc.input)
            self.assertEqual(res, tc.expected, f"[{tc.name}] - with_set, expected {tc.expected}, but got {res}")

            res = checker.check_with_sort(tc.input)
            self.assertEqual(res, tc.expected, f"[{tc.name}] - with_sort, expected {tc.expected}, but got {res}")


if __name__ == "__main__":
    unittest.main()
```
