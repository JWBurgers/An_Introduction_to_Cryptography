# Chapter 2: Mathematical Foundations of Cryptography I

Cryptography relies on mathematics. And if you want to build more than a superficial understanding of cryptography, you need to be comfortable with that mathematics. 

This chapter introduces most of the basic mathematics you will encounter in learning cryptography. The topics include random variables, modulo operations, XOR operations, and pseudorandomness. You should master the material in these sections for any non-superficial understanding of cryptography.  

The next chapter deals with number theory, which is much more challenging. 


## Random variables

A random variable is typically denoted by a non-bold, uppercase letter. So, for instance, we might talk about a random variable X, a random variable Y, or a random variable Z. This is the notation I will also employ from here on out.  

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

Frequently, however, random variables are not just introduced abstractly. Instead, the set of possible outcome values has explicit real-world meaning (rather than just as numbers). In addition, these outcome values might be defined against some specific type of experiment (rather than as any natural experiment with those values). 

Lets now consider an example of variable X that is not defined abstractly. X is defined as follows in order to determine which of two teams starts a football game:

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

Suppose now that instead of uniformly drawing a calendar day, Bob only selects uniformly from among those days on which the noon temperature at Crystal Lake (New Jersey) was 21 degrees Celcius or higher. Given this additional information, what can we conclude about the probability that Bob will select a day in Summer?  

We should really draw a different conclusion than before, even without any further specific information (e.g., the temperature at noon each day last calendar year). 

Knowing that Crystal Lake is in New Jersey, we would certainly not expect the temperature at noon to be 21 degrees Celsius or higher in Winter. Instead, it is much more likely to be a warm day in the Spring or Fall, or a day somewhere in the Summer. Hence, knowing the noon temperature at Crystal Lake on the selected day was 21 degrees Celsius or higher, the probability that the day selected by Bob is in the Summer becomes much higher. This is the **conditional probability** of the randomly selected day being in Summer, given that the noon temperature at Crystal Lake was 21 degrees Celsius or higher.  

Unlike in the previous example, the probabilities of two events can also be completely unrelated. In that case, we say that they are **independent**.

Suppose, for example, that a certain fair coin has landed heads. Given this fact, what, then, is the probability that it will rain tomorrow? The conditional probability in this case should be the same as the unconditional probability that it will rain tomorrow, as a coin flip does not generally have any impact on the weather. 

We use a “|” symbol for writing out conditional probability statements. For instance, the probability of event A given that event B has transpired can be written as follows: Pr[A|B]. So, when two events, A and B, are independent, then Pr[A|B] = Pr[A] and Pr[B|A] = Pr[B]. The condition for independence can be simplified as follows: Pr[A,B] = Pr[A]*Pr[B].  

A key result in probability theory is known as **Bayes Theorem**. It basically states that Pr[A|B] can be rewritten as follows: 

Pr[A|B] = (Pr[B|A] • Pr[A]) / Pr[B]

Instead of using conditional probabilities with specific events, we can also look at the conditional probabilities involved with two or more random variables over a set of possible events. Suppose two random variables, X and Y. We can denote any possible value for X by x, and any possible value for Y by y. We might say, then, that two random variables are independent if the following statement holds: 

Pr[X = x,Y = y] = Pr[X = x] • Pr[Y =  y] for all x and y

Lets be a bit more explicit about what this statement means. 

Suppose that the outcome sets for X and Y are defined as follows: **X** = {x<sub>1</sub>,x<sub>2</sub>….,x<sub>i</sub>,….x<sub>n</sub>} and **Y** = {y<sub>1</sub>,y<sub>2</sub>….,y<sub>i</sub>,….y<sub>m</sub>}. (It is typical to indicate sets of values by bold-faced, upper-case letters.)

Now suppose you sample Y and observe y<sub>1</sub>. The statement above tells us that the probability of now obtaining x<sub>1</sub> from sampling X is exactly the same as if we had never observed y<sub>1</sub>. This is true for any y<sub>i</sub> we could have drawn from our initial sampling of Y. Finally, this holds true not just for x<sub>1</sub>. For any x<sub>i</sub> the probability of occurring is not influenced by the outcome of a sampling of Y. All this also applies to the case where X is sampled first.  

Lets end our discussion on a slightly more philosophical point. In any real-world situation, the probability of some event is always assessed against a particular set of information. There is no “unconditional probability” in any very strict sense of the word. 

For instance, suppose I asked you for the probability that pigs will fly by 2030. Though I give you no further information, you clearly know a lot about the world that can influence your judgment. You have never seen pigs fly. You know that most people will not expect them to fly. You know that they are not really built to fly. And so on. 

Hence, when we speak of an “unconditional probability” of some event in a real-world context, that term really can only have meaning if we take it to mean something like “the probability without any further explicit information”. Any understanding of a “conditional probability” should, then, always be understood against some specific piece of information. 

I might, for instance, ask you the probability that pigs will fly by 2030, after giving you evidence that some goats in New Zealand have learned to fly after a few years of training. In this case, you will probably adjust your judgment of the probability that pigs will fly by 2030. So the probability that pigs will fly by 2030 is conditional upon this evidence about goats in New Zealand.     


## The modulo operation

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

The branch of mathematics that involves modulo operations on numbers and expressions is referred to **modular arithmetic**. You can think of this branch as arithmetic for cases in which the number line is not infinitely long. Though we typically come across modulo operations for (positive) integers within cryptography, you can also perform modulo operations using any real numbers. 


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

Everyone can intuitively understand how the shift cipher works and probably use it themselves. For advancing your knowledge of cryptography, however, it is important to start becoming more comfortable with formalization, as the schemes will become much more difficult. Hence, why the steps for the shift cipher were formalized. 


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

* m<sub>1</sub> XOR m<sub>2</sub> = 01111001 XOR 01011001 = 00100000 

The process is straightforward. Your first XOR the left-most bits of m<sub>1</sub> and m<sub>2</sub>. In this case that is 0 XOR 0 = 0. You then XOR the second pair of bits from the left. In this case that is 1 XOR 1 = 0. You continue this process until you have performed the XOR operation on the right-most bits. 

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

Suppose, for instance, the set of all binary strings with length 8: {0000 0000,0000 0001,….,1111 1111}. (It is typical to write an 8-bit string in two quartets, each called a **nibble**.) Lets call this set of strings **S<sub>8</sub>**. 

Per the definition above, we can, then, call a particular binary string of length 8 random (or uniform), if it was the result of sampling a uniform variable S that gives each string in **S<sub>8</sub>** an equal probability of selection. Given that the set **S<sub>8</sub>** includes 28 elements, the probability of selection upon sampling would have to be 1/2<sup>8</sup> for each string in the set. 

A key aspect to the randomness of a binary string is that it is defined with reference to the process by which it was selected. The form of any particular binary string on its own, therefore, reveals nothing about its randomness in selection. 

For example, many people intuitively have the idea that a string like 1111 1111 could not have been selected randomly. But this is clearly false. 

Defining a uniform variable S over all the binary strings of length 8, the likelihood of selecting 1111 1111 from the set **S<sub>8</sub>** is the same as that of a string such as 0111 01001. Thus, you cannot tell anything about the randomness of a string, just by analyzing the string itself. 

We can also speak of random strings without specifically meaning binary strings. We might, for instance, speak of a random hex string AF 02 82. In this case, the string would have been selected at random from the set of all hex strings of length 6. This is equivalent to randomly selecting a binary string of length 24, as each hex digit represents 4 bits. 

Typically the expression “a random string”, without qualification, refers to a string randomly selected from the set of all strings with the same length. This is how I have described it above. A string of length n can, of course, also be randomly selected from a different set. One, for example, that only constitutes a subset of all the strings of length n, or perhaps a set that includes strings of varying length. In those cases, however, we would not refer to it as a “random string”, but rather “a string that is randomly selected from some set **S**”.

A key concept within cryptography is that of pseudorandomness. A **pseudorandom string** of length n appears *as if* it was the result of sampling a uniform variable S that gives each string in **S<sub>n</sub>** an equal probability of selection. In fact, however, the string is the result of sampling a uniform variable S' that only defines a probability distribution—not necessarily one with equal probabilities for all possible outcomes—on a subset of **S<sub>n</sub>**. The crucial point here is that no one can really distinguish between samples from S and S', even if you take many of them. 

Suppose, for instance, a random variable S. Its outcome set is **S<sub>256</sub>**, this is the set of all binary strings of length 256. This set has 2<sup>256</sup> elements. Each element has an equal probability of selection, 1/2<sup>256</sup>, upon sampling. 

In addition suppose a random variable S’. Its outcome set only includes 2<sup>128</sup> binary strings of length 256. It has some probability distribution over those strings, but this distribution is not necessarily uniform. 

Suppose that I now took a 1000s of samples from S and 1000s of samples from S' and gave the two sets of outcomes to you. I tell you which set of outcomes is associated with which random variable. Next, I take a sample from one of the two random variables. But this time I do not tell you which random variable I sample. If S' were pseudorandom, then the idea is that your probability of making the right guess as to which random variable I sampled is practically no better than 1/2.  

Typically, a pseudorandom string of length n is produced by randomly selecting a string of size n – x, where x is a positive integer, and using it as an input for an expansionary algorithm. This random string of size n – x is known as the **seed**.

Pseudorandom strings are a key concept to making cryptography practical. Consider, for instance, stream ciphers. With a stream cipher, a randomly selected key is plugged into an expansionary algorithm to produce a much larger pseudorandom string. This pseudorandom string is then combined with the plaintext via an XOR operation to produce a ciphertext. 

If we were unable to produce this type of pseudorandom string for a stream cipher, then we would need a key that is as long as the message for its security. This is not a very practical option in most cases. 

The notion of pseudorandomness discussed in this section can be defined more formally. It also extends to other contexts. But we need not delve into that discussion here. All you really need to intuitively understand for much of cryptography is the difference between a random and a pseudorandom string.<sup>[2](#footnote2)</sup>  

The reason for dropping the distinction between “random” and “uniform” in our discussion should now also be clear. In practice everyone uses the term pseudorandom to indicate a string that appears **as if** it was the result of sampling a uniform variable S. Strictly speaking, we should call such a string “pseudo-uniform,” adopting our language from earlier. As the term “pseudo-uniform” is both clunky and not used by anyone, we will not introduce it here for clarity. Instead, we just drop the distinction between “random” and “uniform” in the current context.  


## Notes

<a name="footnote1">1</a>. We can define this statement exactly, using the terminology from the previous section. Let a uniform variable K have **K** as its set of possible outcomes. So Pr [K = 0] = 1/26, Pr [K = 1] = 1/26, and so on. Sample the uniform variable K once to yield a particular key. 

<a name="footnote2">2</a>. If interested in a more formal exposition on these matters, you can consult Katz and Lindell’s *Introduction to Modern Cryptography*, esp. chapter 3. 