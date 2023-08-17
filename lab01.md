# COSC312 Lab 01—Classical cryptography

## Letter frequencies

A fundamental part of classical cryptanalysis is gathering data from typical message texts and collecting statistics about them. The single most useful statistic is that of individual letter frequencies (at least for ciphers that rely in some way on substitutions). This naturally raises a **data-cleaning** problem: given a large block of text including punctuations, capitalisation, etc., gather the frequencies of individual letters that occur.

*Everyone* who has carried out a task like this (or any of its many variations) will tell you that their preferred method, whether it be Unix command-line utilities, a full-featured editor, `awk`, `python`, or ... is the best one. Of course, the real story is that it depends on context and the tools available. So, we invite you to try a variety out and choose for yourself.

:::success
:pencil: 
**Task One** Gather at least 10,000 characters of text in English from each of at least three different domains (e.g., classical literature, wikipedia pages, scientific literature). Carry out letter-frequency counts in each. Do there appear to be any differences?
:::

How does this task change if the text source we have is not plain text but, e.g., source files for web pages?

## Vigenère ciphers

One way of trying to boost the key-length of a Vigenère cipher is to encode the message twice using keywords of different lengths.

:::success
:pencil: 
**Task Two** Convince yourself that doing a Vigenère encoding with a key of length 5 (`happy`) and then re-encoding the ciphertext with another key of length 8 (`ecstatic`) corresponds to encoding with a certain key of length 40. What is that key, and what is the general rule?
:::

:::success
:pencil: 
**Task Three** Write a program or script that takes as input a sequence of two or more keys for the Vigenère cipher and produces as output the shortest key that corresponds to encoding with those keys one after the other.
:::


One of the basic tools for breaking a Vigenère cipher is the Kasiski examination. One of its forms is to take a ciphertext and find all repeated trigrams and the lengths of the gaps between them. The key length is likely to be a factor of most of these gap lengths.

:::success
:pencil: 
**Task Four** Write a program or script that takes as input a string and produces as output the list of all gap lengths between repeated trigrams in the string (the significant thing here is to try and do this as efficiently as possible).
:::

## Stream ciphers and one time pads

The standard digital one time pad would take the exclusive or of (the bit string representing) a message text, $M_1$ with a random bit string, $R$ producing the ciphertext $M_1\oplus R$. If we break the rules and use the same random bit string to encode a second message $M_2$ then Eve could take the exclusive or of the two ciphertexts to obtain $(M_1\oplus R) \oplus (M_2\oplus R) = M_1\oplus M_2$. How much information might that allow her to determine about $M_1$ and $M_2$?

:::success
:pencil: 
**Task Five** For which character pairs $(a,b)$ (where $a$ and $b$ are standard symbols on the keyboard and we assume basic ASCII encoding) can we identify the pair $\{a,b\}$ from $a\oplus b$? If we can't determine them exactly, then how much ambiguity is there in the worst case?
:::
