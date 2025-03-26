# Anagram(đảo chữ)

racecar ⇒ carrace

We can convert from char array to string by

string a = new string(char[] array);

## Solution 1

Turn 2 string into char array, then sort both of the array, then convert to char array to string

if they match, then it’s is anagram!

## Solution 2

Using HashMap to count for the appearance of each word

1. Create a Dictionary
2. Iterate through each char in the string:
    
    If the Dict does not contain that char, which means it’s the first time it appears, mark it value as 1
    
    if the Dict contains, then +1 the value of that key
    
3. On the second iterate, iterate through each char in 2nd string, if that char is contained in the Dict, -1 the value of that key, else return false
4. On the 3rd iterate, check every value of the Dict, using foreach(int a in Dict.Values){}, if any value ≠ 0, return false

  

## Solution 3 (My solution, could be bad)

Add to all the char of the string into a list

check foreach char in the second string, if the list contains, remove it, if it doesnt contains, return false

in the end, if the list is empty (count == 0) return true

  

  

# Two sum

Problem: Find 2 indices (plural of index)( in đi xịs) of 2 numbers in an array that the their sum is = to the target

## **Solution 1 (Brute force)(Not efficient)**

Do 2 for loops, check the sum of every pair of number

  

## Solution 2 (One pass)(better)

Here, we use Key: value, Value: Index, to prevent duplicate value with different index, we just need the value of the first appearance

Just need to iterate 1 time through number array

every iterate, find the **left (còn lại)** variable (target - num[i]), find if the dictionary contains that key(key here is the value of number in array, not index)

if it contains the left value, return the index of i and nums[i]

else, ==**assign (dict[nums[i]] = i)**== the hashmap at nums[i] with index i (==**do not use dict.Add(nums[i], i**==), because it could add more value with same key, for example : [1,1,1,1,1,0,1,4,6,2], this will throw an exception)

eventually, we will find 2 indices that their sum is = to the target

  

# Roman to Integer

Roman numerals are represented by seven different symbols: `I`, `V`, `X`, `L`, `C`, `D` and `M`.

```Plain
SymbolValue
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
```

For example, `2` is written as `II` in Roman numeral, just two ones added together. `12` is written as `XII`, which is simply `X + II`. The number `27` is written as `XXVII`, which is `XX + V + II`.

Roman numerals are usually written largest to smallest from left to right. However, the numeral for four is not `IIII`. Instead, the number four is written as `IV`. Because the one is before the five we subtract it making four. The same principle applies to the number nine, which is written as `IX`. There are six instances where subtraction is used:

- `I` can be placed before `V` (5) and `X` (10) to make 4 and 9.
- `X` can be placed before `L` (50) and `C` (100) to make 40 and 90.
- `C` can be placed before `D` (500) and `M` (1000) to make 400 and 900.

Given a roman numeral, convert it to an integer.

**Example 1:**

```Plain
Input: s = "III"
Output: 3
Explanation: III = 3.
```

**Example 2:**

```Plain
Input: s = "LVIII"
Output: 58
Explanation: L = 50, V= 5, III = 3.
```

**Example 3:**

```Plain
Input: s = "MCMXCIV"
Output: 1994
Explanation: M = 1000, CM = 900, XC = 90 and IV = 4.
```

**Constraints:**

- `1 <= s.length <= 15`
- `s` contains only the characters `('I', 'V', 'X', 'L', 'C', 'D', 'M')`.
- It is **guaranteed** that `s` is a valid roman numeral in the range `[1, 3999]`.

  

First, as we read the problem, we can tell that we will use a Dictionary to store the Romans and their values.

## My solution

Create a list that contains all the number of valid minus (all possible special cases, such as IV, CM,…)

Iterate the list from end to start

every iterate, check for the romans[s[i]] - romans[s[i - 1]];, if the valid minus contains this value, then add to the result

```C#
public class Solution {
    public int RomanToInt(string s) {
        Dictionary<char, int> romans = new Dictionary<char, int>(){
            ['I'] = 1,
            ['V'] = 5,
            ['X'] = 10,
            ['L'] = 50,
            ['C'] = 100,
            ['D'] = 500,
            ['M'] = 1000
        };
        int result = 0;
        List<int> validMinus = new List<int>(){
            4, 9, 40, 90, 400, 900
        };
        for(int i = s.Length - 1; i >= 0; i--){
            int minus = 0;
            if(i > 0){
                minus = romans[s[i]] - romans[s[i - 1]];
            }
            else{
                result += romans[s[i]];
                return result;
            }
            if(validMinus.Contains(minus)){
                result += minus;
                --i;
            }
            else{
                result += romans[s[i]];
            }
        }

        return result;
    }
}
```

  

  

## Group Anagrams (Medium)

  

# **Kids With the Greatest Number of Candies**

There are `n` kids with candies. You are given an integer array `candies`, where each `candies[i]` represents the number of candies the `ith` kid has, and an integer `extraCandies`, denoting the number of extra candies that you have.

Return _a boolean array_ `result` _of length_ `n`_, where_ `result[i]` _is_ `true` _if, after giving the_ `ith` _kid all the_ `extraCandies`_, they will have the **greatest** number of candies among all the kids, or_ `false` _otherwise_.

Note that **multiple** kids can have the **greatest** number of candies.

**Example 1:**

```Plain
Input: candies = [2,3,5,1,3], extraCandies = 3
Output: [true,true,true,false,true]
Explanation: If you give all extraCandies to:
- Kid 1, they will have 2 + 3 = 5 candies, which is the greatest among the kids.
- Kid 2, they will have 3 + 3 = 6 candies, which is the greatest among the kids.
- Kid 3, they will have 5 + 3 = 8 candies, which is the greatest among the kids.
- Kid 4, they will have 1 + 3 = 4 candies, which is not the greatest among the kids.
- Kid 5, they will have 3 + 3 = 6 candies, which is the greatest among the kids.
```

**Example 2:**

```Plain
Input: candies = [4,2,1,1,2], extraCandies = 1
Output: [true,false,false,false,false]
Explanation: There is only 1 extra candy.
Kid 1 will always have the greatest number of candies, even if a different kid is given the extra candy.
```

**Example 3:**

```Plain
Input: candies = [12,1,12], extraCandies = 10
Output: [true,false,true]
```

  

## Idea

First, find the max value in the array

If after adding extra candies to number of candies at a[i] and it’s ≥ the max value, then it will be the largest after adding extra candies.

For example:  
  
`[2,3,5,1,3]`

Extra candies = 3

We can see 5 is the max value in the array. If after adding the extra candies to 2 (2+3 = 5), we can tell that 5 is ≥ max, which means, ==**if you are larger than the max value, you are larger than everyone else**==

  

```C#
public class Solution {
    public IList<bool> KidsWithCandies(int[] candies, int extraCandies) {
		    //c# automatically assign all value to false by default
        bool[] result = new bool[candies.Length];
        int max = 0;
        //find max
        foreach(int candy in candies){
            if(candy > max){
                max = candy;
            }
        }

        for(int i = 0; i < candies.Length; i++){
            if(candies[i] + extraCandies >= max){
                result[i] = true;
            }
        }
        return result;
    }
}
```

  

# Is Subsequence(dãy con)

Given two strings `s` and `t`, return `true` _if_ `s` _is a **subsequence** of_ `t`_, or_ `false` _otherwise_.

A **subsequence** of a string is a new string that is formed from the original string by deleting some (can be none) of the characters without disturbing the relative positions of the remaining characters. (i.e., `"ace"` is a subsequence of `"``a``b``c``d``e``"` while `"aec"` is not).

**Example 1:**

```Plain
Input: s = "abc", t = "ahbgdc"
Output: true
```

**Example 2:**

```Plain
Input: s = "axc", t = "ahbgdc"
Output: false
```

**Constraints:**

- `0 <= s.length <= 100`
- `0 <= t.length <= 104`
- `s` and `t` consist only of lowercase English letters.

  

## Idea

By using two pointer, i and j, we will increase i everytime it matches t[j]. If after the loop and i is still not == to the s.Length → False

![[/Untitled 139.png|Untitled 139.png]]

```C#
public class Solution {
    public bool IsSubsequence(string s, string t) {
        int i = 0;
        int j = 0;
        while(i < s.Length && j < t.Length){
            if(s[i] == t[j]){
                i++;
            }
            j++;
        }
        return i == s.Length;
    }
}
```

  

  

# **Longest Common Prefix**

Today i discover a really good technique, which is used to solve this problem

Write a function to find the longest common prefix string amongst an array of strings.

If there is no common prefix, return an empty string `""`.

**Example 1:**

```Plain
Input: strs = ["flower","flow","flight"]
Output: "fl"
```

**Example 2:**

```Plain
Input: strs = ["dog","racecar","car"]
Output: ""
Explanation: There is no common prefix among the input strings.
```

  

In order to solve this problem, we will check ==**simultaneously**== each string in the string array at each char.

```C#
public class Solution {
    public string LongestCommonPrefix(string[] strs) {
        string res = "";
        for(int i = 0; i < strs[0].Length; i++){
            foreach(string s in strs){
                if(i == s.Length || s[i] != strs[0][i]){
                    return res;
                }
            }
            res += strs[0][i];
        }
        return res;
    }
}
```

Here, we will use the first string (strs[0], or we can use any string), since if a string doesnt match, the other strings won’t match anyway.

The first for loop: this loop is used so we can have an integer (which is i) to go through each char in ==**a string**== (note here is a string, the loop will be magical since it allows us to check all the same position (i) of the the string in the array)

The second foreach: Here is where the magic happens. We have an integer i, which is the position we are checking. The foreach loop and the integer i combined help us to check the ==i==th char of every string.

if(i is equal to some string length, which could be shorter than any string, then we will return the result. Or if the char at position i of a string doesn’t match the string we choose to compare to all the other string, return false;

Then after we check all the position i char of every string without false, we will add that char to the result

Time complexity: O(n.m)

  

# PlusOne O(N)

You are given a **large integer** represented as an integer array `digits`, where each `digits[i]` is the `ith` digit of the integer. The digits are ordered from most significant to least significant in left-to-right order. The large integer does not contain any leading `0`'s.

Increment the large integer by one and return _the resulting array of digits_.

**Example 1:**

```Plain
Input: digits = [1,2,3]
Output: [1,2,4]
Explanation: The array represents the integer 123.
Incrementing by one gives 123 + 1 = 124.
Thus, the result should be [1,2,4].
```

**Example 2:**

```Plain
Input: digits = [4,3,2,1]
Output: [4,3,2,2]
Explanation: The array represents the integer 4321.
Incrementing by one gives 4321 + 1 = 4322.
Thus, the result should be [4,3,2,2].
```

**Example 3:**

```Plain
Input: digits = [9]
Output: [1,0]
Explanation: The array represents the integer 9.
Incrementing by one gives 9 + 1 = 10.
Thus, the result should be [1,0].
```

**Constraints:**

- `1 <= digits.length <= 100`
- `0 <= digits[i] <= 9`
- `digits` does not contain any leading `0`'s.

  

Cách giải bài này:

Đầu tiên ta nhận ra edge case cho bài này là những con số 9, tức là + 1 lên thì số đằng trước nó cũng sẽ + 1 và số hiện tại sẽ bằng 0.

Thứ 2 ta nhận ra được là khi + 1 mà không phải là số 9 thì lặp tức trả về kết quả.

Nếu traverse hết array mà ko có con số nào bé hơn 9 (tức là toàn 9) thì ta sẽ trả về array mới với thêm 1 đơn vị ở đầu tiên là số 1 (VD: 999 + 1 = 1000,…)

```C#
public class Solution {
    public int[] PlusOne(int[] digits) {
        int length = digits.Length;

        for (int i = length - 1; i >= 0; i--){
            if(digits[i] < 9){
                digits[i]++;
                return digits;
            }
            digits[i] = 0;
        }

        int[] result = new int[length + 1];
        result[0] = 1;

        return result;
    }
}
```

  

```Docker
 int[] result = new int[length + 1];
 result[0] = 1;
```

Ở đây ta tạo một array int, tức là ==**mặc định các dữ liệu tại result[i] đều là 0**==

  

  

# Summary Range

You are given a **sorted unique** integer array `nums`.

A **range** `[a,b]` is the set of all integers from `a` to `b` (inclusive).

Return _the **smallest sorted** list of ranges that **cover all the numbers in the array exactly**_. That is, each element of `nums` is covered by exactly one of the ranges, and there is no integer `x` such that `x` is in one of the ranges but not in `nums`.

Each range `[a,b]` in the list should be output as:

- `"a->b"` if `a != b`
- `"a"` if `a == b`

**Example 1:**

```Plain
Input: nums = [0,1,2,4,5,7]
Output: ["0->2","4->5","7"]
Explanation: The ranges are:
[0,2] --> "0->2"
[4,5] --> "4->5"
[7,7] --> "7"
```

**Example 2:**

```Plain
Input: nums = [0,2,3,4,6,8,9]
Output: ["0","2->4","6","8->9"]
Explanation: The ranges are:
[0,0] --> "0"
[2,4] --> "2->4"
[6,6] --> "6"
[8,9] --> "8->9"
```

**Constraints:**

- `0 <= nums.length <= 20`
- `231 <= nums[i] <= 231 - 1`
- All the values of `nums` are **unique**.
- `nums` is sorted in ascending order.

  

## Giải

Ta dùng kỹ thuật 2 vòng lặp để có thể di chuyển “i” theo ý muốn

VD:

[0,2,3,4,6,8,9]

Ở vị trí đầu tiên, nums[i] = 0

Ta muốn check xem vị trí tiếp theo (i + 1) có bằng nums[i] + 1 hay ko

→ Nếu bằng, ta tiếp tục di chuyển i sang bên phải

nếu ko:

Ta check xem start có bằng nums[i] ko, nếu bằng nhau có nghĩa i ko hề thay đổi vị trí, ta add vào results

Nếu start ko bằng nums[i], có nghĩa i đã di chuyển sang phải, ta add string vào result

  

```C#
public IList<string> SummaryRanges(int[] nums) {
        List<string> results = new();
        for(int i = 0; i < nums.Length; i++){
            int start = nums[i];
            while(i < nums.Length - 1 && nums[i] + 1 == nums[i+1]){
                i++;
            }
            if(start != nums[i]){
                results.Add($"{start}->{nums[i]}");
            }
            else{
                results.Add($"{start}");
            }
        }
        return results;
    }
```

→ Kỹ thuật rút ra từ problem: 2 vòng lặp for while để di chuyển i theo ý muốn với điều kiện của while là điều kiện để i di chuyển sang phải

  

# **[238. Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/) (Medium)**

Given an integer array `nums`, return _an array_ `answer` _such that_ `answer[i]` _is equal to the product of all the elements of_ `nums` _except_ `nums[i]`.

The product of any prefix or suffix of `nums` is **guaranteed** to fit in a **32-bit** integer.

You must write an algorithm that runs in `O(n)` time and without using the division operation.

**Example 1:**

```Plain
Input: nums = [1,2,3,4]
Output: [24,12,8,6]
```

**Example 2:**

```Plain
Input: nums = [-1,1,0,-3,3]
Output: [0,0,9,0,0]
```

**Constraints:**

- `2 <= nums.length <= 105`
- `30 <= nums[i] <= 30`
- The product of any prefix or suffix of `nums` is **guaranteed** to fit in a **32-bit** integer.

**Follow up:** Can you solve the problem in `O(1)` extra space complexity? (The output array **does not** count as extra space for space complexity analysis.)

  

By using brute-force (2 for loops overlapped) it would be O(n^2) and not efficient.

  

## Giải

![[/image 154.png|image 154.png]]

Ta để ý rằng

Kết quả ở vị trí i sẽ bằng hai bên left và right nhân lại với nhau

→ Ta sẽ tạo 2 array left và right chứa các số là tích của tất cả số từ bên trái hoặc phải qua

  

![[/image 1 15.png|image 1 15.png]]

Lý do có số 2 con số 1 ở đầu và đuôi là để áp dụng được method left * right

VD: Ở nums[0] = 1, ta có result[1] = left * right = 1 * (2 * 3 * 4)

tương tự nums[3] = 4, ta có result[3] = left * right = 1 * 2 * 3 * (1)

Nếu ko có 2 số 1 ở đầu và cuối → Sai

Vì:

Các số ở trong array left và right sẽ ko đúng

- Nếu không có 1 ở đầu và cuối, left[3] sẽ = 24 thay vì 6, và tương tự right[3] sẽ = 4 thay vì 1 và các số sau cũng sẽ thay đổi

```C#
public class Solution {
    public int[] ProductExceptSelf(int[] nums) {
        int[] left = new int[nums.Length];
        int[] right = new int[nums.Length];
        int left_mul = 1;
        int right_mul = 1;

        for(int i = 0; i < nums.Length; i++){
            int j = nums.Length - 1 - i;
            left[i] = left_mul;
            right[j] = right_mul;
            left_mul *= nums[i];
            right_mul *= nums[j];
        }        

        for(int i = 0; i < nums.Length; i++){
            nums[i] = left[i] * right[i];
        }

        return nums;

    }
}
```

  

  

# 605. Can place flower

You have a long flowerbed in which some of the plots are planted, and some are not. However, flowers cannot be planted in **adjacent** plots.

Given an integer array `flowerbed` containing `0`'s and `1`'s, where `0` means empty and `1` means not empty, and an integer `n`, return `true` _if_ `n` _new flowers can be planted in the_ `flowerbed` _without violating the no-adjacent-flowers rule and_ `false` _otherwise_.

**Example 1:**

```Plain
Input: flowerbed = [1,0,0,0,1], n = 1
Output: true
```

**Example 2:**

```Plain
Input: flowerbed = [1,0,0,0,1], n = 2
Output: false
```

**Constraints:**

- `1 <= flowerbed.length <= 2 * 104`
- `flowerbed[i]` is `0` or `1`.
- There are no two adjacent flowers in `flowerbed`.
- `0 <= n <= flowerbed.length`

  

## Idea

The approach is to check if their left and right plot is empty

If the current plot is at edge (first or last), only check for the next plot to avoid IndexOutOfBound

  

## Solution

```C#
public class Solution {
    public bool CanPlaceFlowers(int[] flowerbed, int n) {
        int len = flowerbed.Length;
        for(int i = 0; i < len; i++){
            bool left = i == 0 || flowerbed[i-1] == 0;
            bool right = i == len - 1 || flowerbed[i+1] == 0;

            if(left && right && flowerbed[i] == 0){
                flowerbed[i] = 1;
                n--;
            }
        }
        return n <= 0;
    }
}
```

The special about this solution is two left and right bool

```C#
bool left = i == 0 || flowerbed[i-1] == 0;
bool right = i == len - 1 || flowerbed[i+1] == 0;
```

- i == 0 and i == len act as a guard clause, prevent IndexOutOfBound, since ==**the || operator will imediately yield true if the first condition is true, it**== ==**doesn’t check the latter conditions**==
- We also gotta ==**mark the plantable plot as one**== so the next iteration know that their previous plot is planted
- We ==**return n ≤ 0 instead of n == 0**== because if we have 1 plant but plenty plots, we can still plan, but the n will be < 0 (negative number)