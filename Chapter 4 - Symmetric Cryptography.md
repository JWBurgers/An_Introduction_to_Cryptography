# Chapter 4: Symmetric Cryptography

One of the two main branches of cryptography is symmetric cryptography. It includes encryption schemes as well as schemes concerned with authentication and integrity. Until the 1970s, all of cryptography would have consisted of symmetric encryption schemes. 

The main discussion starts by looking at symmetric encryption schemes and making the crucial distinction between stream ciphers and block ciphers. We, then, turn to message authentication codes, which are schemes for ensuring message integrity and authenticity. Finally, we explore how symmetric encryption schemes and message authentication codes can be combined to ensure secure communication.

This chapter discusses various symmetric cryptographic schemes from practice in passing. The next chapter offers a detailed exposition of a stream cipher and block cipher from practice, namely RC4 and AES respectively.  

Before starting our discussion on symmetric cryptography, I want to briefly make some remarks on the Alice and Bob illustrations in this and subsequent chapters.


## Alice and Bob

In illustrating the principles of cryptography, people often rely on examples involving Alice and Bob. I will do so as well. 

Especially if you are new to cryptography, it is important to realize that these examples of Alice and Bob are only meant to serve as illustrations of cryptographic principles and constructions in a simplified environment. The principles and constructions, however, are applicable to a much wider range of real-life contexts. 

Following are five key points to keep in mind about examples involving Alice and Bob in cryptography:

1. They can easily be translated into examples with other types of actors such as companies or government organizations.  
2. They can easily be extended to include three or more actors.
3. In the examples, Bob and Alice are typically active participants in creating each message and in the application of cryptographic schemes on that message. But in reality, electronic communications are largely automated. When you visit a website using transport layer security, for example, the cryptography is typically all handled by your computer and the web server. 
4. In the context of electronic communication, the “messages” that are sent across a communication channel are usually TCP/IP packets. These can belong to an e-mail, a Facebook message, a phone conversation, a file transfer, a website, a software upload, and so on. They are not messages in the traditional sense. Nevertheless, cryptographers will often simplify this reality by stating that the message is, for instance, an e-mail. 
5. The examples typically focus on electronic communication, but they can also be extended to traditional forms of communication such as letters.


## Symmetric encryption schemes

We can loosely define a **symmetric encryption scheme** as any cryptographic scheme with three algorithms: 

1. A **key generation algorithm**, which generates a private key.
2. An **encryption algorithm**, which takes the private key and a plaintext as inputs and outputs a ciphertext.
3. A **decryption algorithm**, which takes the private key and the ciphertext as inputs and outputs the original plaintext.

Typically an encryption scheme—whether symmetric or asymmetric—offers a template for encryption based on a core algorithm, rather than an exact specification. 

For instance, consider Salsa20, a symmetric encryption scheme. It can be used with both 128- and 256-bit key lengths. The choice regarding key length impacts some minor details of the algorithm (the number of rounds in the algorithm to be exact). 

But one would not say that using Salsa20 with a 128-bit key is a different encryption scheme than Salsa20 with a 256-bit key. The core algorithm stays the same. Only when the core algorithm changes would we really speak of two different encryption schemes. 
 
Symmetric encryption schemes are typically useful in two kinds of cases: (1) Those in which two or more agents are communicating from a distance and want to keep the contents of their communications secret; and (2) those in which one agent wants to keep the contents of a message secret across time. 

You can see a depiction of situation (1) in *Figure 1* below. Bob wants to send a message M to Alice across a distance, but does not want others to be able to read that message. 

Bob first encrypts the message M with the private key K. He, then, sends the ciphertext C to Alice. Once Alice has received the ciphertext, she can decrypt it using the key K and read the plaintext. With a good encryption scheme, any attacker that intercepts the ciphertext C should not be able to learn anything of real significance about the message M.  

You can see a depiction of situation (2) in *Figure 2* below. Bob wants to prevent others from viewing certain information. A typical situation might be that Bob is an employee storing sensitive data on his computer, which neither outsiders nor his colleagues are supposed to read.  

Bob encrypts the message M at time T<sub>0</sub> with the key K to produce the ciphertext C. At time T<sub>1</sub> he needs the message again, and decrypts the ciphertext C with the key K. Any attacker that might have come across the ciphertext C in a meantime should not have been able to deduce anything significant about M from it. 

*Figure 1: Secrecy across space*

![Figure 1: Secrecy across space](/Images/Figure4-1.png "Figure 1: Secrecy across space")

*Figure 2: Secrecy across time*

![Figure 2: Secrecy across time](/Images/Figure4-2.png "Figure 2: Secrecy across time")


## An example: The shift cipher

In Chapter 2, we encountered the shift cipher which is an example of a very simple symmetric encryption scheme. Let’s look at it again here. 

Suppose a dictionary *D* that equates all the letters of the English alphabet, in order, with the set of numbers {0,1,2…,25}. Assume a set of possible messages **M**. The shift cipher is, then, an encryption scheme defined as follows:

- Select randomly a key k out of the set of possible keys **K**, where **K** = {0,1,2,…,25} 
- Encrypt a message m є **M**, as follows:
    - Separate m into its individual letters m<sub>0</sub>, m<sub>1</sub>,….m<sub>i</sub>….,m<sub>l</sub>
    - Convert each m<sub>i</sub> to a number according to *D*
    - For each m<sub>i</sub>, c<sub>i</sub> = [(m<sub>i</sub> + k) mod 26]
    - Convert each c<sub>i</sub> to a letter according to *D*
    - Then combine c<sub>0</sub>, c<sub>1</sub>,….,c<sub>l</sub> to yield the ciphertext c
- Decrypt a ciphertext c as follows:
    - Convert each c<sub>i</sub> to a number according to *D*
    - For each c<sub>i</sub>, m<sub>i</sub> = [(c<sub>i</sub> – k) mod 26]
    - Convert each m<sub>i</sub> to a letter according to *D*
    - Then combine m<sub>0</sub>, m<sub>1</sub>,….,m<sub>l</sub> to yield the original message m

What makes the shift cipher a symmetric encryption scheme is that the same key is used for both the encryption and the decryption process. For instance, suppose that you want to encrypt the message “DOG” using the shift cipher, and that you randomly selected "24" as a key. Encrypting the message with this key would yield “BME”. The only way to retrieve the original message is by using the same key, "24", for the decryption process. 

This Shift cipher is an example of a **monoalphabetic substitution cipher**: an encryption scheme where the ciphertext alphabet is fixed (i.e, only one alphabet is used). Until the 1700s, many applications of encryption relied heavily on monoalphabetic substitution ciphers, though often these were much more complex than the Shift cipher. You could, for instance, randomly select a letter from the alphabet for each original text letter under the constraint that each letter occurs only once in the ciphertext alphabet. That means you would have factorial 26 possible private keys, which was huge in the precomputer age. 

Note that you will come across the term **cipher** a lot in cryptography. Be aware that this term has various meanings. In fact, I know of at least five distinct meanings of the term within cryptography. 

In some cases it refers to an encryption scheme, as it does in Shift cipher and monoalphabetic substitution cipher. However, the term can also refer specifically to the encryption algorithm, the private key, or just the ciphertext of any such encryption scheme. 

Lastly, the term cipher can also refer to a core algorithm from which you can construct cryptographic schemes. These can include various encryption algorithms, but also other types of cryptographic schemes. This sense of the term becomes relevant in the context of block ciphers (see the section “Block Ciphers” below). 

You may also come across the terms to **encipher** or to **decipher**. These terms are merely synonyms for encryption and decryption. 


## Brute force attacks and Kerckhoff's principle

The shift cipher is a very insecure symmetric encryption scheme, at least in the modern world.<sup>[1](#footnote1)</sup> An attacker could just attempt decryption of any ciphertext with all 26 possible keys to see which result makes sense. This type of attack, where the attacker is just cycling through keys to see what works, is known as a **brute force attack** or **exhaustive key search**. 

For any encryption scheme to meet a minimal notion of security, it must have a set of possible keys, or **keyspace**, which is so large that brute-force attacks are infeasible. All modern encryption schemes meet this standard. It is known as the **sufficient key space principle**. A similar principle typically applies in different types of cryptographic schemes. 

To get a feel for the massive key space size in modern encryption schemes, suppose that a file has been encrypted with a 128-bit using the advanced encryption standard. This means an attacker has a set of 2<sup>128</sup> keys that she needs to cycle through for a brute force attack. A chance of 0.78% of success with this strategy would require the attacker to cycle through roughly 2.65 x 10<sup>36</sup> keys. 

Suppose we optimistically assume that an attacker can attempt 10 quadrillion keys per second (i.e., 10<sup>16</sup> keys per second). To test 0.78% of all keys in the key space, her attack would have to last 2.65 x 10<sup>20</sup> seconds. This is about 8.4 trillion years. So even a brute force attack by an absurdly powerful adversary is not realistic with a modern 128-bit encryption scheme. This is the sufficient key space principle at play.     

Is the shift cipher more secure if the attacker does not know the encryption algorithm? Perhaps, but not by much.

In any case, modern cryptography, always assumes that the security of any symmetric encryption scheme only relies on keeping the private key secret. The attacker is always assumed to know all the other details, including the message space, the key space, the ciphertext space, the key selection algorithm, the encryption algorithm, and the decryption algorithm. 

The idea that the security of a symmetric encryption scheme can only rely on the secrecy of the private key is known as **Kerckhoffs’ principle**.

As originally intended by Kerckhoffs, the principle only applies to symmetric encryption schemes. A more general version of the principle, however, also applies to all other modern-day types of cryptographic schemes: Any cryptographic scheme’s design must not be required to be secret in order for it to be secure; the secrecy can only extend to some strings(s) of information, typically a private key.

Kerckhoffs’ principle is central to modern cryptography for four reasons.<sup>[2](#footnote2)</sup> First, there are only a limited number of cryptographic schemes for particular types of applications. For instance, most modern symmetric encryption applications use the Rijndael cipher. So your secrecy regarding a scheme’s design is just very limited. There is, however, much more flexibility in keeping some private key for the Rijndael cipher secret. 

Second, it is easier to replace some string of information than an entire cryptographic scheme. Suppose that the employees of a company all have the same encryption software, and that every two employees have a private key to communicate confidentially. Key compromises are a hassle in this scenario, but at least the company could keep the software with such security breaches. If the company were relying on the secrecy of the scheme, then any breach of that secrecy would require replacing all the software.  

Third, Kerckhoffs’ principle allows for standardization and compatibility between users of cryptographic schemes. This has massive benefits for efficiency. For instance, it is difficult to imagine how millions of people could securely connect to Google’s web servers each day, if that security required keeping cryptographic schemes secret.  

Fourth, Kerckhoff’s principle allows for the public scrutiny of cryptographic schemes. This type of scrutiny is absolutely necessary to achieve secure cryptographic schemes. Illustratively, the main core algorithm in symmetric cryptography, the Rijndael cipher, was the result of a competition organized by the National Institute of Standards and Technology between 1997 and 2000. 

Any system that attempts to achieve **security by obscurity** is one that relies on keeping the details of its design and/or implementation secret. In cryptography, this would be specifically a system that relies on keeping the design details of the cryptographic scheme secret. So security by obscurity is in direct contrast to Kerckhoffs’ principle.

One can never state unambiguously that a cryptographic scheme is "secure" or "insecure." Instead, there are various notions of security for cryptographic schemes. Each **definition of cryptographic security** must specify (1) security goals, as well as (2) the capabilities of an attacker. Analyzing cryptographic schemes against one or more specific notions of security provides insights into their applications and limitations. 

While we will not delve into all the details of the various notions of cryptographic security, you should know that two assumptions are ubiquitous to all modern cryptographic notions of security pertaining to symmetric and asymmetric schemes (and in some form to other cryptographic primitives):

* The attacker’s knowledge about the scheme conforms to Kerckhoffs’ principle.
* The attacker cannot feasibly perform a brute force attack on the scheme. Specifically, the threat models of cryptographic notions of security typically do not even allow brute force attacks, as they assume that these are not a relevant consideration. 


## Stream ciphers

Symmetric encryption schemes are standardly subdivided into two types: stream ciphers and block ciphers. This distinction is somewhat troublesome, however, as people use these terms in an inconsistent manner. In the next few sections, I will set out the distinction in the way I think is best. You should be aware, however, that many people will use these terms somewhat differently than I set out.

Let’s first turn to stream ciphers. A **stream cipher** is a symmetric encryption scheme where encryption consists of two steps. 

First, a string the length of the plaintext is produced via a private key. This string is called the **keystream**.

Next, the key stream is mathematically combined with the plaintext to produce a ciphertext. This combination is typically an XOR operation. For decryption, you can just reverse the operation. (Note that A XOR B = B XOR A, in the case A and B are bit-strings. So the order to an XOR operation in a stream cipher does not matter for the result. This property is known as commutativity.) 

A typical XOR stream cipher is depicted in *Figure 3*. You first take a private key K and use it to generate a keystream. The keystream is, then, combined with the plaintext via an XOR operation to produce the ciphertext. Any agent that receives the ciphertext can easily decrypt it if they have the key K. All she needs to do is create a keystream as long as the ciphertext according to the specified procedure of the scheme and XOR it with the ciphertext.  

*Figure 3: An XOR stream cipher*

![Figure 3: An XOR stream cipher](/Images/Figure4-3.png "Figure 3: An XOR stream cipher")

Be reminded that an encryption scheme is typically a template for encryption with the same core algorithm, rather than an exact specification. By extension, a stream cipher is typically a template for encryption in which you can use keys of different lengths. Though the key length can impact some minor details of the scheme, it will not impact its essential form. 

The shift cipher is an example of a very simple and insecure stream cipher. Using a single letter (the private key), you can produce a string of letters the length of the message (the keystream). This keystream is, then, combined with the plaintext via a modulo operation to produce a ciphertext. (This modulo operation can be simplified to an XOR operation when representing the letters in bits). 

Another famous example of a stream cipher is the **Vigenere cipher**, after Blaise de Vigenere who fully developed it at the end of the 16th century (though others had done a lot of preceding work). It is an example of a **polyalphabetic substitution cipher**: an encryption scheme where the ciphertext alphabet for a plaintext symbol changes depending on its position in the text. 

As encryption gained popularity in Renaissance Europe, so did **cryptanalysis**—that is, the breaking of ciphertexts—particularly, using **frequency analysis**. The latter employs statistical regularities in our language to break ciphertexts. Even the most sophisticated monoalphabetic substition ciphers were no longer sufficient against frequency analysis by the 1700s, particularly in military and security settings. As the Vigenere cipher offered a significant advancement in security, it became popular in this period and widespread by the late 1700s.

Informally speaking, the encryption scheme works as follows:

1.	Select a multi-letter word as the private key
2.	For any message, apply the shift cipher to each letter of the message using the corresponding letter in the key word as the shift
3.	If you have cycled through the key word but still have not totally enciphered the plaintext, again apply the key word’s letters as a shift cipher to the corresponding letters in the remainder of the text
4.	Continue this process until the entire message has been enciphered 

To illustrate, suppose that your private key is GOLD and you want to encrypt the message "CRYPTOGRAPHY". In that case you would proceed as follows according to the Vigenere cipher:

- c<sub>0</sub>  = [(2 + 6) Mod 26] = 8 = I
- c<sub>1</sub>  = [(17 + 14) Mod 26] = 5 = F
- c<sub>2</sub>  = [(24 + 11) Mod 26] = 9 = J
- c<sub>3</sub>  = [(15 + 3) Mod 26] = 18 = S
- c<sub>4</sub>  = [(19 + 6) Mod 26] = 25 = Z
- c<sub>5</sub>  = [(14 + 14) Mod 26] = 2 = C
- c<sub>6</sub>  = [(6 + 11) Mod 26] = 17 = R
- c<sub>7</sub>  = [(17 + 3) Mod 26] = 20 = U
- c<sub>8</sub>  = [(0 + 6) Mod 26] = 6 = G
- c<sub>9</sub>  = [(15 + 14) Mod 26] = 3 = D
- c<sub>10</sub> = [(7 + 11) Mod 26] = 18 = S
- c<sub>11</sub> = [(24 + 3) Mod 26] = 1 = B
- c = "IFJSZCRUGDSB"

Another famous example of a stream cipher is the **one-time pad**. With the one-time pad, you simply create a string of random bits as long as the plaintext message and produce the ciphertext via the XOR operation. Hence, the private key and the keystream are equivalent with a one-time pad. 

While the Shift cipher and Vigenere ciphers are very insecure in the modern age, the one-time pad is very secure. Probably the most famous application of the one-time pad was, at least until the 1980s, for the **Washington-Moscow hotline**.

The hotline is a direct communications link between Washington and Moscow for urgent matters that was installed after the Cuban Missile Crisis. The technology for the has transformed over the years. Currently, it includes a direct fiber optic cable as well as two satellite links (for redundancy), which enable e-mail and text messaging. The link ends in various places in the US. The Pentagon, the White House, and Raven Rock Mountain are known endpoints. Contrary to popular opinion, the hotline has never involved telephones.  

In essence, the one-time pad scheme worked as follows. Both Washington and Moscow would have two sets of random numbers. One set of random numbers, created by the Russians, pertained to encryption and decryption of any messages in the Russian language. One set of random numbers, created by the Americans, pertained to encryption and decryption of any messages in the English language. From time to time, more random numbers would be delived to the other side by trusted couriers. 

Washington and Moscow were, then, able to communicate secretly by using these random numbers for creating one-time pads. Each time you needed to communicate, you would use the next portion of random numbers for your message.

While highly secure, the one-time pad faces significant practical limitations: the key needs to be as long as the message and no part of a one-time pad can be re-used. This means that you need to keep track of where you are in the one-time pad, store a massive number of bits, and exchange random bits with your counterparties from time to time.   

Due the practical challenges of the one-time pad, the predominant stream ciphers used in practice are **pseudorandom stream ciphers**. Salsa20 and a closely related variant called ChaCha are examples of commonly used pseudorandom stream ciphers.

With these pseudorandom stream ciphers, you first randomly select a key K that is shorter than the length of the plaintext. Such a random key K is usually created by our computer on the basis of unpredictable data which it collects over time, such as the time between network messages, mouse movements, and so on. 

This random key K is then inserted into an expansion algorithm which creates a pseudorandom key stream as long as the message. You can specify precisely how long the keystream needs to be (e.g., 500 bits, 1000 bits, 1200 bits, 29,117 bits, and so on). 

A pseudorandom keystream appears *as if* it was chosen completely randomly from the set of all strings with the same length. Hence, encryption with a pseudorandom keystream appears as if it had been done with a one-time pad. But that is, of course, not the case. 

As our private key is shorter than the keystream and our expansionary algorithm needs to be deterministic in order for the encryption/decryption process to work, not every keystream of that particular length could have resulted as an output from our expansionary operation. 

Suppose, for instance, that our private key has a length of 128 bits and that we can insert it into an expansionary algorithm to create a much longer keystream, say of 2,500 bits. As our expansionary algorithm needs to be deterministic, our algorithm can at most select 1/2<sup>128</sup> strings with a length of 2,500 bits. So such a keystream could never be selected entirely randomly from all the strings of the same length. 

Our definition of a stream cipher has two aspects: (1) a keystream as long as the plaintext is generated with the aid of a private key; and (2) this keystream is combined with the plaintext, typically via an XOR operation, to produce the ciphertext. 

Sometimes people define condition (1) more strictly, by asserting that the keystream must specifically be pseudorandom. This means that neither the shift cipher, nor the one-time pad would be considered stream ciphers. 

In my view, defining condition (1) more broadly provides an easier way to organize encryption schemes. In addition, it means that we do not have to stop calling a particular encryption scheme a stream cipher just because we learn that it does not actually rely on pseudorandom keystreams. 


## Block ciphers

The first way that a **block cipher** is commonly understood is as something more primitive than a stream cipher: A core algorithm that performs a length-preserving transformation on a string of a suitable length with the aid of a key. This algorithm can be used for creating encryption schemes and perhaps other types of cryptographic schemes. 

Frequently, a block cipher can take input strings of varying lengths such as 64, 128, or 256 bits, as well as keys of varying lengths such as 128, 192, or 256 bits. Though some details of the algorithm might change depending on these variables, the core algorithm does not change. If it did, we would speak of two different block ciphers. Note that the use of the core algorithm terminology here is the same as for encryption schemes. 

A depiction of how a block cipher works can be seen in *Figure 4* below. A message M of length L and a key K serve as inputs to the Block cipher. It outputs a message M’ of length L. The key does not necessarily need to be the same length as M and M’ for most block ciphers. 

*Figure 4: A block cipher*

![Figure 4: A block cipher](/Images/Figure4-4.png "Figure 4: A block cipher")

A block cipher on its own is not an encryption scheme. But a block cipher can be used with various **modes of operation** to produce different encryption schemes. A mode of operation simply adds some additional operations outside the block cipher. 

To illustrate how this works, suppose a block cipher (BC) that requires a 128-bit input string and a 128-bit private key. Figure 5 below displays how that block cipher can be used with **electronic code book mode** (**ECB mode**) to create an encryption scheme. (The ellipses on the right indicate that you can repeat this pattern as long as is needed). 

*Figure 5: A block cipher with ECB mode*

![Figure 5: A block cipher with ECB mode](/Images/Figure4-5.png "Figure 5: A block cipher with ECB mode")

The process for electronic code book encryption with the block cipher is as follows. See if you can divide your plaintext message into 128-bit blocks. If not, add **padding** to the message, so that the result can be evenly divided by the block size of 128 bits. This is your data used for the encryption process.

Now split the data into chunks of 128-bit strings (M<sub>1</sub>, M<sub>2</sub>, M<sub>3</sub>, and so on). Run each 128-bit string through the block cipher with your 128-bit key to produce 128-bit chunks of ciphertext (C<sub>1</sub>, C<sub>2</sub>, C<sub>3</sub>, and so on). These chunks re-combined form the full ciphertext. 

Decryption is just the reverse process, although the recipient does need some recognizable way to strip any padding from the decrypted data to produce the original plaintext message.  

Though relatively straightforward, a block cipher with electronic code book mode lacks in security. This is because it leads to **deterministic encryption**. Any two identical 128-bit strings of data are encrypted exactly the same way. That information can be exploited.

Instead, any encryption scheme constructed from a block cipher should be **probabilistic**: that is, the encryption of any message M, or any specific chunk of M, should generally yield a different outcome each time.<sup>[4](#footnote4)</sup> 

The **cipher block chaining mode** (**CBC mode**) is probably the most common mode used with a block cipher. The combination, if done right, produces a probabilistic encryption scheme. You can see a depiction of this mode of operation in Figure 6 below. 

*Figure 6: A block cipher with CBC mode*

![Figure 6: A block cipher with CBC mode](/Images/Figure4-6.png "Figure 6: A block cipher with CBC mode")

Suppose the block size is again 128 bits. So to start, you would again need to assure that your original plaintext message receives the necessary padding.  

Then, you XOR the first 128-bit portion of your plaintext with an **initialization vector** of 128-bits. The result is placed into the block cipher to produce a ciphtertext for the first block. For the second block of 128 bits, you first XOR the plaintext with the ciphertext from the first block, before inserting it into the block cipher. You continue this process until you have encrypted your entire plaintext message. 

When finished, you send the encrypted message together with the unencrypted initialization vector to the recipient. The recipient needs to know the initialization vector, otherwise she cannot decrypt the ciphertext. 

This construction is much securer than electronic code book mode when used correctly. You should, first, ensure that the initialization vector is a random or pseudorandom string. In addition, you should use a different initialization vector each time you use this encryption scheme. 

In other words, your initialization vector should be a random or pseudorandom nonce, where a **nonce** stands for "a number that is only used once." If you keep this practice, then CBC mode with a block cipher ensures that any two identical plaintext blocks will generally be encrypted differently each time. 

Finally, lets turn our attention to **output feedback mode** (**OFB mode**). You can see a depiction of this mode in *Figure 7*.

*Figure 7: A block cipher with OFB mode*

![Figure 7: A block cipher with OFB mode](/Images/Figure4-7.png "Figure 7: A block cipher with OFB mode")

With OFB mode you also select an initialization vector. But here, for the first block, the initialization vector is directly inserted into the block cipher with your key. The resulting 128-bits are, then, treated as a keystream. This keystream is XORed with the plaintext to produce the ciphertext for the block. For subsequent blocks, you use the keystream from the previous block as an input into the block cipher and repeat the steps. 

If you look carefully, what has actually been created here from the block cipher with OFB mode is a stream cipher. You generate keystream portions of 128-bits until you have the length of the plaintext (discarding the bits you do not need from the last 128-bit keystream portion). You, then, XOR the keystream with your plaintext message to obtain the ciphertext. 

In the previous section on stream ciphers, I stated that you produce a keystream with the aid of a private key. To be exact, it does not only have to be created with a private key. As you can see in OFB mode, the keystream is produced with the support of both a private key and an initialization vector. 

Note that as with CBC mode, it is important to select a pseudorandom or random nonce for the initialization vector each time you use a block cipher in OFB mode. Otherwise, the same 128-bit message string sent in different communications will be encrypted in the same manner. This is one way to create probabilistic encryption with a stream cipher.

Some stream ciphers only use a private key to create a keystream. For those stream ciphers, it is important that you use a random nonce to select the private key for each instance of communication. Otherwise, the results of encryption with those stream ciphers will also be deterministic, leading to security issues. 

The most popular modern block cipher is the **Rijndael cipher**. It was the winning entry out of fifteen submissions to a competition held by the National Institute of Standards and Technology (NIST) between 1997 and 2000 in order to replace an older encryption standard, the **data encryption standard** (**DES**).

The Rijndael cipher can be used with different specifications for key lengths and block sizes, as well as in different modes of operation. The committee for the NIST competition adopted a constricted version of the Rijndael cipher—namely one which requires 128-bit block sizes and key lengths of either 128 bits, 192 bits, or 256 bits—as part of the **advanced encryption standard** (**AES**). This is really the main standard for symmetric encryption applications. It is so secure that even the NSA is apparently willing to use it with 256-bit keys for top secret documents.<sup>[5](#footnote5)</sup>

The AES block cipher will be explained in detail in *Chapter 5*. 


## Clearing up the confusion

The confusion about the distinction between block ciphers and stream ciphers arises because sometimes people will understand the term block cipher as referring specifically to a *block cipher with a block mode of encryption*. 

Consider the ECB and CBC modes in the previous section. These specifically require the data for encryption to be divisible by the block size (meaning you might have to use padding for the original message). In addition, the data in these modes is also operated on by the block cipher directly (and not just combined with the result of a block cipher operation as in OFB mode). 

Hence, alternatively, you can define a **block cipher** as any encryption scheme, which operates on fixed-length blocks of the message at a time (where any block must be longer than a byte, otherwise it collapses into a stream cipher). Both the data for encryption and the ciphertext must divide evenly into this block size. Typically, the block size is 64, 128, 192, or 256 bits in length. By contrast, a stream cipher can encrypt any messages in chunks of one bit or byte at a time. 

With this more specific understanding of block cipher, you can indeed claim that modern encryption schemes are either stream or block ciphers. From here on out, I will mean the term block cipher in the more general sense unless otherwise specified.  

The discussion on OFB mode in the previous section also raises another interesting point. Some stream ciphers are built from block ciphers, like Rijndael with OFB. Some like Salsa20 and ChaCha are not created from block ciphers. You might called the latter **primitive stream ciphers**. (There is not really a standardized term to refer to such stream ciphers.)

When people speak about the advantages and disadvantages of stream ciphers and block ciphers, they are typically comparing primitive stream ciphers to encryption schemes based on block ciphers.  

While you can always easily construct a stream cipher from a block cipher, it is typically very difficult to build some type of construct with a block mode of encryption (such as with CBC mode) from a primitive stream cipher. 

From this discussion, you should now understand *Figure 8*. It provides an overview on symmetric encryption schemes. We use three kinds of encryption schemes: primitive stream ciphers, block cipher stream ciphers, and block ciphers in a block mode (also called “block ciphers” in the diagram). 

*Figure 8: Overview of symmetric encryption schemes*

![Figure 8: Overview of symmetric encryption schemes](/Images/Figure4-8.png "Figure 8: Overview of symmetric encryption schemes")


## Message authentication codes

Encryption is concerned with secrecy. But cryptography is also concerned with broader themes, such as message integrity, authenticity, and non-repudiation. So called **message authentication codes** (MACs) are symmetric key cryptographic schemes that support authenticity and integrity in communications. 

Why is anything, but secrecy needed in communication? Suppose that Bob sends Alice a message using practically unbreakable encryption. Any attacker that intercepts this message will not be able to ascertain any significant insights regarding the contents. However, the attacker still has at least two other attack vectors available to her:

1. She could intercept the ciphertext, alter its contents, and send the altered ciphertext on to Alice.
2. She could block Bob’s message entirely and send her own created ciphertext. 

In both these cases, the attacker might not have any insights into the contents from ciphertexts (1) and (2). But she could still cause significant damage in this way. This is where message authentication codes become important.

Message authentication codes are defined loosely as symmetric cryptographic schemes with three algorithms: a key generation algorithm, a tag generation algorithm, and a verification algorithm. A secure MAC ensures that tags are **existentially unforgeable** for any attacker—that is, they cannot successfully create a tag on the message which verifies, unless they have the private key. 

Bob and Alice can combat the manipulation of a particular message using a MAC. Suppose for the moment that they do not care about secrecy. They only want to ensure that the message received by Alice was indeed from Bob and not changed in any way. 

The process is depicted in *Figure 9*. To use a MAC, they would first generate a private key K that is shared between the two of them. Bob creates a tag T for the message using the private key K. He, then, sends the message as well as the message tag to Alice. She can, then, verify that Bob indeed made the tag, by running the private key, the message, and the tag through a verification algorithm. 

*Figure 9: Overview of symmetric encryption schemes*

![Figure 9: Overview of symmetric encryption schemes](/Images/Figure4-9.png "Figure 9: Overview of symmetric encryption schemes")

Due to existential unforgeability, an attacker cannot alter the message M in any way or create a message of her own with a valid tag. This is so, even if the attacker observes the tags of many messages between Bob and Alice that uses the same private key. At most, an attacker could block Alice from receiving the message M (a problem which cryptography cannot address). 

A MAC guarantees that a message was actually created by Bob. This authenticity, automatically implies message integrity—that is, if Bob has created some message, then, ipso facto, it was not altered in any way by an attacker. So from here on out, any concern for authentication should be automatically understood to imply a concern for integrity. 

While I have drawn a distinction between message authenticity and integrity in my discussion, it is also common to use those terms as synonyms. They, then, refer to the idea of messages that were both created by a particular sender an not altered any way. In this spirit, message authentication codes are frequently also called **message integrity codes**. 


## Authenticated encryption

Typically, you would want to guarantee both secrecy and authenticity in communication and, hence, encryption schemes and MAC schemes are typically used together. 

An **authenticated encryption scheme** is a scheme that combines encryption with a MAC in a highly secure manner. Specifically, it has to meet the standards for existential unforgeability as well as a very strong notion of secrecy, namely one that is resistant to **chosen-ciphertext attacks**.<sup>[6](#footnote6)</sup> 

In order for an encryption scheme to be resistant to chosen-ciphertext attacks, it must meet the standards for **non-malleability**: that is, any modification of a ciphertext by an attacker should yield either an invalid ciphertext or one that decrypts to a plaintext having no relation to the original one.<sup>[7](#footnote7)</sup> 

As an authenticated encryption scheme ensures that a ciphertext created by an attacker is always invalid (as the tag will not be verified), it meets the standards for resistance to chosen-ciphertext attacks. Interestingly, you can prove that an authenticated encryption scheme can always be created from the combination of an existentially unforgeable MAC and an encryption scheme that meets a less stronger notion of security, known as **chosen-plaintext-attack security**.

We will not delve into all the details of constructing authenticated encryption schemes. But it is important to know two details of their construction. 

First, an authenticated encryption scheme first handles the encryption and then creates a message tag on the ciphertext. It turns out that other approaches—such as combining the ciphertext with a tag on the plaintext, or first creating a tag and then encrypting both the plaintext and the tag—are insecure. In addition, both operations have their own randomly selected private key, otherwise your security is severely compromised. 

The aforementioned principle applies more generally: *you should always use distinct keys when combining basic cryptographic schemes*.

An authenticated encryption scheme is depicted in *Figure 10*. Bob first creates a ciphertext C from the message M using a randomly selected key K<sub>C</sub>. He, then, creates a message tag T by running the ciphertext and a different randomly selected key K<sub>T</sub> through the tag generation algorithm. Both the ciphtertext and the message tag are sent to Alice. 

Alice now first checks whether the tag is valid given the ciphertext C and the key K<sub>T</sub>. If valid, she can, then, decrypt the message using the key K<sub>C</sub>. Not only is she assured of a very strong notion of secrecy in their communications, she also knows the message was created by Bob. 

*Figure 10: An authenticated encryption scheme*

![Figure 10: An authenticated encryption scheme](/Images/Figure4-10.png "Figure 10: An authenticated encryption scheme")


## HMAC

How are MACs created? While MACs can created via multiple methods, and common and efficient way to create them is via hash functions. 

.....Still to be completed.....




## Secure communication sessions

Suppose that two parties are in a communication session, so they send multiple messages back and forth. 

An authenticated encryption scheme allows a recipient of a message to verify that it was created by her partner in the a communication session (as long as the private key has not leaked). This works well enough for a single message. Typically, however, two parties are sending messages back and forth in a communication session. And in that setting, a plain authenticated encryption scheme as described in the previous section falls short in providing security.

The main reason is that an authenticated encryption scheme does not provide any guarantees that the message was actually also sent by the agent who created it within a communication session. Consider the following three attack vectors:

1. **Replay attack**: An attacker re-sends a ciphertext and a tag that she intercepted between two parties at an earlier point. 
2. **Re-ordering attack**: An attacker intercepts two messages at different times, and sends them on to the recipient in the reverse order.
3. **Reflection attack**: An attacker observes a message sent from A to B, and also sends that message to A. 

Though the attacker has no knowledge of the ciphertext and cannot create spoof ciphertexts, the attacks above can still cause significant damage in communications. 

Suppose, for instance, that a particular message between the two parties involves the transfer of financial funds. A replay attack might transfer the funds a second time. A vanilla authenticated encryption scheme has no defense against such attacks.

Fortunately, these kinds of attacks can be easily mitigated in a communication session using **identifiers** and **relative time indicators**.

Identifiers can be added to the plaintext message before encryption. This would bar any reflection attacks. A relative time indicator can, for example, be a sequence number in a particular communication session. Each party adds a sequence number to a message before encryption, so the recipient knows in what order the messages were sent. This eliminates the possibility of re-ordering attacks. It also eliminates replay attacks. Any message an attacker sends down the line will have an old sequence number, and the recipient will know not to process the message again. 

To illustrate how secure communication sessions work, suppose again Alice and Bob. They send a total of four messages back and forth. You can see how an authenticated encryption scheme with identifiers and sequence numbers would work below in *Figure 11*.

The communication session starts by Bob sending a ciphertext C<sub>0,B</sub> to Alice with a message tag T<sub>0,B</sub>. The ciphertext contains the message, as well as an identifier (BOB) and a sequence number (0). The tag T<sub>0,B</sub> is made over the entire ciphertext. In their subsequent communications, Alice and Bob maintain this protocol, updating fields as necessary. 

*Figure 11: A secure communication session*

![Figure 11: A secure communication session](/Images/Figure4-11.png "Figure 11: A secure communication sessesion")


## Notes

<a name="footnote1">1</a>. According to Seutonius, a shift cipher with a constant key value of 3 was used by Julius Caeser in his military communications. So A would always become D, B always E, C always F, and so on. This particular version of the Shift cipher has, thus, become known as the **Caesar Cipher** (though it is not really a cipher in the modern sense of the word, as the key value is constant). The Caesar cipher may have been secure in the first century BC, if Rome’s enemies were very unfamiliar with encryption. But it clearly would not be a very secure scheme in modern times.

<a name="footnote2">2</a>. Jonathan Katz and Yehuda Lindell, *Introduction to Modern Cryptography*, CRC Press (Boca Raton, FL: 2015), p. 7f. 

<a name="footnote3">3</a>. Internet Engineering Task Force, "Prohibiting RC4 Cipher Suites," RFC 7465, available at https://tools.ietf.org/html/rfc7465. 

<a name="footnote4">4</a>. The importance of probabilistic encryption was first emphasized by Shafi Goldwasser and Silvio Micali, “Probabilistic encryption,” *Journal of Computer and System Sciences*, 28 (1984), 270–99. 

<a name="footnote5">5</a>. See NSA, "Commercial National Security Algorithm Suite", https://apps.nsa.gov/iaarchive/programs/iad-initiatives/cnsa-suite.cfm.

<a name="footnote6">6</a>. The specific results discussed in this section are from Katz and Lindell, pp. 131–47.

<a name="footnote7">7</a>. Technically, the definition of chosen cipher text attacks is different than the notion of non-malleability. But you can show that those two notions of security are equivalent. 
