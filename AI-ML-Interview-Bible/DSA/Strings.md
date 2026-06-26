# Strings

## What is it?
A string is a sequence of characters. In Python, strings are immutable — once created, they cannot be changed. Any operation that modifies a string creates a new string. Strings are the primary way text data is represented and processed in NLP and ML pipelines.

## Why do we use it?
Text data dominates AI/ML. Sentiment analysis, chatbots, document classification, machine translation — all work with strings. Feature extraction (TF-IDF, Bag-of-Words, word embeddings) starts with string preprocessing: cleaning, tokenizing, lowercasing, removing stopwords, stemming.

## Real-Time Example 1
**Chatbot intent classification:** User message "What's the weather in London?" needs parsing. Palindrome checks aren't relevant, but substring matching finds "weather" keyword. Anagram detection normalizes typos. Pattern matching extracts location entity ("London") using regex. The cleaned string is vectorized for the ML classifier.

## Real-Time Example 2
**Automated essay grading system:** Student essays must be checked for plagiarism. The system uses substring search to find copied passages, anagram detection to catch paraphrased text (same letters rearranged), and palindrome checking for pattern analysis. String preprocessing removes punctuation before feeding to an NLP scoring model.

## Concept Example 1
```python
# Palindrome check (ignoring non-alphanumeric characters)
def is_palindrome(s):
    l, r = 0, len(s) - 1
    while l < r:
        while l < r and not s[l].isalnum():
            l += 1
        while l < r and not s[r].isalnum():
            r -= 1
        if s[l].lower() != s[r].lower():
            return False
        l, r = l + 1, r - 1
    return True

print(is_palindrome("A man, a plan, a canal: Panama"))  # Output: True
```
**Input:** "A man, a plan, a canal: Panama"
**Output:** True
**Explanation:** Two pointers skip non-alphanumeric chars, compare case-insensitively from both ends.

## Concept Example 2
```python
# Anagram check using character count
def is_anagram(s, t):
    if len(s) != len(t):
        return False
    count = [0] * 26
    for c in s:
        count[ord(c) - ord('a')] += 1
    for c in t:
        count[ord(c) - ord('a')] -= 1
        if count[ord(c) - ord('a')] < 0:
            return False
    return True

print(is_anagram("listen", "silent"))  # Output: True
```
**Input:** "listen", "silent"
**Output:** True
**Explanation:** Both strings have same character frequency. Use array count of size 26 for lowercase letters. O(n) time, O(1) space.

## Common Mistakes
- Forgetting strings are immutable (concatenation in loop creates O(n^2) time)
- Not handling empty strings or single characters
- Case-sensitivity issues (not using .lower() when needed)
- Using brute-force O(n^2) for substring search instead of KMP or built-in find()

## How Interviewers Ask This
- "Check if two strings are anagrams"
- "Find the longest palindromic substring"
- "Implement string to integer (atoi)"
- "Group anagrams from a list of strings"

## How To Impress The Interviewer
- Use character frequency arrays (size 26/128/256) instead of hash maps for ASCII
- Know sliding window with hash map for substring problems
- Mention KMP and Rabin-Karp for pattern matching (rolling hash)
- For AI/ML: connect to text preprocessing pipelines, tokenization, regex for feature extraction
- Know that building strings with ''.join(list) is O(n), but string + string creates new object

## Most Asked Questions

**1. Valid Anagram**  
*Q:* Given two strings, check if one is an anagram of the other.  
*A:*
```python
def is_anagram(s, t):
    if len(s) != len(t):
        return False
    return sorted(s) == sorted(t)  # O(n log n)
# OR using Counter
from collections import Counter
def is_anagram(s, t):
    return Counter(s) == Counter(t)  # O(n)
```

**2. Valid Palindrome**  
*Q:* Check if string is palindrome considering only alphanumeric chars.  
*A:*
```python
def is_palindrome(s):
    s = [c.lower() for c in s if c.isalnum()]
    return s == s[::-1]
```

**3. Longest Palindromic Substring**  
*Q:* Find longest palindrome substring in O(n^2).  
*A:*
```python
def longest_palindrome(s):
    res = ""
    for i in range(len(s)):
        # odd length
        l = r = i
        while l >= 0 and r < len(s) and s[l] == s[r]:
            if r - l + 1 > len(res):
                res = s[l:r+1]
            l -= 1; r += 1
        # even length
        l, r = i, i + 1
        while l >= 0 and r < len(s) and s[l] == s[r]:
            if r - l + 1 > len(res):
                res = s[l:r+1]
            l -= 1; r += 1
    return res
```

**4. Longest Substring Without Repeating Characters**  
*Q:* Find length of longest substring without repeating characters.  
*A:*
```python
def length_of_longest_substring(s):
    used = {}
    start = max_len = 0
    for i, c in enumerate(s):
        if c in used and used[c] >= start:
            start = used[c] + 1
        else:
            max_len = max(max_len, i - start + 1)
        used[c] = i
    return max_len
```

**5. String to Integer (atoi)**  
*Q:* Convert string to 32-bit signed integer.  
*A:*
```python
def my_atoi(s):
    s = s.strip()
    if not s:
        return 0
    sign = -1 if s[0] == '-' else 1
    if s[0] in '+-':
        s = s[1:]
    res = 0
    for c in s:
        if not c.isdigit():
            break
        res = res * 10 + int(c)
        if sign * res >= 2**31 - 1:
            return 2**31 - 1
        if sign * res <= -2**31:
            return -2**31
    return sign * res
```

**6. Group Anagrams**  
*Q:* Group anagrams from list of strings.  
*A:*
```python
from collections import defaultdict
def group_anagrams(strs):
    groups = defaultdict(list)
    for s in strs:
        key = ''.join(sorted(s))
        groups[key].append(s)
    return list(groups.values())
```

**7. First Unique Character**  
*Q:* Find first non-repeating character index, return -1 if none.  
*A:*
```python
def first_uniq_char(s):
    count = [0] * 26
    for c in s:
        count[ord(c) - ord('a')] += 1
    for i, c in enumerate(s):
        if count[ord(c) - ord('a')] == 1:
            return i
    return -1
```

**8. Implement strStr()**  
*Q:* Return index of first occurrence of needle in haystack.  
*A:*
```python
def str_str(haystack, needle):
    if not needle:
        return 0
    for i in range(len(haystack) - len(needle) + 1):
        if haystack[i:i+len(needle)] == needle:
            return i
    return -1
```

**9. Reverse Words in String**  
*Q:* Reverse order of words (preserving spaces).  
*A:*
```python
def reverse_words(s):
    return ' '.join(reversed(s.split()))
```

**10. Count and Say**  
*Q:* Generate nth term of count-and-say sequence.  
*A:*
```python
def count_and_say(n):
    res = "1"
    for _ in range(n - 1):
        cur, i = "", 0
        while i < len(res):
            count = 1
            while i + 1 < len(res) and res[i] == res[i+1]:
                count += 1
                i += 1
            cur += str(count) + res[i]
            i += 1
        res = cur
    return res
```
