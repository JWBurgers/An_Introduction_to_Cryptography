# Chapter 6: Asymmetric Cryptography

As with symmetric cryptography, asymmetric schemes can be used to ensure both secrecy and authentication. By contrast, however, these schemes employ two keys rather than one: a private and a public key.

We start our enquiry with the discovery of asymmetric cryptography, particularly the problems that spurred it on. Next, we discuss how asymmetric schemes for encryption and authentication work on a high level. We, then, introduce hash functions, which are key to understanding asymmetric authentication schemes, and also have relevance in other cryptographic contexts, such as for the hash-based message authentication codes we discussed in Chapter 4.   


## The key distribution and management problem

Suppose that Bob wants to buy a new rain coat from Jim’s Sporting Goods, an online sporting goods retailer with millions of customers in North America. This will be his first purchase from them and he wants to use his credit card. So, Bob will first need to create an account with Jim’s Sporting Goods, which requires sending over personal details such as his address and credit card information. He can, then, go through the steps needed to purchase the rain coat. 

Bob and Jim’s Sporting Goods will want to ensure that their communications are secure throughout this process, considering that the Internet is an open communications system. They will want to ensure, for example, that no potential attacker can ascertain Bob’s credit card and address details, and that no potential attacker can repeat his purchases or create fake ones on his behalf. 

An advanced authenticated encryption scheme as discussed in the previous chapter could certainly make the communications between Bob and Jim’s Sporting Goods secure. But there are clearly practical obstacles to implementing such a scheme. 

To illustrate these practical obstacles, suppose that we lived in a world in which only the tools of symmetric cryptography existed. What could Jim’s Sporting Goods and Bob, then, do to ensure secure communication?

Under those circumstances, they would face substantial costs to communicating securely. As the Internet is an open communications system, they cannot just exchange a set of keys over it. Hence, Bob and a representative for Jim’s Sporting Goods will need to make a key exchange in person.  

One possibility is that Jim’s Sporting Goods creates special key exchange locations, where Bob and other new customers can retrieve a set of keys for secure communication. This would obviously come at substantial organizational costs and greatly reduce the efficiency with which new customers can make purchases.  

Alternatively, Jim’s Sporting Goods can send Bob a pair of keys with a highly trusted courier. This is probably more efficient than organizing key exchange locations. But this would still come at substantial costs, particularly if many customers only make one or a few purchases.   

Next, a symmetric scheme for authenticated encryption also forces Jim’s Sporting Goods to store seperate sets of keys for all their customers. This would be a signficant practical challenge for thousands of customers, let alone millions.  

To understand this latter point, suppose that Jim’s Sporting Goods provides each customer the same pair of keys. This would allow each customer—or anyone else that could obtain this pair of keys—to read and even manipulate all the communications between Jim’s Sporting Goods and its customers. You might, then, as well not use cryptography at all in your communications. 

Even repeating a set of keys for only some customers is a terrible security practice. Any potential attacker could attempt to exploit that feature of the scheme (remember that attackers are assumed to know everything about a scheme but the keys, in accordance with Kerckhoffs’ principle.)

So, Jim’s Sporting Goods would have to store a pair of keys for each customer, regardless of how these key pairs are distributed. This clearly presents several practical problems. 

- Jim’s Sporting Goods would have to store millions of pairs of keys, one set for each customer.
- These keys would have to be properly secured, as they would be a sure fire target for hackers. Any breaches of security would require the repetition of costly key exchanges, either at special key exchange locations or by courier. 
- Any customer of Jim’s Sporting Goods would have to safely store a pair of keys at home. Losses and thefts will occur, requiring a repetition of key exchanges. Customers would also have to go through this process for any other online stores or other types of entities they wish to communicate and transact with over the Internet.  

These two main challenges just described were very fundamental concerns until the late 1970s. They were known as the **key distribution problem** and the **key management problem**, respectively. 

These problems had always existed, of course, and often created headaches in the past. Military forces, for instance, would have to constantly distribute books with keys for secure communication to personnel in the field at great risks and costs. But these problems were becoming worse as the world was increasingly moving into one of long-distance, digital communication, particularly for non-governmental entities. 

If these problems had not been resolved in the 1970s, efficient and secure shopping at Jim’s Sporting Goods would likely not have existed. In fact, most of our modern world with practical and secure e-mailing, online banking, and shopping would probably be just a distant fantasy. Anything even resembling Bitcoin could not have existed at all. 

So, what happened in the 1970s? How is it possible that we can instantly make purchases online and securely browse the World Wide Web? How is it possible that we can instantaneously send Bitcoins all across the world from our smart phones? 


## New directions in cryptography

By the 1970s, the key distribution and key management problems had grabbed the attention of a group of American academic cryptographers: Whitfield Diffie, Martin Hellman, and Ralph Merkle. In the face of severe skepticism from the majority of their peers, they ventured to devise a solution to it. 

At least one primary motivation for their venture was the foresight that open computer communications would profoundly affect our world. As Diffie and Helmann note in 1976, 

> The development of computer controlled communication networks promises effortless and inexpensive contact between people or computers on opposite sides of the world, replacing  most mail and many excursions with telecommunications. For many applications these contacts must be made secure against both eavesdropping and the injection of illegitimate messages. At present, however, the solution of security problems lags well behind other areas of communications technology. *Contemporary cryptography is unable to meet the requirements, in that its use would impose such severe inconveniences on the system users, as to eliminate many of the benefits of teleprocessing.*<sup>[1](#footnote1)</sup>

The tenacity of Diffie, Hellman, and Merkle paid off. The first publication of their results was a paper by Diffie and Helmann in 1976 entitled “New Directions in Cryptography.” In it, they presented two original ways to address the key distribution and the key management problems. 

The first solution they offered was a remote *key-exchange protocol*, that is, a set of rules for the exchange of one or more symmetric keys over an insecure communication channel. This protocol is now known as *Diffie-Helmann key exchange* or *Diffie-Helmann-Merkle key exchange*.<sup>[2](#footnote2)</sup>

With Diffie-Helmann key exchange, two parties first exchange some information publicly on an insecure channel such as the Internet. On the basis of that information they, then, independently create a symmetric key (or a pair of symmetric keys) for secure communication. While both parties create their keys independently, the information they shared publicly ensures that this key creation process yields the same result for the both of them. 

Importantly, while everyone can observe the information that is exchanged publicly by the parties over the insecure channel, only the two parties engaging in the information exchange can create symmetric keys from it.

This, of course, sounds completely counterintuitive. How could two parties exchange some information publicly that would allow only them to create symmetric keys from it? Why would anyone else observing the information exchange not be able to create the same keys?

It relies on some beautiful mathematics of course. Diffie-Helmann key exchange works via a one-way function with a trapdoor. Lets discuss the meaning of these two terms in turn.

Suppose that you are given some function f(x) and the result f(a) = y, where a is a particular value for x. We say that f(x) is a **one-way function** if it is easy to calculate the value y when given a and f(x), but it is computationally infeasible to calculate the value a when given y and f(x). The name one-way function, of course, stems from the fact that such a function is only practical to calculate in one direction.

Some one-way functions have what is known as a trapdoor. While it is practically impossible to calculate a given only y and f(x), there is a certain piece of information Z which makes calculating a from y computationally feasible. This piece of information Z is known as the **trapdoor**. One-way functions that have a trapdoor are known as **trapdoor functions**.

We will not delve into the details of Diffie-Helmann key exchange here. But essentially each participant creates some information, of which a part is publicly shared and of which some remains secret. Each party, then, takes their secret piece of information and the public information shared by the other party to create a private key. And somewhat miraculously, both parties will end up with the same private key. 

Any party observing just the publicly shared information between the two parties in a Diffie Helmann key exchange is unable to replicate these calculations. They would need the private information from one of the two parties in order to do so. 

Although the basic version of Diffie-Helmann key exchange presented in the 1976 paper is not very secure, sophisticated versions of the basic protocol are certainly still in use today. Most importantly, every key exchange protocol in the latest version of the transport layer security protocol (version 1.3) is essentially an enriched version of the protocol presented by Diffie and Hellman in 1976. The transport layer security protocol is the predominant protocol for securely exchanging information formatted according to the hypertext transfer protocol (http), the standard for exchanging Web content.  

Importantly, Diffie-Helmann key exchange is not an asymmetric scheme. Strictly speaking, it arguably belongs to the realm of symmetric key cryptography. But as both Diffie-Helmann key exchange and asymmetric cryptography rely on one-way number-theoretic functions with trapdoors, they are typically discussed together. 

The second way which Diffie and Helmann offered to address the key distribution and management problem in their 1976 paper was, of course, via asymmetric cryptography. 

In contrast to their presentation of Diffie-Hellman key exchange, they only provided the general contours of how asymmetric cryptographic schemes could plausibly be constructed. They did not offer any one-way function that could specifically fulfill the conditions needed for reasonable security in such schemes. 

A practical implementation of an asymmetric scheme was, however, found a year later by three different academic cryptographers and mathematicians: Ronald Rivest, Adi Shamir, and Leonard Adleman.<sup>[3](#footnote3)</sup> The cryptosystem they introduced became known as the **RSA cryptosystem** (after their last names).

The trapdoor functions used in asymmetric cryptography (and Diffie Helmann key exchange) are all related to two main **computationally hard problems**: prime factorization and the calculation of discrete logarithms. 

**Prime factorization** requires, as the name implies, breaking down an integer into its prime factors. The RSA problem is by far the most well-known example of a cryptosystem related to prime factorization. 

The **discrete logarithm problem** is a problem that occurs in cyclic groups. Given a generator in a particular cyclic group, it requires the calculation of the unique exponent needed to produce another element in the group from the generator. 

Discrete logarithm-based schemes rely on two main kinds of cyclic groups: multiplicative groups of integers and groups that include the points on elliptic curves. The original Diffie Helmann key exchange as presented in “New Directions in Cryptography” works with a cyclic multiplicative group of integers. Bitcoin’s digital signature algorithm and recently introduced Schnorr signature scheme (2021) are both based on the discrete logarithm problem for a particular elliptic curve cyclic group.  

Next, we will turn to a high-level overview of secrecy and authentication in the asymmetric setting. Before doing so, however, we do need to make a brief historical note. 

It now seems plausible that a group of British cryptographers and mathematicians working for the Government Communications Headquarters (GCHQ) had independently made the discoveries mentioned above a few years earlier. This group consisted of James Ellis, Clifford Cocks, and Malcolm Williamson.

According to their own accounts and that of GCHQ, it was James Ellis who first devised the concept of public key cryptography in 1969. Supposedly, Clifford Cocks then discovered the RSA cryptographic system in 1973, and Malcolm Williamson the concept of Diffie Helmann key exchange in 1974.<sup>[4](#footnote4)</sup> Their discoveries were, however, purportedly not revealed until 1997, given the secret nature of the work done at GCHQ. 


## Asymmetric encryption and authentication

An overview of asymmetric encryption with the help of Bob and Alice is provided in *Figure 1*.

Alice first creates a pair of keys, consisting of one public key (K<sub>P</sub>) and one private key (K<sub>S</sub>), where the “P” in K<sub>P</sub> stands for “public” and the “S” in K<sub>S</sub> for “secret”. She, then, distributes this public key freely to others. We will return to the details of this distribution process a little later. But for now assume that anyone, including Bob, can securely obtain Alice’s public key K<sub>P</sub>.

At some later point, Bob wants to write a message M to Alice. As it includes sensitive information, however, he wants the contents to remain secret for everyone but Alice. So, Bob first encrypts his message M using K<sub>P</sub>. He then sends the resulting ciphertext C to Alice, who decrypts C with K<sub>S</sub> to produce the original message M.

*Figure 1: Asymmetric encryption*

![Figure 1: Asymmetric encryption](/Images/Figure6-1.png "Figure 1: Asymmetric encryption")

Any adversary that listens in on Bob and Alice’s communication can observe C. She also knows K<sub>P</sub> and the encryption algorithm E(·). Importantly, however, this information does not allow the attacker to feasibly decrypt the ciphertext C. Decryption specifically requires K<sub>S</sub>, which the attacker does not possess.

Symmetric encryption schemes generally need to be secure against an attacker that can validly encrypt plaintext messages (known as chosen-ciphertext attack security). It is not designed, however, with the explicit purpose of allowing the creation of such valid ciphertexts by an attacker or anyone else. 

This is in stark contrast to an asymmetric encryption scheme, where its whole purpose is to allow anyone, including attackers, to produce valid ciphertexts. Asymmetric encryption schemes can, therefore, be labeled as **multiple access ciphers**.

To understand better what is happening, imagine that instead of sending a message electronically, Bob wanted to send a physical letter in secrecy. One way of ensuring secrecy would be for Alice to send a secure padlock to Bob, but keep the key to unlock it. After writing his letter, Bob could put the letter in a box and close it with Alice’s padlock. He could, then, send the locked box with the message to Alice who has the key to unlock it. 

While Bob is able to lock the padlock, neither he nor any other person who intercepts the box can undo the padlock if it is indeed secure. Only Alice can unlock it and see the contents of Bob’s letter because she has the key. 

An asymmetric encryption scheme is, roughly speaking, a digital version of this process. The padlock is akin to the public key and the padlock key is akin to the private key. Because the padlock is digital, however, it is much easier and not so costly for Alice to distribute it to anyone that might want to send secret messages to her.  

For authentication in the asymmetric setting, we use **digital signatures**. These, thus, have the same function as message authentication codes in the symmetric setting. An overview of digital signatures is provided in *Figure 2*.

Bob first creates a pair of keys, consisting of the public key (K<sub>P</sub>) and the private key (K<sub>S</sub>), and distributes his public key. When he wants to send an authenticated message to Alice, he first takes his message M and his private key to create a digital signature D. Bob, then, sends Alice his message together with the digital signature. Alice inserts the message, the public key, and the digital signature into a verification algorithm. This algorithm produces either true for a valid signature, or false for an invalid signature. 

A digital signature is, as the name clearly implies, the digital equivalent of a written signature on letters, contracts, and so on. In fact, a digital signature is usually much more secure. With some effort, you can falsify a written signature; a process made easier by the fact that written signatures are frequently not closely verified. A secure digital signature, however, is, just as a secure message authentication code, **existentially unforgeable**: that is, with a secure digital signature scheme, no one can feasibly create a signature for a message which passes the verification procedure, unless they have the private key. 

*Figure 2: Asymmetric authentication*

![Figure 2: Asymmetric authentication](/Images/Figure6-2.png "Figure 2: Asymmetric authentication")

As with asymmetric encryption, we see an interesting contrast between digital signatures and message authentication codes. For the latter, the verification algorithm can only be employed by one of the parties privy to the secure communication. This is because it requires a private key. In the asymmetric setting, however, anyone can verify a digital signature S made by Bob. 

All this makes digital signatures an extremely powerful tool. It forms the basis, for instance, of creating signatures on contracts that can be verified for legal purposes. If Bob had made a signature on a contract in the exchange above, Alice can show the message M, the contract, and the signature S to a court of law. The court of law can, then, verify the signature using Bob’s public key.<sup>[5](#footnote5)</sup>

For another example, digital signatures are an important aspect to secure software and software update distribution. This type of public verifiability could never be created using just message authentication codes. 

As a last example of the power of digital signatures, consider Bitcoin. One of the most common misconceptions about Bitcoin, particularly in the media, is that transactions are encrypted: they are not. Instead, Bitcoin transactions work with digital signatures for ensuring security.  

Bitcoins exist in batches called unspent transaction outputs (or UTXO’s). Suppose you receive three payments on a particular Bitcoin address for 2 bitcoins each. You technically do not now have 6 bitcoins on that address. Instead, you have three batches of 2 bitcoins that are locked by a cryptographic problem associated with that address. For any payment you make, you can use one, two, or all three of these batches, depending on how much you need for the transaction. 

The proof of ownership over unspent transaction outputs is usually shown via one or more digital signatures. Bitcoin works precisely because valid digital signatures on unspent transaction outputs are computationally infeasible to make, unless you are in possession of the secret information required to make them. 

Currently, Bitcoin transactions transparently include all the information that needs to be verified by participants in the network, such as the origins of the unspent transaction outputs used in the transaction. While it is possible to hide some of that information and still allow for verification (as some alternative cryptocurrencies such as Monero do), this also creates particular security risks. 

Confusion sometimes arises over digital signatures and written signatures captured digitally. In the latter case, you capture an image of your written signature and paste it to an electronic document such as an employment contract. This, however, is not a digital signature in the cryptographic sense. The latter is just a long number that can only be produced by being in possession of a private key. 

Just as in the symmetric key setting, you can also use asymmetric encryption and authentication schemes together. Similar principles apply. First of all, you should use different private-public key pairs for encryption and making digital signatures. In addition, you should first encrypt a message and then authenticate it. 

Importantly, in many applications asymmetric cryptography is not used throughout the entire communication process. Instead, it will typically only be used to *exchange symmetric keys* between the parties by which they will actually communicate.

This is the case, for instance, when you purchase goods online. Knowing the vendor’s public key, she can send you digitally signed messages which you can verify for their authenticity. On this basis, you can follow one of multiple protocols for exchanging symmetric keys to securely communicate.   

The main reason for the frequency of the aforementioned approach is that asymmetric cryptography is much less efficient than symmetric cryptography in producing a particular level of security. This is one reason why we still need symmetric key cryptography next to public cryptography. In addition, symmetric key cryptography is much more natural in particular applications such as a computer user encrypting their own data. 

So how exactly do digital signatures and public key encryption address the key distribution and key management problems? 

There is not one answer here. Asymmetric cryptography is a tool and there is not one way to employ that tool. But let’s take our earlier example from Jim’s Sporting Goods to show how the issues would typically be addressed in this example.  

To start, Jim’s Sporting Goods would probably approach a **certificate authority**, an organization that supports in public key distribution. The certificate authority would register some details about Jim’s Sporting Goods and grant it a public key. It would, then, send Jim’s Sporting Goods a certificate, known as a **TLS/SSL certificate**, with Jim’s Sporting Goods’s public key digitally signed using the certificate authority’s own public key. In this way, the certificate authority affirms that a particular public key indeed belongs to Jim’s Sporting Goods. 

The key to understanding this process with TLS/SSL certificates is that, while you will generally not have Jim’s Sporting Goods’s public key stored anywhere on your computer, the public keys of recognized certificate authorities are indeed stored in your browser or in your operating system. These are stored in what are called **root certificates**. 

Hence, when Jim’s Sporting Goods provides you with its TLS/SSL certificate, you can verify the certificate authority’s digital signature via a root certificate in your browser or operating system. If the signature is valid, you can be relatively sure that the public key on the certificate indeed belongs to Jim’s Sporting Goods. On this basis, it is easy to set up a protocol for secure communication with Jim’s Sporting Goods. 

Key distribution has now become vastly simpler for Jim’s Sporting Goods. It is not hard to see that key management has also become greatly simplified. Instead of having to store thousands of keys, Jim’s Sporting Goods merely needs to store a private key that allows it to make signatures for the public key on its SSL certificate. Each time a customer comes to Jim’s Sporting Goods’s site, they can establish a secure communication session from this public key. Customers also do not need to store any information (other than the public keys of recognized certificate authorities in their operating system and browser).  


## Hash functions

Hash functions are ubiquitous in cryptography. They are neither symmetric nor asymmetric schemes, but fall into a cryptographic category in their own right. 

We already came across hash functions in Chapter 4 with the creation of hash-based authentication messages. They are also important in the context of digital signatures, though for a slightly different reason: Digital signatures are namely typically made over the hash value of some (encrypted) message, rather than the actual (encrypted) message. In this section, I will offer a more thorough introduction of hash functions. 

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

1.	You first need to download and import the public keys of one or more contributors Bitcoin Core into software that can verify digital signatures (e.g. Kleopetra). You can find these public keys [here](https://github.com/bitcoin/bitcoin/blob/master/contrib/builder-keys/keys.txt). It is recommended that you verify the Bitcoin Core software with the public keys from multiple contributors.  
2.	Next, you need to verify the public keys which you imported. At least one step you should take is to verify that the public keys you found are the same as published in various other locations. You might, for instance, consult the personal web pages, Twitter pages, or Github pages of the people whose public keys you imported. Typically this comparison of public keys is done by comparing a short hash of the public key known as a fingerprint. 
3.	Next, you need to download the executable for Bitcoin Core from their [website](www.bitcoincore.org). There will be packages available for Linux, Windows, and MAC operating systems.  
4.	Next, you have to locate two release files. The first one contains the official SHA-256 hash for the executable you downloaded together with the hashes over all the other packages that were released. Another release file will contain the signatures from various contributors over release file with the package hashes. Both these release files should be located on the Bitcoin Core website. 
5.	 Next, you would need to calculate the SHA-256 hash of the executable you downloaded from the Bitcoin Core website on your own computer. You, then, compare this result with that for the official package hash for the executable. They should be the same.
6.	Finally, you would have to verify that one or more of the digital signatures over the release file with the official package hashes indeed corresponds to one or more public keys you imported (releases of Bitcoin Core are not always signed by everyone). You can do this with an application such as Kleopetra.   

This process of software verification has two main benefits. First, it ensures that there were no errors in transmission while downloading from Bitcoin Core’s website. Second, it ensures that no attacker could have gotten you to download modified, malicious code, either by hacking the Bitcoin Core website or by intercepting traffic.

How exactly does the software verification process above protect against these issues?

If you diligently verified the public keys you imported, then you can be fairly certain these keys is actually theirs and have not been compromised. Given that digital signatures have existential unforgeability, you know that only these contributors could have made a digital signature over the official package hashes on the release file.

Suppose the signatures on the release file you downloaded check out. You can now compare the hash value you calculated locally for the Windows executable you downloaded with that included in the properly signed release file. As you know the SHA-256 hash function is collion resistant, a match means that your executable is exactly the same as the official executable. 

Lets now turn to the second common property of hash functions: hiding. Any hash function H is said to have the property of hiding, if, for any randomly selected x from a very large range, it is infeasable to find x when only given H(x). 

Below, you can see the SHA-256 output of a message I wrote. To ensure sufficient randomness, the message included a randomly generated string of characters at the end. Given that SHA-256 has the hiding property, no one would be able to decipher this message. 

* b194221b37fa4cd1cfce15aaef90351d70de17a98ee6225088b523b586c32ded

But I will not leave you in suspense until SHA-256 becomes weaker. The original message I wrote was as follows:

* “This is a very random message, or well kind of random. This beginning part is not, but I will end with some relatively random characters to ensure a very unpredictable message. XLWz4dVG3BxUWm7zQ9qS”. 

A common way in which hash functions with the hiding property are used is in password management (collision-resistance is also important to this application). Any decent online account-based service such as Facebook or Google will not store your passwords directly to manage access to your account. Instead, they will only store a hash of that password. Each time you fill in your password on a browser, a hash is first calculated. Only that hash is sent to the service provider’s server and compared with the hash stored in the back-end database. The hiding property can help ensure that attackers cannot recover it from the hash value.

Password management via hashes, of course, only works if users actually choose difficult passwords. The hiding property assumes that x is chosen randomly from a very large range. Selecting passwords such as “1234”, “mypassword”, or your birthday date will not provide any real security. Long lists of common passwords and their hashes exist which attackers can leverage if they ever obtain the hash of your password. These types of attacks are known as **dictionary attacks**. If attackers know some of your personal details, they might also attempt some informed guesses. Hence, you always need long, secure passwords (preferably long, random strings from a password manager). 

Sometimes an application might need a hash function which has both collision resistance and hiding. But certainly not always. The software verification process we discussed, for example, only requires that the hash function displays collision-resistance, hiding is not important.  

While collision resistance and hiding are the main properties sought of hash functions in cryptography, in certain applications other types of properties might also be desirable. 


## Notes

<a name="footnote1">1</a>. Whitfield Diffie and Martin Hellman, “New directions in cryptography,” *IEEE Transactions on Information Theory* IT-22 (1976), pp. 644–654, at p. 644. 

<a name="footnote2">2</a>. Ralph Merkle also discusses a key exchange protocol in “Secure communications over insecure channels”, *Communications of the Association for Computing Machinery*, 21 (1978), 294–99. While Merkle actually submitted this paper before the paper by Diffie and Hellman, it was published later. Merkle’s solution is not exponentially secure, unlike Diffie-Hellman’s. 

<a name="footnote3">3</a>. Ron Rivest, Adi Shamir, and Leonard Adleman, “A method for obtaining digital signatures and public-key cryptosystems”, *Communications of the Association for Computing Machinery*, 21 (1978), pp. 120–26.

<a name="footnote4">4</a>. A good history of these discoveries is provided by Simon Singh, *The Code Book*, Fourth Estate (London, 1999), Chapter 6.

<a name="footnote5">5</a>. Any schemes attempting to achieve non-repudiation, the other theme we discussed in *Chapter 1*, will at its basis need to involve digital signatures.

<a name="footnote6">6</a>. The “hiding” terminology is not common language, but taken specifically from Arvind Narayanan, Joseph Bonneau, Edward Felten, Andrew Miller, and Steven Goldfeder, *Bitcoin and Cryptocurrency Technologies*, Princeton University Press (Princeton, 2016), Chapter 1.