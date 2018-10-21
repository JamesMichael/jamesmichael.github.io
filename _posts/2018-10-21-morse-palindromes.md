---
layout: post
title: Morse Palindromes
excerpt_separator: <!-- more -->
---
In a recent video ([• − − • • • − • − • • • − − •](https://youtu.be/HY_OIwideLg)),
the people behind [VSauce](https://www.youtube.com/Vsauce)
took a look at words whose [Morse encodings](https://en.wikipedia.org/wiki/Morse_code) were palindromes.

I thought it would be a fun exercise to find all such words,
see which were the longest,
and whether there were any words that were palindromes in both English and Morse code.

<!-- more -->

<center>
<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/HY_OIwideLg" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
</center>

### The Algorithm

Linux distributions come with (or have a package that supplies) a list of words.
On CentOS, that file is `/usr/share/dict/words`.
Other distributions might place the file in different places.
These files list one word per line.
This is going to be our souce of words.

We want to load them, convert the word into Morse code,
and output them only if the encoded word is a palindrome.

### Implementation

Such a task is well suited to Perl.
I found a module on CPAN, [`Text::Morse`](https://metacpan.org/pod/Text::Morse) that'll handle the conversion of words to Morse code, saving a lot of time.

While writing this script, I noticed that some words contained characters that couldn't be encoded in Morse code (`&`, for example),
so limited the input to the set of words which only contained '[word characters](https://www.regular-expressions.info/shorthand.html)'.

I also noticed that many words appeared twice &mdash; once in lowercase, and once capitalised.
Capitalised words may not be valid in scrabble, but I'm allowing them.
I transliterated all words into lowercase and only output the first instance of each word.

The script I wrote will output the list of Morse palindromes, one per line,
with two columns: the first being the word, and the second being the associated Morse palindrome.

<script src="https://gist.github.com/JamesMichael/c8e6de83af4cde5420b52b1e107e8a4a.js"></script>

### Results

My script found 1,952 words that were Morse palindromes.
You can find the [full list here](/assets/morse-palindromes.txt).

#### The longest English word that is a Morse palindrome

Cutting up the data, I found that *intransigence* is the longest English word that is also a Morse palindrome.

    $ awk '{ print length($1), $0 }' morse-palindromes.txt |
      sort -rn | head | column -t
    13  intransigence  ..-.-.-..--......--..-.-.-..
    12  ultranatural   ..-.-..-.-..--..--..-.-..-.-..
    12  raphaelesque   .-..-.--......-..-......--.-..-.
    12  protectorate   .--..-.----.-.-.----.-..--.
    12  predestitute   .--..-..-......-..-..--.
    12  predestinate   .--..-..-......-..-..--.
    12  intercentral   ..-.-..-.-.-..-.-.-..-.-..
    12  incalescence   ..-.-.-..-.-......-.-..-.-.-..
    12  antinganting   .--.-..-.--..--.-..-.--.
    11  shrimpiness    ........-...--.--...-........

The longest Morse palindrome is *raphaelesque*, at an impressive 32 characters long.

    $ awk '{ print length($2), $0 }' morse-palindromes.txt |
      sort -rn | head | column -t
    32  raphaelesque   .-..-.--......-..-......--.-..-.
    30  ultranatural   ..-.-..-.-..--..--..-.-..-.-..
    30  incalescence   ..-.-.-..-.-......-.-..-.-.-..
    29  snellville     ...-...-...-.....-...-...-...
    29  shrimpiness    ........-...--.--...-........
    29  inconcocted    ..-.-.-.----.-.-.----.-.-.-..
    28  shelterless    .........-..-..-..-.........
    28  intransigence  ..-.-.-..--......--..-.-.-..
    28  hairballs      .....-...-.-....-.-...-.....
    28  furnivall      ..-...-.-.-......-.-.-...-..

#### Longest sequence of consecutive dots or dashes

Working out this would be more tricky.
To do so, I needed to write another Perl script that would output the length of the longest
sequence of dots or dashes in each word.

<script src="https://gist.github.com/JamesMichael/c41fa128611c7a914c336caf94502b77.js"></script>

And we find there are three Morse palindromes with 17 consecutive dots or dashes:
*tootmoot*, *dishiest*, and another I've redacted below.

    $ ./longest-consecutive morse-palindromes.txt --col 1 |
       sort -rn | head | column -t
    17  tootmoot    -----------------
    17  XXXXXXX     .................
    17  dishiest    -.................-
    16  shish       ................
    16  hisses      ................
    16  heishi      ................
    15  mammotomy   --.---------------.--
    15  bottommost  -...---------------...-
    14  XXXXXX      ..............
    14  shieh       ..............

#### How many Morse palindromes are English palindromes

With a minor alteration to my `morse-palindromes` script, I narrowed the
list down to 114 such instances.
The [full list is here](/assets/double-palindromes.txt).

The longest double palindrome English words are *sememes* and *ottetto*, both seven letters.

The longest double palindrome Morse palindromes are *SSTTSS* &mdash; *Space-Space-Time-Time-Space-Space* &mdash; (`......--......`), and
*poop* (`.--.------.--.`), both at fourteen characters.
