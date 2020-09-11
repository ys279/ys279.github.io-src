Title: Yiping's Blog2
Date: 2020-09-10 20:55
headline: Solutions to Eluer Project 




##  1.Solve fewer than 25,000 people:Problem 80
### It is well known that if the square root of a natural number is not an integer, then it is irrational. The decimal expansion of such square roots is infinite without any repeating pattern at all.

### The square root of two is 1.41421356237309504880..., and the digital sum of the first one hundred decimal digits is 475.

### For the first one hundred natural numbers, find the total of the digital sums of the first one hundred decimal digits for all the irrational square roots.

The idea of sovling this is to write a loop to see if the number in the loop(range 1-100) is irrational, if it is,  then get the  digital sums of irrational square roots and sum them up.  


First write a function to find the total of the digital sums of the first n decimal digits for all the irrational square roots, define the function as "dsum(n)"

Next specific the range of the loop is 
```
for i in range(1, n+1):
```
where n =100. 

Then, to determine if the number is irrational number and get the digits of square roots, we know that "decimal" library deal with decimal problem and Context function give the the decimal with percision. We know that the square root of a rational number is a integer thus its length can not be bigger than 2(for limit 100), so when we use 
```
[:-2]
```
to get rid of the rational numbers and use 

```
str(Decimal(i).sqrt(Context(prec=102)))
```
to get the digit of the squaer root, use
```
.replace('.', '')
```
to get rid of the radix point  
next step sum all the digits
```
digit = int(digit)
sums = sums + digit
```
And then run function dsum(100) can get the answer.  





## 2.Solve fewer than 100,000 people: Problem 36
### The decimal number, 585 = 1001001001 (binary), is palindromic in both bases.

### Find the sum of all numbers, less than one million, which are palindromic in base 10 and base 2.

### (Please note that the palindromic number, in either base, may not include leading zeros.)

The idea of this solution if to write a loop to see if the number in the loop(range 1000000) is if it is,  palindromic in both bases 10 and 2. If it is, then sum them up.  

First write a function to find the sum of all numbers, in range(n), which are palindromic in base 10 and base 2.define the function as "palindromic_sum(n)" 

Next specific the range of the loop is 
```
for i in range(n):
```
Where n = 1000000  

see if it is palindromic in both base10 use:
```
str(i) == str(i)[::-1]
```
see if it is palindromic in both base2 use:
```
str(bin(i))[2:] == (str(bin(i))[2:])[::-1]
```
since "bin"return the  number of base2 from number base10 and also get rid of the first digit that "bin" produce which is useless for this problem.  
And then sum all these palindromic number
```
x = x+i
    return(x)
```

And then run function palindromic_sum(10**6) to get the answer.  




## 3.Solve fewer than 500,000 people:Problem 4
### A palindromic number reads the same both ways. The largest palindrome made from the product of two 2-digit numbers is 9009 = 91 × 99. Find the largest palindrome made from the product of two 3-digit numbers.

The idea of this solution if to write two loops , each loop means one 3-digit number and mutiple them to see if the product reads the same both ways. If it is ,add into a list and after finish loop, find the max of this list to get the answer.  

First write a function to find largest palindrome made from the product of two n-digit numbers.define the function as "large_palindromic(x,y)" use x and y to define the upper bound and lower bound:

Next specific the range of the loop is 
```
for i in range(x, y, -1):
        for j in range(x, y, -1):
```
Where x = 999,y = 99  
Then get the product and determine if it reads the same both ways use:
```
n = i*j
            if str(i*j) == str(i*j)[::-1]:
                res.append(n)
```

After get the list of palindromic number use:
```
max(res)
```
to get the largest one.  

And then run function large_palindromic(999,99) can get the answer.  