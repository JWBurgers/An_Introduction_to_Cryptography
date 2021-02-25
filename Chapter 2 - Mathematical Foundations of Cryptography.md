# Chapter 2: Mathematical Foundations of Cryptography

Cryptography relies on mathematics. And if you want to build more than a superficial understanding of cryptography, you need to be comfortable with that mathematics. 

This chapter introduces most of the mathematics you will encounter in learning cryptography. The relatively straightforward topics are random variables, modulo operations, XOR operations, and pseudorandomness. You should master the material in these sections for any non-superficial understanding of cryptography.  

The last topic of this chapter, number theory, is much more challenging. If you are finding the details cumbersome, I would recommend a high-level reading the first time around. You can always come back to it at a later point. 


## Random variables

A **random variable** can take on two or more possible values, each with a certain positive probability. The possible values are listed in the **outcome set**.

Each time you **sample** a random variable, you draw a particular value from its outcome set according to the defined probabilities. 

Lets turn to a simple example. Suppose a variable X that is defined as follows: 

* X has the outcome set {1,2} 
* Pr [X = 1] = 0.5
* Pr [X = 2] = 0.5

It is easy to see that X is a random variable. First, there are two or more possible values that X can take on, namely 1 and 2. Second, each possible value has a positive probability of occurring whenever you sample X, namely 0.5.

All that a random variable requires is an outcome set with two or more possibilities, where each possibility has a positive probability of occurring upon sampling. In principle, then, a random variable can be defined abstractly, devoid of any context. In this case, you might think of “sampling” as running some natural experiment to determine the value of the random variable. 

The variable X above was defined abstractly. You might, thus, think of sampling the variable X above as flipping a fair coin and assigning “2” in the case of heads and “1” in the case of tails. For each sample of X, you flip the coin again. 

Alternatively, you might also think of sampling X, as rolling a fair die and assigning “2” in case the die lands 1, 3, or 4, and assigning “1” in case the die lands 2, 5, or 6. Each time you sample X, you roll the die again. 

Really, any natural experiment that would allow you to define the probabilities of the possible values of X above can be imagined with respect to the drawing.

Frequently, however, random variables are not just introduced abstractly. The set of possible outcome values can have explicit real-world meaning (rather than just as numbers). In addition, these outcome values might be defined against some specific type of experiment (rather than as any natural experiment with those values). 

Lets now consider an example variable X that is not defined abstractly. X is defined as follows in order to determine which of two teams starts a football game:

* X has the outcome set {red kicks off,blue kicks off}
* Flip a particular coin C: tails = “red kicks off”; heads = “blue kicks off”
* Pr [X = red kicks off] = 0.5
* Pr [X = blue kicks off] = 0.5

In this case, the outcome set of X is provided with a concrete meaning, namely which team starts in a football game. In addition, the possible outcomes and their associated probabilities are determined by a concrete experiment, namely flipping a particular coin C. 

Within discussions of cryptography, random variables are usually introduced against an outcome set with real-world meaning. It might be the set of all messages that could be encrypted, known as the message space, or the set of all keys the parties using the encryption can choose from, known as the key space. 

Random variables in discussions on cryptography are, however, not usually defined against some specific natural experiment, but against any experiment that might yield the right probability distributions. 

Random variables can have discrete or continuous probability distributions. Random variables with a **discrete probability distribution**—that is, discrete random variables—have a finite number of possible outcomes. The random variable X in both examples given so far was discrete. 

**Continuous random variables** can instead take on values in one or more intervals. You might say, for instance, that a random variable, upon sampling, will take on any real value between 0 and 1, and that each real number in this interval is equally likely. Within this interval, there are infinitely possible values. 

For cryptographic discussions, you will only need to understand discrete random variables. Any discussion of random variables from here on out should, therefore, be understood as referring to discrete random variables, unless specifically stated otherwise.
 
A random variable is typically denoted by a non-bold, uppercase letter. So, for instance, we might talk about a random variable X, a random variable Y, or a random variable Z. 


### Graphing random variables

The possible values and associated probabilities for a random variable can be easily visualized through a graph. For instance, consider the random variable X from the previous section with an outcome set of {1,2}, and Pr [X = 1] = 0.5 and Pr [X = 2] = 0.5. We would typically display such a random variable in the form of a bar graph as in *Figure 1*.

*Figure 1: Random variable X*

![Figure 1: Random variable X.](/Images/Figure2-1.png "Figure 1: Random variable X")

The wide bars in *Figure 1* obviously do not mean to suggest that the random variable X is actually continuous. Instead, the bars are made wide in order to be more visually appealing (just a line straight up provides a less intuitive visualization).  


### Uniform variables

In the expression “random variable,” the term “random” just means “probabilistic”. In other words, it just means that two or more possible outcomes of the variable occur with certain probabilities. These outcomes, however, do *not necessarily* have to be equally likely (though the term “random” can indeed have that meaning in other contexts). 

A **uniform variable** is a special case of a random variable. It can take on two or more values all with an equal probability. The random variable X depicted in *Figure 1* is clearly a uniform variable, as both possible outcomes occur with a probability 0.5. There are, however, many random variables that are not instances of uniform variables. 

Consider, for example, the random variable Y. It has an outcome set {1,2,3,8,10} and the following probability distribution: Pr [Y = 1] = 0.25; Pr [Y = 2] = 0.35; Pr [Y = 3] = 0.1; Pr [Y = 8] = 0.25; Pr [Y = 10] = 0.05. 

While two possible outcomes indeed have an equal probability of occurring, namely 1 and 8, Y can also take on certain values with different probabilities than 0.25 upon sampling. Hence, while Y is indeed a random variable, it is not a uniform variable. 

A graphical depiction of Y is provided in *Figure 2*.

*Figure 2: Random variable Y*

![Figure 2: Random variable Y.](/Images/Figure2-2.png "Figure 2: Random variable Y")

For a final example, consider the random variable Z. It has the outcome set {1,3,7,11,12} and the following probability distribution: Pr (2) = 0.2; Pr (3) = 0.2; Pr (9) = 0.2; Pr (11) = 0.2; Pr (12) = 0.2. You can see it depicted in Figure 3. The random variable Z is, in contrast to Y, indeed a uniform variable, as all the probabilities for the possible values upon sampling are equal. 

*Figure 3: Random variable Z*

![Figure 3: Random variable Z.](/Images/Figure2-3.png "Figure 3: Random variable Z")


### Conditional probability

Suppose that Bob intends to uniformly select a day from the last calendar year. What should we conclude is the probability of the selected day being in Summer? 

As long as we think Bob’s process will indeed be truly uniform, we should conclude that there is a 1/4 probability Bob selects a day in Summer. This is the **unconditional probability** of the randomly selected day being in Summer. 

Suppose now that instead of uniformly drawing a calendar day, Bob only selects only selects uniformly from among those days on which the noon temperature at Crystal Lake (New Jersey) was 21 degrees Celcius or higher.  Given this additional information, what can we conclude about the probability that Bob will select a day in Summer?  

We should really draw a different conclusion than before, even without any further specific information (e.g., the temperature at noon each day last calendar year). 

Knowing that Crystal Lake is in New Jersey, we would certainly not expect the temperature at noon to be 21 degrees Celsius or higher in Winter. Instead, it is much more likely to be a warm day in the Spring or Fall, or a day somewhere in the Summer. Hence, knowing the noon temperature at Crystal Lake on the selected day was 21 degrees Celsius or higher, the probability that the day selected by Bob is in the Summer becomes much higher.  

This was an example where the probability of one event—the chance that the selected day was in the Summer—was influenced by that of another event—the outside noon temperature at Crystal Lake. By contrast, when the probabilities of two events are completely unrelated, we say that they are **independent**.

Suppose, for example, that a certain fair coin has landed heads. Given this fact, what, then, is the probability that it will rain tomorrow? The conditional probability in this case should be the same as the unconditional probability that it will rain tomorrow, as a coin flip does not generally have any impact on the weather. 

We use a “|” symbol for writing out conditional probability statements. For instance, the probability of event A given that event B has transpired can be written as follows: Pr [A|B]. So, when two events, A and B, are independent, then Pr [A|B] = Pr[A] and Pr [B|A] = Pr[B]. 

A key result in probability theory is known as **Bayes Theorem**. It basically states that Pr [A|B] can be rewritten as follows: 

Pr [A|B] = (Pr[B|A] • Pr[A]) / Pr[B]

Instead of using conditional probabilities with specific events, we can also look at random variables over a set of possible events. Suppose two random variables, X and Y. We can denote any possible value for X by x, and any possible value for Y by y. We might say, then, that two random variables are independent if the following expression holds: 

Pr [X = x | Y = y] = pr [X = x] for all x and y

Lets be a bit more explicit about what this statement means. 

Suppose that the outcome sets for X and Y are defined as follows: **X** = {x<sub>1</sub>,x<sub>2</sub>….,x<sub>i</sub>,….x<sub>n</sub>} and **Y** = {y<sub>1</sub>,y<sub>2</sub>….,y<sub>i</sub>,….y<sub>m</sub>}. (It is typical to indicate sets of values by bold-faced, upper-case letters.)

Now suppose you sample Y and observe y<sub>1</sub>. The statement above tells us that the probability of now obtaining x1 from sampling X is exactly the same as if we had never observed y<sub>1</sub>. This is true for any y<sub>i</sub> we could have drawn from our initial sampling of Y. Finally, this holds true not just for x<sub>1</sub>. For any x<sub>i</sub> the probability of occurring is not influenced by the outcome of a sampling of Y.  

Hence, we say that two random variables X and Y are **independent** if it is true that Pr [X = x | Y = y] = pr [X = x] for all x and y.

Lets end our discussion on a slightly more philosophical point. In any real-world situation, the probability of some event is always assessed against a particular set of information. There is no “unconditional probability” in any very strict sense of the word. 

For instance, suppose I asked you for the probability that pigs will fly by 2030. Though I give you no further information, you clearly now a lot about the world that can influence your judgment. You have never seen pigs fly. You know that most people will not expect them to fly. You know that they are not really built to fly. And so on. 

Hence, when we speak of an “unconditional probability” of some event in a real-world context, that term really can only have meaning if we take it to mean something like “the probability without any further information”. Any understanding of a “conditional probability” should, then, always be understood against some specific piece of information. I might, for instance, ask you the probability that pigs will fly by 2030, after giving you evidence that apparently some goats in New Zealand have learned to fly.  


### The modulo operation

The most basic expression with the **modulo operation** is of the following form: x mod y.

The variable x is called the dividend and the variable y the divisor. To perform a modulo operation with a positive dividend and a positive divisor, you just determine the remainder of the division. 

For instance, consider the expression 25 mod 4. The number 4 goes into the number 25 a total of 6 times. The remainder of that division is 1. Hence, 25 mod 4 equals 1. In a similar manner, we can evaluate the expressions below:

* 29 mod 30 = 29 (as 30 goes into 29 a total of 0 times and the remainder is 29)
* 42 mod 2 = 0 (as 2 goes into 42 a total of 21 times and the remainder is 0)
* 12 mod 5 = 2 (as 5 goes into 12 a total of 2 times and the remainder is 2)
* 20 mod 8 = 4 (as 8 goes into 20 a total of 2 times and the remainder is 4)

When the dividend or divisor is negative, modulo operations can be handled differently by programming languages. 

You will definitely come across cases with a negative dividend in cryptography. In these cases, the typical approach is as follows:

* First determine the closest value *lower than or equal to* the dividend into which the divisor divides with a remainder of zero. Call that value p. 
* If the dividend is x, then the result of the modulo operation is the value of x – p.  

For instance, suppose that the dividend is – 20 and the divisor 3. The closest value lower than or equal to – 20 into which 3 divides evenly is – 21. The value of x – p in this case is – 20 – – 21. This equals 1 and, hence, – 20 mod 3 equals 1. In a similar manner, we can evaluate the expressions below:

* – 8 mod 5 = 2 
* – 19 mod 16 = 13 
* – 14 mod 6 = 4

Regarding notation, you will typically see the following types of expressions: x = [y mod z]. Due to the brackets, the modulo operation in this case only applies to the right-hand side of the expression. If y equals 25 and z equals 4, for example, then x evaluates to 1. 

Without brackets, the modulo operation acts on *both sides* of an expression. Suppose, for instance, the following expression: x = y mod z. If y equals 25 and z equals 4, then all we know is that x mod 4 evaluates to 1. This is consistent with any value for x from the set {….– 7, – 3, 1, 5, 9….}. 

The branch of mathematics that involves modulo operations on numbers and expressions is referred to modular arithmetic. You can think of this branch as arithmetic for cases in which the number line is not infinitely long. Though we typically come across modulo operations for (positive) integers within cryptography, you can perform modulo operations using any real numbers. 


### The shift cipher

The modulo operation is frequently encountered within cryptography. To illustrate, lets consider one of the most famous historical encryption schemes: the shift cipher. 

Lets first define it. Suppose a dictionary *D* that equates all the letters of the English alphabet, in order, with the set of numbers {0,1,2…,25}. Assume a message space **M**. The **shift cipher** is, then, an encryption scheme defined as follows:

- Select uniformly a key k out of the key space **K**, where **K** = {0,1,2,…,25}<sup>[1](#footnote1)</sup> 
- Encrypt a message m є **M**, as follows:
    - Separate m into its individual letters m<sub>0</sub>, m<sub>1</sub>,….m<sub>i</sub>….,m<sub>l</sub>
    - Convert each m<sub>i</sub> to a number according to *D*
    - For each m<sub>i</sub>, c<sub>i</sub> = [(m<sub>i</sub> + k) mod 26]
    - Convert each c<sub>i</sub> to a letter according to *D*
    - Then combine c<sub>0</sub>, c<sub>1</sub>,….,c<sub>l</sub> to yield the ciphertext c
- Decrypt a ciphertext c as follows:
    -- Convert each c<sub>i</sub> to a number according to *D*
    -- For each c<sub>i</sub>, m<sub>i</sub> = [(c<sub>i</sub> – k) mod 26]
    -- Convert each m<sub>i</sub> to a letter according to *D*
    -- Then combine m<sub>0</sub>, m<sub>1</sub>,….,m<sub>l</sub> to yield the original message m

The modulo operator in the shift cipher ensures that letters wrap around, so that all ciphertext letters are defined. To illustrate, consider the application of the shift cipher on the word “DOG”. 

Suppose that you uniformly selected a key to have the value of 17. The letter “O” equates to 15. Without the modulo operation, the addition of this plaintext number with the key would amount to a ciphertext number of 32. However, that ciphertext number cannot be turned into a ciphertext letter, as the English alphabet only has 26 letters. The modulo operation ensures that the ciphertext number is actually 6 (the result of 32 mod 26), which equates to the ciphertext letter “G”.  

The entire encryption of the word “DOG” with a key value of 17 is as follows:

* Message = DOG = D,O,G = 3,15,6
* c<sub>0</sub> = [(3 + 17) Mod 26] = [(20) Mod 26] = 20 = U
* c<sub>1</sub> = [(15 + 17) Mod 26] = [(32) Mod 26] = 6 = G
* c<sub>2</sub> = [(6 + 17) Mod 26] = [(23) Mod 26] = 23 = X
* c = UGX

Everyone can intuitively understand how the shift cipher works and probably use it themselves. For our advancing your knowledge of cryptography, however, it is important to start becoming more comfortable with formalization, as the schemes will become much more difficult. Hence, why the steps for the shift cipher were formalized. 


## The XOR operation

All computer data is processed, stored, and sent across networks at the level of bits. Any cryptographic schemes that are applied to computer data also operate at the bit-level. 

For instance, suppose that you have typed an e-mail into your e-mail application. Any encryption you apply does not occur on the ASCII characters of your e-mail directly. Instead, it is applied to the bit-representation of the letters and other symbols in your e-mail. 

A key mathematical operation to understand for modern cryptography, besides the modulo operation, is that of the **XOR operation**, or “exclusive or” operation. This operation takes as inputs two bits and yields as output another bit. The XOR operation will simply be denoted as "XOR". It yields 0 if the two bits are the same and 1 if the two bits are different. You can see the four possibilities below. 

* 0 XOR 0 = 0
* 0 XOR 1 = 1
* 1 XOR 0 = 1
* 1 XOR 1 = 0

You can perform an XOR operation on two messages longer than a single bit by lining up the bits of those two messages and performing the XOR operation on each individual pair of bits. 

To illustrate, suppose that you have a message m<sub>1</sub> (01111001) and a message m<sub>2</sub> (01011001). The XOR operation of these two messages can be seen below. 

m<sub>1</sub> XOR m<sub>2</sub> = 01111001 XOR 01011001 = 00100000 

The process is straightforward. Your first XOR the left-most bits of m1 and m2. In this case that is 0 XOR 0 = 0. You then XOR the second pair of bits from the left. In this case that is 1 XOR 1 = 0. You continue this process until you have performed the XOR operation on the right-most bits. 

It is easy to see that the XOR operation is commutative, namely that m<sub>1</sub> XOR m<sub>2</sub> = m<sub>2</sub> XOR m<sub>1</sub>. In addition, the XOR operation is also associative. That is, (m<sub>1</sub> XOR m<sub>2</sub>) XOR m<sub>3</sub> = m<sub>1</sub> XOR (m<sub>2</sub> XOR m<sub>3</sub>). 

An XOR operation on two strings of alternative lengths can have different interpretations, depending on the context. We will not concern ourselves here with any XOR operations on strings of different lengths. 

An XOR operation is equivalent to the special case of performing a modulo operation on the addition of bits when the divisor is 2. You can see the equivalency in the following results:

* (0 + 0) mod 2 = 0 XOR 0 = 0
* (1 + 0) mod 2 = 1 XOR 0 = 1
* (0 + 1) mod 2 = 0 XOR 1 = 1
* (1 + 1) mod 2 = 1 XOR 1 = 0


## Pseudorandomness

In our discussion of random and uniform variables, we drew a specific distinction between “random” and “uniform”. That distinction is typically maintained in practice when describing random variables. However, in our current context, this distinction needs to be dropped and “random” and “uniform” are used synonymously. I will explain why at the end of the section.  

To start, we can call a binary string of length n **random** (or **uniform**), if it was the result of sampling a uniform variable S which gives each binary string of such a length n an equal probability of selection. 

Suppose, for instance, the set of all binary strings with length 8: {0000 0000,0000 0001,….,1111 1111}. (It is typical to write an 8-bit string in two quartets.) Lets call this set of strings **S<sub>8</sub>**. 

Per the definition above, we can, then, call a particular binary string of length 8 random (or uniform), if it was the result of sampling a uniform variable S that gives each string in **S<sub>8</sub>** an equal probability of selection. Given that the set **S<sub>8</sub>** includes 28 elements, the probability of selection upon sampling would have to be 1/2<sup>8</sup> for each string in the set. 

A key aspect to the randomness of a binary string is that it is defined with reference to the process by which it was selected. The form of any particular binary string on its own, therefore, reveals nothing about its randomness in selection. 

For example, many people intuitively have the idea that a string like 1111 1111 could not have been selected randomly. But this is clearly false. 

Defining a uniform variable S over all the binary strings of length 8, the likelihood of selecting 1111 1111 from the set **S<sub>8</sub>** is the same as that of a string such as 0111 01001. Thus, you cannot tell anything about the randomness of a string, just by analyzing the string itself. 

We can also speak of random strings without specifically meaning binary strings. We might, for instance, speak of a random hex string AF 02 82. In this case, the string would have been selected at random from the set of all hex strings of length 6. This is equivalent to randomly selecting a binary string of length 24, as each hex digit represents 4 bits. 

Typically the expression “a random string”, without qualification, refers to a string randomly selected from the set of all strings with the same length. This is how I have described it above. A string of length n can, of course, also be randomly selected from a different set. One, for example, that only constitutes a subset of all the strings of length n, or perhaps a set that includes strings of varying length. In those cases, however, we would not refer to it as a “random string”, but rather “a string that is randomly selected from some set **S**”.

A key concept within cryptography is that of pseudorandomness. A **pseudorandom** string of length n appears *as if* it was the result of sampling a uniform variable S that gives each string in **S<sub>n</sub>** an equal probability of selection. In fact, however, the string is the result of sampling a uniform variable S' that only defines a probability distribution—not necessarily one with equal probabilities for all possible outcomes—on a subset of **S<sub>n</sub>**. The crucial point here is that no one can really distinguish between samples from S and S', even if you take many of them. 

Suppose, for instance, a random variable S. Its outcome set is **S<sub>256</sub>**, this is the set of all binary strings of length 256. This set has 2256 elements. Each element has an equal probability of selection, 1/2<sup>256</sup>, upon sampling. 

In addition suppose a random variable S’. Its outcome set only includes 2<sup>128</sup> strings of length 256. It has some probability distribution over those strings, but this distribution is not necessarily uniform. 

Suppose that I now took a 1000s of samples from S and 1000s of samples from S' and gave the two sets of outcomes to you. I tell you which set of outcomes is associated with which random variable. Next, I take a sample from one of the two random variables. But this time I do not tell you which random variable I sample. If S' were pseudorandom, then the idea is that your probability of making the right guess as to which random variable I sampled is practically no better than 1/2.  

Typically, a pseudorandom string of length n is produced by randomly selecting a string of size n – x, where x is a positive integer, and using it as an input for an expansionary algorithm. This random string of size n – x is known as the **seed**.

Pseudorandom strings are a key concept to making cryptography practical. Consider, for instance, stream ciphers. With a stream cipher, a randomly selected key is plugged into an expansionary algorithm to produce a much larger pseudorandom string. This pseudorandom string is then combined with the plaintext via an XOR operation to produce a ciphertext. 

If we were unable to produce this type of pseudorandom string for a stream cipher, then we would need a key that is as long as the message for its security. This is not a very practical option in most cases. 

The notion of pseudorandomness discussed in this section can be defined more formally. It also extends to other contexts. But we need not delve into that discussion here. All you really need to intuitively understand for much of cryptography is the difference between a random and a pseudorandom string.<sup>[2](#footnote2)</sup>  

The reason for dropping the distinction between “random” and “uniform” in our discussion should now also be clear. In practice everyone uses the term pseudorandom, even though they strictly mean “pseudo-uniform” when adopting our language from earlier. As the term “pseudo-uniform” is both clunky and not used by anyone, we will not introduce it here for clarity. Instead, we just drop the distinction between “random” and “uniform” in the current context.  


## Number theory

You might characterize **number theory** as the study of the properties of integers and mathematical functions that work with integers. 

Consider, for example, that any two numbers a and N are considered **coprimes** (or **relative primes**) if their greatest common divisor equals 1. Suppose now a particular integer N. How many integers smaller than N are coprimes with N? Can we make general statements about the answers to this question? These are the typical types of questions that number theory seeks to answer.

Modern number theory relies on the tools of abstract algebra. The field of **abstract algebra** is a subdiscipline of mathematics where the main objects of analysis are abstract objects known as algebraic structures. An **algebraic structure** is a set of elements conjoined with one or more operations, which meets certain axioms. Through algebraic structures mathematicians can gain insights into specific mathematical problems, by abstracting away from their details. 

The field of abstract algebra is sometimes also called modern algebra. You may also come across the concept of abstract mathematics (or pure mathematics). This latter term is not a reference to abstract algebra, but rather means the study of mathematics for its own sake, and not just with eye on potential applications. 

The sets from abstract algebra can deal with many types of objects, from the shape-preserving transformations on an equilateral triangle to wall paper patterns. For number theory, we only consider sets of elements that contain integers or functions that work with integers. 

Though number theory is important to symmetric cryptography as well (such as in the Rijndael Cipher), it is particularly important in the public key cryptographic setting.  


### Groups

A basic concept in mathematics is that of a set of elements. A set is usually denoted by accolade signs with the elements separated by commas. 

For instance, the set of all integers is {….,-2,-1,0,1,2,….}. The ellipses here means that a certain pattern continues in a particular direction. So the set of all integers also includes 3,4,5,6 and so on, as well as -3,-4,-5,-6 and so on. This set of all integers is typically denoted by ℤ.

Another example of a set is ℤ mod 11, or the set of all integers modulo 11. In contrast to the entire set ℤ, this set only contains a finite number of elements, namely {0,1,….,9,10}. 

A common mistake is to think that the set ℤ mod 11 actually is {-10,-9,….,0,….,9,10}. But this is not the case, given the way we defined the modulo operation earlier . Any negative integers reduced by modulo 11 wrap onto {0,1,….,9,10}. For instance, the expression -2 mod 11 wraps around to 9, while the expression -27 mod 11 wraps around to 5.  

Another basic concept in mathematics is that of a binary operation. This is any operation that takes two elements to produce a third. For instance, from basic arithmetic and algebra, you would be familiar with four fundamental binary operations: addition, subtraction, multiplication, and division. 

These two basic mathematical concepts, sets and binary operations, are used to define the notion of a group, the most essential structure in abstract algebra. 

Specifically, suppose some binary operation ◌. In addition, suppose some set of elements S equipped with that operation. All “equipped” means here is that the operation ◌ can be performed between any two elements in the set **S**. 

The combination 〈S, ◌〉 is, then, a **group** if it meets four specific conditions, known as the group axioms.

1. For any a and b that are elements of **S**, a ◌ b is also an element of **S**. This is known as the closure condition. 
2. For any a, b, and c that are elements of **S**, it is the case that (a ◌ b) ◌ c = a ◌ (b ◌ c). This is known as the associativity condition. 
3. There is a unique element e in **S**, such that for every element a in **S**, the following equation holds: e ◌ a = a ◌ e = a. As there is only one such element e, it is called the identity element. This condition is known as the identity condition. 
4. For each element a in **S**, there exists an element b in **S**, such that the following equation holds: a ◌ b = b ◌ a = e, where e is the identity element. Element b here is known as the inverse element, and it is commonly denoted as a<sup>-1</sup>. This condition is known as the inverse condition or the invertibility condition. 

Lets explore groups a little further. Denote the set of all integers by ℤ. This set combined with standard addition, or 〈ℤ, +〉, clearly fits the definition of a group, as it meets the four axioms above.

1. For any x and y that are elements of ℤ, x + y is also an element of ℤ. So 〈ℤ, +〉 meets the closure condition.
2. For any x, y, and z that are elements of ℤ, (x + y) + z = x + (y + z). So 〈 ℤ, +〉 meets the associativity condition.
3. There is an identity element in 〈ℤ, +〉, namely 0. For any x in ℤ, it namely holds that: 0 + x = x + 0 = x. So 〈ℤ, +〉 meets the identity condition.
4. Finally, for each element x in ℤ, there is a y so that x + y = y + x = 0. If x were 10, for instance, y would be – 10 (in the case that x is 0, y is also 0). So 〈ℤ, +〉 meets the inverse condition.

Importantly, that the set of integers with addition constitutes a group does not mean that it constitutes a group with multiplication. You can verify this by testing 〈ℤ, •〉 against the four group axioms (where • means standard multiplication). 

The first two axioms obviously hold. In addition, under multiplication the element 1 can serve as the identity element. Any integer x multiplied by 1, namely yields x. However, 〈ℤ, •〉 does not meet the inverse condition. That is, there is not a unique element y in ℤ for every x in ℤ, so that x • y = 1. 

For instance, suppose that x = 22. What value y from the set ℤ multiplied with x would yield the identity element 1? The value of 1/22 would work, but this is not in the set ℤ. In fact, you run into this problem for any integer x, other than the values of 1 and -1 (where y would have to be 1). 

If we allowed real numbers for our set, then our problems disappear. For any element x in the set, multiplication by 1/x yields 1. As fractions are included in the set of real numbers, an inverse can be found for every real number. The exception is zero, as any multiplication with zero will never yield the identity element 1. Hence, the set of non-zero real numbers equipped with multiplication is indeed a group.  

Some groups meet a fifth general condition, known as the commutativity condition. This condition is as follows:

* Suppose a group G with a set **S** and a binary operator ◌.  Suppose that a and b are elements of **S**. If it is the case that a ◌ b = b ◌ a for any two elements a and b in **S**, then G meets the commutativity condition.

Any group that meets the commutativity condition is known as a **commutative group**, or an **Abelian group** (after Niels Henrik Abel). It is easy to verify that both the set of real numbers over addition and the set of integers over addition are Abelian groups. The set of integers over multiplication is not a group at all, so ipso facto cannot be an Abelian group. The set of non-zero real numbers over multiplication, by contrast, is also an Abelian group.

You should heed two important conventions on notation. First, the signs “+” or “x” will frequently be employed to symbolize group operations, even when the elements are not, in fact, numbers. In these cases, you should not interpret these signs as standard arithmetic addition or multiplication. Instead, they are operations with only an abstract similarity to these arithmetic operations. 

Unless you are specifically referring to arithmetic addition or multiplication, it is easier to use symbols such as ◌ and ◊ for group operations, as these do not have very culturally engrained connotations.

Second, for the same reason that “+” and “x” are often used for indicating non-arithmetic operations, the identity elements of groups are frequently symbolized by “0” and “1”, even when the elements in these in groups are not numbers. Unless you are referring to the identity element of a group with numbers, it is easier to use a more neutral symbol such as “e” to indicate the identity element.  

Many different and very important sets of values in mathematics equipped with certain binary operations are groups. Cryptographic applications, however, only work with sets of integers or at least elements that are described by integers, that is, within the domain of number theory. Hence, sets with real numbers other than integers are not employed in cryptographic applications.   

Lets finish by providing an example of elements that can be “described by integers”, even though they are not integers. A good example is the points of elliptic curves. Though any point on an elliptic curve is clearly not an integer, such a point is indeed described by two integers. 

Elliptic curves are, for instance, crucial to Bitcoin. Any standard Bitcoin private and public key pair is selected from the set of points that is defined by the following elliptic curve: x<sup>3</sup> + 7 = y<sup>2</sup> mod 2<sup>256</sup> – 232 – 29 – 28 – 27 – 26 - 24 - 1 (the largest prime number less than 2<sup>256</sup>). The x-coordinate is the private key and the y-coordinate is your public key.

The Bitcoin Block Chain registers transactions between what are typically transformations of a public keys. The value from these transactions can, then, be unlocked making digital signatures with the corresponding private keys. 


### Cyclic groups

A major distinction we can draw is between **finite** and **infinite groups**. The former has a finite number of elements, while the latter has an infinite number of element. The number of elements in any finite group is known as the **order of the group**. All practical cryptography that involves the use of groups relies on finite (number-theoretic) groups. 

Within public key cryptography, a certain class of finite Abelian groups known as cyclic groups are particularly important. In order to understand cyclic groups, we first need to understand the concept of group element exponentiation.

Suppose a group G with a group operation ◌, and that a is an element of G. The expression a<sup>n</sup> should, then, be interpreted as the element a combined with itself a total of n – 1 times. For instance, a<sup>2</sup> means a ◌ a, a<sup>3</sup> means a ◌ a ◌ a, and so on. (Note that exponentiation here is not necessarily exponentiation in the standard arithmetic sense.)

Lets turn to an example. Suppose that G = 〈ℤ mod 7,+〉, and that our value for a equals 4. In this case, a<sup>2</sup> = [4 + 4 mod 7] = [8 mod 7] = 1 mod 7. Alternatively, a<sup>4</sup> would represent [4 + 4 + 4 + 4 mod 7] = [16 mod 7] = 2 mod 7.   

Some Abelian groups have one or more elements, which can yield all other group elements through continued exponentiation. These elements are called **generators** or **primitive elements**. 

An important class of such groups is 〈ℤ* mod N, •〉, where N is a prime number. The notation ℤ* here means that the group contains all non-zero integers mod N. Such a group, therefore, always has N – 1 elements. 

Consider, for instance, G = 〈ℤ* mod 11, •〉. This group has the following elements: {1, 2, 3, 4, 5, 6, 7, 8, 9, 10}. The order of this group is 10 (which is indeed equal to 11 – 1).  

Lets explore exponentiating the element 2 from this group. The calculations up until 2<sup>12</sup> are shown below. Note that on the left side of the equation, the exponent refers to group element exponentiation. In our particular example, this indeed involves arithmetic exponentiation on the right side of the equation (but it could have also involved, for instance, addition). To clarify, I have written out the repeated operation, rather than the exponent form on the right side.   

* 2<sup>1</sup> = 2 mod 11 
* 2<sup>2</sup> = 2 · 2 mod 11 = 4 mod 11
* 2<sup>3</sup> = 2 · 2 · 2 mod 11 = 8 mod 11
* 2<sup>4</sup> = 2 · 2 · 2 · 2 mod 11 = 16 mod 11 = 5 mod 11
* 2<sup>5</sup> = 2 · 2 · 2 · 2 · 2 mod 11 = 32 mod 11 = 10 mod 11
* 2<sup>6</sup> = 2 · 2 · 2 · 2 · 2 · 2 mod 11 = 64 mod 11 = 9 mod 11
* 2<sup>7</sup> = 2 · 2 · 2 · 2 · 2 · 2 · 2 mod 11 = 128 mod 11 = 7 mod 11
* 2<sup>8</sup> = 2 · 2 · 2 · 2 · 2 · 2 · 2 · 2 mod 11 = 256 mod 11 = 3 mod 11
* 2<sup>9</sup> = 2 · 2 · 2 · 2 · 2 · 2 · 2 · 2 · 2 mod 11 = 512 mod 11 = 6 mod 11
* 2<sup>10</sup> = 2 · 2 · 2 · 2 · 2 · 2 · 2 · 2 · 2 · 2 mod 11 = 1024 mod 11 = 1 mod 11
* 2<sup>11</sup> = 2 · 2 · 2 · 2 · 2 · 2 · 2 · 2 · 2 · 2 · 2 mod 11 = 2048 mod 11 = 2 mod 11
* 2<sup>12</sup> = 2 · 2 · 2 · 2 · 2 · 2 · 2 · 2 · 2 · 2 · 2 · 2 mod 11 = 4096 mod 11 = 4 mod 11

If you look carefully, you can see that performing exponentiation on the element 2 cycles through all the elements of 〈ℤ* mod 11, •〉 in the following order: 2, 4, 8, 5, 10, 9, 7, 3, 6, 1. After 2<sup>10</sup>, continued exponentiation of the element 2 cycles through all the elements again and in the same order. Hence, the element 2 is a generator in 〈ℤ* mod 11, •〉.

Though 〈ℤ* mod 11, •〉 has multiple generators, not all the elements of this group are generators. Consider, for example, the element 3. Running through the first 10 exponentiations, without showing the cumbersome calculations, yields the following results: 

* 3<sup>1</sup> = 3 mod 11 
* 3<sup>2</sup> = 9 mod 11
* 3<sup>3</sup> = 5 mod 11
* 3<sup>4</sup> = 4 mod 11
* 3<sup>5</sup> = 1 mod 11
* 3<sup>6</sup> = 3 mod 11
* 3<sup>7</sup> = 9 mod 11
* 3<sup>8</sup> = 5 mod 11
* 3<sup>9</sup> = 4 mod 11
* 3<sup>10</sup> = 1 mod 11

Instead of cycling through all the values in 〈ℤ* mod 11, •〉, exponentiation of the element 3 only leads to a subset of those values: 3,9,5,4, and 1. After the fifth exponentiation, these values start repeating. 

We can now define a **cyclic group** as any group with at least one generator. That is, there is at least one group element from which you can produce all other group elements through exponentiation. 

You may have noticed in our example above that both 2<sup>10</sup> and 3<sup>10</sup> equal 1 mod 11. In fact, though we will not perform the calculations, the exponentiation by 10 of any element in the group 〈ℤ* mod 11, •〉 will yield 1 mod 11. Why is this the case?

This is an important question, but it takes some work to answer. 

To start, suppose two positive integers a and N. An important theorem in number theory states that a has a multiplicative inverse modulo N (that is, an integer b so that a • b = 1 mod N) if and only if the greatest common divisor between a and N equals 1. That is, if a and N are coprimes. 

So, for any group of integers equipped with multiplication modulo N only the smaller coprimes with N are included in the set. We can denote this set by ℤ<sup>c</sup> mod N.

For instance, suppose that N is 10. Only the integers 1,3,7, and 9 are coprimes with 10. So the set ℤ<sup>c</sup> mod 10 only includes {1,3,7,9}. You cannot create a group with integer multiplication modulo 10 using any other integers between 1 and 10. For this particular group, the inverses are the pairs 1 and 9, and 3 and 7.

In the case where N itself is prime, all the integers from 1 through N – 1 are coprimes with N. Such a group, thus, has an order of N – 1. Using our earlier notation, ℤ<sup>c</sup> mod N equals ℤ* mod N when N is prime. The group we selected for our earlier example, 〈ℤ* mod 11, •〉, is a particular instance of this class of groups.

Next, the function φ(N) calculates the number of coprimes up until a number N, and is known as **Euler’s Phi function**.<sup>[3](#footnote3)</sup> According to **Euler’s Theorem**, whenever two integers a and N are coprimes, the following holds:

* a<sup>φ(N)</sup> mod N = 1 mod N

This has an important implication for the class of groups 〈ℤ* mod N, •〉 where N is prime. For these groups, group element exponentiation represents arithmetic exponentiation. That is, a<sup>φ(N)</sup> mod N represents the arithmetic operation a<sup>φ(N)</sup> mod N. As any element a in these multiplicative groups is coprime with N, it means that a<sup>φ(N)</sup> mod N = a<sup>N – 1</sup> mod N = 1 mod N. 

Euler’s theorem is a really important result. To start, it implies that all elements in 〈ℤ* mod N, •〉 can only cycle through a number of values by exponentiation that divides into N – 1. In the case of 〈ℤ* mod 11, •〉, this means that each element can only cycle through 2, 5, or 10 elements. The group values that any element cycles through upon exponentiation is known as the **order of the element**. An element with an order equivalent to the order of a group is a generator. 

Furthermore, Euler’s theorem implies that we can always know the result of a<sup>N – 1</sup> mod N for any group 〈ℤ* mod N, •〉 where N is prime. This is so regardless of how complicated the actual calculations might be. 

For instance, suppose our group is ℤ* mod 160,481,182 (where 160,481,182 is indeed a prime number). We know that all integers 1 through 160,481,181 must be elements of this group, and that φ(n) = 160,481,181. Though we can not make all the steps in the calculations, we know that expressions such as 514<sup>160,481,181</sup>, 2,005<sup>160,481,181</sup>, and 256,212<sup>160,481,181</sup> must all evaluate to 1 mod 160,481,182. 


### Fields

A group is the basic algebraic structure in abstract algebra, but there are many more. The only other algebraic structure you need to be familiar with is that of a field, specifically that of a finite field. This type of algebraic structure is frequently used in cryptography, such as in the Advanced Encryption Standard. The latter is the main symmetric encryption scheme that you will encounter in practice.  

A field is derived from the notion of a group. Specifically, a **field** is a set of elements **S** equipped with two binary operators ◌ and ◊, which meets the following conditions: 

1. The set **S** equipped with ◌ is an Abelian group. 
2. The set **S** equipped with ◊ is an Abelian group for the “non-zero” elements. 
3. The set **S** equipped with the two operators meets what is known as the distributive condition: Suppose that a, b, and c are elements of **S**. Then **S** equipped with the two operators meets the distributive property when a ◌ (b ◊ c) = a ◌ b ◊ a ◌ c. 

Note that, as with groups, the definition of a field is very abstract. It makes no claims about the types of elements in **S**, or about the operations ◌ and ◊. It just states that a field is any set of elements with two operations for which the three above conditions hold. (The “zero” element in the second Abelian group can be abstractly interpreted.)

So what might be an example of a field? A good example is the set ℤ mod 7, or {0,1,….,7} defined over standard addition (in place of ◌ above) and standard multiplication (in place of ◊ above).

First, ℤ mod 7 meets the condition for being an Abelian group over addition, and it meets the condition for being an Abelian group over multiplication if you only consider the non-zero elements. Second, the combination of the set with the two operators meets the distributive condition. 

It is didactively worthwhile to explore these claims by using some particular values. Lets take the experimental values 5, 2, and 3, some randomly selected elements from the set ℤ mod 7, to inspect the field 〈ℤ mod 7, +, •〉. We will use these three values in order, as needed to explore particular conditions.  

Let’s first explore if ℤ mod 7 equipped with addition is an Abelian group. 

1. Closure condition: Let’s take 5 and 2 as our values. In that case, [5 + 2] mod 7 = 7 mod 7 = 0. This is indeed an element of ℤ mod 7, so the result is consistent with the closure condition.  
2. Associativity condition: Let’s take 5, 2, and 3 as our values. In that case, [(5 + 2) + 3] mod 7 = [5 + (2 + 3)] mod 7 = 10 mod 7 = 3. This is consistent with the associativity condition.  
3. Identity condition: Let’s take 5 as our value. In that case, [5 + 0] mod 7 = [0 + 5] mod 7 = 5. So 0 looks to be the identity element for addition.   
4. Inverse condition: Consider the inverse of 5. It needs to be the case that [5 + d] mod 7 = 0, for some value of d. In this case, the unique value from ℤ mod 7 that meets this condition is 2.  
5. Commutativity condition: Let’s take 5 and 3 as our values. In that case, [5 + 3] mod 7 = [3 + 5] mod 7 = 1. This is consistent with the commutativity condition.

The set ℤ mod 7 equipped with addition clearly appears to be an Abelian group. Let’s now explore if ℤ mod 7 equipped with multiplication is an Abelian group for all the non-zero elements.

1. Closure condition: Let’s take 5 and 2 as our values. In that case, [5 • 2] mod 7 = 10 mod 7 = 3. This is also an element of ℤ mod 7, so the result is consistent with the closure condition.  
2. Associativity condition: Let’s take 5, 2, and 3 as our values. In that case, [(5 • 2) • 3] mod 7 = [5 • (2 • 3)] mod 7 = 30 mod 7 = 2. This is consistent with the associativity condition.  
3. Identity condition: Let’s take 5 as our value. In that case, [5 • 1] mod 7 = [1 • 5] mod 7 = 5. So 1 looks to be the identity element for multiplication.   
4. Inverse condition: Consider the inverse of 5. It needs to be the case that [5 • d] mod 7 = 1, for some value of d. The unique value from ℤ mod 7 that meets this condition is 3. This is consistent with the inverse condition. 
5. Commutativity condition: Let’s take 5 and 3 as our values. In that case, [5 • 3] mod 7 = [3 • 5] mod 7 = 15 mod 7 = 1. This is consistent with the commutativity condition.

The set ℤ mod 7 clearly seems to meet the rules for being an Abelian group when conjoined with either addition or multiplication over the non-zero elements. 

Finally, this set combined with both operators seems to meet the distributive condition. Let’s take 5, 2, and 3 as our values. We can see that [5 • (2 + 3)] mod 7 = [5 • 2 + 5 • 3] mod 7 = 25 mod 7 = 4.

We have now seen that ℤ mod 7 equipped with addition and multiplication meets the axioms for a finite field when testing with particular values. Of course we can also show that generally, but will not do so here.  

A key distinction is between two types of fields: finite and infinite fields. 

An **infinite field** involves a field where the set S is infinitely large. The set of real numbers ℝ equipped with addition and multiplication is an example of an infinite field. A **finite field**, also known as a **Galois field**, is a field where the set S is finite. Our example above of 〈ℤ mod 7, +, •〉 is a finite field. 

In cryptography, we are primarily interested in finite fields. Generally, it can be shown that a finite field exists for some set of elements **S** if and only if it has p<sup>m</sup> elements, where p is a prime number and m a positive integer greater than or equal to one. In other words, if the order of some set **S** is a prime number (p<sup>m</sup> where m = 1) or some prime power (p<sup>m</sup> where m > 1), then you can find two operators ◌ and ◊ such that the conditions for a field are satisfied.  

If some finite field has a prime number of elements, then it is called a **prime field**. If the number of elements in the finite field is a prime power, then the field is called an **extension field**. In cryptography, we are interested in both prime and extension fields.<sup>[4](#footnote4)</sup>

The main prime fields of interest in cryptography are those where the set of all integers is modulated by some prime number, and the operators are standard addition and multiplication. This class of finite fields would include ℤ mod 2, ℤ mod 3, ℤ mod 5, ℤ mod 7, ℤ mod 11, ℤ mod 13, and so on. For any prime field ℤ mod p, the set of integers of the field is as follows: {0,1,….,p – 2, p – 1}.

In cryptography we are also interested in extension fields, particularly any fields with 2<sup>m</sup> elements where m > 1. Such finite fields are, for instance, used in the Rijndael Cipher, which forms the basis of the Advanced Encryption Standard. While prime fields are relatively intuitive, these base 2 extension fields are probably not for anyone unfamiliar with abstract algebra. 

To start, it is indeed true that any set of integers with 2<sup>m</sup> elements can be assigned two operators that would make their combination a field (as long as m is a positive integer). Yet, just because a field exists does not necessarily mean that it is easy to discover or particularly practical for certain applications.

As it turns out, particularly applicable prime power fields of 2<sup>m</sup> in cryptography are those defined over particular sets of polynomial expressions, rather than some set of integers. 

For instance, suppose that we wanted an extension field with 2<sup>3</sup> (i.e., 8) elements in the set. While there might be many different sets that can be used for fields of that size, one such set includes all unique polynomials of the form of a<sub>2</sub>x<sup>2</sup> + a<sub>1</sub>x + a<sub>0</sub>, where each coefficient a<sub>i</sub> is either 0 or 1. Hence, this set **S** includes the following elements:

1. 0: The case where a<sub>2</sub> = 0, a<sub>1</sub> = 0, and a<sub>0</sub> = 0.
2. 1: The case where a<sub>2</sub> = 0, a<sub>1</sub> = 0, and a<sub>0</sub> = 1.
3. x: The case where a<sub>2</sub> = 0, a<sub>1</sub> = 1, and a<sub>0</sub> = 0.
4. x + 1: The case where a<sub>2</sub> = 0, a<sub>1</sub> = 1, and a<sub>0</sub> = 1.
5. x<sup>2</sup>: The case where a<sub>2</sub>= 1, a<sub>1</sub> = 0, and a<sub>0</sub> = 0. 
6. x<sup>2</sup> + 1: The case where a<sub>2</sub> = 1, a<sub>1</sub> = 0, and a<sub>0</sub> = 1. 
7. x<sup>2</sup> + x: The case where a<sub>2</sub> = 1, a<sub>1</sub> = 1, and a<sub>0</sub> = 0. 
8. x<sup>2</sup> + x + 1: The case where a<sub>2</sub> = 1, a<sub>1</sub> = 1, and a<sub>0</sub> = 1. 

So **S** would be the set {0,1,x,x + 1, x<sup>2</sup>,x<sup>2</sup> + 1, x<sup>2</sup> + x, x<sup>2</sup> + x + 1}. What two operations can be defined over this set of elements to ensure their combination is a field?

The first operation on the set S (◌) can be defined as standard polynomial addition modulo 2. All you have to do is add the polynomials as you normally would, and then apply the modulo 2 to each of the coefficients of the resulting polynomial. Here are some examples:

* [(x<sup>2</sup>) + (x<sup>2</sup> + x + 1)] mod 2 = [2x<sup>2</sup> + x + 1] mod 2 = x + 1
* [(x<sup>2</sup> + x) + (x)] mod 2 = [x<sup>2</sup> + 2x] mod 2 = x<sup>2</sup>
* [(x + 1) + (x<sup>2</sup> + x + 1)] mod 2 = [x<sup>2</sup> + 2x + 2] mod 2 = x<sup>2</sup> + 1

The second operation on the set S (◌) that is needed for creating the field is more complicated. It is a kind of multiplication, but not standard multiplication from arithmetic. Instead, you have to see each element as a vector and understand the operation as the multiplication of those two vectors modulo an irreducible polynomial. 

Let’s first turn to the idea of an irreducible polynomial. An **irreducible polynomial** is one that cannot be factored (just as a prime number cannot be factored into components other than 1 and the prime number itself). For our purposes, we are interested in polynomials that are irreducible with respect to the set of all integers. (Note that you may be able to factor certain polynomials by, for example, the real or complex numbers, even if you cannot factor them using integers.) 

For instance, consider the polynomial x<sup>2</sup> - 3x + 2. This can be rewritten as (x – 1)(x – 2). Hence, this is not irreducible. Now consider the polynomial x<sup>2</sup> + 1. Using only integers, there is no way to further factor this expression. Hence, this is an irreducible polynomial with respect to the integers. 

Next, let’s turn to the concept of vector multiplication. We will not explore this topic in depth, you just need to understand a basic rule: Any vector division can take place as long as the dividend has a degree higher than or equal to that of the divisor. If the dividend has a lower degree than the divisor, then the dividend can no longer be divided by the divisor.  

For instance, consider the expression x<sup>6</sup> + x + 1 mod x<sup>5</sup> + x<sup>2</sup>. This clearly reduces further as the degree of the dividend, 6, is higher than the degree of the divisor, 5. Now consider the expression x<sup>5</sup> + x + 1 mod x<sup>5</sup> + x<sup>2</sup>. This also reduces further, as the degree of the dividend, 5, and the divisor, 5, are equal. 

However, now consider the expression x<sup>4</sup> + x + 1 mod x<sup>5</sup> + x<sup>2</sup>. This does not reduce further, as the degree of the dividend, 4, is lower than the degree than the divisor, 5.

On the basis of this information, we are now ready to find our second operation for the set {0,1,x,x + 1,x<sup>2</sup>,x<sup>2</sup> + 1,x<sup>2</sup> + x,x<sup>2</sup> + x + 1}. 

I have already said that the second operation should be understood as vector multiplication modulo some irreducible polynomial. This irreducible polynomial should ensure that the second operation defines an Abelian group over **S** and is consistent with the distributive condition. So what should that irreducible polynomial be?

As all vectors in the set are of degree 2 or lower, the irreducible polynomial should be of degree 3. If any multiplication of two vectors in the set yields a polynomial degree 3 or higher, we know that modulo a polynomial of degree 3 always yields a polynomial of degree 2 or lower. This is the case because any polynomial of degree 3 or higher is always divisible by a polynomial of degree 3. In addition, the polynomial that functions as the divisor has to be irreducible. 

It turns out that there are several irreducible polynomials of degree 3 that we could use as our divisor. Each of these polynomials defines a different field in conjunction with our set S and addition modulo 2. This means you have multiple options when using extension fields 2<sup>m</sup> in cryptography.  

For our example, suppose that we select the polynomial is x<sup>3</sup> + x + 1. This indeed is irreducible, because you cannot factor it using integers. In addition, it will ensure that any multiplication of two elements will yield a polynomial of degree 2 or less.  

Let’s work through an example of the second operation using the polynomial x<sup>3</sup> + x + 1 as the divisor to illustrate how it works. Suppose that you multiply the elements x<sup>2</sup> + 1 with x<sup>2</sup> + x in our set **S**. We, then, need to calculate the expression [(x<sup>2</sup> + 1) • (x<sup>2</sup> + x)] mod x<sup>3</sup> + x + 1. This can be simplified as follows:

* [(x<sup>2</sup> + 1) • (x<sup>2</sup> + x)] mod x<sup>3</sup> + x + 1 =
* [x<sup>2</sup> • x<sup>2</sup> + x<sup>2</sup> • x + 1 • x<sup>2</sup> + 1 • x] mod x<sup>3</sup> + x + 1 = 
* [x<sup>4</sup> + x<sup>3</sup> + x<sup>2</sup> + x] mod x<sup>3</sup> + x + 1
    
We know that [x<sup>4</sup> + x<sup>3</sup> + x<sup>2</sup> + x] mod x<sup>3</sup> + x + 1 can be reduced as dividend has a higher degree (4) than the divisor (3). 

To start, you can see that the expression x<sup>3</sup> + x + 1 goes into x<sup>4</sup> + x<sup>3</sup> + x<sup>2</sup> + x a total of x times. You can verify this by multiplying x<sup>3</sup> + x + 1 by x, which is x<sup>4</sup> + x<sup>2</sup> + x. As the latter term is of the same degree as the dividend, namely 4, we know this works. You can calculate the remainder of this division by x as follows: 

* [(x<sup>4</sup> + x<sup>3</sup> + x<sup>2</sup> + x) – (x<sup>4</sup> + x<sup>2</sup> + x)] mod x<sup>3</sup> + x + 1 = 
* [x<sup>3</sup>] mod x<sup>3</sup> + x + 1 =
* x<sup>3</sup>

So after dividing x<sup>4</sup> + x<sup>3</sup> + x<sup>2</sup> + x by x<sup>3</sup> + x + 1 a total of x times, we have a remainder of x<sup>3</sup>. Can this be further divided by x<sup>3</sup> + x + 1?

Intuitively, it might be appealing to say that x<sup>3</sup> can no longer be divided by x<sup>3</sup> + x + 1, because the latter term seems larger. However, remember our discussion on vector division earlier. As long as the dividend has a degree larger or equal to the divisor, the expression can be further reduced. Specifically, the expression x<sup>3</sup> + x + 1 can go into x<sup>3</sup> exactly 1 time. The remainder is calculated as follows:

[(x<sup>3</sup>) – (x<sup>3</sup> + x + 1)] mod x<sup>3</sup> + x + 1 = 
[x + 1] mod x<sup>3</sup> + x + 1 = 
x + 1

You might be wondering why (x<sup>3</sup>) – (x<sup>3</sup> + x + 1) evaluates to x + 1 and not – x – 1. Remember that the first operation of our field is defined modulo 2. Hence, the subtraction of two vectors yields exactly the same result as the addition of two vectors. 

To sum up the multiplication of x<sup>2</sup> + 1 and x<sup>2</sup> + x: When you multiply those two terms you get a degree 4 polynomial, x<sup>4</sup> + x<sup>3</sup> + x<sup>2</sup> + x, which needs to be reduced modulo x<sup>3</sup> + x + 1. The degree 4 polynomial is divisible by x<sup>3</sup> + x + 1 exactly x + 1 times. The remainder after dividing x<sup>4</sup> + x<sup>3</sup> + x<sup>2</sup> + x by x<sup>3</sup> + x + 1 exactly x + 1 times is x + 1. This is indeed an element in our set {0,1,x,x + 1,x<sup>2</sup>,x<sup>2</sup> + 1,x<sup>2</sup> + x,x<sup>2</sup> + x + 1}.

Why would extension fields with base 2 over sets of polynomials, like in the example above, be useful for cryptography? The reason is that you can view the coefficients in the polynomials of such sets, either 0 or 1, as elements of binary strings with a particular length. The set **S** in our example above, for example, could be viewed instead as a set S that includes all binary strings of length 3 (000 through 111). The operations on **S**, then, can also be used to perform operations on these binary strings and produce a binary string of the same length.   


### Abstract algebra in practice

Despite the formal language and abstractness of the discussion, the concept of a group should not be too difficult to grasp. It is just a set of elements together with an binary operation, where performance of that binary operation on those elements meets four general conditions. An Abelian group just has an extra condition known as commutativity. A cyclic group, in turn, is a special kind of Abelian group, namely one that has a generator. A field is merely a more complex construct from the basic group notion.  

But if you are a practically inclined person, you might wonder at this point: Who cares? Does knowing some set of elements with an operator is a group, or even an Abelian or cyclic group, have any real world relevance? Does knowing something is a field?

Without venturing into too much detail right now, the answer is “yes”. Groups were first created in the 19th century by the French mathematician Evariste Galois. He used them to draw conclusions about solving polynomial equations of a degree higher than five. 

Since then the concept of a group has helped shed light on a number of problems in mathematics and elsewhere. On their basis, for instance, the physicist Murray-Gellman was able to predict the existence of a particle before it was actually observed in experiments.<sup>[5](#footnote5)</sup> For another example, chemists use group theory to classify the shapes of molecules. Mathematicians have even used the concept of a group to draw conclusions about something so concrete as wall paper! 

Essentially showing that a set of elements with some operator is a group, means that what you are describing has a particular symmetry. Not a symmetry in the common sense of the word, but in a more abstract form. And this can provide substantial insights into particular systems and problems. The more complex notions from abstract algebra just give us additional information. 

Most importantly, you will see the importance of number theoretic groups and fields in practice through their application in cryptography, particularly public key cryptography. We have already seen in our discussion of fields, for instance, how extension fields are employed in the Rijndael Cipher.  


### Further exploration

For further discussion on abstract algebra, I would recommend the excellent video series on abstract algebra by Socratica.<sup>[6](#footnote6)</sup> I would particularly recommend the following videos: “What is abstract algebra?”, “Group definition (expanded)”, “Ring definition (expanded)”, and “Field definition (expanded).” These four videos will give you some additional insight into much of the discussion above. (We did not discuss rings, but a field is just a special type of ring.) 

For further discussion on modern number theory, you can consult many advanced discussions on cryptography. I would offer as suggestions Jonathan Katz and Yehuda Lindell’s Introduction to Modern Cryptography or Christof Paar and Jan Pelzl’s Understanding Cryptography for further discussion.<sup>[7](#footnote7)</sup>


## Notes

<a name="footnote1">1</a>. We can define this statement exactly, using the terminology from the previous section. Let a uniform variable K have **K** as its set of possible outcomes. So Pr [K = 0] = 1/26, Pr [K = 1] = 1/26, and so on. Sample the uniform variable K once to yield a particular key. 

<a name="footnote2">2</a>. If interested in a more formal exposition on these matters, you can consult Katz and Lindell’s *Introduction to Modern Cryptography*, esp. chapter 3. 

<a name="footnote3">3</a>. The function works as follows. Any integer N can be factored into a product of primes. Suppose that a particular N is factored as follows: p<sub>1</sub><sup>e1</sup> • p<sub>2</sub><sup>e2</sup> …. • p<sub>m</sub><sup>em</sup> where all the p’s are prime numbers and all the e’s are integers greater than or equal to 1. Then, φ(N) = Sum<sub>i=1…m</sub>[p<sub>i</sub><sup>ei</sup> – p<sub>i</sub><sup>ei - 1</sup>]. 

<a name="footnote4">4</a>. Extension fields become very counterintuitive. Instead of having elements of integers, they have sets of polynomials. In addition, any operations are performed modulo some irreducible polynomial.

<a name="footnote5">5</a>. See https://www.youtube.com/watch?v=NOMUnMuxDZY&feature=youtu.be. 

<a name="footnote6">6</a>. Socratica, Abstract Algebra, available at: https://www.socratica.com/subject/abstract-algebra. 

<a name="footnote7">7</a>. Katz and Lindell, *Introduction to Modern Cryptography*, 2nd edn, 2015 (CRC Press: Boca Raton, FL). Paar and Pelzl, *Understanding Cryptography*, 2010 (Springer-Verlag: Berlin). 
