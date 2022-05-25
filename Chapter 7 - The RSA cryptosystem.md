# The RSA cryptosystem

While symmetric cryptography is usually fairly intuitive for most people, this is typically not the case with asymmetric cryptography. Though you are likely comfortable with the high-level description offered in the previous sections, you are probably wondering what precisely one-way functions are and how exactly they are used to construct asymmetric schemes. 

In this chapter, I will remove some of the mystery surrounding asymmetric cryptography, by looking more deeply into a specific example, namely the RSA cryptosystem. In the first section, I will introduce the factorization problem on which the RSA problem is based. In will, then, cover a number of key results from number theory. In the last section, we will put this information together to explain the RSA problem, and how this can be used for creating asymmetric cryptographic schemes. 

Adding this depth to our discussion is not an easy task. It requires introducing quite a few number-theoretic theorems and propositions. But don’t let the mathematics dissuade you. Working through this discussion will significantly improve your comprehension of what underlies asymmetric cryptography and is a worthwhile investment.  

Lets now first turn to the factoring problem. 


## The factoring problem

Whenever you multiply two numbers, say a and b, we refer to the numbers a and b as **factors**, and the result as the **product**. Attempting to write a number N into the multiplication of two or more factors is called **factorization** or **factoring**.<sup>[1](#footnote1)</sup> You can call any problem that requires this a **factorization problem**.

Around 2,500 years ago, the Greek mathematician Euclid of Alexandria discovered a key theorem about the factorization of integers. It is commonly called the **unique factorization theorem** and states the following: 

*Theorem 1*. Every integer N which is greater than 1 is either a prime number, or can be expressed as a product of prime factors. 

All the latter part of this statement means is that you can take any non-prime integer N greater than 1, and write it out as a multiplication of prime numbers. Below are several examples of non-prime integers written as the product of prime factors.  

* 18 = 2 • 3 • 3 = 2 • 3<sup>2</sup>
* 84 = 2 • 2 • 3 • 7 = 2<sup>2</sup> • 3 • 7
* 144 = 2 • 2 • 2 • 2 • 3 • 3 = 2<sup>4</sup> • 3<sup>2</sup>

For all three of the integers above, calculating their prime factors is relatively easy, even if you are only given N. You start with the smallest prime number, namely 2, and see how many times the integer N is divisible by it. You, then, move on to testing the divisibility of N by 3, 5, 7, and so forth. You continue this process until your integer N is written as the product of only prime numbers. 

Take, for instance, the integer 84. Below you can see the process for determining its prime factors. At each step we take out the smallest remaining prime factor (on the left) and determine the remainder term to be factored. We continue until the remainder term is also a prime number. At each step, the current factorization of 84 is displayed on the far right. 

* Prime factor = 2: remainder term = 42 	(84 = 2 • 42)
* Prime factor = 2: remainder term = 21 	(84 = 2 • 2 • 21)
* Prime factor = 3: remainder term = 7 		(84 = 2 • 2 • 3 • 7)
* As 7 is a prime number, the result is 2 • 2 • 3 • 7, or 2<sup>2</sup> • 3 • 7. 

Suppose now that N is very large. How difficult would it be to reduce N into its prime factors?

That really depends on N. Suppose, for instance, that N is 50,450,400. Though this number looks intimidating, the calculations are not that complicated and can easily be done by hand. As above, you just start with 2 and work your way onwards. Below, you can see the result of this process in a similar manner as above.    

* 2: 25,225,200 	(50,450,400 = 2 • 25,225,200)  
* 2: 12,612,600 	(50,450,400 = 2<sup>2</sup> • 12,612,600)  
* 2: 6,306,300 		(50,450,400 = 2<sup>3</sup> • 6,306,300)  
* 2: 3,153,150 		(50,450,400 = 2<sup>4</sup> • 3,153,150)  
* 2: 1,576,575 		(50,450,400 = 2<sup>5</sup> • 1,576,575)  
* 3: 525,525 		(50,450,400 = 2<sup>5</sup> • 3 • 525,525)
* 3: 175,175 		(50,450,400 = 2<sup>5</sup> • 3<sup>2</sup> • 175,175)
* 5: 35,035 		(50,450,400 = 2<sup>5</sup> • 3<sup>2</sup> • 5 • 35,035)
* 5: 7,007		    (50,450,400 = 2<sup>5</sup> • 3<sup>2</sup> • 5<sup>2</sup> • 7,007)
* 7: 1,001		    (50,450,400 = 2<sup>5</sup> • 3<sup>2</sup> • 5<sup>2</sup> • 7 • 1,001)
* 7: 143			(50,450,400 = 2<sup>5</sup> • 3<sup>2</sup> • 5<sup>2</sup> • 7<sup>2</sup> • 143)
* 11: 13			(50,450,400 = 2<sup>5</sup> • 3<sup>2</sup> • 5<sup>2</sup> • 7<sup>2</sup> • 11 • 13)
* As 13 is a prime number, the result is 2<sup>5</sup> • 3<sup>2</sup> • 5<sup>2</sup> • 7<sup>2</sup> • 11 • 13.

Working this problem out by hand takes some time. A computer, of course, could do all of this in a small fraction of a second. In fact, a computer can frequently even factorize extremely large integers in a fraction of a second. 

There are, however, certain exceptions. Suppose that we first randomly select two very large prime numbers. It is typical to label these p and q, and I will adopt that convention here. 

For concreteness, let’s say that p and q are both 1024-bit primes, and that they indeed require at least 1024 bits in order to be represented (so the first bit must be 1). So, for example, 37 could not be one of the prime numbers. You can certainly represent 37 with 1024 bits. But clearly *you do not need* this many bits to represent it. You can represent 37 by any string that has 6 bits or more. (In 6 bits, 37 would be represented as 100101). 

It is important to appreciate how large p and q are if selected under the conditions above. As an example, I have selected a random prime number that needs at least 1024 bits for representation below.

* 14,752,173,874,503,595,484,930,006,383,670,759,559,764,562,721,397,166,747,289,220,945,457,932,666,751,048,198,854,920,097,085,690,793,755,254,946,188,163,753,506,778,089,706,699,671,722,089,715,624,760,049,594,106,189,662,669,156,149,028,900,805,928,183,585,427,782,974,951,355,515,394,807,209,836,870,484,558,332,897,443,152,653,214,483,870,992,618,171,825,921,582,253,023,974,514,209,142,520,026,807,636,589

Suppose now after randomly selecting prime numbers p and q, we multiply them to obtain an integer N. This latter integer, therefore, is a 2048 bit number which requires at least 2048 bits for its representation. It is much, much larger than either p or q.   

Suppose you only give a computer N, and ask it to find the two 1024 bit prime factors of N. The probability that the computer discovers p and q is extremely small. You can say it is impossible for all practical purposes. This is so, even if you were to employ a supercomputer or even a network of supercomputers.

To start, suppose that the computer attempts to solve the problem by cycling through 1024 bit numbers, testing in each case whether they are prime and if they are factors of N. The set of prime numbers to be tested is then approximately 1.265 • 10<sup>305</sup>.<sup>[2](#footnote2)</sup>

Even if you take all the computers on the planet and have them attempt to find and test 1024-bit prime numbers in this way, a 1 in a billion chance of successfully finding a prime factor of N would require a period of calculation much longer than the age of the Universe.    

Now in practice a computer can do better than the rough procedure just described. There exist several algorithms that the computer can apply to come to a factorization more quickly. The point, however, is that even using these more efficient algorithms, the computer’s task is still computationally infeasible.<sup>[3](#footnote3)</sup>

Importantly, the difficulty of the factorization under the conditions just described rests on the assumption that there are no computationally efficient algorithms for calculating prime factors. We cannot actually prove that an efficient algorithm does not exist. Nevertheless, this assumption is very plausible: despite extensive efforts spanning hundreds of years, we have yet to find such a computationally efficient algorithm. 

Hence, the factorization problem, under certain circumstances, can plausibly be assumed to be a hard problem. Specifically, when p and q are very large prime numbers, their product N is not difficult to calculate; but factorization only given N is practically impossible. 


## Number theoretic results

Unfortunately, the factoring problem cannot be used directly for asymmetric cryptographic schemes. However, we can use a more complex but related problem to this effect: the RSA problem. 

To understand the RSA problem, we will need to understand a number of theorems and propositions from number theory. These are presented in this section in three subsections: (1) the order of N, (2) invertibility modulo N, and (3) Euler’s theorem.

Some of the material in the three subsections has already been introduced in *Chapter 3*. But I will here restate that material for convenience.   


### The order of N

An integer a is **coprime** or a **relative prime** with an integer N, if the greatest common divisor between them is 1. While 1 is by convention not a prime number, it is a coprime of every integer (as is – 1). 

For instance, consider the case when a = 18 and N = 37. These are clearly coprimes. The greatest integer which divides into both 18 and 37 is 1. By contrast, consider the case when a = 42 and N = 16. These are clearly not coprimes. Both numbers are divisible by 2, which is greater than 1.  

We can now define the order of N as follows. Suppose that N is an integer greater than 1. The **order of N** is, then, the number of all coprimes with N such that for each coprime a, the following condition holds: 1 ≤ a < N. 

For instance, if N = 12, then 1, 5, 7, and 11 are the only coprimes that meet the requirement above. Hence, the order of 12 is equal to 4. 

Suppose that N is a prime number. Then any integer smaller than N but greater or equal to 1 is coprime with it. This includes all the elements in the following set: {1,2,3….,N – 1}. Hence, when N is prime, the order of N is N – 1. This is stated in proposition 1, where φ(N) denotes the order of N. 

**Proposition 1**. φ(N) = N – 1 when N is prime

Suppose that N is not prime. You can, then, calculate its order using **Euler’s Phi function**. While calculating the order of a small integer is relatively straightforward, Euler’s Phi function becomes particularly important for larger integers. The proposition of Euler’s Phi function is stated below.

*Theorem 2*. Let N equal p<sub>1</sub><sup>e_1</sup> • p<sub>2</sub><sup>e_2</sup> • … • p<sub>i</sub><sup>e_i</sup> • … • p<sub>n</sub><sup>e_n</sup>, where the set {p<sub>i</sub>} consists of all the distinct prime factors of N and each e_i indicates how many times the prime factor p<sub>i</sub> occurs for N. Then, φ(N) = p<sub>1</sub><sup>e_1 - 1</sup> • (p<sub>1</sub> - 1) • p<sub>2</sub><sup>e_2 - 1</sup> • (p<sub>2</sub> - 1) • … • p<sub>n</sub><sup>e_n - 1</sup> • (p<sub>n</sub> - 1).

*Theorem 2* shows that once you have broken down any non-prime N into its distinct prime factors, it is easy to calculate the order of N. 

For instance, suppose that N = 270. This is clearly not a prime number. Breaking down N into its prime factors yields the expression: 2 • 3<sup>3</sup> • 5. According to Euler’s Phi function, the order of N is then as follows:

* φ(N) = 2<sup>1 – 1</sup> (2 – 1) + 3<sup>3 – 1</sup> (3 – 1) + 5<sup>1 – 1</sup> (5 – 1) = 1 (1) + 9 (2) + 1 (4) = 1 + 18 + 4 = 23

Suppose next that N is a product of two primes, p and q. *Theorem 2* above, then, states that the order of N is as follows: p<sup>1 – 1</sup> (p – 1) x q<sup>1 – 1</sup> (q – 1) = (p – 1) x (q – 1). This is a key result for the RSA problem particularly, and is stated in *Proposition 2* below. 

*Proposition 2*. If N is the product of two primes, p and q, the order of N is the product (p – 1) x (q – 1). 

To illustrate, suppose that N = 119. This integer can be factored into two primes, namely 7 and 17. Hence, Euler’s Phi function suggests that the order of 119 is as follows: 

* φ(119) = (7 – 1) • (17 – 1) = 6 • 16 = 96.  

In other words, the integer 119 has 96 coprimes in the range from 1 until 119. In fact, this set includes all integers from 1 until 119, which are not multiples of either 7 or 17. 

From here on, lets denote the set of coprimes that determines the order of N as **C<sub>N</sub>**. For our example where N = 119, the set **C<sub>119</sub>** is far too large to list completely. But some of the elements are as follows: **C<sub>119</sub>** = {1,2,….6,8….13,15,16,18,….,33,35….,96}.


### Invertibility modulo N

We can say that an integer a is **invertible modulo N**, if there exists at least one integer b such that a x b modulo N = 1 modulo N. Any such integer b is referred to as an **inverse** (or **multiplicative inverse**) of a given reduction by modulo N. 

Suppose, for example, that a = 5 and N = 11. There are many integers by which you can multiply 5, so that 5 x b mod 11 = 1 mod 11. Consider, for instance, the integers 20 and 31. It is easy to see that both these integers are inverses of 5 for reduction modulo 11. 

* 5 x 20 mod 11 = 100 mod 11 = 1 mod 11
* 5 x 31 mod 11 = 155 mod 11 = 1 mod 11

While 5 has many inverses reduction modulo 11, you can show that only a single positive inverse of 5 exists which is less than 11. In fact, this is not unique to our particular example, but a general result. 

*Proposition 3*. If the integer a is invertible modulo N, it must be the case that exactly one positive inverse of a is less than N. (So, this unique inverse of a must come from the set {1,…,N – 1}).

Lets denote the unique inverse of a from Proposition 3 as a<sup>-1</sup>. For the case when a = 5 and N = 11, you can see that a<sup>-1</sup> = 9, given that 5 x 9 mod 11 = 45 mod 11 = 1 mod 11. 

Notice that you can also obtain the value 9 for a<sup>-1</sup> in our example by simply reducing any other inverse of a by modulo 11. For instance, 20 mod 11 = 31 mod 11 = 9 mod 11. So whenever an integer a > N is invertible modulo N, then a mod N must also be invertible modulo N. 

It is not necessarily the case that an inverse of a exists reduction modulo N. Suppose, for example, that a = 2 and N = 8. There is no b, or any a<sup>-1</sup> specifically, such that 2 x b mod 8 = 1 mod 8. This is because any value of b will always produce a multiple of 2, so no division by 8 can ever yield a remainder that equals 1.

How exactly do we know if some integer a has an inverse for a given N? As you may have noticed in the example above, the greatest common divisor between 2 and 8 is higher than 1, namely 2. And this is actually illustrative of the following general result:

*Proposition 4*. An inverse exists of an integer a given reduction modulo N, and specifically a unique positive inverse less than N, if and only if the greatest common divisor between a and N is 1 (that is, if they are coprimes). 

For the case when a = 5 and N = 11, we concluded that a<sup>-1</sup> = 9, given that 5 x 9 mod 11 = 45 mod 11 = 1 mod 11. It is important to note that the reverse is also true. That is, when a = 9 and N = 11, it is the case that a<sup>-1</sup> = 5. 


### Euler's theorem

Before moving onto the RSA problem, we need to understand one more crucial theorem, namely **Euler’s theorem**. It states the following:

*Theorem 3*. Suppose two integers a and N are coprimes. Then, a<sup>φ(N)</sup> mod N = 1 mod N. 

This is a remarkable result, but a little confusing at first. Lets turn to an example to understand it.

Suppose that a = 5 and N = 7. These are indeed coprimes as Euler’s theorem requires. We know that the order of 7 equals 6, given that 7 is a prime number (see **Proposition 1**). 

What Euler’s theorem now states is that 5<sup>6</sup> mod 7 must be equal to 1 mod 7. Below are the calculations to show that this is indeed true.

* 5<sup>6</sup> mod 7 = 15,625 mod 7 = 1 mod N 

The integer 7 divides into 15,624 a total of 2,233 times. Hence, the remainder of dividing 16,625 by 7 is 1. 

Next, using Euler’s Phi function, *Theorem 2*, you can derive *Proposition 5* below. 

*Proposition 5*. φ(a • b) = φ(a) • φ(b) for any positive integers a and b.

We will not show why this is the case. But merely note that you have already seen evidence of this proposition by the fact that φ(p • q) = φ(p) • φ(q) = (p – 1) • (q – 1) when p and q are primes, as stated in *Proposition 2*.

Euler’s theorem in conjunction with *Proposition 5* has important implications. See what happens, for instance, in the expressions below, where a and N are coprimes. 

* a<sup>2 • φ(N)</sup> mod N = a<sup>φ(N)</sup> • a<sup>φ(N)</sup> mod N = 1 • 1 mod N = 1 mod N
* a<sup>φ(N) + 1</sup> mod N = a<sup>φ(N)</sup> • a<sup>1</sup> mod N = 1 • a<sup>1</sup> mod N = a mod N
* a<sup>8 • φ(N) + 3</sup> mod N = a<sup>8 • φ(N)</sup> • a<sup>3</sup>  mod N = 1 • a<sup>3</sup>  mod N = a<sup>3</sup>  mod N

Hence, the combination of Euler’s theorem and *Proposition 5* allow us to simply calculate a number of expressions. In general, we can summarize the insight as in *Proposition 6*.

*Proposition 6*. a<sup>x</sup> mod N = a<sup>x mod φ(N)</sup>

Now we have to put everything together in a tricky last step. 

Just as N has an order φ(N) which includes the elements of the set **C<sub>N</sub>**, we know that the integer φ(N) must in turn also have an order and a set of coprimes. Lets set φ(N) = R. Then we know that there is also a value for φ(R) and a set of coprimes **C<sub>R</sub>**.

Suppose that we now select an integer e from the set **C<sub>R</sub>**. We know from *Proposition 3* that this integer e only has one unique positive inverse less than R. That is, e has one unique inverse from the set **C<sub>R</sub>**. Lets call this inverse d. Given the definition of an inverse, this means that e • d = 1 mod R.

We can use this result to make a statement about our original integer N. This is summarized in *Proposition 7*.

*Proposition 7*. Suppose that e • d mod φ(N) = 1 mod φ(N). Then for any element a of the set **C<sub>N</sub>** it must be the case that a<sup>e • d mod φ(N)</sup> = a<sup>1 mod φ(N)</sup> = a mod N. 

We know have all the number theoretic results needed to state the RSA problem clearly.


## The RSA cryptosystem

We are now ready to state the RSA problem. Suppose you create a set of variables consisting of p, q, N, φ(N), e, d, and y. Call this set Π. It is created as follows: 

1. Generate two random primes p and q of equal size and calculate their product N. 
2. Calculate the order of N, φ(N), by the following product: (p – 1) • (q – 1). 
3. Select an e > 2 such that it is smaller and coprime to φ(N). 
4. Calculate d by setting e • d mod φ(N) = 1. 
5. Select a random value y that is smaller and coprime to N.

The RSA problem consists of finding an x such that x<sup>e</sup> = y, while only being given a subset of information regarding Π, namely the variables N, e, and y. When p and q are very large, typically it is recommended that they are 1024 bits in size, the RSA problem is thought to be hard. You can see now why this is the case given the foregoing discussion. 

An easy way to calculate x when x<sup>e</sup> mod N = y mod N is simply by calculating y<sup>d</sup> mod N. We know y<sup>d</sup> mod N = x mod N by the following calculations:

* y<sup>d</sup> mod N = x<sup>e • d</sup> mod N = x<sup>e • d mod φ(N)</sup> mod N = x<sup>1 mod φ(N)</sup> mod N = x mod N. 

The problem is that we do not know the value d, as it is not given in the problem. Hence, we cannot directly calculate y<sup>d</sup> mod N to produce x mod N. 

We might be able, however, to indirectly calculate d from the order of N, φ(n), as we know that e • d mod φ(N) = 1 mod φ(N). But by assumption the problem does not give a value for φ(N) either. 

Finally, the order could be calculated indirectly with the prime factors p and q, so that we can eventually calculate d. But by assumption, the values p and q were also not provided to us. 

Strictly speaking, even if the factoring problem within an RSA problem is hard, we cannot prove that the RSA problem is also hard. There may namely be other ways to solve the RSA problem than through factoring. However, it is generally accepted and assumed that if the factoring problem within the RSA problem is hard, that the RSA problem itself is also hard.

If the RSA problem is indeed hard, then it produces a one-way function with a trap door. The function here is f(g) = g<sup>e</sup> mod N. With knowledge of f(g), anyone could easily calculate a value y for a particular g = x. However, it is practically impossible to calculate a particular value x just from knowing the value y and the function f(g). The exception is when you are given a piece of information d, the trapdoor. In that case, you can simply calculate y<sup>d</sup> to give x. 

Lets walk through a specific example to illustrate the RSA problem. I cannot select an RSA problem that would be considered hard under the conditions above, as the numbers would be unwieldy. Instead, this example is just to illustrate how the RSA problem generally works. 

To start, suppose you select two random prime numbers 13 and 31. So p = 13 and q = 31. The product N of these two primes equals 403. We can easily calculate the order of 403. It is equivalent to (13 – 1) • (31 – 1) = 360. 

Next, as dictated by step 3 of the RSA problem, we need to select a coprime for 360 that is greater than 2 and less than 360. We do not have to select this value randomly. Suppose that we select 103. This is a coprime of 360 as its greatest common divisor with 103 is 1. 

Step 4 now requires that we calculate a value d such that 103 • d mod 360 = 1. This is not an easy task by hand when the value for N is large. It requires that we use a procedure called the **extended Euclidean algorithm**.

Though I do not show the procedure here, it yields the value 7 when e = 103. You can verify that the pair of values 103 and 7 indeed meets the general condition e • d mod φ(n) = 1 through the calculations below. 

* 103 • 7 mod 360 = 721 mod 360 = 1 mod 360 

Importantly, given *Proposition 4*, we know that no other integer between 1 and 360 for d will produce the result that 103 • d = 1 mod 360. Additionally, the proposition implies that selecting a different value for e, will yield a different unique value for d.

In step 5 of the RSA problem, we have to select some positive integer y which is a smaller coprime of 403. Suppose that we set y = 2<sup>103</sup>. Exponentiation of 2 by 103 yields the result below. 

* 2<sup>103</sup> mod 403 = 10,141,204,801,825,835,211,973,625,643,008 mod 403 = 349 mod 403

The RSA problem in this particular example is now as follows: You are provided with N = 403, e = 103, and y = 349 mod 403. You now have to calculate x such that x<sup>103</sup> = 349 mod 403. That is, you must find that the original value before exponentiation by 103 was 2. 

It would be easy (for a computer at least) to calculate x if we knew that d = 7. In that case, you could just determine x as below.

* x = y<sup>7</sup> mod 403 = 349<sup>7</sup> mod 403 = 630,634,881,591,804,949 mod 403 = 2 mod 403

The problem is that you have not been provided the information that d = 7. You could, of course, calculate d from the fact that 103 • d = 1 mod 360. The problem is that you are also not given the information that the order of N = 360. Finally, you could also calculate the order of 403 by calculating the following product: (p – 1) • (q – 1). But you are also not told that p = 13 and p = 31. 

Of course, a computer could still solve the RSA problem for this example relatively easily, because the prime numbers involved are not large. But when the primes become very large, it faces a practically impossible task.  

We have now presented the RSA problem, a set of conditions under which it is hard, and the underlying mathematics. How does any of this help with asymmetric cryptography? Specifically, how can we turn the hardness of the RSA problem under certain conditions into an encryption scheme or a digital signature scheme? 

One approach is to take the RSA problem and build schemes in a straightforward manner. For instance, suppose that you generated a set of variables Π as described in the RSA problem, and ensure that p and q are sufficiently large. You set your public key equal to (N, e) and share this information with the world. As described above, you keep the values for p, q, φ(n), and d secret. In fact, d is your private key. 

Anyone that wants to send you a message m which is an element of **C<sub>N</sub>** could simply encrypt it as follows: c = m<sup>e</sup> mod N. (The ciphertext c here is equivalent to the value y in the RSA problem.) You can easily decrypt this message by just calculating c<sup>d</sup> mod N. 

You might attempt to create a digital signature scheme in the same manner. Suppose that you want to send someone a message m with a digital signature S. You could just set S = m<sup>d</sup> mod N and send the pair (m,S) to the recipient. Anyone can verify the digital signature just by checking whether S<sup>e</sup> mod N = m mod N. Any attacker, however, would have a really difficult time creating a valid S for a message, given that they do not posses d. 

Unfortunately, turning what is on its own a hard problem, the RSA problem, into a cryptographic scheme is not this straightforward. For the straightforward encryption scheme you can only select coprimes of N as your messages. That does not leave us with many possible messages, certainly not enough for standard communication. In addition, these messages have to be selected randomly. That seems somewhat impractical. Finally, any message that is selected twice will yield the exact same ciphertext. This is extremely undesirable in any encryption scheme, and does not meet any rigorous modern standard notion of security in encryption. 

The problems become even worse for our straightforward digital signature scheme. As it stands, any attacker can easily forge digital signatures just by first selecting a coprime of N as the signature and then calculating the corresponding original message. This clearly breaks the requirement of existential unforgeability. 

Nevertheless, with adding a bit of clever complexity, the RSA problem can be used to create a secure public key encryption scheme as well as a secure digital signature scheme. We will not enter into the details of such constructions here.<sup>[4](#footnote4)</sup> Importantly, however, this additional complexity does not change the fundamental underlying RSA problem on which these schemes are based.


## Notes

<a name="footnote1">1</a>. Factorization can also be important for working with other types of mathematical objects than numbers. For instance, it can be useful to factor polynomial expressions such x<sup>2</sup> – 2x + 1. In our discussion, we will only focus on the factorization of numbers, specifically integers.

<a name="footnote2">2</a>. According to the prime number theorem, the number of primes less than or equal to N is approximately N/ln⁡(N). This means that you can approximate the number of primes of length 1024 bits by 2<sup>1024</sup>/ln⁡(2<sup>1024</sup>) - 2<sup>1023</sup>/ln⁡(2<sup>1023</sup>) which equals approximately 1.265 x 10<sup>305</sup>.

<a name="footnote3">3</a>. The same is true for discrete logarithm problems. Hence, why asymmetric constructions work with much larger keys than symmetric cryptographic constructions. 

<a name="footnote4">4</a>. See, for example, Jonathan Katz and Yehuda Lindell, *Introduction to Modern Cryptography*, CRC Press (Boca Raton, FL: 2015), pp. 410–32 on RSA encryption and pp. 444–41 for RSA digital signatures. 
