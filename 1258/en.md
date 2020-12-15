### Problem Title: 1258 - Making Huge Palindromes

**Problem Description:** ``You will be given a non-empty string containing only lowercase English letters. Your task is to make it a palindrome
adding minimum numbers of characters at the right side of the string.``

**Observation:** Suppose the input string length is `N`. We are allowed to add characters only right side of the given string to make the string\
palindrome. We can do it by adding the reversed string at the right side of the input string. But we have to minimize it as much as possible.\
Let’s, observe the following strings:

Input string: kamal\
Reversed string: lamak\
Input string + **reversed string**: kamal**lamak** (it is palindrome but not optimal)\
Optimal palindrome: kama**l**amak\
If we add `"amak"` to the right side then it be a palindrome (4 characters added).

Input string: ahdaa\
Reversed string: aadha\
Input string + **reversed string**: ahdaa**aadha** (it is palindrome but not optimal)\
Optimal palindrome: ahd**aa**dha\
If we add `"dha"` to the right side then it be a palindrome (3 characters added).

Input string: nooroo\
Reversed string: ooroon\
Input string + **reversed string**: nooroo**ooroon** (it is palindrome but not optimal)\
Optimal palindrome: n**ooroo**n\
If we add `"n"` to the right side then it be a palindrome (1 character added).

Input string: madam\
Reversed string: madam\
Input string + **reversed string**: madam**madam** (it is palindrome but not optimal)\
Optimal palindrome: **madam**\
It is already palindrome. We needn’t to add any characters (0 character added).

We have observed that if we need to add `K` characters to the right side of a string to make the string palindrome then `N-K` characters are\
already palindrome which are at the right side of the string. These `N-K` characters are suffix of the input string and prefix of the reversed\
string. We need to erase one (suffix or prefix) to get the optimal answer.

**So, how can we detect it?**

**1. Solution by KMP:** Here, already palindrome characters are a suffix of the belonged string. If we can match it with the prefix of another\
string (reverse of input string) then we are done (the idea of KMP).

We can easily do it by adding both strings. Then the last value of the LPS array will be “already palindrome” part length. We will add both\
strings in this way: **``reversed_string#input_string``** → lamak#kamal  (Here, input string is "kamal")

``N.B. We need to add ‘#’ to separate each string (‘#’ will never be given in the input string).``

**Time Complexity:** O(T*N)\
(Where, N denotes string length and T denotes total number of test cases.)\
**Memory Complexity:** O(N)\
``Both complexities are for creating the LPS array.``

**2. Solution by Hashing:** Reverse the input string and calculate the hash values from first to last. Take a variable to store max palindrome\
length starting from the first position initializing 1. Each time match the forward & reversed hash value. If matched then update the max\
palindrome length value.
**Output:** 

**Time Complexity:** O(T*N)\
Cause, we can calculate both hash values just running a single loop.\
(Where, N denotes string length and T denotes total number of test cases.)\
**Memory Complexity:** O(N)\
``Both complexities are for calculating the hash values.``

**Output:** 2*N - alreday_palindrome_length

________________________________________________________________________________

#### C++ Code using KMP:
```
#include <bits/stdc++.h>
using namespace std;

vector<int> lps;

void createLPS(string pat){
    lps.push_back(0);
    int i = 0, j = 1;
    for ( ; pat[j]; ){
        if (pat[i] == pat[j]){
            lps.push_back(i+1);
            ++i, ++j;
        }
        else{
            if (i != 0) i = lps[i-1];
            else {
                ++j;
                lps.push_back(0);
            }
        }
    }
}

int main(){
    int sz, T; cin>>T;
    string text, pat;
    for (int t = 1; t <= T; t++){
        cin>>text;
        pat = text;
        reverse(pat.begin(), pat.end());
        pat += '#' + text;
        createLPS(pat);
        int sz = text.size();
        int total = 2*sz - lps[lps.size()-1];
        printf("Case %d: %d\n", t, total);

        lps.clear();
    }
}
```

#### C++ Code using Hashing:
```
#include <bits/stdc++.h>
using namespace std;
#define  LL    long long
#define  LIM   1000000
#define  base1 129
#define  base2 137
#define  MOD1  1479386893
#define  MOD2  1928476349

string text;
LL pow1[LIM+5], pow2[LIM+5];

void calPower(){
    pow1[0] = pow2[0] = 1;
    for(int i = 1; i < LIM; i++){
        pow1[i] = (pow1[i-1]*base1)%MOD1;
        pow2[i] = (pow2[i-1]*base2)%MOD2;
    }
}

int getHash(){
    LL hash1 = 0, hash2 = 0, revHash1 = 0, revHash2 = 0;
    int maxPal = 1;
    for(int i = 0; text[i]; i++){
        hash1 = ((hash1*base1)%MOD1 + (text[i]-'a' + 1))%MOD1;
        hash2 = ((hash2*base2)%MOD2 + (text[i] - 'a' + 1))%MOD2;
        revHash1 = (revHash1 + ((text[i]-'a' + 1)*pow1[i])%MOD1)% MOD1;
        revHash2 = (revHash2 + ((text[i]-'a' + 1)*pow2[i])%MOD2)% MOD2;
        if(hash1 == revHash1 && hash2 == revHash2) maxPal = max(maxPal, i+1);
    }
    return maxPal;
}

int main(){
    calPower();
    int T; cin>>T;
    for(int t = 1; t <= T; t++){
        cin>>text;
        reverse(text.begin(), text.end());
        int maxPal = getHash(); //to store max palindrome length from right side
        int matched = maxPal;
        maxPal = 2*text.size() - maxPal;
        printf("Case %d: %d\n", t, maxPal);
    }
}

```


**Contributed by: AH Kamal, HSTU**