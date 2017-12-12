---
layout: page
title: "Assignments"
excerpt: "Assignments"
tags: ["assignment"]
context: assign
image:
   feature: assignments.jpg
   credit: Delft University of Technology
   creditlink: http://repository.tudelft.nl/view/MMP/uuid:785a7433-f8b0-40b8-b420-b59d88fc2254
---

In the practical part of the course, you build an IDE for MiniJava, a small subset of Java.
We split this task into three milestones of (roughly) weekly individual assignments:

* [Milestone 1: Syntax Analysis](ms1)
  * [Testing Syntax Analysis](ms1/lab1.html)
  * [Syntax Definition](ms1/lab2.html)
  * [Simple Term Rewriting](ms1/lab3.html)
* [Milestone 2: Semantic Analysis](ms2)
  * [Testing Name Analysis](ms2/lab4.html)
  * [Name Analysis](ms2/lab5.html)
  * [Testing Type Analysis](ms2/lab6.html)
  * [Type Analysis](ms2/lab7.html)
* [Milestone 3: Code Generation](ms3)
  * [Compiling Minimal Programs](ms3/lab8.html)
  * [Compiling Expressions and Statements](ms3/lab9.html)
  * [Compiling, Fields, Parameters, and Variables](ms3/lab10.html)

## Submission

We rely on GitHub for assignment submissions.
We assign a private GitHub repository to each student and encourage students to commit and push their work frequently.
To submit your assignment, you need to file a pull request.

To check your progress on an assignment, you can submit a *preliminary solution*.
We will provide limited early feedback on preliminary solutions.
This feedback typically comes with a tentative grade and points out areas where your solution is incomplete or insufficient, without giving any details on the reasons.
We do *not give any feedback on the day of a deadline*, since this will not be early at all.

We compute your *final grade* based on the last merged PR.
We will provide a detailed grading report on final solutions.
To ensure fairness and equal chances for all students, grading reports will not be published before the 1-day deadline extension has expired.
We do our best to hand out grading reports as quick as possible, but manual grading can be tedious and might result in longer waiting times for some assignments.

Early feedback and most parts of final grading are automated and supported by an AWS in Education Grant award.
Each pull request triggers the launch of an Amazon EC2 instance which runs automated feedback and grading for your solution.
Be aware that feedback might be delayed due to submissions by other students, instance limits, server problems, etc.

## Grades

We grade the first final solution of each of your assignments.
Grades reflect on correctness, clarity, shown programming skills, and readability of your submission.
To pass the practical part of the course, you need to meet all of the following criteria:

1. You completed each assignment with a grade of 4.0 or better.
2. You completed the assignments of each milestone with an average grade of 5.0 or better.
3. You completed all the assignments with an average grade of 6.0 or better.

These rules allow you to compensate for lower grades in single assignments, but ensure a minimal quality in all your milestones as well as in your overall practical work.

## Deadlines 2017-2018

In Q1 and Q2 the lab is on Friday. The deadlines for the assignments (labs) are listed below and are at 23:59 on that date. All assignments except lab 9 have a 24 hour deadline extension with a penalty of 2 points (off the final grade). Lab 9 has an extension until 12/01 with a penalty of 1 point.

These dates are subject to change.
{: .notice .notice-warning}

* Milestone 1: Syntax Analysis
   - 20/09: Lab 1: Testing Syntax Analysis
   - 27/09: Lab 2: Syntax Definition
   - 04/10: Lab 3: Simple Term Rewriting
* Milestone 2: Semantic Analysis
   - 18/10: Lab 4: Testing Name Analysis
   - 27/10: Lab 5: Name Analysis
* Milestone 2: Semantic Analysis (continued)
   - 22/11: Lab 6: Testing Type Analysis
   - 06/12: Lab 7: Type Analysis
* Milestone 3: Code Generation
   - 13/12: Lab 8: Compiling Minimal Programs
   - 22/12: Lab 9: Compiling Expressions and Statements
   - 19/01: Lab 10: Compiling, Fields, Parameters, and Variables

## Academic Misconduct

All actual, detailed work on assignments must be **individual work**.
You are encouraged to discuss assignments, programming languages used to solve the assignments, their libraries, and general solution techniques in these languages with each other.
If you do so, then you must **acknowledge** the people with whom you discussed at the top of your submission.
You should not look for assignment solutions elsewhere; but if material is taken from elsewhere, then you must **acknowledge** its source.
You are not permitted to provide or receive any kind of solutions of assignments.
This includes partial, incomplete, or erroneous solutions.
You are also not permitted to provide or receive programming help from people other than the teaching assistants or instructors of this course.
Any violation of these rules will be reported as a suspected case of fraud to the Board of Examiners and handled according to the EEMCS Faculty's fraud procedure.
If the case is proven, a penalty will be imposed: a minimum of exclusion from the course for the duration of one academic year up to a maximum of a one-year exclusion form all courses at TU Delft.
For details on the procedure, see Section 2.1.26 in the faculty's Study Guide for MSc Programmes.

## Retry

To ensure fairness and equal chances for all students, you are not allowed to take the same practical assignments in another edition of this course.
In case you participated in previous editions of the course, please contact the course manager to discuss an individual assignment.

{% include _toc.html %}
