# Table of Contents
[Isomorphic Strings](#isomorphic-strings)

# Isomorphic Strings
Given two strings s and t, determine if they are isomorphic.

Two strings s and t are isomorphic if the characters in s can be replaced to get t.

All occurrences of a character must be replaced with another character while preserving the order of characters. No two characters may map to the same character, but a character may map to itself.

- Both strings will always be the same length
- Both strings can be one to thousands of characters long

## First Attempt
- Go through string s, assign a unique int code to each character. The code should start from 0 and increment by 1 when a character that has not been seen before is encountered
- Go through string t, do the same thing as we did above with the code starting from 0 as well
- If the string is isomorphic, the sequence of codes should be identical
- For example abbca -> 1,2,2,3,1 and hiiah -> 1,2,2,3,1
- We can iterate through both strings simultaneously. If there is ever an iteration where the int codes are not equal between strings s and t, then break out of the function because we for sure do not have an isomorphic string

```Python
class Solution:
    def isIsomorphic(self, s: str, t: str) -> bool:
        map_s = {} # memory of what int we assigned for a char
        map_t = {} # memory of what int we assigned for a char
        current_s_code = None # 
        current_t_code = None
        code = 0
        
        for i in range(len(s)):
            if s[i] in map_s:
                current_s_code = map_s[s[i]]
            else:
                map_s[s[i]] = code
                current_s_code = code
            
            if t[i] in map_t:
                current_t_code = map_t[t[i]]
            else:
                map_t[t[i]] = code
                current_t_code = code
                
            if current_s_code != current_t_code:
                return False
            else:
                code += 1
        
        return True
```

## Simpler Solution
- If we make a set out of the characters of each isomorphic string, the two sets should be the same length
- For example: title -> {t, i, l, e} and paper -> {p, a, e, r}, both sets are length 4
- It's easonable to think that if len(set(s)) is equal to len(set(t)), s and t are isomorphic 
- There is one edge case however that must be accounted for: "babb" and "baab" are clearly not isomorphic but if they are converted to sets of chars, they will be of the same length since "babb" -> {b, a} and "baab" -> {b, a}
- To account for this, we must also ensure the set of the zip of the two strings is also the same length as the sets of the two strings
- Recall that zipping two strings gives us tuples pairing characters by their indexes. For instance zipping "all" and "odd" gives us (a, o), (l, d), (l, d). Taking the set of this zip removes the duplicate entry (l, d)
- zipping "babb" and "baab" and removing its duplciates gives us {(b, b), (a, a), (b, a)} which has 3 entries, one more than the length of each individual set
- zipping "title" and "paper" gives us {(t, p), (i, a), (l, e), (e, r)} which has 4 entires, which is the same number of entries that set("title") and set("paper") will have

```Python
class Solution:
    def isIsomorphic(self, s: str, t: str) -> bool:
        return len(set(s)) == len(set(t)) == len(set(zip(s,t)))
```

# Is Subsequence
Given two strings s and t, return true if s is a subsequence of t, or false otherwise.

A subsequence of a string is a new string that is formed from the original string by deleting some (can be none) of the characters without disturbing the relative positions of the remaining characters. (i.e., "ace" is a subsequence of "abcde" while "aec" is not).

- s is the proposed substring, t is the original string
- only lowercase letters are used
- s can be up to 100 chars while t can be up to thousands of chars

## First Attempt
- Iterate through each char in s to see if it is in t
- This idea alone however is not good enough to solve the problem because in a case like s="law" and t="wallow", s is not a proper substring of t because 'a' comes before 'l' in "wallow"
- To solve this, after iterating a character in s, we can slice t into a string containing only the characters after the first character we checked from s
- For example:
- Iteration 1: look for 'l' in "wallow", 'l' was found at index 2 so slice t at index 2 -> "low"
- Iteration 2: look for 'a' in "low", 'a' was not found so s is not a substring of t
- Before using this algorithm however we can knock out some edge cases. if the length of s (the substring) is greater than t (the original string) then s is definitely not a substring of t. Also if s and t are identical, then we do not even need to go through the algorithm

```Python
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        prev_char_index: int = -1
        
        if s == t:
            return True
        if len(t) < len(s):
            return False
        
        for char in s:
            if char in t[prev_char_index + 1:]:
                prev_char_index = t.index(char, prev_char_index + 1)
            else:
                return False
        
        return True
            
```




