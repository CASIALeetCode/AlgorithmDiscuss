##Array

###1. [Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/)

	Given an array of n integers where n > 1, nums, return an array output such that output[i] is equal to the product of all the elements of nums except nums[i].

	Solve it without division and in O(n).

	For example, given [1,2,3,4], return [24,12,8,6].

	Follow up:
	Could you solve it with constant space complexity? (Note: The output array does not count as extra space for the purpose of space complexity analysis.)

###2. [Missing Number](https://leetcode.com/problems/missing-number/)

	Given an array containing n distinct numbers taken from 0, 1, 2, ..., n, find the one that is missing from the array.

	For example,
	Given nums = [0, 1, 3] return 2.

	Note:
	Your algorithm should run in linear runtime complexity. Could you implement it using only constant extra space complexity?

##Math

###1. [Number of Digit One](https://leetcode.com/problems/number-of-digit-one/)

	Given an integer n, count the total number of digit 1 appearing in all non-negative integers less than or equal to n.

	For example:
	Given n = 13,
	Return 6, because digit 1 occurred in the following numbers: 1, 10, 11, 12, 13.

##List

###1.[Remove Duplicates from Sorted List II](https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/)

	Given a sorted linked list, delete all nodes that have duplicate numbers, leaving only distinct numbers from the original list.

	For example,
	Given 1->2->3->3->4->4->5, return 1->2->5.
	Given 1->1->1->2->3, return 2->3.

##string

###1. [Integer to English Words](https://leetcode.com/problems/integer-to-english-words/)

	Convert a non-negative integer to its english words representation. Given input is guaranteed to be less than 231 - 1.

	For example,
	123 -> "One Hundred Twenty Three"
	12345 -> "Twelve Thousand Three Hundred Forty Five"
	1234567 -> "One Million Two Hundred Thirty Four Thousand Five Hundred Sixty Seven"

	Hint:

	Did you see a pattern in dividing the number into chunk of words? For example, 123 and 123000.
	Group the number by thousands (3 digits). You can write a helper function that takes a number less than 1000 and convert just that chunk to words.
	There are many edge cases. What are some good test cases? Does your code work with input such as 0? Or 1000010? (middle chunk is zero and should not be printed out)

##Binary Search

###1. [Sqrt(x)](https://leetcode.com/problems/sqrtx/)

	Implement int sqrt(int x).

	Compute and return the square root of x.

##Tree

###1. [Count Complete Tree Nodes](https://leetcode.com/problems/count-complete-tree-nodes/)

	Given a complete binary tree, count the number of nodes.

##Graph

###1.[gCampus](https://code.google.com/codejam/contest/4284486/dashboard#s=p2)

	Company G has a main campus with N offices (numbered from 0 to N - 1) and M bidirectional roads (numbered from 0 to M - 1). The ith road connects a pair of offices (Ui, Vi), and it takes Ci minutes to travel on it (in either direction).
	
	A path between two offices X and Y is a series of one or more roads that starts at X and ends at Y. The time taken to travel a path is the sum of the times needed to travel each of the roads that make up the path. (It's guaranteed that there is at least one path connecting any two offices.)
	
	Company G specializes in efficient transport solutions, but the CEO has just realized that, embarrassingly enough, its own road network may be suboptimal! She wants to know which roads in the campus are inefficient. A road is inefficient if and only if it is not included in any shortest paths between any offices.
	
	Given the graph of offices and roads, can you help the CEO find all of the inefficient roads?
	
	Input
	
	The first line of the input gives the number of test cases, T. T test cases follow. Each case begins with one line with two integers N and M, indicating the number of offices and roads. This is followed by M lines containing three integers each: Ui, Vi and Ci, indicating the ith road is between office Ui and office Vi, and it takes Ci minutes to travel on it.
	
	Output
	
	For each test case, output one line containing "Case #x:", where x is the test case number (starting from 1). Then output the road numbers of all of the inefficient roads, in increasing order, each on its own line. (Note that road 0 refers to the first road listed in a test case, road 1 refers to the second road, etc.)
	
	Limits
	
	0 < Ci ≤ 1000000.
	
	Small dataset
	
	1 ≤ T ≤ 10.
	1 ≤ N = M ≤ 100.
	Large dataset
	
	1 ≤ T ≤ 3.
	1 ≤ N ≤ 100.
	1 ≤ M ≤ 10000.
	Sample
	
	
	Input 
	 	
	Output 
	 
	2
	3 3
	0 1 10
	1 2 3
	2 0 3
	3 3
	0 1 10
	1 2 3
	2 1 3
	Case #1:
	0
	Case #2:
