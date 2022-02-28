---
permalink: /2019/05/i-recently-spent-about-6-hours-trying.html
tags: [qa, testing, memory allocation]
---
# Check the boundaries of your function output

I recently spent about 6 hours trying to figure out why my answer to the [Count Triplets problem on Hacker Rank](https://www.hackerrank.com/challenges/count-triplets-1/problem) was failing 4/12 of their test cases.  
  
After failing to create a test case that could fail my solution, I decided to spend five Hackos to reveal one of the failing test cases.  
  
It turned out that my return type wasn't large enough to accommodate the correct answer. I should have known based on the problem statement that the output would exceed what int32 can hold since I was calculating combinations of values where the count of values was almost touching the boundary of int32. I also ignored a clue given to me by the skeleton code Hacker Rank provided.  
  
The skeleton code they provide for the solution in C# uses the long variable type everywhere instead of int, even though all the input value are constrained within int numeric space; all the input constraints were below 10^9 (1 billion) and int supports roughly up to 2x10^9 (2 billion). I changed all of the instances of long to int because I thought that would save considerable memory allocation space and some execution time. I however should have noticed that the output could be much larger than int due to what is being calculated.  
  
The problem was basically find all sequential sets of three values within an array that could have up to 100,000 (10^5) values in it. That means an upper limit on the answer is roughly: 10 ^ 5 ^ 3 = 10 ^ 15. An int can only hold 2x10^9, but a long can hold 2x10^18. So a long is the proper data type to use.  
  
All I had to do to get my solution to pass the remaining four test cases was to change the type of any variable holding the return value or an intermediate value to long.  
  
What I learned from this issue is that I need to make sure that I've definitely figured out the output bounds for a function I'm testing based on the most extreme situation possible for the code. I've known this for many years as it applies to input values, but I forgot to apply it to the output.  
  
Also, my reasoning behind "optimizing" the code was wrong. An int is 32 bits on both 64 and 32 bit processors, but the 64-bit processor performs operations using 64 bits of precision. So using an int rather than a long saves memory but not execution time.