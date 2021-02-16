# Chapter 1: What is Cryptography?

Let’s start our enquiry with one of the more charming and entertaining episodes from the history of cryptography: that of the Beale ciphers.<sup>[1](#footnote1)</sup>

The purported history behind the Beale ciphers is, in my opinion, more likely to be fiction than reality. But it supposedly transpired as follows.


## The Beale ciphers

In both the Winter of 1820 and 1822, a man named Thomas J. Beale stayed at an inn owned by Robert Morriss in Lynchburg (Virginia). At the end of Beale’s second stay, he handed Morriss an iron box with valuable papers for safekeeping. 

A few months later, Morriss received a letter from Beale dated May 9, 1822. It emphasized the great value of the contents of the iron box and related some instructions to Morriss: if neither Beale nor any of his associates ever came to claim the box, he should open it precisely ten years from the date of the letter (that is, May 9, 1832). Some of the papers inside would be written in regular text. Several others, however, would be “unintelligible without the aid of a key.” This “key” would, then, be delivered to Morriss by an unnamed friend of Beale’s in June of 1832.    

Despite the clear instructions, Morriss did not open the box in May of 1832 and Beale’s mysterious friend never turned up in June of that year. It was not until 1845 that the innkeeper finally decided to open the box. In it, Morriss found a note explaining how Beale and his associates discovered gold and silver out West and buried it (and some jewelry) for safekeeping. In addition, the box contained three **ciphertexts**: that is, texts written in code which require a **cryptographic key**, or a secret, and an accompanying algorithm to unlock. This process of unlocking is known as **decryption**, while the locking process is known as **encryption**.

The three ciphertexts that Morriss found in the iron box each consist of a series of numbers separated by commas. According to Beale’s note, these ciphertexts separately provide the location of the treasure, the contents of the treasure, and a list of names with rightful heirs to the treasure and their shares (the latter information being relevant in case Beale and his associates never came to claim the box).

Morris attempted to decrypt the three ciphertexts for twenty years. This would have been easy with the key. But Morriss did not have the key and was unsuccessful in his attempts to recover the original texts, or **plaintexts** as they are typically called in cryptography.  

Nearing the end of his life, Morriss passed the box on to a friend in 1862. This friend subsequently published a pamphlet in 1885, under the pseudonym J.B. Ward. It included a description of the (alleged) history of the box, the three ciphertexts, and a solution that he had cracked for the second ciphertext. (Apparently, there is one key for each ciphertext, and not one key that works on all three ciphertexts as Beale originally seems to have suggested in his letter to Morriss.) 

You can see the second Beale ciphertext in *Figure 2* below.<sup>[2](#footnote2)</sup> The key to this ciphertext is the United States Declaration of Independence. The decryption procedure comes down to the applying the following two rules:

* For any number n in the ciphertext, locate the nth word in the United States Declaration of Independence
* Replace the number n with the first letter of the word you found


*Figure 1: Beale cipher no. 2*

![Figure 1: Beale cipher no 2.](/Images/Figure1-1.png "Figure 1: Beale cipher no. 2")


For instance, the first number of the second ciphertext is 115. The 115th word of the Declaration of Independence is “instituted,” so the first letter of the plaintext is “i.” The ciphertext does not directly indicate word spacing and capitalization. But after decrypting the first few words, you can logically deduce that the first word of the plaintext was simply “I.” (The plaintext starts with the phrase “I have deposited in the county of Bedford.”)

After decryption, the second message provides the detailed contents of the treasure (gold, silver, and jewels), and suggests that it was buried in iron pots and covered with rocks in Bedford County (Virginia). People love a good mystery, so great efforts have been expended on decrypting the other two Beale ciphers, particularly the one describing the location of the treasure. Even various prominent cryptographers have tried their hands on them. However, as of yet, no one has been able to decrypt the other two ciphertexts.   


## Modern cryptography

Colorful stories such as that of the Beale ciphers are what most of us associate with cryptography. Yet, modern cryptography differs in at least four important ways from these types of historical examples.

First, historically cryptography has only been concerned with **secrecy** (or confidentiality).<sup>[3](#footnote3)</sup> Ciphertexts would be created to ensure that only certain parties could be privy to the information in the plaintexts, as in the case of the Beale ciphers. In order for an encryption scheme to serve this purpose well, decrypting the ciphertext should only be feasible if you have the key. 

Modern cryptography is concerned with a wider range of themes than just secrecy. These themes include primarily (1) **message integrity**—that is, assuring that a message has not been changed; (2) **message authenticity**—that is, assuring that a message has really come from a particular sender; and (3) **non-repudiation**—that is, assuring that a sender cannot falsely deny later that she sent a message.<sup>[4](#footnote4)</sup>

An important distinction to keep in mind is, thus, between an **encryption scheme** and a **cryptographic scheme**. An encryption scheme is just concerned with secrecy. While an encryption scheme is a cryptographic scheme, the reverse is not true. A cryptographic scheme can also serve the other main themes of cryptography, including integrity, authenticity, and non-repudiation.   

The themes of integrity and authenticity are just as important as secrecy. Our modern communications systems would not be able to function without guarantees regarding the integrity and authenticity of communications. Non-repudiation is also an important concern, such as for digital contracts, but less ubiquitously needed in cryptographic applications than secrecy, integrity, and authenticity. 

Second, classical encryption schemes such as the Beale ciphers always involve one key that was shared among all the relevant parties. However, many modern cryptographic schemes involve not just one, but two keys: a **private** and a **public key**. While the former should remain private in any applications, the latter is typically public knowledge (hence, their names). Within the realm of encryption, the public key can be used to encrypt the message, while the private key can be used for decryption. 

The branch of cryptography that deals with schemes where all parties share one key is known as **symmetric cryptography**. The single key in such a scheme is usually called the **private key** (or secret key). The branch of cryptography which deals with schemes that require a private-public key pair is known as **asymmetric cryptography**. 

These branches are sometimes also referred to as private key cryptography and public key cryptography, respectively (though this can raise confusion, as public key cryptographic schemes also have private keys).  

The advent of asymmetric cryptography in the late 1970s has been one of the most important events in the history of cryptography. Without it, most of our modern communication systems, including Bitcoin, would not be possible, or at least very impractical.  

Third, classical encryption schemes, like those used in the Beale ciphers, were more art than science. Their perceived security was largely based on intuitions regarding their complexity. They would typically be patched when a new attack on them was learned, or dropped entirely if the attack was particularly severe. Modern cryptography, however, is a rigorous science with a formal, mathematical approach to both developing and analyzing cryptographic schemes.<sup>[5](#footnote5)</sup>

Fourth, whereas historically cryptography was primarily utilized in military settings, it has come to permeate our daily activities in the digital age. Whether you are banking online, posting on social media, buying a product from Amazon with your credit card, or tipping a friend in bitcoins, cryptography is the sine qua non of our digital age.

Given these four aspects to modern cryptography, we might characterize modern **cryptography** as the science concerned with the formal development and analysis of cryptographic schemes to secure digital information against adversarial attacks.<sup>[6](#footnote6)</sup> Security here should be broadly understood as preventing attacks that damage secrecy, integrity, authentication, and/or non-repudiation in communications. 

Cryptography is best seen as a subdiscipline of **cybersecurity**, which is concerned with preventing the theft, damaging, and misuse of computer systems. Note that many cybersecurity concerns have little or only a partial connection to cryptography. 

For instance, if a company houses expensive servers locally, they may be concerned with securing this hardware from theft and damage. While this is a cybersecurity concern, it has little to do with cryptography. 

For another example, phishing attacks are a common problem in our modern age. These attacks attempt to deceive people via an e-mail or some other message medium to relinquish sensitive information such as passwords or credit card numbers. While cryptography can help address phishing attacks to a certain degree, a comprehensive approach requires more than just using some cryptography. 


## Open communications

Modern cryptography is designed to provide security assurances in an **open communications** environment. If our communication channel is so well-protected that eavesdroppers have no chance of manipulating or even just observing our messages, then cryptography is superfluous. Most of our communication channels, however, are hardly this well-guarded.

To illustrate, a modern household may have multiple types of data connections, including coaxial cable, (asymmetric) digital subscriber line, fiber optic cable, and satellite. For most of the developed world, the majority of our data—whether this data concerns phone calls, television, web browsing, and so on—quickly travels from these connections to a massive network of fiber optic cables which can transport it all around the globe. In some remote areas of the developed world, such as in the United States or Australia, traffic might still also travel substantial distances over traditional copper telephone wires. 

It would be impossible to prevent potential attackers from physically accessing this network of cables and its supporting infrastructure. In fact, we already know that most of our data is intercepted by various national intelligence agencies at crucial intersections of the internet.<sup>[7](#footnote7)</sup> This includes everything from Facebook messages to website addresses that you visit. 

While surveilling data on a massive scale requires a powerful adversary, such as a national intelligence agency, attackers with only few resources can easily attempt to snoop at a more local scale. Though this can happen at the level of tapping wires, it is far easier just to intercept wireless communications. 

Most of our local network data—whether in our homes, at the office, or in a café—now travels via radio waves to wireless access points on all-in-one routers, rather than through physical cables. So an attacker needs little resources to intercept any of your local traffic. This is particularly concerning as most people do very little to protect the data that travels across their local networks. In addition, potential attackers can also target our mobile broadband connections, such as 3G, 4G, and 5G. All these wireless communications are an easy target for attackers.

Hence, the idea of keeping communications secret by protecting the communication channel is a hopelessly delusional aspiration for much of the modern world. Everything we know warrants severe paranoia: you should always assume that someone is listening. And cryptography is the main tool we have to obtain any kind of security in this modern environment.    

## Notes

<a name="footnote1">1</a>. For a good summary of the story, see Simon Singh, *The Code Book*, Fourth Estate (London, 1999), pp. 82-99. A short movie of the story was made by Andrew Allen in 2010. You can find the movie, “The Thomas Beale Cipher,” on its website (http://www.thomasbealecipher.com/).

<a name="footnote2">2</a>. This image is available on the Wikipedia page for the Beale ciphers. 

<a name="footnote3">3</a>. To be exact, the important applications of cryptographic schemes have been concerned with secrecy. Kids, for instance, frequently use simple cryptographic schemes for “fun”. Secrecy is not really a concern in those cases.  

<a name="footnote4">4</a>. Bruce Schneier, Applied Cryptography, 2nd edn, 2015 (Indianapolis, IN: John Wiley & Sons), p. 2. 

<a name="footnote5">5</a>. See Jonathan Katz and Yehuda Lindell, *Introduction to Modern Cryptography*, CRC Press (Boca Raton, FL: 2015), esp. pp. 16–23, for a good description. 

<a name="footnote6">6</a>. Cf. Katz and Lindell, ibid., p. 3. I think their characterization has some issues, so present a slightly different version of their statement here. 

<a name="footnote7">7</a>. See, for instance, Olga Khazan, “The creepy, long-standing practice of undersea cable tapping”, *The Atlantic*, July 16, 2013 (available at https://www.theatlantic.com/international/archive/2013/07/the-creepy-long-standing-practice-of-undersea-cable-tapping/277855/). 







