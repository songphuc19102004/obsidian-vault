# Palindrome string

**A palindrome is a string that reads the same forward and backward. It is also case-insensitive and ignores all non-alphanumeric characters.**

  

To solve this, we will use 2 pointer way of solving

we will create 2 pointers to check start and end of the string, check it until the pointers’ indices passed or equal

first, at each pointer, check if it’s letter or digit or not, if it’s not(which is special char or space, then we will move the pointer by 1)(left++, right--)

at the else case(which is the case that 2 pointers is at a valid char), it will check if two of them are the same

if not, then it’s not a palindrome string

else move 2 pointers by 1

  

```C#
public class Solution {
    public bool IsPalindrome(string s) {
        int left = 0, right = s.Length - 1;
        while(left <= right){
            if(!char.IsLetterOrDigit(s[left])){
                left++;
            }
            else if(!char.IsLetterOrDigit(s[right])){
                right--;
            }
            else{
                if(char.ToLower(s[left]) != char.ToLower(s[right])){
                    return false;
                }
                left++;
                right--;
            }
            
        }
        return true;
    }
}
```