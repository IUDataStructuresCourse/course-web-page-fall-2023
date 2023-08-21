# Lecture: Course Introduction

>  In the beginning was the Tao. The Tao gave birth to Space and
>  Time. Therefore Space and Time are the Yin and Yang of programming.
>
>  Programmers that do not comprehend the Tao are always running out of
>  time and space for their programs. Programmers that comprehend the
>  Tao have enough time and space to accomplish their goals.
>
>  How could it be otherwise? -- The Tao of Programming

## Introduce myself

## Introduce course AI's

* Tianyu Chen

## Course web page, syllabus, schedule (due dates, etc.)

    https://iudatastructurescourse.github.io/course-web-page-fall-2023/

## Participation:

* Write your name on a "name tent" and place it at the front of your desk

* Slack: ask and answer questions
  if not on slack, send me email

## Suggested weekly schedule (4 credits * 3 = 12 hours of outside-class work)

- Before each lecture, read the assigned chapters.
  Do the textbook exercises as you read the chapters.
  The lecture should feel like a review with a few extra tidbits.
  Humans learn by repetition.
  If you don't read, then you're going to have a hard time
		understanding the lecture.
		
- Labs are every week. They will include a mixture of 
  programming exercises, lecture review, 
  help with homework and projects, and 15 minute quizzes.
  
- Homework (textbook exercises), 6 total for the semester

- Projects, about 1 per month, groups of 2-4 students.
  To complete the projects, you'll typically need a firm understanding
  of the lectures from the previous two weeks and the Homework.

## Overview of course content

* prerequisites:
	* C211 Intro. to CS
	   basic programming skills, strings, lists, dicts.
	* C212 Software Systems: object-oriented programming, Java
	* C241 Discrete Math: the mathematics of programming
* postrequisites:
	* B403: Intro. to Algorithm Design and Analysis
	* B461: Database Concepts
	* B481: Interactive Graphics
	* P434: Distributed Systems
	* P436: Intro. to Operating Systems
	* P465: Software Engineering for Info. Systems I
* efficient procedures for solving problems on large data: 
  U.S. highway system, Human genome, Social network/facebook
* tools of the trade, basic ways to organize information on computers
* real implementations in Java, testing on small to large inputs
* Many job interview questions are drawn from data structures & algorithms
* schedule on course web site, overview of projects (1 every few weeks)

## Software Tools

* IntelliJ: Learn to use an Integrated Development Environment 
  (Text Editor + Debugger)

* Autograder for submitting assignments

* Optional: learn github for managing group work

* ChatGPT/AI: you can use it but beware that if you use it to
  avoid learning, then you are likely to perform poorly on the
  midterm exam and final exam (60% of the course grade).

## Lab 1: Testing Linear Search and Binary Search

## Project 1: Flood It!

* Choose a team

## Appetizer: The Maximum Subsequence Problem

Find the section of the Tecumseh Trail Marathon with the
greatest net change in altitude. Show TecumsehMarathon.pdf.

	change:   -25, -100, +100, -25,-75,-125,+100,+100,-25, +75,   -50, -100
				0     1     2    3   4    5    6    7   8    9   10    11

We want the contiguous subsequence with the largest sum.  This is also
known as the [Maximum Subarray Problem](https://en.wikipedia.org/wiki/Maximum_subarray_problem).

* Brute force: try every pair of locations

	The time it takes, given an input array of size n, is

		n choose 2 = n * (n-1) / 2

	which is roughly n².

* Divide-and-conquer:
	Cut the array in half, then consider three cases:
	1. max-subarray is to the left of the cut
	2. max-subarray is to the right of the cut
	3. max-subarray crosses the cut

	Cases 1&2 are the same problem but smaller -> recursion
	Case 3 needs work:
	* any crossing subarray is made of a left and right part that
	  meet in the middle
	* finding a max-subarray with one end held constant is easy:
	  start at the constant end and keep growing the subarray toward
	  the other end, keeping track of the max sum and index seen so
	  far.

Analysis of the time complexity:

The crossing-subarray is linear, that is, c * m where m is the
size of the subarray.

Recursion tree:

                    c*n                     = c*n
                /         \
           c*n/2          c*n/2             = c*n
          /    \          /    \
        c*n/4  c*n/4   c*n/4   c*n/4        = c*n
        ...

What's the height?

At the bottom, at a leaf, we have n/(2^h) = 1.
We want to solve for h, so we isolate the 2^h term on one side.

    n = 2^h
	  
Then we take the log of both sides. (Recall that log (2^x) = x for any x.)

    log n = h

Total work is c * n * lg n, so the time for this divide-and conquer algorithm
for maximum-subarray is is roughly 

    n lg n
