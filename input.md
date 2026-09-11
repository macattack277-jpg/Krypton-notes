Krypton Wargame

About:

I really don't know to expect of this WarGame as there is no information
about it on OverTheWire's page. From discussion I've heard in Discord
servers and such, it apparently involves cryptography. Let's do it!

Level 0:

The first page provides a link that I can apparently SSH to on port
2231, with the username 'krypton1'.

This command: ssh krypton1@krypton.labs.overthewire.org -p 2231 gets me
into the level, where a password is prompted.

According to the level, the password is encoded in base64:
S1JZUFRPTklTR1JFQVQ=

Base64 is used to encode strings so that they can be interpreted by
different systems which may have compatibility issues. It is not an
encryption scheme, therefore it can be easily decrypted.

On my host machine, this command on a file that contains the encoded
password:

base64 -d krypton1.txt

returns the password:

KRYPTONISGREAT

Level 1:

I logged in with the password I previously retrieved. It's stated that
files for other levels are stored in /krypton/.

It's stated that the password for the next level is stored in the file
'krypton2' but is encrypted with ROT13. This 'cipher' simply rotates
characters by 13.

I simply used an online tool for decrypting ROT13, rot13.com, to
decipher the password:

LEVEL TWO PASSWORD ROTTEN

There's other ways to do this, such as using a script to rotate each
character, but this was just the easist.

Level 2:

I'm a little confused as the password for this level does not seem to be
working for SSH.

I was wrong, the password is simply 'ROTTEN', not the entire string.

By using the encryption program on a string, we can see by how
iterations it rotates each character.

Ciphered password:

OMQEMDUEQMEK

Double ciphered password:

AYCQYPGQCYQW

What do these characters all have in common?

P Q R S T U V W X Y Z A

1 2 3 4 5 6 7 8 9 10 11 12

N O P Q R S T U V W X Y

1 2 3 4 5 6 7 8 9 10 11 12

And so on. They're rotated by 12 characters.

This also means that we can rotate the ciphered password by 14, and we
SHOULD get the original password. But let's test:

CAESARISEASY

Looks like a valid password to me! I Did that all without cryptools.
Thank the universe I know my ABCs!

Krypton3

This level involves frequency analysis. I remember learning about this
in preparation for a coding tournament I did in 2025. It involves
deciphering characters based on their frequency and placement. Let's go:

\- BB appears

\- EE appears once

\- GG appears 5 times

\- NN appears

\- TT appears 4 times

\- UU appears

\- WW appears once

\- XX appears only once

\- YY appears 3 times

\- ZZ appears twice

I also whipped up a quick Python script to order the characters in the
variable from least to most occurrences:

![](./10000001000003D4000001C956F4542B.png){width="17cm"
height="7.927cm"}

The output is this:

![](./100000010000006D000001BFF4AF381F.png){width="2.884cm"
height="11.827cm"}![](./10000001000001FF000001075CF75E86.png){width="13.52cm"
height="6.959cm"}

This will definitely help.

My next step was to use grep on the text to see where different
characters were occurring.

H only occurs four times, and also always occurs before an M. From this
I can determine that H=Q and M = U.

The letters Q and S deciphered are almost definitely vowels, because
they occur after the deciphered QU combo.

Q is likely A as it's the most common non double letter, and it occurs
after the deciphered QU.

\- FIND S

S can't be I as it appears as double, so it's either E or O. Likely E as
it's more common.

HOWEVER, SQ (Q being A) appears as a very common combination.

Bit of an update (a 'bit' lol), I completed the cipher continuing on my
frequency analysis! I'll post some of the other scripts I made to help
me here:

![](./10000001000002A00000022877E06F5C.png){width="9.266cm"
height="7.609cm"}

![](./10000001000002A000000228362D76D6.png){width="8.583cm"
height="7.05cm"}

In this script, I would fill out the keys with their prospective values
and the program would then swap the keys with the values in lowercase.
Trial and error with this method eventually found me the key. I was able
to use 'grep -E \[a-z\]' in my terminal to highlight the lowercase
letters.

The key is welld oneth eleve lfour passw ordis brute

So from this I can establish that the password for level four is BRUTE.

KRYPTON4

This level involves the use of a Vigenère cipher, which means that each
letter is converted to a number (a=1, b=2 etc.), and of these different
numbers has a corresponding number which is added to form the encrypted
message.

The hint also informs that the key is 6 letters long. Using this
information, I can grab every 6^th^ letter in the text (1, 7, 13, etc.)
and perform static analysis on them as they will have the same key, then
do the same thing for 2, 8, 14, etc.

![](./10000001000003600000013370400E68.png){width="17cm"
height="6.04cm"}

I originally tried using Python, but was struggling, so instead I used
an online tool (not sure if this constitutes cheating). I also probed
brute forcing the password using an english wordlist and iterating over
rotations for the six letter key, but this tool can do it for me.

![](./10000001000003550000025A1219D9F1.png){width="13.365cm"
height="9.432cm"}

The tool finds the iteration of the key that is most likely correct
based off of how much valid english there is. From this, I can determine
that the key is FREKEY.

When using that key to decrypt the password, I get:

CLEARTEXT

Krypton5

This time the key length isn't provided, so I have to use frequency
analysis to find it. Yikes.

Going to try this one without a tool this time, just to sharpen my
brain. I'm researching this site for the method:

<https://www.omnicalculator.com/kasiski-examination>

GNX appears a few times in the first one, and every number of letters
between each instance of 'GNX' is a multiple of 9. But just to be sure,
I'm going to do more analysis.

Indeed, the key length was 9! It's short length helped me find it more
quickly and I actually didn't have to decipher the whole thing.

This WarGame has surprisingly helped me practice my Python skills and
has helped me explore some more non basic concepts, such as lists of
lists and complex nested loops.

The password for the next level is RANDOM.

Krypton6

\- The cipher is symmetrically encrypted, meaning it uses a singular key

\- According to 'HINT2,' an 8 bit LFSR is used. This means that it uses
8 binary '1' or '0's.

\- The text is encrypted with the key + a text which is 'random.'

\- A 'known ciphertext' attack is possible.

\- For some reason I'm having trouble using the key to get my own
ciphertext.

\- Hexxdump may be necessary.

The README reads:

Hopefully by now its obvious that encryption using repeating keys

is a bad idea. Frequency analysis can destroy repeating/fixed key

substitution crypto.

A feature of good crypto is random ciphertext. A good cipher must

not reveal any clues about the plaintext. Since natural language

plaintext (in this case, English) contains patterns, it is left up

to the encryption key or the encryption algorithm to add the

\'randomness\'.

Modern ciphers are similar to older plain substitution

ciphers, but improve the \'random\' nature of the key.

An example of an older cipher using a complex, random, large key

is a vigniere using a key of the same size of the plaintext. For

example, imagine you and your confident have agreed on a key using

the book \'A Tale of Two Cities\' as your key, in 256 byte blocks.

The cipher works as such:

Each plaintext message is broken into 256 byte blocks. For each

block of plaintext, a corresponding 256 byte block from the book

is used as the key, starting from the first chapter, and progressing.

No part of the book is ever re-used as key. The use of a key of the

same length as the plaintext, and only using it once is called a \"One
Time Pad\".

Look in the krypton6/onetime directory. You will find a file called
\'plain1\', a 256

byte block. You will also see a file \'key1\', the first 256 bytes of

\'A Tale of Two Cities\'. The file \'cipher1\' is the cipher text of

plain1. As you can see (and try) it is very difficult to break

the cipher without the key knowledge.

(NOTE - it is possible though. Using plain language as a one time pad

key has a weakness. As a secondary challenge, open README in that
directory)

If the encryption is truly random letters, and only used once, then it

is impossible to break. A truly random \"One Time Pad\" key cannot be

broken. Consider intercepting a ciphertext message of 1000 bytes. One

could brute force for the key, but due to the random key nature, you
would

produce every single valid 1000 letter plaintext as well. Who is to know

which is the real plaintext?!?

Choosing keys that are the same size as the plaintext is impractical.

Therefore, other methods must be used to obscure ciphertext against

frequency analysis in a simple substitution cipher. The

impracticality of an \'infinite\' key means that the randomness, or

entropy, of the encryption is introduced via the method.

We have seen the method of \'substitution\'. Even in modern crypto,

substitution is a valid technique. Another technique is
\'transposition\',

or swapping of bytes.

Modern ciphers break into two types; symmetric and asymmetric.

Symmetric ciphers come in two flavours: block and stream.

Until now, we have been playing with classical ciphers, approximating

\'block\' ciphers. A block cipher is done in fixed size blocks
(suprise!).

For example, in the previous paragraphs we discussed breaking text and
keys

into 256 byte blocks, and working on those blocks. Block ciphers use a

fixed key to perform substituion and transposition ciphers on each

block discretely.

Its time to employ a stream cipher. A stream cipher attempts to create

an on-the-fly \'random\' keystream to encrypt the incoming plaintext one

byte at a time. Typically, the \'random\' key byte is xor\'d with the

plaintext to produce the ciphertext. If the random keystream can be

replicated at the recieving end, then a further xor will produce the

plaintext once again.

From this example forward, we will be working with bytes, not ASCII

text, so a hex editor/dumper like hexdump is a necessity. Now is the

right time to start to learn to use tools like cryptool.

In this example, the keyfile is in your directory, however it is

not readable by you. The binary \'encrypt6\' is also available.

It will read the keyfile and encrypt any message you desire, using

the key AND a \'random\' number. You get to perform a \'known
ciphertext\'

attack by introducing plaintext of your choice. The challenge here is

not simple, but the \'random\' number generator is weak.

As stated, it is now that we suggest you begin to use public tools, like
cryptool,

to help in your analysis. You will most likely need a hint to get going.

See \'HINT1\' if you need a kicktstart.

If you have further difficulty, there is a hint in \'HINT2\'.

The password for level 7 (krypton7) is encrypted with \'encrypt6\'.

Good Luck!

P-T = 4

N-V = 8

I finally got the binary actually working! I just had to change the file
permissions to allow all in my working directory and use absolute paths
like the second level... silly me.

When encrypting the text:

AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA

I can see that it seems to repeat, giving me some insight into the
length of the key.

EICTDGYIYZKTHNSIRFXYCPFUEOCKRNEICTDGYIYZKTHNSIRFXYCPFUEOCKRNEICTDGYIYZKTHNSIRFXYCPFUEOCKRNEICTDGYIYZKTHNSIRFXYCPFUEOCKRNEICTDGYIYZKTHNSIRFXYCPFUEOCKR

This part repeats once finished:

EICTDGYIYZKTHNSIRFXYCPFUEOCKRN

30 characters.

To beat this level, I can compare the password with the ciphertext by
converting characters between their integer and ASCII versions.

![](./100000010000033400000288AA98CCB4.png){width="11.629cm"
height="9.19cm"}

And the output is:

LFSRISNOTRANDOM

And with that, Krypton is finished!

This challenge was easy overall, I did have to use solutions a bit for
the last one though. My issue was that I was caught up on thinking that
it was still a regular XOR cipher and that I needed to go through this
whole two step process. The worst part was that I've literally learnt
about LFSR before, but only a year ago after which I had never used it
again!
