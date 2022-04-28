# Chapter 5: RC4 and AES

In this Chapter, we will discuss the details of an encryption scheme with a modern primitive stream cipher, RC4 (or "Rivest cipher 4"), and a modern block cipher, AES. While the RC4 cipher has fallen into disfavor as a method of encryption, AES is the standard for modern symmetric encryption. These two examples should give a better idea of how symmetric encryption works under the hood. 


## The RC4 stream cipher

In order to have a sense of how modern pseudorandom stream ciphers work, I will focus on the RC4 stream cipher. It is a pseudorandom stream cipher that was used in the WEP and WAP wireless access point security protocols as well as in TLS. As RC4 has many proven weaknesses, it has fallen into disfavor. In fact, the Internet Engineering Task Force now forbids the use of RC4 suites by client and server applications in all instances of TLS.<sup>[3](#footnote3)</sup> Nevertheless, it works well as an example to illustrate how a primitive stream cipher works.  

To start, I will first show how to encrypt a plaintext message with a baby RC4 cipher. Suppose our plaintext message is “SOUP.” Encryption with our baby RC4 cipher, then, proceeds in four steps.

### Step 1

First, define an array S with S[0] = 0 to S[7] = 7. An array here is simply means a mutable collection of values organized by an index, also called a list in some programming languages (e.g., Python). In this case, the index runs from 0 through 7, and the values also run from 0 to 7. So S is as follows:

- S = [0,1,2,3,4,5,6,7]

The values here are not ASCII numbers, but the decimal value representations of 1 byte strings. So the value 2 would equal 0000 0011. The length of the array S is, thus, 8 bytes. 

### Step 2

Second, define a key array K of 8 bytes length by choosing a key between 1 and 8 bytes (with no fractions of bytes permissible). As each byte is 8 bits, you can select any number between 0 and 255 for each byte of your key.   

Suppose we choose our key k as [14,48,9], so that it has length of 3 bytes. Each index of our key array is, then, set according to the decimal value for that particular element of the key, in order. If you run through the entire key, start again at the beginning, until you have filled the 8 slots of the key array. Hence, our key array is as follows

- K = [14,48,9,14,48,9,14,48]

### Step 3

Third, we will transform the array S using the key array K, in a process known as key scheduling. The process is as follows in pseudocode: 

- Create variables j and i
- Set the variable j = 0
- For each i from 0 to 7:
	- Set j = j + S[i] + K[i] mod 8
	- Swap S[i] and S[j] 

The transformation of array S is captured by *Table 1*. 

To start, you can see the initial state of S as [0,1,2,3,4,5,6,7] with an initial value of 0 for j. This will be transformed using the key array [14,48,9,14,48,9,14,48]. 

The for loop starts with i = 0. According to our pseudocode above, the new value of j becomes 6 (j = j + S[0] + K[0] mod 8 = 0 + 0 + 14 mod 8 = 6 mod 8). Swapping S[0] and S[6], the state of S after 1 round becomes [6,1,2,3,4,5,0,7]. 

In the next row, i = 1. Going through the for loop again, j obtains a value of 7 (j = j + S[1] + K[1] mod 8 = 6 + 1 + 48 mod 8 = 55 mod 8 = 7 mod 8). Swapping S[1] and S[7] from the current state of S, [6,1,2,3,4,5,0,7], yields [6,7,2,3,4,5,0,1] after round 2. 

We continue with this process until we produce the final row at the bottom for the array S, [6,4,1,0,3,7,5,2].  

*Table 1: Key scheduling table*

![Table 1: Key scheduling table](/Images/Table5-1.png "Table 1: Key scheduling table")

### Step 4

As a fourth step, we produce the keystream. This is the pseudorandom string of a length equal to the message we want to send. This is what will be used to encrypt the original message “SOUP.” As the keystream needs to be as long as the message, we need one that has 4 bytes. 

The keystream is produced by the following pseudocode:

- Create the variables j, i, and t
- Set j = 0
- For each i of the plaintext, starting with i = 1 and going until i = 4, each byte of the keystream is produced as follows:
    - j = j + S[i] mod 8
	- Swap S[i] and S[j]
	- t = S[i] + S[j] mod 8
	- The ith byte of the keystream = S[t]

You can follow the calculations in *Table 2*. 

The initial state of S = S = [6,4,1,0,3,7,5,2]. Setting i = 1, the value j becomes 4 (j = j + S[i] mod 8 = 0 + 4 mod 8 = 4). We, then, swap S[1] and S[4] to produce the transformation of S in the second row, [6,3,1,0,4,7,5,2]. The value t is, then, 7 (t = S[i] + S[j] mod 8 = 3 + 4 mod 8 = 7). Finally, the byte for the keystream is, then, S[7], or 2. 

We can, then, continue to produce the other bytes until we have the following four bytes: 2, 6, 3, and 7. Each of these bytes can, then, be used to encrypt each letter of the plaintext, "SOUP". 

To start, using an ASCII table, we can see that “SOUP” encoded by the decimal values of the underlying byte strings is “83 79 85 80”. Combination with the keystream “2 6 3 2” yields “85 85 88 82”, which stays the same after a modulo 256 operation. In ASCII, the ciphertext “85 85 88 82” equals “UUXR”. 

What happens if the word to encrypt were longer than the array S? In that case, the array S just keeps transforming in this manner displayed above for every byte i of the plaintext, until we have a number of bytes in the keystream equal to the number of letters in the plaintext. 

*Table 2: Keystream generation*

![Table 2: Keystream generation](/Images/Table5-2.png "Table 2: Keystream generation")

The example that we just discussed is only a watered down version of the RC4 stream cipher. The actual RC4 stream cipher has an S array of 256 bytes in length, not 8 bytes, and a key that can be between 1 and 256 bytes, not between 1 and 8 bytes. The key array and the keystreams are, then, all produced considering the 256 byte length of the S array. That calculations become immensely more complex, but the principles stay the same. Using the same key, [14,48,9], with the standard RC4 cipher, the plaintext message "SOUP" is encrypted as 67 02 ed df in hexadecimal format. 

A stream cipher in which the key stream updates independently of the plaintext message or the ciphertext is a **synchronous stream cipher**. The keystream is only dependent on the key. Clearly, RC4 is an example of a synchronous stream cipher, as the keystream has no relationship with the plaintext or the ciphertext. All our primitive stream ciphers mentioned in the previous chapter, including the shift cipher, the Vigenere cipher, and the one-time pad, were also of the synchronous variety. 

By contrast, an **asynchronous stream cipher** has a keystream that is produced by both the key and previous elements of the ciphertext. This type of cipher is also called a **self-synchronizing cipher**. 

Importantly, the keystream produced with RC4 should be treated as a one-time pad, and you cannot produce the keystream in exactly the same way the next time. Rather than changing the key each time, the practical solution is to combine the key with a nonce to produce the bytestream.


## AES with a 128-bit key

As mentioned in the previous chapter, the National Institute of Standards and Technology (NIST) held a competition between 1997 and 2000 to determine a new symmetric encryption standard. The Rijndael cipher turned out to be the winning entry. The name is a word play on the names of the Belgian creators, Vincent Rijmen en Joan Daemen. 

The Rijndael cipher is a block cipher, meaning there is a core algorithm, which can be used with different specifications for key lengths and block sizes. You can, then, use it with different modes of operation to construct encryption schemes.  

The committee for the NIST competition adopted a constricted version of the Rijndael cipher—namely one which requires 128-bit block sizes and key lengths of either 128 bits, 192 bits, or 256 bits—as part of the advanced encryption standard. This constricted version of the Rijndael cipher can also be used under multiple modes of operation. The specification for the standard is what is known as the Advanced Encryption Standard (AES).

In order to show how the Rijndael cipher works, the core of AES, I will illustrate the process for encryption with a 128-bit key. The key size has an impact on the number of rounds held for each block of encryption. For 128-bit keys, 10 rounds are required. With 192 bits and 256 bits, it would have been 12 and 14 rounds respectively. 

In addition, I will assume that AES is used in ECB-mode. This makes exposition slightly easier and doesn't matter for the Rijndael algorithm. To be sure, ECB mode is not secure in practice because it leads to deterministic encryption. The most commonly used secure mode with AES is CBC. 

Let's call the key K<sub>0</sub>. The construction with the above parameters, then, looks as in Figure 1, where M<sub>i</sub> stands for a part of the plaintext message of 128 bits and C<sub>i</sub> for a part of the ciphertext of 128 bits. Padding is added to the plaintext for the last block, if the plaintext cannot be evenly divided by the block size.

*Figure 1: AES-ECB with a 128-bit key*

![Figure 1: AES-ECB with a 128-bit key](/Images/Figure5-1.png "Figure 1: AES-ECB with a 128-bit key")

Each 128-block of text goes through ten rounds in the Rijndael encryption scheme. This requires a separate round key for each round (K<sub>1</sub> through K<sub>10</sub>). These are produced for each round from the original 128-bit key K<sub>0</sub> using a key expansion algorithm. Hence, for each block of text to be encrypted, we will use the original key K<sub>0</sub> as well as ten separate round keys. Note that these same 11 keys are used for each 128-bit block of plaintext that requires encryption.  

The key expansion algorithm is long and complex. Working through it has little didactic benefits. You can look through the key expansion algorithm on your own, if you wish. Once the round keys are produced, the Rijndael cipher will manipulate the first 128-bit block of plaintext, M<sub>1</sub>, as seen in Figure 2. We will now go through these steps.

*Figure 2: The manipulation of M<sub>1</sub> with the Rijndael cipher*

![Figure 2: AES-ECB with a 128-bit key](/Images/Figure5-2.png "Figure 2: AES-ECB with a 128-bit key")

### Round 0

Round 0 of the Rijndael cipher is straightforward. An array S<sub>0</sub> is produced by an XOR operation between the 128-bit plaintext and the private key. That is,

- S<sub>0</sub> = M<sub>1</sub> XOR K<sub>0</sub>

### Round 1

In round 1, the array S<sub>0</sub> is first combined with the round key K<sub>1</sub> using an XOR operation. This produces a new state of S. 

Second, the byte substitution operation is performed on the current state of S. It works by taking each byte of the 16-byte S array and subtituting it with a byte of an array called **Rijndael’s S-box**. Each byte has a unique transformation, and a new state of S is produced as a result. Rijndael's S-box is displayed in *Figure 3*. 

*Figure 3: Rijndael's S-Box*

![Figure 3: Rijndael's S-Box](/Images/Figure5-3.png "Figure 3: Rijndael's S-Box")

This S-Box is one place where abstract algebra comes into play in the Rijndael cipher, specifically Galois fields. 

To start, you define each possible byte element 00 through FF as an 8-bit vector. Each such vector is an element of the Galois field GF(2<sup>8</sup>) where the irreducible polynomial for the modulo operation is x<sup>8</sup> + x<sup>4</sup> + x<sup>3</sup> + x + 1. The Galois field with these specifications is also called Rijndael’s Finite Field. 

Next, for each possible element in the field we create what is called the **Nyberg S-Box**. In this box, each byte is mapped onto its multiplicative inverse (i.e., so that their product equals 1). We, then, map those values from the Nyberg S-box to Rijndael’s S-Box using the affine transformation. 

The third operation on the S array is the shift rows operation. It takes the state of S and lists all of the sixteen bytes in a matrix. The filling of the matrix starts on the top left and works its way around by going from top to bottom and then, each time a column is filled, shifting one column right and to the top. 

Once the matrix of S has been constructed, the four rows are shifted. The first row stays the same. The second row moves one over to the left. The third moves two over to the left. The fourth moves three over to the left. An example of the process is provided in *Figure 4*. The original state of S is shown on the top, the resultant state after the shift rows operation is shown below it.

*Figure 4: Shift rows operation*

![Figure 4: Shift rows operation](/Images/Figure5-4.png "Figure 4: Shift rows operation")

In the fourth step, Galois fields make an appearance again. To start, each column of the S matrix is multiplied by the column of the 4 x 4 matrix seen in *Figure 5*. But instead of being regular matrix multiplication, it is vector multiplication modulo an irreducible polynomial, x<sup>8</sup> + x<sup>4</sup> + x<sup>3</sup> + x + 1. The resulting vector coefficients represent the individual bits of a byte. 

*Figure 5: Mix columns matrix*

![Figure 5: Mix columns matrix](/Images/Figure5-5.png "Figure 5: Mix columns matrix")

Multiplication of the first column of the S matrix with the 4 x 4 matrix above yields the result in *Figure 6*. 

*Figure 6: Multiplication of the first column*

![Figure 6: Multiplication of the first column](/Images/Figure5-6.png "Figure 6: Multiplication of the first column")

As a next step, all the terms in the matrix would have to be turned into polynomials. For instance, F1 represents 1 byte and would become x<sup>7</sup> + x<sup>6</sup> + x<sup>5</sup> + x<sup>4</sup> + 1 and 03 represents 1 byte and would become x + 1. 

All the multiplications are, then, performed modulo x<sup>8</sup> + x<sup>4</sup> + x<sup>3</sup> + x + 1. This results with the addition of four polynomials in each of the four cells of the column. After performing those additions modulo 2, you will end up with four polynomials. Each of these polynomials represents an 8-bit string, or 1 byte, of S. We will not perform all these calculations here on the matrix in *Figure 6* as they are extensive. 

Once the first column has been processed, the other three columns of the S matrix are processed in the same manner. Eventually this will yield a matrix with sixteen bytes that can be transformed into an array. 

As a final step, the array S is combined wtih the round key again in an XOR operation. This produces the state S<sub>1</sub>. That is,

- S<sub>1</sub> = S XOR K<sub>0</sub>

### Rounds 2 through 10

Rounds 2 through 9 are just a repetition of round 1, *mutatis mutandis*. The final round looks very similar to the previous rounds, except that the mix columns step is eliminated. That is, round 10 is executed as follows:

- S<sub>9</sub> XOR K<sub>10</sub>
- Byte Substitution
- Shift Rows
- S<sub>10</sub> = S XOR K<sub>10</sub>

The state S<sub>10</sub> is now set to C<sub>1</sub>, the first 128 bits of the ciphertext. Proceeding through the remaining 128-bit plaintext blocks yields the full ciphertext C. 