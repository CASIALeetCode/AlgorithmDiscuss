#Week 3 Problems(string)


##Problem1: [Integer Inquiry](http://poj.org/problem?id=1503)
![Interger_Inquiry](/Week3-string/Images/Integer_Inquiry.png)
###Description

One of the first users of BIT's new supercomputer was Chip Diller. He extended his exploration of powers of 3 to go from 0 to 333 and he explored taking various sums of those numbers. 
\`\`This supercomputer is great,'' remarked Chip. \`\`I only wish Timothy were here to see these results.'' (Chip moved to a new apartment, once one became available on the third floor of the Lemon Sky apartments on Third Street.) 

###Input

The input will consist of at most 100 lines of text, each of which contains a single VeryLongInteger. Each VeryLongInteger will be 100 or fewer characters in length, and will only contain digits (no VeryLongInteger will be negative). 

The final input line will contain a single zero on a line by itself. 
Output

Your program should output the sum of the VeryLongIntegers given in the input.

###Sample Input
	123456789012345678901234567890
	123456789012345678901234567890
	123456789012345678901234567890
	0

###Sample Output

	370370367037037036703703703670

##Problem2: [Oulipo](http://poj.org/problem?id=3461)
![Oulipo](/Week3-string/Images/Oulipo.png)

###Description

The French author Georges Perec (1936–1982) once wrote a book, La disparition, without the letter 'e'. He was a member of the Oulipo group. A quote from the book:

Tout avait Pair normal, mais tout s’affirmait faux. Tout avait Fair normal, d’abord, puis surgissait l’inhumain, l’affolant. Il aurait voulu savoir où s’articulait l’association qui l’unissait au roman : stir son tapis, assaillant à tout instant son imagination, l’intuition d’un tabou, la vision d’un mal obscur, d’un quoi vacant, d’un non-dit : la vision, l’avision d’un oubli commandant tout, où s’abolissait la raison : tout avait l’air normal mais…

Perec would probably have scored high (or rather, low) in the following contest. People are asked to write a perhaps even meaningful text on some subject with as few occurrences of a given “word” as possible. Our task is to provide the jury with a program that counts these occurrences, in order to obtain a ranking of the competitors. These competitors often write very long texts with nonsense meaning; a sequence of 500,000 consecutive 'T's is not unusual. And they never use spaces.

So we want to quickly find out how often a word, i.e., a given string, occurs in a text. More formally: given the alphabet {'A', 'B', 'C', …, 'Z'} and two finite strings over that alphabet, a word W and a text T, count the number of occurrences of W in T. All the consecutive characters of W must exactly match consecutive characters of T. Occurrences may overlap.

###Input

The first line of the input file contains a single number: the number of test cases to follow. Each test case has the following format:

* One line with the word W, a string over {'A', 'B', 'C', …, 'Z'}, with 1 ≤ |W| ≤ 10,000 (here |W| denotes the length of the string W).
* One line with the text T, a string over {'A', 'B', 'C', …, 'Z'}, with |W| ≤ |T| ≤ 1,000,000.
Output

For every test case in the input file, the output should contain a single number, on a single line: the number of occurrences of the word W in the text T.

###Sample Input

```
3
BAPC
BAPC
AZA
AZAZAZA
VERDI
AVERDXIVYERDIAN
```

###Sample Output
```
1
3
0
```

##Problem3: [Travel Games](http://poj.org/problem?id=2138)
![Travel Games](/Week3-string/Images/Travel_Games.png)
###Description

The cows are taking a trip to the lakes in Minnesota. Like everyone else, they are bored and are playing "travel games" to pass the time away. 

In this travel game, the first cow thinks of a three letter word from the Sacred Travel Game Dictionary (STGD). The next cow in line must add a letter to the word (at the beginning, between two letters, or at the end) to make another word in the STGD. The cows are curious as to just how big the final word can be. 

Given a dictionary of D (1 <= D <= 1000) words and a starting word, find any of the longest possible words that can be formed playing this travel game. 
Input

* Line 1: The integer D followed by a space followed by a legal three letter word. 

* Line 2 through D+1: Each line contains a legal word no longer than 80 characters, consisting only of lowercase letters, from the STGD. 
Output

A single line with the longest word that can be formed by extending the input seed. 
The input ensure the correct result will be unique.

###Sample Input
```
9 cal
cal
calf
calfs
call
calls
choral
chorale
coal
coral
```

###Sample Output
```
chorale
```

##Program4: [Palindrome](http://poj.org/problem?id=1159)
![Palindrome](/Week3-string/Images/Palindrome.png)
###Description

A palindrome is a symmetrical string, that is, a string read identically from left to right as well as from right to left. You are to write a program which, given a string, determines the minimal number of characters to be inserted into the string in order to obtain a palindrome. 

As an example, by inserting 2 characters, the string "Ab3bd" can be transformed into a palindrome ("dAb3bAd" or "Adb3bdA"). However, inserting fewer than 2 characters does not produce a palindrome. 
Input

Your program is to read from standard input. The first line contains one integer: the length of the input string N, 3 <= N <= 5000. The second line contains one string with length N. The string is formed from uppercase letters from 'A' to 'Z', lowercase letters from 'a' to 'z' and digits from '0' to '9'. Uppercase and lowercase letters are to be considered distinct.
Output

Your program is to write to standard output. The first line contains one integer, which is the desired minimal number.
###Sample Input
```
5
Ab3bd
```
###Sample Output
```
2
```