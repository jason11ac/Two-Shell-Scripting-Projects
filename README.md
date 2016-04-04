# Two-Shell-Scripting-Projects
This project includes two shell scripts. The first modifies a list of Hawaiian words (explained in lab2.log) and the second finds duplicate files in a current directory and creates hard links to those duplicates. 

Explanation for buildwords script:

~~~~~~~~~
lab2.log
~~~~~~~~~

Setup:

First I checked my locale command and 
all the LC_* were set to "en_US.UTF-8". 
To fix this I typed export LC_ALL='C' and 
all my LC_* changed to 'C', which is correct

Next I created my words.txt file using this command:

cat < /usr/share/dict/words | sort > words.txt

Used wget http://web.cs.ucla.edu/classes/winter16/cs35L/assign/assign2.html
	 wget http://mauimapp.com/moolelo/hwnwdseng.htm

to download the the assignment website and the English to Hawaiian website.

tr -c 'A-Za-z' '[\n*]' : This command changes single spaces and non-letters
into new lines

tr -cs 'A-Za-z' '[\n*]': This command changes single spaces and non-letters
into new lines as well, but then squeezes all the new lines into one so
that there is one word per line. 

tr -cs 'A-Za-z' '[\n*]' | sort: This sorts the web page in alphabetical order
so that each line's first letter is in order.

tr -cs 'A-Za-z' '[\n*]' | sort -u: This command sorts the web page in 
alphabetical order once again by each line's first letter, but only outputs the 
unique values

tr -cs 'A-Za-z' '[\n*]' | sort -u | comm - words: 


I then created several copies of the Hawaiian/English website so that I could
run tests on them.

My first idea was to get rid of all the HTML tags first and then eliminate
the English words second so that only the Hawaiian words remained. 

I used the command:

sed 's/<[^>]*>//g' test1.txt > test2.txt

to eliminate the HTML tags. But I quickly realized that there was not an 
easy way to identify the English words from the Hawaiian with no HTML tags.
To remedy this I decided to try eliminating all the words that don't contain 
just the Hawaiian letters:

p k ' m n w l h a e i o u

This worked for the most part but unfortunately some English words that only 
contained Hawaiian letters still remained and were not filtered out. For 
example: women and all. 

I decided to change tactics and use the HTML tags to my advantage. I quickly 
noticed that all the English/Hawaiian word pairs in the HTML file were 
arranged in the reoccurring pattern:

<tr>
    <td>Affection</td>
    <td>Pumehana</td>
</tr>

This brought me to my next idea to use sed in a way that gets rid of the 
<tr> at the beginning and then the next immediate line following. I 
used this command which did exactly that: 

sed '/<tr>/{N;d;}' lab1.txt > lab2.txt

At this point I learned that having words in English that only contain 
Hawaiian letters were actually OK to keep so I replaced my above command 
with this sed command that only kept words with Hawaiian letters in them:

sed '/[^p^k^'\''^m^n^w^l^h^a^e^i^o^u]/d' lab1.txt > lab2.txt

I put this command at the end before sorting because I didn't want to
mess up the grep and HTML tag commands below.

Next I used grep to select only the words on the page. Because each Hawaiian
word had <td> before it on the line, I used the command:

grep "<td>" lab2.txt > lab3.txt 

to select only the words in the file. 
Then I eliminated the HTML tags using the command:

sed 's/<[^>]*>//g' lab3.txt > lab4.txt

Now my file contained all the Hawaiian words themselves
with nothing else. Next I eliminated spaces and carriage 
returns using the commands:

tr -s "[:blank:]" < lab4.txt > lab5.txt

sed 's/\r/\n/g' lab5.txt > lab6.txt		//Changes carriage returns (\r)
						to new lines (\n)
tr , '\n' < lab6.txt > lab7.txt		//Changes the commas to new 
						new lines (\n)									
tr [:blank:] '\n' < lab7.txt > lab8.txt	//Changes blank spaces to
						new lines (\n)
tr -s "\n" < lab8.txt > lab9.txt   		//Finally, this squeezes 
						all the new line spaces into
						one     

Finally, to finish my Hawaiian words list, I made everything lowercase 
using the command: 

tr '[:upper:]' '[:lower:]' < lab9.txt > lab10.txt

and then changed all the ` marks to ' (apostrophe) marks:

sed 's/`/'"'"'/g' lab10.txt > lab11.txt

After sorting the words using sort -u lab11.txt > lab12.txt,
my Hawaiian words file was complete.

I put all the above commands into a bin/bash script called buildwords
and the ran the command below to put all the words into hwords.txt
as a Hawaiian dictionary:

cat hwnwdseng.htm | ./buildwords > hwords.txt

I then ran the spec command:

tr -cs 'A-Za-z' '[\n*]' | sort -u | comm -23 - words 

but with hwords.txt instead on the website:

tr -cs 'A-Za-z' '[\n*]' | sort -u | comm -23 - hwords.txt < assign2.html

and the entire website was outputted because hwords.txt only contains 
Hawaiian words and the whole website is in English. The -23 suppresses
columns 2 and 3 so only column 1, the lines unique to the website,
should be outputted. 

I then typed the commands:

tr -cs 'A-Za-z' '[\n*]' < hwnwdseng.htm | tr '[:upper:]' '[:lower:]' |
sort -u | comm -23 - hwords.txt

tr -cs 'A-Za-z' '[\n*]' < assign2.html | tr '[:upper:]' '[:lower:]' | 
sort -u | comm -23 - hwords.txt

which showed me all the misspelled words as English, 
but not as Hawaiian and vice versa. To get the amount for each i added
| wc -l to the end of both to get the number of lines/words.

The number of words for the hwnwdseng.htm (Hawaiian/English Words) was: 376
The number of words for the assign2.html (Lab website) was: 400 
 
