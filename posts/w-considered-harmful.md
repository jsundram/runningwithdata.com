# 'W' Considered Harmful

Not [the magazine](http://www.wmagazine.com) and not even [the former president](https://www.snopes.com/fact-check/someone-set-us-up-the-google-bomb/). But the [letter 'W' itself]("http://en.wikipedia.org/wiki/W"). The letter 'W', 23rd in the English alphabet, is unique in two ways: it is the only letter whose name is more than one syllable, and also the only letter whose name doesn't include the sound it makes.  

The fact that 'W' takes 3 syllables to say bothers me. Even [Wikipedia's entry on 'W'](http://en.wikipedia.org/wiki/W#Name) points out, twice, that the abbreviation *www* requires *nine* syllables to say. Crazy. So I wondered, how often is it the case that words that start with W (hereafter W-words) have fewer syllables than the letter W (double-yew)?  

[Syllabification](http://en.wikipedia.org/wiki/Syllabification) in general is a hard problem in English, but fortunately I don't have to solve it. The [Carnegie Mellon University (CMU) Pronouncing Dictionary](http://www.speech.cs.cmu.edu/cgi-bin/cmudict) provides the pronunciations for over 125,000 words. I say pronunciations, plural, because words can be pronounced in a variety of different ways (e.g. *fire* can be pronounced to rhyme with *higher*, or [in a single syllable](http://www.learnersdictionary.com/blog.php?action=ViewBlogArticle&amp;ba_id=55). Only 41 W-words in the CMU dict have pronunciations with different numbers of syllables (e.g. *warrior*). Using the CMU Pronouncing Dictionary, it's possible to count syllables in a word in a short (if cryptic) Python function, courtesy of [Jordan Boyd-Graber](http://umiacs.umd.edu/~jbg/) &mdash; I found it on the [nltk-users google group](https://groups.google.com/g/nltk-users/c/mCOh_u7V8_I/m/HsBNcLYM54EJ):  

```python
from nltk.corpus import cmudict 

dictionary = cmudict.dict()  # Get the CMU Pronouncing Dictionary
entry = dictionary[word.lower()]

def nsyl(entry): 
    """Return the max syllable count in the case of multiple pronunciations.""" 
    return max([len([s for s in p if s[-1].isdigit()]) for p in entry])
```

So, now that we've got a syllable counter, let's get all the W-words in the CMU dictionary, and see what the syllable distribution looks like.  

```python
import pylab 

src = "The Carnegie Mellon Pronouncing Dictionary [cmudict.0.6]"
w_words = {w: nsyl(entry) for (w, entry) in dictionary.items() if w[0] == 'w'}

pylab.hist(w_words.values(), align='left')
fig = pylab.gcf()
pylab.xlabel("Number of Syllables")
pylab.ylabel("Count")
pylab.title("Number of Syllables in {:,} words starting with 'W'".format(len(w_words)))
pylab.figtext(0.99, 0.01, 'Data Source: ' + src, ha='right', c=fig.get_edgecolor())
pylab.savefig('w.png', facecolor=fig.get_facecolor())

worth_abbreviating = [(w, n) for (w, n) in w_words.items() if n < 3] 
```

![Number of Syllables in words starting with 'W'](https://user-images.githubusercontent.com/150536/128645465-2edd941b-ba6c-4acc-b2bd-2bd22de928fe.png)

Only 101 W-words in the CMU dictionary (of 3,805 total W-words) have more than 3 syllables. That's 2.6%. Here's a sampling of the words where using W to abbreviate them actually *saves* syllables: wagnerian, wallpapering, washingtonians, weatherperson, workaholic. So, by all means, call a meeting of the Wagnerian Wallpapering Workaholic Weatherperson Washingtonians the WWWWW. It will save time. Otherwise, consider not using an abbreviation. Or looking for synonyms.   

# Suggestions for further work:  
* Take the [data from Google n-grams viewer ](https://storage.googleapis.com/books/ngrams/books/datasetsv3.html) and count syllables for W-words using nltk's ~90%-accurate
[syllabification code](https://github.com/nltk/nltk_contrib/blob/master/nltk_contrib/readability/syllables_en.py) for words that may not be in the CMU dictionary.
* Get a list of acronyms (maybe from [netlingo](http://www.netlingo.com/acronyms.php)) and see how many of them require more syllables to say than the phrase they stand for.

---
*<sub>Originally published: Mon, 28 Feb 2011 to [https://runningwithdata.com/post/3576752158](https://runningwithdata.com/post/3576752158)</sub>*
