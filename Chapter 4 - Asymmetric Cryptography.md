# Asymmetric Cryptography

As with symmetric cryptography, asymmetric schemes can be used to ensure both secrecy and authentication. By contrast, however, these schemes employ two keys rather than one: a private and a public key.

We start our enquiry with the discovery of asymmetric cryptography, particularly the problems that spurred it on. Next, we discuss how asymmetric schemes for encryption and authentication work on a high level. We, then, introduce hash functions, which are key to understanding asymmetric authentication schemes, and also have relevance in other cryptographic contexts.  

To close this chapter, we will venture into more advanced territory. All of asymmetric cryptography relies on a few computationally hard problems. I will introduce such a problem at the end of this chapter, namely the RSA problem. I will, then, roughly illustrate how the RSA problem can be used to create encryption and authentication schemes.  

Adding this depth to our discussion is not an easy task. It requires introducing quite a few number-theoretic theorems and propositions. 

But don’t let the mathematics dissuade you. Working through this discussion will significantly improve your comprehension of what underlies asymmetric cryptography and is a worthwhile investment.  


## The key distribution and management problem

Suppose that Bob wants to buy a new rain coat from Jim’s Sporting Goods over the internet. This will be his first purchase from them and he wants to use his credit card. So, Bob will first need to create an account with Jim’s Sporting Goods, which requires sending over personal details such as his address and credit card information. He can, then, go through the steps needed to purchase the rain coat. 

Bob and Jim’s Sporting Goods will want to ensure that their communications are secure throughout this process, considering that the internet is an open communications system. They will want to ensure, for example, that no potential attacker can ascertain Bob’s credit card and address details, and that no potential attacker can repeat his purchases or create fake ones on his behalf. 

An advanced authenticated encryption scheme as discussed in the previous chapter could certainly make the communications between Bob and Jim’s Sporting Goods secure. But there clearly is a practical obstacle: How do they actually exchange a set of private keys (one for encryption and one for authentication)? 

Suppose for the moment that we lived in a world in which only the tools of symmetric cryptography existed. What could Jim’s Sporting Goods and Bob, then, do to ensure secure communication?

Under those circumstances, they would face substantial costs to communicating securely. As the Internet is an open communications system, they cannot just exchange a set of keys over it. Hence, Bob and a representative for Jim’s Sporting Goods will need to make a key exchange in person.  

One possibility is that Jim’s Sporting Goods creates special key exchange locations, where Bob and other new customers can retrieve a set of keys for secure communication. This would obviously come at substantial organizational costs and greatly reduce the efficiency with which new customers can make purchases.  

Alternatively, Jim’s Sporting Goods can send Bob a pair of keys with a highly trusted courier. This is probably more efficient than organizing key exchange locations. But this would still come at substantial costs, particularly if many customers only make one or a few purchases.   

Both key-exchange location and courier systems face an additional challenge. Jim’s Sporting Goods has millions of customers around the world and separate sets of keys are required for each of them. 

To understand this latter point, suppose that Jim’s Sporting Goods provides each customer the same pair of keys. This would allow each customer—or anyone else that could obtain this pair of keys—to read and even manipulate all the communications between Jim’s Sporting Goods and its customers. You might, then, as well not use cryptography at all in your communications. 

Even repeating a set of keys for only some customers, so you have fewer sets of keys that providing each customers a unique set of keys, is a terrible security practice. Any potential attacker could attempt to exploit that feature of the scheme (remember that attackers are assumed to know everything about a scheme but the keys, in accordance with Kerckhoffs’ principle.)

So, Jim’s Sporting Goods would have to store a pair of keys for each customer, regardless of their distribution system. This clearly presents various practical problems: 

- Jim’s Sporting Goods would have to store thousands of pairs of keys, one set for each customer.
- These keys would have to be properly secured, as they would be a sure fire target for hackers. Any breaches of security would require the repetition of costly key exchanges, either at special key exchange locations or by courier. 
- Any customer of Jim’s Sporting Goods would have to safely store a pair of keys. Losses and thefts will occur, requiring a repetition of key exchanges. Customers would also have to go through this process for any other online stores or other types of entities they wish to communicate with over the Internet.  

These two main challenges just described were very fundamental concerns until the late 1970s. They were known as the **key distribution problem** and the **key management problem**, respectively. 

These problems had always existed, of course, and often created headaches in the past. Military forces, for instance, would have to constantly distribute books with keys for secure communication to personnel in the field at great risks and costs. But these problems were becoming worse as the world was increasingly moving into one of long-distance, digital communication, particularly for non-governmental entities. 

If these problems had not been resolved in the 1970s, efficient and secure shopping at Jim’s Sporting Goods would likely not have existed. In fact, most of our modern world with practical and secure e-mailing, online banking, and shopping would probably be just a distant fantasy. Anything even resembling Bitcoin could not have existed at all. 

So, what happened in the 1970s? How is it possible that we can instantly make purchases online and securely browse the World Wide Web? How is it possible that we can instantaneously send Bitcoins all across the world from our smart phones? 


## New directions in cryptography

By the 1970s, the key distribution and key management problems had grabbed the attention of a group of American academic cryptographers: Whitfield Diffie, Martin Hellman, and Ralph Merkle. In the face of severe skepticism from the majority of their peers, they ventured to devise a solution to it. 

At least one primary motivation for their venture was the foresight that open computer communication would profoundly affect our world. As Diffie and Helmann note in 1976, 

> The development of computer controlled communication networks promises effortless and inexpensive contact between people or computers on opposite sides of the world, replacing  most mail and many excursions with telecommunications. For many applications these contacts must be made secure against both eavesdropping and the injection of illegitimate messages. At present, however, the solution of security problems lags well behind other areas of communications technology. *Contemporary cryptography is unable to meet the requirements, in that its use would impose such severe inconveniences on the system users, as to eliminate many of the benefits of teleprocessing.*<sup>[1](#footnote1)</sup>

The tenacity of Diffie, Hellman, and Merkle paid off. The first publication of their results was a paper by Diffie and Helmann in 1976 entitled “New Directions in Cryptography”. In it, they presented two original ways to address the key distribution and the key management problems. 

The first solution they offered was a remote *key-exchange protocol*, that is, a set of rules for the exchange one or more symmetric keys over an insecure communication channel. This protocol is now known as *Diffie-Helmann key exchange* or *Diffie-Helmann-Merkle key exchange*.<sup>[2](#footnote2)</sup>

With Diffie-Helmann key exchange, two parties first exchange some information publicly on an insecure channel such as the Internet. On the basis of that information they, then, independently create a symmetric key (or a pair of symmetric keys) for secure communication. While both parties create their keys independently, the information they shared publicly ensures that this key creation process yields the same result for the both of them. 

Importantly, while everyone can observe the information that is exchanged publicly by the parties over the insecure channel, only the two parties engaging in the information exchange can create symmetric keys from it.

This, of course, sounds completely counterintuitive. How could two parties exchange some information publicly that would allow only them to create symmetric keys from it? Why would anyone else observing the information exchange not be able to create the same keys?

It relies on some beautiful mathematics of course. Diffie-Helmann key exchange works via a one-way function with a trapdoor. Lets discuss the meaning of these two terms in turn.

Suppose that you are given some function f(x) and the result f(a) = y, where a is a particular value for x. We say that f(x) is a **one-way function** if it is easy to calculate the value y when given a and f(x), but it is computationally infeasible to calculate the value a when given y and f(x). The name one-way function, of course, stems from the fact that such a function is only practical to calculate in one direction.

Some one-way functions have what is known as a trapdoor. While it is practically impossible to calculate a given only y and f(x), there is a certain piece of information Z which makes calculating a from y computationally feasible in these one-way functions. This piece of information Z is known as the **trapdoor**. One-way functions that have a trapdoor are known as **trapdoor functions**.

We will not delve into the details of Diffie-Helmann key exchange here. But essentially each participant creates some information, of which a part is publicly shared and of which some remains secret. Each party, then, takes their secret piece of information and the public information shared by the other party to create a private key. And somewhat miraculously, both parties will end up with the same private key. 

Any party observing just the publicly shared information between the two parties in a Diffie Helmann key exchange is unable to replicate these calculations. They would need the private information from one of the two parties in order to do so. 

Although the basic version of Diffie-Helmann key exchange presented in the 1976 paper is not very secure, sophisticated versions of the basic protocol are certainly still in use today. Most importantly, every key exchange protocol in the latest version of the transport layer security protocol (version 1.3) is essentially an enriched version of the protocol presented by Diffie and Hellman in 1976. The transport layer security protocol is the predominant protocol for securely exchanging information formatted according to the hypertext transfer protocol (http), the standard for exchange Web content.  

Importantly, Diffie-Helmann key exchange is not an asymmetric scheme. Strictly speaking, it arguably belongs to the realm of symmetric key cryptography. But as both Diffie-Helmann key exchange and asymmetric cryptography rely on one-way number-theoretic functions with trapdoors, they are typically discussed together. 

The second way which Diffie and Helmann offered to address the key distribution and management problem in their 1976 paper was, of course, via asymmetric cryptography. 

In contrast to their presentation of Diffie-Hellman key exchange, they only provided the general contours of how asymmetric cryptographic schemes could plausibly be constructed. They did not offer any one-way function that could specifically fulfill the conditions needed for reasonable security in such schemes. 

A practical implementation of an asymmetric scheme was, however, found a year later by three different academic cryptographers and mathematicians: Ronald Rivest, Adi Shamir, and Leonard Adleman.<sup>[3](#footnote3)</sup> The cryptosystem they introduced became known as the **RSA cryptosystem** (after their last names).

The trapdoor functions used in asymmetric cryptography (and Diffie Helmann key exchange) are all related to two main **computationally hard problems**: prime factorization and the calculation of discrete logarithms. 

**Prime factorization** requires, as the name implies, breaking down an integer into its prime factors. The RSA problem is by far the most well-known example of a cryptosystem related to prime factorization. 

The **discrete logarithm problem** is a problem that occurs in cyclic groups. Given a generator in a particular cyclic group, it requires the calculation of the unique exponent needed to produce another element in the group from the generator. 

Asymmetric schemes are based on two main kinds of cyclic groups: multiplicative groups of integers and groups that include the points on elliptic curves. The original Diffie Helmann key exchange as presented in “New Directions in Cryptography” works with a cyclic multiplicative group of integers. Bitcoin’s elliptic curve digital signature algorithm is, as the name clearly implies, an example of an asymmetric scheme based on an elliptic curve cyclic group.  

Next, we will turn to a high-level overview of secrecy and authentication in the asymmetric setting. Before doing so, however, we do need to make a brief historical note. 

It now seems plausible that a group of British cryptographers and mathematicians working for the Government Communications Headquarters (GCHQ) had independently made the discoveries mentioned above a few years earlier. This group consisted of James Ellis, Clifford Cocks, and Malcolm Williamson.

According to their own accounts and that of GCHQ, it was James Ellis who first devised the concept of public key cryptography in 1969. Supposedly, Clifford Cocks then discovered the RSA cryptographic system in 1973, and Malcolm Williamson the concept of Diffie Helmann key exchange in 1974.<sup>[4](#footnote4)</sup> Their discoveries were, however, purportedly not revealed until 1997, given the secret nature of the work done at GCHQ. 


## Asymmetric encryption and authentication

An overview of asymmetric encryption with the help of Bob and Alice is provided in *Figure 1*.

Alice first creates a pair of keys, consisting of one public key (K<sub>P</sub>) and one private key (K<sub>S</sub>), where the “P” in K<sub>P</sub> stands for “public” and the “S” in K<sub>S</sub> for “secret”. She, then, distributes this public key freely to others. We will return to the details of this distribution process a little later. But for now assume that anyone, including Bob, can securely obtain Alice’s public key K<sub>P</sub>.

At some later point, Bob wants to write a message M to Alice. As it includes sensitive information, however, he wants the contents to remain secret for everyone but Alice. So, Bob first encrypts his message M using K<sub>P</sub>. He then sends the resulting ciphertext C to Alice, who decrypts C with K<sub>S</sub> to produce the original message M.

*Figure 1: Asymmetric encryption*

![Figure 1: Asymmetric encryption](/Images/Figure4-1.png "Figure 1: Asymmetric encryption")

Any adversary that listens in on Bob and Alice’s communication can observe C. She also knows K<sub>P</sub> and the encryption algorithm E(·). Importantly, however, this information does not allow the attacker to easily decrypt the ciphertext C. Decryption specifically requires K<sub>S</sub>, which the attacker does not possess.

Symmetric encryption schemes generally need to be secure against an attacker that can validly encrypt plaintext messages (known as chosen-ciphertext attack security). It is not designed, however, with the explicit purpose of allowing just anyone to generate valid ciphertexts. 

This is in stark contrast to an asymmetric encryption scheme, where its whole purpose is to allow anyone, including attackers, to produce valid ciphertexts. Asymmetric encryption schemes can, therefore, be labeled as **multiple access ciphers**.

To understand better what is happening, imagine that instead of sending a message electronically, Bob wanted to send a physical letter in secrecy. One way of ensuring secrecy would be for Alice to send a secure padlock to Bob, but keep the key to unlock it. After writing his letter, Bob could put the letter in a box and close it with Alice’s padlock. He could, then, send the locked box with the message to Alice who has the key to unlock it. 

While Bob is able to lock the padlock, neither he nor any other person who intercepts the box can undo the padlock if it is indeed secure. Only Alice can unlock it and see the contents of Bob’s letter because she has the key. 

An asymmetric encryption scheme is, roughly speaking, a digital version of this process. The padlock is akin to the public key and the padlock key is akin to the private key. Because the padlock is digital, however, it is much easier and not so costly for Alice to distribute it to anyone that might want to send secret messages to her.  

For authentication in the asymmetric setting, we use **digital signatures**. These, thus, have the same function as message authentication codes in the symmetric setting. An overview of digital signatures is provided in *Figure 2*.

Bob first creates a pair of keys, consisting of the public key (K<sub>P</sub>) and the private key (K<sub>S</sub>), and distributes his public key. When he wants to send an authenticated message to Alice, he first takes his message M and his private key to create a digital signature D. Bob, then, sends Alice his message together with the digital signature. Alice inserts the message, the public key, and the digital signature into a verification algorithm. This algorithm produces either true for a valid signature, or false for an invalid signature. 

A digital signature is, as the name clearly implies, the digital equivalent of a written signature on letters, contracts, and so on. In fact, a digital signature is usually much more secure. With some effort, you can falsify a written signature; a process made easier by the fact that written signatures are frequently not closely verified. A secure digital signature, however, is, just as a secure message authentication code, existentially unforgeable: that is, with a secure digital signature scheme, no one can feasibly create a signature for a message which passes the verification procedure, unless they have the private key. 

*Figure 2: Asymmetric authentication*

![Figure 2: Asymmetric authentication](/Images/Figure4-2.png "Figure 2: Asymmetric authentication")

As with asymmetric encryption, we see an interesting contrast between digital signatures and message authentication codes. For the latter, the verification algorithm can only be employed by one of the parties privy to the secure communication. This is because it requires a private key. In the asymmetric setting, however, anyone can verify a digital signature S made by Bob. 

All this makes digital signatures an extremely powerful tool. It forms the basis, for instance, of creating signatures on contracts that can be verified for legal purposes. If Bob had made a signature on a contract in the exchange above, Alice can show the message M, the contract, and the signature S to a court of law. The court of law can, then, verify the signature using Bob’s public key.<sup>[5](#footnote5)</sup>

For another example, digital signatures are an important aspect to secure software and software update distribution. This type of public verifiability could never be created using just message authentication codes. 

As a last example of the power of digital signatures, consider Bitcoin. One of the most common misconceptions about Bitcoin, particularly in the media, is that transactions are encrypted: they are not. Instead, Bitcoin transactions work with digital signatures for ensuring security.  

Bitcoins exist in batches called unspent transaction outputs (or UTXO’s). Suppose you receive three payments on a particular Bitcoin address for 2 bitcoins each. You technically do not now have 6 bitcoins on that address. Instead, you have three batches of 2 bitcoins that are locked by a cryptographic problem associated with that address. For any payment you make, you can use one, two, or all three of these batches, depending on how much you need for the transaction. 

The proof of ownership over unspent transaction outputs is usually shown via one or more digital signatures. Bitcoin works precisely because valid digital signatures on unspent transaction outputs are computationally infeasible to make unless you are in possession of the secret information required to make them. 

Currently, Bitcoin transactions transparently include all the information that needs to be verified by participants in the network, such as the origins of the unspent transaction outputs used in the transaction. While it is possible to hide some of that information and still allow for verification (as some alternative cryptocurrencies such as Monero do), this also creates particular security risks. 

Confusion sometimes arises over digital signatures and written signatures captured digitally. In the latter case, you capture an image of your written signature and paste it to an electronic document such as an employment contract. This, however, is not a digital signature in the cryptographic sense. The latter is just a long number that can only be produced by being in possession of a private key. 

Just as in the symmetric key setting, you can also use asymmetric encryption and authentication schemes together. Similar principles apply. First of all, you should use different private-public key pairs for encryption and making digital signatures. In addition, you should first encrypt a message and then authenticate. 

Importantly, in many applications asymmetric cryptography is not used throughout the entire communication process. Instead, it will typically only be used to *exchange symmetric keys* between the parties by which they will actually communicate.

This is the case, for instance, when you purchase goods online. Knowing the vendor’s public key, she can send you digitally signed messages which you can verify for their authenticity. On this basis, you can follow one of multiple protocols for exchanging symmetric keys to securely communicate.   

The main reason for the frequency of the aforementioned approach is that asymmetric cryptography is much less efficient than symmetric cryptography in producing a particular level of security. This is one reason why we still need symmetric key cryptography next to public cryptography. In addition, symmetric key cryptography is much more natural in particular applications such as a computer user encrypting their own data. 

So how exactly do digital signatures and public key encryption address the key distribution and key management problems? 

There is not one answer here. Asymmetric cryptography is a tool and there is not one way to employ that tool. But let’s take our earlier example from Jim’s Sporting Goods to show how the issues would typically be addressed in this example.  

To start, Jim’s Sporting Goods would probably approach a **certificate authority**, an organization that supports in public key distribution. The certificate authority would register some details about Jim’s Sporting Goods and grant it a public key. It would, then, send Jim’s Sporting Goods a certificate, known as a **TLS/SSL certificate**, with Jim’s Sporting Goods’s public key digitally signed using the certificate authority’s own public key. In this way, the certificate authority affirms that a particular public key indeed belongs to Jim’s Sporting Goods. 

The key to understanding this process with TLS/SSL certificates is that, while you will generally not have Jim’s Sporting Goods’s public key stored anywhere on your computer, the public keys of recognized certificate authorities are indeed stored in your browser or in your operating system. These are stored in what are called **root certificates**. 

Hence, when Jim’s Sporting Goods provides you with its TLS/SSL certificate, you can verify the certificate authority’s digital signature via a root certificate in your browser or operating system. If the signature is valid, you can be relatively sure that the public key on the certificate indeed belongs to Jim’s Sporting Goods. On this basis, it is easy to set up a protocol for secure communication with Jim’s Sporting Goods. 

Key distribution has now become vastly simpler for Jim’s Sporting Goods. It is not hard to see that key management has also become greatly simplified. Instead of having to store thousands of keys, Jim’s Sporting Goods merely needs to store a private key that allows it to make signatures for the public key on its SSL certificate. Each time a customer comes to Jim’s Sporting Goods’s site, they can establish a secure communication session from this public key. Customers also do not need to store any information (other than the public keys of recognized certificate authorities in their browser).  


## Hash functions

Hash functions are ubiquitous in cryptography. They are neither symmetric nor asymmetric schemes, but fall into a cryptographic category in their own right. 

We will not spend extensive time on hash functions, but do think its fitting to briefly introduce them here: Digital signatures are namely typically made over the hash value of some (encrypted) message, rather than the actual (encrypted) message. 

Lets start with defining a hash function. A **hash function** is any efficiently computable function that takes inputs of arbitrary size and yields fixed length outputs. 

A **cryptographic hash function** is just a hash function that is useful for applications in cryptography. The output of a cyptographic hash function is typically called the **hash**, **hash-value**, or **message digest**.

In the context of crypgraphy, a “hash function” typically refers to a cryptographic hash function. I will adopt that practice from here on out. 

An example of a popular hash function is **SHA-256** (secure hash algorithm 256). Regardless of the size of the input (e.g., 15 bits, 100 bits, or 10,000 bits), this function will yield a 256-bit hash value. Below you can see a few example outputs of the SHA-256 function.

* “Hello”: 185f8db32271fe25f561a6fc938b2e264306ec304eda518007d1764826381969
* “52398”: a3b14d2bf378c1bd47e7f8eaec63b445150a3d7a80465af16dd9fd319454ba90
* “Cryptography is fun”: 3cee2a5c7d2cc1d62db4893564c34ae553cc88623992d994e114e344359b146c

All the outputs are exactly 256 bits written out in hexadecimal format (each hex digit can be represented by four binary digits). So even if you had inserted Tolkien’s *The Lord of the Rings* book into the SHA-256 function, the output would still be 256 bits. 

Hash functions such as SHA-256 are employed to various ends in cryptography. Which properties are required from a hash function really depends on the context of a particular application. There are two main properties generally desired of hash functions in cryptography:<sup>[6](#footnote6)</sup>

1.	Collision-resistance
2.	Hiding

A hash function H is said to be **collision-resistant** if it is infeasible to find two values, x and y, such that x ≠ y, yet H(x) = H(y). 

Collision-resistant hash functions are important, for instance, in the verification of software. Suppose that you wanted to download the Windows release of Bitcoin Core 0.21.0 (a server application for processing Bitcoin network traffic). The main steps you would have to take, in order to verify the legitimacy of the software, are as follows: 

1.	You first need to download and import the public key of the release manager for Bitcoin Core, namely Wladimir van der Laan. You can find a copy of his public key on https://www.bitcoincore.org. 
2.	Next, you need to verify that this public key indeed belongs to Wladimir van der Laan, and ensure that he is a reliable person who releases trustworthy software. At least one step you should take is to verify that the public key you found on the Bitcoin Core web page is the same as the version published in various other locations. (Typically comparison of public keys is done by comparing a short hash of the public key known as a fingerprint.) 
3.	Next, you need to download the Windows executable for Bitcoin Core from their website. There will also be packages available for the MAC operating system and various types of Linux operating systems.  
4.	Next, you have to obtain the official SHA-256 hash for the Windows executable. This is included in a release file (also on the website), together with the hashes over all the other packages that were released. The release file will also contain Wladimir’s signature over all the SHA-256 hashes over the available packages included in the release.  
5.	 Next, you would need to calculate the SHA-256 hash of the Windows executable you downloaded from the Bitcoin Core website on your own computer. You, then, calculate this result with that for the official package hash for the Windows executable. They should be the same.
6.	Finally, you would have to verify that the digital signature over all the official package hashes in the release file indeed belongs Wladimir’s van der Laan’s public key. You can do this with an application such as GNU Privacy Guard (GPG).   

This process of software verification has two main benefits. First, it ensures that there were no errors in transmission while downloading from Bitcoin Core’s website. Second, it ensures that no attacker could have gotten you to download modified, malicious code, either by hacking the Bitcoin Core website or by intercepting traffic.

How exactly does the software verification process above protect against these issues?

If you diligently verified Wladimir’s public key, then you can be fairly certain the key is actually his and has not been compromised. Given that digital signatures have existential unforgeability, you know that only Wladimir could have made a digital signature over the official package hashes on the release file.

Suppose the signature on the release file you downloaded checks out. You can now compare the hash value you calculated locally for the Windows executable you downloaded with that included in the properly signed release file. As you know the SHA-256 hash function is collion resistant, a match means that your Windows executable is exactly the same as the official Windows executable. 

Lets now turn to the second common property of hash functions: hiding. Any hash function H is said to have the property of hiding, if, for any randomly selected x from a very large range, it is infeasable to find x when only given H(x). 

Below, you can see the SHA-256 output of a message I wrote. To ensure sufficient randomness, the message included a randomly generated string of characters at the end. Given that SHA-256 has the hiding property, no one would be able to decipher this message. 

* b194221b37fa4cd1cfce15aaef90351d70de17a98ee6225088b523b586c32ded

But I will not leave you in suspense until SHA-256 becomes weaker. The original message I wrote was as follows:

* “This is a very random message, or well kind of random. This beginning part is not, but I will end with some relatively random characters to ensure a very unpredictable message. XLWz4dVG3BxUWm7zQ9qS”. 

A common way in which hash functions with the hiding property are used is in password management. Any decent online account-based service such as Facebook or Google will not store your passwords directly to manage access to your account. Instead, they will only store a hash of that password. Each time you fill in your password on a browser, a hash is first calculated. Only that hash is sent to the service provider’s server and compared with the hash stored in the back-end database.

Password management via hashes, of course, only works if users actually choose difficult passwords. Selecting passwords such as “1234”, “mypassword”, or your birthday date will not provide any real security. Long lists of common passwords and their hashes exist which attackers can leverage if they ever obtain the hash of your password. If they know some of your personal details, they might also attempt some informed guesses. Hence, you always need long, secure passwords (preferably long, random strings from a password manager). 

Sometimes an application might need a hash function which has both collision resistance and hiding. But certainly not always. The software verification process we discussed, for example, only requires that the hash function displays collision-resistance, hiding is not all that important.  

While collision resistance and hiding are the main properties sought of hash functions in cryptography, in certain applications other types of properties might also be desirable. 


## The factoring problem

While symmetric cryptography is usually fairly intuitive for most people, this is typically not the case with asymmetric cryptography. Though you are likely comfortable with the high-level description offered in the previous sections, you are probably wondering what precisely one-way functions are and how they are used exactly to construct asymmetric schemes. 

The last three sections in this chapter will remove some of the mystery surrounding asymmetric cryptography, by looking more deeply into a specific example, namely the RSA cryptosystem. 

In this section, I will introduce the factorization problem on which the RSA problem is based. In the next section, we will cover a number of key results from number theory. In the last section, we will put this information together to explain the RSA problem, and how this can be used for creating asymmetric cryptographic schemes. Lets now first turn to the factoring problem. 

Whenever you multiply two numbers, say a and b, we refer to the numbers a and b as **factors**, and the result as the **product**. Attempting to write a number N into the multiplication of two or more factors is called **factorization** or **factoring**.<sup>[7](#footnote7)</sup> You can call any problem that requires this a **factorization problem**.

Around 2,500 years ago, the Greek mathematician Euclid of Alexandria discovered a key theorem about the factorization of integers. It is commonly called the **unique factorization theorem** and states the following: 

*Theorem 1*. Every integer N which is greater than 1 is either a prime number, or can be expressed as a product of prime factors. 

All the latter part of this statement means is that you can take any non-prime integer N greater than 1, and write it out as a multiplication of prime numbers. Below are several examples of non-prime integers written as the product of prime factors.  

* 18 = 2 • 3 • 3 = 2 • 3<sup>2</sup>
* 84 = 2 • 2 • 3 • 7 = 2<sup>2</sup> • 3 • 7
* 144 = 2 • 2 • 2 • 2 • 3 • 3 = 2<sup>4</sup> • 3<sup>2</sup>

For all three of the integers above, calculating their prime factors is relatively easy, even if you are only given N. You start with the smallest prime number, namely 2, and see how many times the integer N is divisible by it. You, then, move on to testing the divisibility of N by 3, 5, 7, and so forth. You continue this process until your integer N is written as the product of only prime numbers. 

Take, for instance, the integer 84. Below you can see the process for determining its prime factors. At each step we take out the smallest remaining prime factor (on the left) and determine the remainder term to be factored. We continue until the remainder term is also a prime number. At each step, the current factorization of 84 is displayed on the far right. 

* Prime factor = 2; remainder term = 42 	(84 = 2 • 42)
* Prime factor = 2:remainder term = 21 	(84 = 2 • 2 • 21)
* Prime factor = 3:remainder term = 7 		(84 = 2 • 2 • 3 • 7)
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

To start, suppose that the computer attempts to solve the problem by cycling through 1024 bit numbers, tests whether they are prime, and then assesses if they are factors of N. The set of prime numbers to be tested is then approximately 1.265 • 10<sup>305</sup>.<sup>[8](#footnote8)</sup>

Even if you take all the computers on the planet and have them attempt to find and test 1024-bit prime numbers, a 1 in a billion chance of successfully finding a prime factor of N would require a period of calculation much longer than the age of the Universe.    

Now in practice a computer can do better than the rough procedure just described. There exist several algorithms that the computer can apply to come to a factorization more quickly. The point, however, is that even using these more efficient algorithms, the computer’s task is still computationally infeasible.<sup>[9](#footnote9)</sup>

Importantly, the difficulty of the factorization under the conditions just described rests on the assumption that there are no computationally efficient algorithms for calculating prime factors. We cannot actually prove that an efficient algorithm does not exist. Nevertheless, this assumption is very plausible: despite extensive efforts spanning hundreds of years, we have yet to find such a computationally efficient algorithm. 

Hence, the factorization problem, under certain circumstances, can plausibly be assumed to be a hard problem. Specifically, when p and q are very large prime numbers, their product N is not difficult to calculate; but factorization only given N is practically impossible. 


## Number theoretic results

Unfortunately, the factoring problem cannot be used directly for asymmetric cryptographic schemes. However, we can use a more complex but related problem to this effect: the RSA problem. 

To understand the RSA problem, we will need to understand a number of theorems and propositions from number theory. These are presented in this section in three subsections: (1) the order of N, (2) invertibility modulo N, and (3) Euler’s theorem.

Some of the material in the three subsections has already been introduced in *Chapter 2*. But I will here restate that material for convenience.   


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

*Proposition 4*. An inverse exists of an integer a given reduction modulo N, and specifically a unique positive inverse less than N, if and only if the greatest common divisor between a and N is 1. 

For the case when a = 5 and N =11, we concluded that a<sup>-1</sup> = 9, given that 5 x 9 mod 11 = 45 mod 11 = 1 mod 11. It is important to note that the reverse is also true. That is, when a = 9 and N = 11, it is the case that a<sup>-1</sup> = 5. 


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

The problem is we do not know the value d, as it is not given in the problem. Hence, we cannot directly calculate y<sup>d</sup> mod N to produce x mod N. 

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

Nevertheless, with adding a bit of clever complexity, the RSA problem can be used to create a secure public key encryption scheme as well as a secure digital signature scheme. We will not enter into the details of such constructions here.<sup>[10](#footnote10)</sup> Importantly, however, this additional complexity does not change the fundamental underlying RSA problem on which these schemes are based.


## Notes

<a name="footnote1">1</a>. Whitfield Diffie and Martin Hellman, “New directions in cryptography,” *IEEE Transactions on Information Theory* IT-22 (1976), pp. 644–654, at p. 644. 

<a name="footnote2">2</a>. Ralph Merkle also discusses a key exchange protocol in “Secure communications over insecure channels”, *Communications of the Association for Computing Machinery*, 21 (1978), 294–99. While Merkle actually submitted this paper before the paper by Diffie and Hellman, it was published later. Merkle’s solution is not exponentially secure, unlike Diffie-Hellman’s. 

<a name="footnote3">3</a>. Ron Rivest, Adi Shamir, and Leonard Adleman, “A method for obtaining digital signatures and public-key cryptosystems”, *Communications of the Association for Computing Machinery*, 21 (1978), pp. 120–26.

<a name="footnote4">4</a>. A good history of these discoveries is provided by Simon Singh, *The Code Book*, Fourth Estate (London, 1999), Chapter 6.

<a name="footnote5">5</a>. Any schemes attempting to achieve non-repudiation, the other theme we discussed in *Chapter 1*, will at its basis need to involve digital signatures.

<a name="footnote6">6</a>. The “hiding” terminology is not common language, but taken specifically from Arvind Narayanan, Joseph Bonneau, Edward Felten, Andrew Miller, and Steven Goldfeder, *Bitcoin and Cryptocurrency Technologies*, Princeton University Press (Princeton, 2016), Chapter 1.

<a name="footnote7">7</a>. Factorization can also be important for working with other types of mathematical objects than numbers. For instance, it can be useful to factor polynomial expressions such x<sup>2</sup> – 2x + 1. In our discussion, we will only focus on the factorization of numbers, specifically integers.

<a name="footnote8">8</a>. According to the prime number theorem, the number of primes less than or equal to N is approximately N/ln⁡(N). This means that you can approximate the number of primes of length 1024 bits by 2<sup>1024</sup>/ln⁡(2<sup>1024</sup>) - 2<sup>1023</sup>/ln⁡(2<sup>1023</sup>) which equals approximately 1.265 x 10<sup>305</sup>.

<a name="footnote9">9</a>. The same is true for discrete logarithm problems. Hence, why asymmetric constructions work with much larger keys than symmetric cryptographic constructions. 

<a name="footnote10">10</a>. See, for example, Jonathan Katz and Yehuda Lindell, *Introduction to Modern Cryptography*, CRC Press (Boca Raton, FL: 2015), pp. 410–32 on RSA encryption and pp. 444–41 for RSA digital signatures. 
