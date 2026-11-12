# Personal-Search-Engine
A follow-up on the Personal Digital Lexicon from last semester

## What I had last time:

When I rushed this project after a last minute pivot in my internet collection assignment, I managed to get my slow and prone to dying laptop to run a chrome extension overnight to get over 150,000 of my messages onto a series of files. Thanks to people years ago on Stack Overflow and Google (unfortunately includes the AI-generated results too), I was able to read and edit those files with some lines of Python code to see my most common bigrams, or two word combinations. I was also able to get some fun emoji statistics. 

<img width="686" height="149" alt="Screenshot 2026-11-11 193417" src="https://github.com/user-attachments/assets/9af91130-0a57-40a6-9e8e-6509530eed8b" />


## What I disliked and wanted to change:

The charts I haphazardly made on google sheets by hand were ugly. After some time in art school and thinking about what I really liked about programming, I became less interested in using exact representations of my data but in how I could interpret and perform this information. 

This eventually led me to the idea of a search engine.

Conveniently the idea of bigrams (or just collecting any n-gram series) lends itself to the fill-in-the-blank that happens while typing into a search engine.

*How easy would it be to use the bigrams I already had and add trigrams?*

In starting to implement it I confirmed the existence of some flaws that were in my initial bijection code.

### 1) Adding trijections

Early on, this was just duplicating the code I already had and changing the variable in the Stack Overflow n-grams code to 3 instead of 2.

### 2) Setting up bigrams and trigrams for search engines

Search engines generate from the first few words, so I had to check to make sure the n-grams actually were from the start of the sentences I was scrapping. I thought it would be as easy as checking the casing of the start of the ngram.

```python
for biject in allBijects:
  firstWord, secondWord=biject
  if firstWord[0].isupper():
    uppercaseBijectTotals.update([biject])

for triject in allTrijects:
  firstWord, secondWord,thirdWord =triject
  if firstWord[0].isupper():
    uppercaseTrijectTotals.update([triject])
```

Then I combined the top 100 most common bigrams and trigrams into a new list. Counters are great.

### 3) Simulating a search result

A user of my search engine would be able to type in anything, so I randomly selected a n-gram from this newly combined list to pretend an entry was being made:

```python
baseJectIndex=random.randint(0,199)
baseJect=searchBases[baseJectIndex]
print(baseJect)
```

The print statements were mainly for debugging, and besides forgetting how the code from last semester worked and syntax errors on my part it was looking pretty good.

#### The start of the unfortunate problems

I, like many people who text, drop apostrophes. However, since how much I abide by standard English varies, and the n-gram code I used does drop special characters, some phrases that should be searched through as bigrams were being categorized as trigrams.

*For example, "I didn't" would get sometimes get split into ("I", "didn", "t"). It correctly treats "I didn't" as "I did not", so a trigram. However, theses messages I'm looking at would use "I didnt" "I didn't" interchangeably and I did not want to lose the casual tone of the messages. So it would make more sense to treat it and other contractions as a bigram ("I", "didnt") and search from there.*

I hard coded around some of the issues.

```python
if thirdSearchTerm=="t" or thirdSearchTerm=="s":
    #solve issue of appostrophy based on last term being length 1
    searchTerm=firstSearchWord + " " + secondSearchWord+thirdSearchTerm
  elif thirdSearchTerm=="ve" or thirdSearchTerm=="nt" :
    searchTerm=firstSearchWord + " " + secondSearchWord+thirdSearchTerm
```

In theory, I could randomly select an n-gram and search through the files __AGAIN__ , store the sentences in a list (though now thinking about it I could just build the list in the first run through), see if the n-gram is in the start of the sentence, if so, store it in a list, and then randomly select a sentence in that list to display.

Once I implemented this, I noticed that some of the trigrams would lead to no sentences being added to the list, so the call to random.randint would be flagged from trying to randomly select an int between (0,0). 

So I went backwards and just said to display if *any* part of the sentence included the n-gram. I even worked only with trigrams. Then I realized some issues.

In thinking capitalized start == start of sentence, I forgot that I pretty consistently capitalize my I's, thanks mostly to autocorrect. So there were some trigrams that going back I did say often, but almost exclusively in the *middle* of sentences because they started with "I". Special characters, including periods, are removed from the content pretty early. There also might be some weird things the n-gram code and things I do afterwards that might be causing problems in the equivalence. Maybe white space I have not used strip() on yet. I had to go back and rework how I handled contractions which did help some of the issues.

I also had the same problem with contractions at the end of phrases come up in the start. So "I'm not" gets incorrectly split to ("I","m","not). I have allegedly fixed that.

```python
firstSearchWord,secondSearchWord,thirdSearchTerm=searchPhrase
  if thirdSearchTerm=="t" or thirdSearchTerm=="s" or thirdSearchTerm=="ve"or thirdSearchTerm=="nt":
    #solve issue of appostrophe based on last term being length 1
    searchTerm=firstSearchWord + " " + secondSearchWord+thirdSearchTerm
  elif secondSearchWord=="m" or secondSearchWord=="t" or secondSearchWord=="s" or secondSearchWord=="ve":
    searchTerm=firstSearchWord+secondSearchWord+ " "+thirdSearchTerm
  else:
    searchTerm=firstSearchWord + " " + secondSearchWord+ " "+thirdSearchTerm
```

Bigrams are trigrams are sometimes weird.

## The good news

For now, I can return a random and real sentence I said for the most part, which makes it funnier. It is not bound by coming up in the start of the sentence yet. It is very slow though.

<img width="437" height="29" alt="Screenshot 2026-11-11 193451" src="https://github.com/user-attachments/assets/bde837df-153e-4a1c-a789-94dea990351d" />



## Other considerations

I realize that even though I filtered to only exclusively look at what I wrote, I talk about other people or some personal things I'm not okay with releasing or did not have consent to release. My professor has wanted us to consider how Claude could be useful for some coding help but explicitly warned us against sharing personal information to a machine and company that can do whatever they want with it. At first I was hesitant to even return to this project, but after thinking about it, I should explore what I can control before I decide if its worthwhile to continue pursuing.

I used Claude to figure out the library and lines of code that allows me to filter out the common English names, just in case I do not hard code around all of them. I did not share any of the files with it. I just told it I had a list of sentences. Honestly after how easy the result was, I knew I should keep haunting Stack Overflow, W3schools, reddit, and other help forums for answers. Its the fun and frustrating part of learning.

I would like this project to be made primarily by me and what I learn from other real people. Because its all about me.








