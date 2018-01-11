
### Text Learning



Bag of Words

http://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.CountVectorizer.html


```python
#example
from sklearn.feature_extraction.text import CountVectorizer
vectorizer = CountVectorizer()
string1 = "hi Katie the self driving car will be late Best Sebastian"
string2 = "Hi Sebastian the machine learning class will be great great great Best Katie"
string3 = "Hi Katie the machine learning class will be most excellent"
email_list = [string1, string2, string3]
bag_of_words = vectorizer.fit(email_list)
bag_of_words = vectorizer.transform(email_list)
```


```python
print vectorizer.vocabulary_.get("great")
```

    6
    

Low-information Words

http://www.nltk.org/book/ch02.html


```python
#getting the low information words

from nltk.corpus import stopwords
```


```python
#if you use it for the first time, need to download first; the downloading will take a few minutes
nltk.download()
```


```python
sw = stopwords.words("english")
```


```python
len(sw)
```




    153




```python
# Stemming with NLTK
from nltk.stem.snowball import SnowballStemmer
stemmer = SnowballStemmer("english")
stemmer.stem("responsive")
```




    u'respons'




```python
stemmer.stem("responsivity")
```




    u'respons'




```python
stemmer.stem("unresponsive")
```




    u'unrespons'



In the beginning of this class, you identified emails by their authors using a number of supervised classification algorithms. In those projects, we handled the preprocessing for you, transforming the input emails into a TfIdf so they could be fed into the algorithms. Now you will construct your own version of that preprocessing step, so that you are going directly from raw data to processed features.

You will be given two text files: one contains the locations of all the emails from Sara, the other has emails from Chris. You will also have access to the parseOutText() function, which accepts an opened email as an argument and returns a string containing all the (stemmed) words in the email.

You’ll start with a warmup exercise to get acquainted with parseOutText(). Go to the tools directory and run parse_out_email_text.py, which contains parseOutText() and a test email to run this function over.

parseOutText() takes the opened email and returns only the text part, stripping away any metadata that may occur at the beginning of the email, so what's left is the text of the message. We currently have this script set up so that it will print the text of the email to the screen, what is the text that you get when you run parseOutText()?


```python
# parseOutText function

from nltk.stem.snowball import SnowballStemmer
import string

def parseOutText(f):
    """ given an opened email file f, parse out all text below the
        metadata block at the top
        (in Part 2, you will also add stemming capabilities)
        and return a string that contains all the words
        in the email (space-separated) 
        
        example use case:
        f = open("email_file_name.txt", "r")
        text = parseOutText(f)
        
        """


    f.seek(0)  ### go back to beginning of file (annoying)
    all_text = f.read()

    ### split off metadata
    content = all_text.split("X-FileName:")
    words = ""
    if len(content) > 1:
        ### remove punctuation
        text_string = content[1].translate(string.maketrans("", ""), string.punctuation)

        ### project part 2: comment out the line below after this test
        words = text_string

        ### split the text string into individual words, stem each word,
        ### and append the stemmed word to words (make sure there's a single
        ### space between each stemmed word)
        




    return words

    

def main():
    ff = open("../text_learning/test_email.txt", "r")
    text = parseOutText(ff)
    print text



if __name__ == '__main__':
    main()

```

    
    
    Hi Everyone  If you can read this message youre properly using parseOutText  Please proceed to the next part of the project
    
    

#### Deploying stemming
In parseOutText(), comment out the following line: 

words = text_string 

Augment parseOutText() so that the string it returns has all the words stemmed using a SnowballStemmer (use the nltk package, some examples that I found helpful can be found here: http://www.nltk.org/howto/stem.html ). Rerun parse_out_email_text.py, which will use your updated parseOutText() function--what’s your output now?

Hint: you'll need to break the string down into individual words, stem each word, then recombine all the words into one string.


```python
# parseOutText function for the next project (where the line has been commented out and codes are added)

from nltk.stem.snowball import SnowballStemmer
import string

def parseOutText(f):
    """ given an opened email file f, parse out all text below the
        metadata block at the top
        (in Part 2, you will also add stemming capabilities)
        and return a string that contains all the words
        in the email (space-separated) 
        
        example use case:
        f = open("email_file_name.txt", "r")
        text = parseOutText(f)
        
        """


    f.seek(0)  ### go back to beginning of file (annoying)
    all_text = f.read()

    ### split off metadata
    content = all_text.split("X-FileName:")
    words = ""
    if len(content) > 1:
        ### remove punctuation
        text_string = content[1].translate(string.maketrans("", ""), string.punctuation)
        print(text_string)
        ### project part 2: comment out the line below after this test
        #words = text_string

        ### split the text string into individual words, stem each word,
        ### and append the stemmed word to words (make sure there's a single
        ### space between each stemmed word)
        text_list = text_string.split()
        #print(text_list)
        from nltk.stem.snowball import SnowballStemmer
        stemmer = SnowballStemmer("english")
        stem_list = []
        for i in text_list:
            a = stemmer.stem(i)
            #print (a)
            stem_list.append(a)
        #print(stem_list)
        words = ' '.join(stem_list)    
    return words

    

def main():
    ff = open("../text_learning/test_email.txt", "r")
    text = parseOutText(ff)
    print text



if __name__ == '__main__':
    main()

```

    
    
    Hi Everyone  If you can read this message youre properly using parseOutText  Please proceed to the next part of the project
    
    hi everyon if you can read this messag your proper use parseouttext pleas proceed to the next part of the project
    

#### Clean away "signature words"

In vectorize_text.py, you will iterate through all the emails from Chris and from Sara. For each email, feed the opened email to parseOutText() and return the stemmed text string. Then do two things:

remove signature words (“sara”, “shackleton”, “chris”, “germani”--bonus points if you can figure out why it's "germani" and not "germany")
append the updated text string to word_data -- if the email is from Sara, append 0 (zero) to from_data, or append a 1 if Chris wrote the email.
Once this step is complete, you should have two lists: one contains the stemmed text of each email, and the second should contain the labels that encode (via a 0 or 1) who the author of that email is.

Running over all the emails can take a little while (5 minutes or more), so we've added a temp_counter to cut things off after the first 200 emails. Of course, once everything is working, you'd want to run over the full dataset.

In the box for answer, put the string that you get for word_data[152].


```python
#!/usr/bin/python

import os
import pickle
import re
import sys

#sys.path.append( "../tools/" )
#from parse_out_email_text import parseOutText

#we use the function above where the line has been commented out.
```


```python
"""
    Starter code to process the emails from Sara and Chris to extract
    the features and get the documents ready for classification.

    The list of all the emails from Sara are in the from_sara list
    likewise for emails from Chris (from_chris)

    The actual documents are in the Enron email dataset, which
    you downloaded/unpacked in Part 0 of the first mini-project. If you have
    not obtained the Enron email corpus, run startup.py in the tools folder.

    The data is stored in lists and packed away in pickle files at the end.
"""

from_sara  = open("from_sara.txt", "r")
from_chris = open("from_chris.txt", "r")

from_data = []
word_data = []

### temp_counter is a way to speed up the development--there are
### thousands of emails from Sara and Chris, so running over all of them
### can take a long time
### temp_counter helps you only look at the first 200 emails in the list so you
### can iterate your modifications quicker
temp_counter = 0

count_sara = 0
count_chris = 0

for name, from_person in [("sara", from_sara), ("chris", from_chris)]:
    for path in from_person:
        ### only look at first 200 emails when developing
        ### once everything is working, remove this line to run over full dataset
        temp_counter += 1
        if temp_counter < 200:
            path = os.path.join('..', path[:-1])
            print path
            email = open(path, "r")

            ### use parseOutText to extract the text from the opened email
            text_extract = parseOutText(email)

            ### use str.replace() to remove any instances of the words
            ### ["sara", "shackleton", "chris", "germani"]
            text_extract = text_extract.replace('sara','') 
            text_extract = text_extract.replace('shackleton','') 
            text_extract = text_extract.replace('chris','') 
            text_extract = text_extract.replace('germani','') 
            ### append the text to word_data
            word_data.append(text_extract)
            ### append a 0 to from_data if email is from Sara, and 1 if email is from Chris
            if name == 'sara':
                from_data.append(0)
                count_sara += 1
            elif name == 'chris':
                from_data.append(1)
                count_chris += 1
            email.close() #close the file

print "emails processed"
from_sara.close()
from_chris.close()

```

    ..\maildir/bailey-s/deleted_items/101.
     sbaile2 NonPrivilegedpst
    
    Susan
    
    Please send the foregoing list to Richard  Thanks
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    ..\maildir/bailey-s/deleted_items/106.
     sbaile2 NonPrivilegedpst
    
    1	TXU Energy Trading Company	
    
    2	BP Capital Energy Fund LP  may be subject to mutual termination
    
    2	Noble Gas Marketing Inc
    
    3	Puget Sound Energy Inc
    
    4	Virginia Power Energy Marketing Inc
    
    5	T Boone Pickens  may be subject to mutual termination 
    
    5	Neumin Production Co
    
    6	Sodra Skogsagarna Ek For  probably an ECTRIC counterparty
    
    6	Texaco Natural Gas Inc  may be booked incorrectly for Texaco Inc financial trades
    
    7	ACE Capital Re Overseas Ltd
    
    8	Nevada Power Company
    
    9	Prior Energy Corporation
    
    10	Select Energy Inc	
    
     Original Message
    From 	Tweed Sheila  
    Sent	Thursday January 31 2002 310 PM
    To	Shackleton Sara
    Subject	
    
    Please send me the names of the 10 counterparties that we are evaluating  Thanks
    ..\maildir/bailey-s/deleted_items/132.
     sbaile2 NonPrivilegedpst
    
    All 
    
    Heres the second tier of counterparties to add to the data retrieval list
    
    11	Medianews Group Inc
    
    12	Macromedia Incorporated
    
    13	British Airways plc	
    
    14	Merced Irrigation District
    
    15	Eugene Water  Electric Board
    
    16	Johns Manville INternational Inc
    
    17	Public Service Company of Colorado
    
    18	James Hardie Australia Finance Pty Ltd
    
    19	KnightRidder Inc	
    
    20	Airtran Holdings Inc 
    
    Please include Bob Susan Samantha and Stephanie on the email for your counterparty analysis since they are compiling the counterparty data books
    
    Please let me know if you have any questions
    
    Thanks  Sara
    
    
    
     Original Message
    From 	Shackleton Sara  
    Sent	Thursday January 31 2002 433 PM
    To	Gonzalez Veronica
    Subject	FW  TOP TEN counterparties for ENA  NonTerminated inthemoney positions based upon FMTM information as of 113001
    
    
    
     Original Message
    From 	Shackleton Sara  
    Sent	Thursday January 31 2002 417 PM
    To	Tweed Sheila
    Cc	Bailey Susan Boyd Samantha Panus Stephanie Sevitz Robert Moran Tom
    Subject	RE  TOP TEN counterparties for ENA  NonTerminated inthemoney positions based upon FMTM information as of 113001
    
    1	TXU Energy Trading Company	
    
    2	BP Capital Energy Fund LP  may be subject to mutual termination
    
    2	Noble Gas Marketing Inc
    
    3	Puget Sound Energy Inc
    
    4	Virginia Power Energy Marketing Inc
    
    5	T Boone Pickens  may be subject to mutual termination 
    
    5	Neumin Production Co
    
    6	Sodra Skogsagarna Ek For  probably an ECTRIC counterparty
    
    6	Texaco Natural Gas Inc  may be booked incorrectly for Texaco Inc financial trades
    
    7	ACE Capital Re Overseas Ltd
    
    8	Nevada Power Company
    
    9	Prior Energy Corporation
    
    10	Select Energy Inc	
    
     Original Message
    From 	Tweed Sheila  
    Sent	Thursday January 31 2002 310 PM
    To	Shackleton Sara
    Subject	
    
    Please send me the names of the 10 counterparties that we are evaluating  Thanks
    ..\maildir/bailey-s/deleted_items/185.
     sbaile2 NonPrivilegedpst
    
    
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    ..\maildir/bailey-s/deleted_items/186.
     sbaile2 NonPrivilegedpst
    
    
    
     Original Message
    From 	Shackleton Sara  
    Sent	Thursday February 14 2002 1202 PM
    To	Ellenberg Mark Aronowitz Alan Bailey Susan Boyd Samantha Panus Stephanie
    Subject	Mark Q has moved to EB 3820 where there is a working phone
    
    
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    ..\maildir/bailey-s/deleted_items/187.
     sbaile2 NonPrivilegedpst
    
    We need to research now
    
    Original Message
    From Dicarlo Louis 
    Sent Thursday February 14 2002 228 PM
    To Shackleton Sara
    Cc Garza Maria
    Subject Terminated
    
    
    As we build the matrix of data discussed yesterday in our meeting we are encountering some missing data  Below is a list of counterparties which are listed as terminated in at least one of the various lists of counterparties we have but which dont have termination dates
    
    BP Corporation North America Inc	
    ConAgra Trade Group Inc	
    Deutsche Bank AG New York Branch	
    Hallwood Energy Corporation	
    IGI Resources Inc	
    NJR Energy Corp	
    
    Can you please reply with termination dates for these counterparties  Let me know if you need additional information
    
    Thanks
    
    PS I have a few more requests coming shortly
    
    Louis R DiCarlo
    ENA Gas Structuring
    Phone 7133454666
    Email louisdicarloenroncom
    ..\maildir/bailey-s/deleted_items/193.
     sbaile2 NonPrivilegedpst
    
    We cannot locate any financial contracts for this counterparty
    
    Please verify that this party belongs on the ITM Nonterminated financial gas list
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    ..\maildir/bailey-s/deleted_items/195.
     sbaile2 NonPrivilegedpst
    
    
    
     Original Message
    From 	Dicarlo Louis  
    Sent	Friday February 15 2002 249 PM
    To	Aronowitz Alan Shackleton Sara Hall Bob M Apollo Beth Couch Greg Miroballi Angelo
    Cc	McMichael Jr Ed Chilkina Elena Garza Maria Rostant Justin Tian Yuan
    Subject	
    
    Below is our top 30 gas financial ITM terminated counterparties CP  We should focus on these CPs first  If anyone is aware of other CPs which should be included please let me know
    
    Counterparty				Million
    	
    Calpine Energy Services LP		3583
    Coral Energy Holding LP		1334
    PMI Trading LTD			806
    Sempra Energy Trading Corp		758
    PSEG Energy Resources  Trade LLC	553
    AIG Energy Trading Inc			506
    Reliant Energy Services Inc		384
    The New Power Company		368
    Conectiv Energy Supply Inc		352
    WPS Energy Services Inc		324
    Canadian Imperial Bank of Commerce	304
    FPL Energy Power Marketing Inc	286
    JPMorgan Chase Bank			258
    Arizona Public Service Company		219
    FirstEnergy Solutions Corp		206
    BNP Paribas				204
    Texaco Inc				197
    MidAmerican Energy Company		174
    Vitol Capital Management Ltd		174
    BP Corporation North America Inc	171
    Occidental Energy Marketing Inc	150
    Anadarko Petroleum Corporation		143
    PCS Nitrogen Fertilizer LP		142
    Enbridge Marketing US Inc		137
    CMS Marketing Services and Trading Co 131
    Royal Bank of Canada The		125
    Tudor Proprietary Trading LLC		121
    e prime inc		Terminated	121
    American Public Energy Agency		120
    Garden State Paper Company LLC	116
    
    We should begin or continue with the process of contract review and collecting confirmations AR and AP
    
    Thank you
    
    Louis R DiCarlo
    ENA Gas Structuring
    Phone 7133454666
    Email louisdicarloenroncom
    ..\maildir/bailey-s/deleted_items/214.
     sbaile2 NonPrivilegedpst
    
    Did Dominic Carolan already request that do contract reviews for these two parties
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    ..\maildir/bailey-s/deleted_items/215.
     sbaile2 NonPrivilegedpst
    
    Weezie
    
    Please email the names of any financial counterparties which you have reviewed on behalf of global markets to Susan Stephanie and Samantha  Such counterparties may have been previously reviewed for a different financial product and we do not want to create multiple files or conduct multiple reviews
    
    Bob Sevitz is the gatekeeper for the data collection process
    
    Thanks
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    ..\maildir/bailey-s/deleted_items/233.
     sbaile2 NonPrivilegedpst
    
    I already spoke with Harlan  We need to check with him on Friday  Sara
    
     Original Message
    From 	Dicarlo Louis  
    Sent	Thursday February 28 2002 413 PM
    To	Shackleton Sara
    Cc	Murphy Harlan Bridges Michael
    Subject	Demand Letters
    
    Ed Baughman said that before sending the Demand Letters we should have discussion w Harlan to be sure there are no adverse issues associated w the following
    
    Nevada Power
    Select Energy
    
    Louis R DiCarlo
    ENA Gas Structuring
    Phone 7133454666
    Email louisdicarloenroncom
    ..\maildir/bailey-s/deleted_items/242.
     sbaile2 NonPrivilegedpst
    
    Baltimore Gas  Electric
    Brian Russo finance area  4107833623
    
    Someone in settlements needs to contact him to determine Default Rate since BGE wants to pay full amount sounds like all deals have terminated
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    ..\maildir/bailey-s/deleted_items/243.
     sbaile2 NonPrivilegedpst
    
    Someone from settlements contact Kay at 8438713200 EXT 203 to determine default rate of interest  May or maynot terminate
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    ..\maildir/bailey-s/deleted_items/244.
     sbaile2 NonPrivilegedpst
    
    fyi
    
     Original Message
    From 	Dicarlo Louis  
    Sent	Tuesday March 05 2002 935 AM
    To	Shackleton Sara
    Cc	Bridges Michael Garza Maria
    Subject	Contract Type Request
    
    We are attempting to complete the missing information on our spreadsheet  To that end below are live CPs that have been reviewed for the purpose of sending demand letters  However because they are live we cannot determine what type of contract they have  Our source for this information has been the Master Terminated Log  These CPs are obviously not included therein
    
    Please provide next to each CP below the contract type
    
    COUNTERPARTY				CONTRACT TYPE
    Texaco Natural Gas Inc	
    Select Energy Inc	
    Nevada Power Company	
    Johns Manville International Inc	
    Stone Energy Corporation	
    Clinton Energy Management Services Inc	
    Florida Power Corporation	
    Imperial Holly Corporation	
    Old World Industries Inc	
    Rochester Gas  Electric Corporation	
    Navajo Refining Company	
    Coast Energy Group a division of Cornerstone	
    Cross Oil Refining  Marketing Inc	
    Kern Oil  Refining Co	
    US Brick Company	
    Municipal Gas Authority Of Florida	
    TRC Operating Company Inc	
    Lauscha Fiber International Corp	
    Whirlpool Corporation	
    Central Illinois Light Company	
    EOTT Energy Partners LP	
    TriState Generation and Transmission Association Inc	
    Scana Energy Marketing Inc	
    AIG Commodity Arbitrage Fund Limited	
    AIG Commodity Arbitrage Fund LP	
    TotalFinaElf Gas  Power North America Inc	
    
    Thank you for your support
    
    Louis R DiCarlo
    ENA Gas Structuring
    Phone 7133454666
    Email louisdicarloenroncom
    ..\maildir/bailey-s/deleted_items/246.
     sbaile2 NonPrivilegedpst
    
    Debbie is sending check to Angelo they are netting
    7167714739
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    ..\maildir/bailey-s/deleted_items/247.
     sbaile2 NonPrivilegedpst
    
    1	Citizens Gas Utility District of Tennessee Scott  Morgan Counties  sent wire on 3102  Contact Missy Ancy at 4235694457  This was a voice mail
    
    2	Ormet claims that failure to pay Invoice  0202112 is incorrect  On 2502 Ormet wired 36247697 ref 2511 and this amount includes the netting of 1464503 owed to Ormet by Enron Metals and Commodity LImited for commissions applied to metal trades	  Letter given to Susan
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    ..\maildir/bailey-s/deleted_items/254.
     sbaile2 NonPrivilegedpst
    
    Susan  Please add Bob Halls name to the daily delivery list for this log
    
    Thanks
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    ..\maildir/bailey-s/deleted_items/259.
     sbaile2 NonPrivilegedpst
    
    Received written response from CP claiming the it has no payment obligations under the ISDA because of our Bankruptcy
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    ..\maildir/bailey-s/deleted_items/260.
     sbaile2 NonPrivilegedpst
    
    1  Received a voice mail from Bill OBrien credit manager of Select Energy at 8606652376
    
    He says that payments were previously made on the referenced invoices and he is pulling the data together  He wants to talk on Thursday  Louis can you call him please
    
    2  Received a demand letter from Stone Energy Corporation which cites ENAs i failure to pay Stone 46564 under the swap EV39431 which will be a default in 2 business days and ii bankruptcy  Stone may later designate an Early Termination Date
    
    Stone will also offset amounts owed by ENA Upstream Company to Stone
    
    Louis  we need to verify the swap payment issue and Ill have a copy of the letter sent to you
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    ..\maildir/bailey-s/deleted_items/261.
     sbaile2 NonPrivilegedpst
    
    Myra Gary 8707253611 EXT 114 left message that funds wired 3502 they never received the Jan 02 invoice
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    ..\maildir/bailey-s/deleted_items/263.
     sbaile2 NonPrivilegedpst
    
    I received a voice mail from Burlingtons CG Rick Plaeger who is reviewing the letter  7136249161
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    ..\maildir/bailey-s/deleted_items/278.
     sbaile2 NonPrivilegedpst
    
     
    Original Message
    From Mann Kay 
    Sent Friday March 08 2002 1136 AM
    To Tweed Sheila Lyons Dan Shackleton Sara Bushman Teresa G
    Subject FW New Math
    
    
     
    Original Message
    From Kroll Heather 
    Sent Friday March 08 2002 1132 AM
    To Charlie Vetters Email Dad Swan Email Jim King Email Mann Kay Mark Williams Email
    Subject FW New Math
    
    
     
    Original Message
    From Jafry Rahil 
    Sent Friday March 08 2002 1127 AM
    To Kroll Heather Rorschach Reagan Pagan Ozzie Charlie Vetters Email Jennifer Bagwell Email Marshall McCaleb Heuertz Kelly Bhabi Bhabi Email
    Subject FW New Math
    
    
    NEW MATH 
    Teaching Math in 1950 A logger sells a truckload of lumber for 100 His cost of production is 45 of the price What is his profit 
    Teaching Math in 1960 A logger sells a truckload of lumber for 100 His cost of production is 45 of the price or 80 What is his profit 
    Teaching Math in 1980 A logger sells a truckload of lumber for 100 His cost of production is 80 and his profit is 20 Your assignment Underline the number 20 
    Teaching Math in 1990 By cutting down beautiful forest trees the logger makes 20 What do you think of this way of making a living Topic for class participation after answering the question How did the forest animals feel as the logger cut down the trees There are no wrong answers 
    Teaching Math in 2000 A logger sells a truckload of lumber for 100 His cost of production is 120 How does Arthur Andersen determine that his profit margin is 60
    ..\maildir/bailey-s/deleted_items/290.
     sbaile2 NonPrivilegedpst
    
    Jim
    
    Are you looking solely for Enron Corp executory contracts
    
    We can identify the Enron Corp ISDA Master Agreements which are safe harbor contracts under the Code as well as securities contracts also safe harbor contracts  Please be a bit more specific
    
    Thanks  Sara
    
     Original Message
    From 	Armogida Jim  
    Sent	Friday March 08 2002 954 AM
    To	Goebel Peter Moore Tom Borgman Laine Freeland Clint Shackleton Sara
    Subject	FW executory contracts
    
    In rereading my original email which is below I see that I misspokeplease delete in your mind the words I have put in bold letters below and ignore them  Thanks again
    
     Original Message
    From 	Armogida Jim  
    Sent	Friday March 08 2002 938 AM
    To	Goebel Peter Moore Tom Borgman Laine Freeland Clint Shackleton Sara
    Subject	executory contracts
    
    There is a requirement to identify all executory contractsthat is all contracts that are currently active and still have performance required by one party or the others  You each have probably already been asked to list those for somebody as part of the gathering effort  If you have not I am asking that
    
    Would you please let me know whether you have already engaged in this effort and provided the information or if that is not the case give me a list of those types of contracts that you know of or tell me where I can gomaybe I shouldnt have started that way joketo get such a list
    
    Thanks  I appreciate it
    ..\maildir/bailey-s/deleted_items/296.
     sbaile2 NonPrivilegedpst
    
    Louis
    
    Just a reminder that ENA received a letter from TriState last month and you are going to verify that Deal Nos YB 57081 and 58331 are the only active financial deals with ENA  If you can confirm then we can indicate on the Master Log that TriState has actually terminated all outstanding trades effective as of 22702  
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    ..\maildir/bailey-s/deleted_items/302.
     sbaile2 NonPrivilegedpst
    
    Spoke with Dale Ginter bankruptcy lawyer  Problem is that Merced wants to cut a deal and terminate if they can pay over 18 months  I explained that we would terminate if he didnt  I agreed to not act today and advise him of our decision  
    
    Louis we should talk about this one
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    ..\maildir/bailey-s/deleted_items/306.
     sbaile2 NonPrivilegedpst
    
    Even though we have already terminated this counterparty I received a voice mail today caller did not leave name  for a commercial person to contact the company to discuss a settlement proposal  The phone number is 3039784965  The termination was not mentioned
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    ..\maildir/bailey-s/deleted_items/307.
     sbaile2 NonPrivilegedpst
    
    You should show Hallwood as the terminated party see Louiss email below  I dont know when the acquisition took place but we would normally hear from Global Contracts about name changes acquisitions etc
    
    What exactly do you need  I believe the new entity is HEP Pure and the termination was effective 12301  You should follow up with Susan Bailey or Blake Estes
    
    Sara
    
    Original Message
    From Dicarlo Louis 
    Sent Thursday February 14 2002 228 PM
    To Shackleton Sara
    Cc Garza Maria
    Subject Terminated
    
    
    As we build the matrix of data discussed yesterday in our meeting we are encountering some missing data  Below is a list of counterparties which are listed as terminated in at least one of the various lists of counterparties we have but which dont have termination dates
    
    BP Corporation North America Inc	
    ConAgra Trade Group Inc	
    Deutsche Bank AG New York Branch	
    Hallwood Energy Corporation	
    IGI Resources Inc	
    NJR Energy Corp	
    
    Can you please reply with termination dates for these counterparties  Let me know if you need additional information
    
    Thanks
    
    PS I have a few more requests coming shortly
    
    Louis R DiCarlo
    ENA Gas Structuring
    Phone 7133454666
    Email louisdicarloenroncom
    ..\maildir/bailey-s/deleted_items/317.
     sbaile2 NonPrivilegedpst
    
    I received a valid termination notice from AirTran designating March 13 2002 as the Early Termination Date for financial deals
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    ..\maildir/bailey-s/deleted_items/320.
     sbaile2 NonPrivilegedpst
    
    We need to prepare files for contract review now
    
    Blake you will need to prepare demand letters
    
     Original Message
    From 	Mao Shari  
    Sent	Thursday March 14 2002 259 PM
    To	Bruce Robert Shackleton Sara
    Cc	Sweeney Kevin Holzer Eric Shivers Lynn Mausser Gregory A
    Subject	RE Financial Pulp  Paper
    
    The following two counterparties will require demand letters  The following numbers include total AR outstanding through February  
    
    Vernon Lillian Corporation
    Owes Enron 541925
    
    Enterprise Newsmedia Inc
    Owes Enron 243500
    
    Let me know if you have any questions
    
    Shari
    33859
     Original Message
    From 	Sweeney Kevin  
    Sent	Thursday March 14 2002 919 AM
    To	Bruce Robert Shackleton Sara
    Cc	Mao Shari Holzer Eric Shivers Lynn
    Subject	Financial Pulp  Paper
    
    
    Bob and Sarah
    
    Further to our conversation on Wednesday night we have identified the top 1015 counterparties from both a terminated and nonterminated perspective that we want to begin working on  Of the nonterminated 6 are counterparties that should be sent demand letters
    
    Shari Mao will be providing you today a list of those counterparties that owe us money and should be sent demand letters  Lynn Shivers will be pulling these contracts as well as those for the overall top 10 to 15 and we will begin the binder process and get you copies of the contracts as soon as possible  We will also do the credit agg check for collateral and any overlap with other commodities
    
    Please let me know if you have any questions
    
    Kevin
    ..\maildir/bailey-s/deleted_items/335.
     sbaile2 NonPrivilegedpst
    
    Susan  Can you and Stephanie provide me with an update tomorrow afternoon  Thanks
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    ..\maildir/bailey-s/deleted_items/70.
     sbaile2 NonPrivilegedpst
    
    Bob
    
    Can you please handle for EIM  Susan has a lot of information  Also it is my understanding that any information going out the door must be logged in by Raz  Thanks  Sara
    
     Original Message
    From 	Newbrough Jennifer  
    Sent	Thursday January 24 2002 953 AM
    To	Bailey Susan Shackleton Sara
    Subject	RE Forest Products Trading Counterparties
    
    I have attached a file which shows the information we would like to send to International Paper regarding counterparties  It includes a description of the company but not the name the  of total tons traded and the credit rating of the counterparty  Can you let me know if you think this information can be given to a third party under the confidentiality provisions mentioned below  Please let me know
    
    Thanks
    Jennifer
    
     
    
     Original Message
    From 	Bailey Susan  
    Sent	Friday January 18 2002 453 PM
    To	Newbrough Jennifer
    Cc	Shackleton Sara
    Subject	RE Forest Products Trading Counterparties
    
    Jennifer
    
    I have completed my review of the selected Forest Products counterparties listed in your email request of January 15th   My review focused on the Confidentiality provision contained in either the Master Agreement or the Confirmations being the documentation which established the trading relationship with either Enron North America Corp ENA or Enron Canada Corp ECC   I was asked to determine if the Confidentiality provision in our trading documents would prohibit ENA andor ECC from disclosing to third parties a the existence of a Master Agreement or b the existence of a transaction under a Confirmation
    
    Conclusion  Whether the trading relationship was established under a Master Agreement or under the Confirmations  the Confidentiality language employed by ENA and ECC prohibits the public disclosure relating to the Master Agreement or to the Confirmations   Therefore if ENA andor ECC desire to public disclose the existence of a Master Agreement or the existence of a transaction under a Confirmation  ENA andECC must first secure the prior consent of any counterparty before public disclosure to third party can be made
    
    For your convenience attached are my findings as set forth in the following
    
    1	Master Agreement List for the Forest Products Trading Counterparties along with the forms of Confidentiality provisions
    
      File master agreement list forest products counterpartydoc                   File confidentiality  master standard provisiondoc                      File confidentialitymaster nonstandard provisiondoc  
    2	Forest Products Trading Counterparties without a Master Agreement along with the Confidentiality provision
    	
      File non master agreement  list forest products counterpartydoc                    File confidentiality  confirms standard provisiondoc  
    
    If I can be of further assistance please let me know
    
    Cordially 
    Susan S Bailey
    Enron North America Corp
    1400 Smith Street Suite 3803A
    Houston Texas 77002
    Phone 713 8534737
    Fax 713 6463490
    Email SusanBaileyenroncom
    
    
    
     Original Message
    From 	Newbrough Jennifer  
    Sent	Tuesday January 15 2002 1135 AM
    To	Bailey Susan
    Cc	Shackleton Sara
    Subject	Forest Products Trading Counterparties
    
    Susan
    
    Sorry about the previous attempt  This is the list of counterparties  We need to know if we can disclose to third parties that we have traded with them and the volumes associated with the trades  Thanks for your help  Call me if you need clarification
    
    Thanks for your help
    Jennifer Adams
    
    Waste Management Inc
    Georgia Pacific Corp
    Casella Waste Systems Inc
    US Gypsum
    Inland Paper and Packaging
    Rand Whitney Counterboard
    National Gypsum Company
    Norampac
    Atlantic Packaging Products ltd
    General Mills
    Papier Mason
    Media News
    Times Mirror
    Macro Media
    Knight Ridder
    Media General
    New York Times
    Tembec Industries
    Pacifica Paper
    Rock Ten
    Conagra Energy Svcs
    Frito Lay
    Lin Packaging
    Dial
    National Banc of Canada
    Master Packaging
    Sodra
    James Hardie NV
    Appleton Paper
    Merita Bank
    Repap New Brunswick
    Boise Cascade
    Caima
    Irving Pulp and Paper
    UPM Kymmene
    Proctor  Gamble
    
    Jennifer Adams
    Manager Corporate Development
    Enron Corp
    1400 Smith Street
    Houston TX  77002
    Tele  7138533919
    Fax  7136464043
    ..\maildir/heard-m/brokerage_agreements/1.
     MHEARD NonPrivilegedpst
    
    Marie  OK heres a request from the outside  Have we heard from Cassandra
    
    Sara Shackleton
    Enron North America Corp
    1400 Smith Street EB 3801a
    Houston Texas  77002
    7138535620 phone
    7136463490 fax
    sarashackletonenroncom
     Forwarded by Sara ShackletonHOUECT on 06052001 1139 AM 
    
    
    	Thomas P Alterson altersonthomasjpmorgancom 06052001 1118 AM 	   To SaraShackletonenroncom  cc   Subject List of authorized traders	
    
    
    
    Sara
    
    Would you please send me a list of authorized traders for Enron NA Corp  We are
    updating our files and noticed that we are missing an ATL for Enron
    
    ATL form
    See attached file ATL Enron NA Corpdoc
    
    Thank you
    Tom Alterson
    
      ATL Enron NA Corpdoc 
    ..\maildir/heard-m/brokerage_agreements/16.
     MHEARD NonPrivilegedpst
    
    Sheila  Please send your usual message on this one  I assume it is for ECT Investments Inc
    Thanks  Sara
    
    Original Message
    From Dallmann Shane 
    Sent Monday September 24 2001 845 AM
    To Shackleton Sara
    Subject FW Access Agreements
    
    
    Sara
    
    We are setting up access to JP Morgans online FX system These are the documents they sent us to get signed before they will give us access Are you the correct person to send them to
    
    Regards
    Shane
    
    Original Message
    From CLAIREBURLEYchasecom mailtoCLAIREBURLEYchasecom
    Sent 18 September 2001 1736
    To Dallmann Shane
    Subject Access Agreements
    
    
    
     Forwarded by Claire BurleyCHASE on 18092001 1735
    
    
    
    Tracey Tommasini
    18092001 1337
    
    Legal Department  44 020 7777 4880    Fax Number 44 020 7777 4758
    
    To   shanedallmanenroncom
    cc   Claire BurleyCHASECHASE Anna BellBoothCHASECHASE
    Subject  Access Agreements
    
    At the request of Claire Burley attached please find the
    
              Access Agreement
              ChaseFX  Electronic Trading Service
              Additional Terms Covering FX Transactions
              Authorized Users  Single FX Trade
              NetMatch Schedule
              NetMatch Authorized Users
    
    between Enron North America and The Chase Manhattan Bank
    
    Please  note  that the attached documents can be viewed and printed but not
    edited  Please print review and complete the Address for Notices which is
    located  on page five of the Access Agreement as well as the Designation of
    Authorized   Users   Forms   and  have  an  authorized  officer  of  your
    organization  sign  each  of  the  agreements  and return two 2 hard copy
    executed agreements to the attention of Tracey Tommasini Legal Department
    The Chase Manhattan Bank 125 London Wall London  EC2Y 5AJ United Kingdom
    for further execution
    
    Please provide an incumbency certificate signed by the Corporate Secretary
    for the person or persons who sign the agreements and form
    
    Should  you have any questions please contact Andrew Hamper on 44 20 7777
    1803
    
    Tracey Tommasini
    Assistant
    
    1   See attached file Access Agreement US lawdoc
    2   See attached file Chase FX Electronic Trading Servicedoc
    3   See attached file Additional FX Termsdoc
    4   See attached file Authorized UsersSingle FX Tradedoc
    5      See    attached    file   NetMatch   Schedule   with   Settlement
    Instructionsdoc
    6     See  attached  file  NetMatch  Authorized  Users  with  Settlement
    Instructionsdoc
    ..\maildir/heard-m/brokerage_agreements/17.
     MHEARD NonPrivilegedpst
    
    Sheila  Please send your usual message on this one as well  There are several written agreements involved  Thanks  Sara
    
     Original Message
    From 	Shackleton Sara  
    Sent	Thursday September 06 2001 1056 AM
    To	Summers Kelly
    Cc	Adams Laurel
    Subject	RE FX confirmation info
    
    Kelly  Please send the paperwork to me  I will also need to review the site and legal works through an internal Networks person for consistency purposes  Please forward contact information to me name phone email etc  Thanks
    
     Original Message
    From 	Summers Kelly  
    Sent	Thursday September 06 2001 1025 AM
    To	Adams Laurel Shackleton Sara
    Subject	FW FX confirmation info
    
    Laurel and Sara  UBS has an Internet Module that they are going to start using for Verbal Confims on FX trades  You can see a little of what it contains but they have informed me that we have to be set up with IDs and passwords sign a contract etc  The contact at UBS is Max Squire and he will provide me with more info when needed  Max has explained to me that as the trades are entered into the system we will go in and mark confirmed when we agree  It sounds very interesting to me  Let me know what you think  I can ask Max to send me the paperwork for your review
    
    Thank you
    
    Kelly Summers
    
     Original Message
    From 	valerielambeletubscomENRON mailtoIMCEANOTESvalerie2Elambelet40ubs2Ecom40ENRONENRONcom 
    Sent	Thursday September 06 2001 955 AM
    To	Summers Kelly
    Subject	FX confirmation info
    
    
    Kelly
    
    As per your request please find enclosed information about our FX confirmation
    tool as well as our URL to be able to have a look at our module in Internet
    
    httpwwwubscomecckeylinkmodulesconfirmationhtml
    
    Please feel free to call me if you need further information
    
    Regards
    Valerie
    
    
    Valerie Lambelet
    Relationship Manager
    UBS KeyLink Sales  Support
    Americas
    
    Helpdesk 1 203 719 3800
    Direct 1 203 719 3283
    Fax 1 203 719 5510
    httpwwwubscomkeylink
    
    
      Confirmationspdf  File Confirmationspdf  
    ..\maildir/heard-m/brokerage_agreements/2.
     MHEARD NonPrivilegedpst
    
    Lets add this to this afternoon
    
    Sara Shackleton
    Enron North America Corp
    1400 Smith Street EB 3801a
    Houston Texas  77002
    7138535620 phone
    7136463490 fax
    sarashackletonenroncom
     Forwarded by Sara ShackletonHOUECT on 06062001 1144 AM 
    
    
    	Thomas P Alterson altersonthomasjpmorgancom 06062001 1131 AM 	   To SaraShackletonenroncom  cc   Subject FW List of authorized traders	
    
    
    
    Have you been able to take a look at this
    
    I hate to be a pest but we are in the midst of an internal audit right now and
    we are being pressured for this list
    
    Thank you for your time
    Tom Alterson
    
    
    
    
    Thomas P Alterson
    06052001 1218 PM
    
    To   SaraShackletonenroncom
    cc
    Subject  List of authorized traders  Document link not converted
    
    Sara
    
    Would you please send me a list of authorized traders for Enron NA Corp  We are
    updating our files and noticed that we are missing an ATL for Enron
    
    ATL form
    See attached file ATL Enron NA Corpdoc
    
    Thank you
    Tom Alterson
    
    
    
      ATL Enron NA Corpdoc 
    ..\maildir/heard-m/brokerage_agreements/24.
     MHEARD NonPrivilegedpst
    
    Daniel  With respect to the Terms of Business Letter please email a copy of the proposed side letter to handle arbitration and limitation of liability  I just want to review the final product  We have all other documents ready for immediate execution  Sorry for the delay and I appreciate your patience  Regards
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    
    
     Original Message
    From 	Harris Daniel DanielHarrisgscomENRON mailtoIMCEANOTES22Harris2C20Daniel22203CDaniel2EHarris40gs2Ecom3E40ENRONENRONcom 
    Sent	Thursday September 20 2001 344 AM
    To	Shackleton Sara
    Cc	karasaxongscom Heard Marie talyagordongscom Glover Sheila
    Subject	RE ECT Investments Inc account with Goldman Sachs International
    
    Sara
    
    Arbitration  we will agree to English courts as per the language amending
    the osla I will prepare an amendment side letter
    
    Limitation of Liability  this is our standard position I propose the
    language agreeed to by you for the PB agreement
    
    I trust this will now close the open issues
    
    I look forward to hearing from you
    
    Kind regards
    
    Daniel
    
    Original Message
    From SaraShackletonenroncom mailtoSaraShackletonenroncom
    Sent 17 September 2001 2351
    To DanielHarrisgscom
    Cc karasaxongscom MarieHeardenroncom talyagordongscom
    SheilaGloverenroncom
    Subject RE ECT Investments Inc account with Goldman Sachs
    International
    
    
    Daniel
    
    Thank you for your response  Unfortunately the outstanding issues
    relating to the Terms of Business Letter impact our corporate policy  If
    you insist upon arbitration it should be at either partys option and we
    can agree to arbitrate in accordance with the International Chamber of
    Commerce Rules  Also as you mentioned below there may be nonprime
    brokerage issues that relate to the terms of business and therefore are
    not adequately addressed in the terms of business letter  We do have other
    business relationships with GSI and again request inclusion of limitation
    of liability language in the terms of business letter  I propose
    
    Neither party shall have any liability arising from this Letter or from
    any obligations which relate to this Letter for any indirect special
    punitive exemplary incidental or consequential loss or damage
    
    Please reconsider the foregoing with explanation  I will be out of the
    office 91801 in the am
    
    All remaining documents have been completed and we will have them executed
    together with the terms of business letter
    
    Regards  Sara
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    
    
        Original Message
       From   Harris Daniel DanielHarrisgscomENRON
    
    mailtoIMCEANOTES22Harris2C20Daniel22203CDaniel2EHarris40gs2Ecom
    3E40ENRONENRONcom
    
    
       Sent   Tuesday September 11 2001 315 AM
       To     Shackleton Sara
       Subject  RE ECT Investments Inc account with Goldman Sachs
                 International
    
       Sara
    
       The terms of business are GSIs general terms and span your relationship
       with GSI generally There may be nonprime brokerage issues that relate
       to
       the terms of business Not everything in the TOBs intersects with the PB
       relationship certainly if you do other business with GSI
    
       Re the liability provision I think your concerns are adequately
       addressed
       in the documentation as drafted
    
       I would be grateful if you would come back to me as soon as possible so
       we
       can try to get this wrapped up today
    
       Kind regards
    
       Daniel
    
       Original Message
       From SaraShackletonenroncom mailtoSaraShackletonenroncom
       Sent 10 September 2001 2102
       To DanielHarrisgscom
       Subject RE ECT Investments Inc account with Goldman Sachs
       International
    
    
       Daniel
    
       Thanks for the message  It seems to me that the terms of the PB
       conflict
       because J14 conflicts with A3 that is i  J14 conflicts with Par8
       requiring the conclusion that English courts will not apply to the Terms
       of
       Business agreement and ii A3 requires that English courts prevail
       Are
       you agreeing with this analysis
    
       Also there is nothing in the Terms of Business agreement to conflict
       with
       the limitation of liability language of the PB applicable to the PB
       except
       for silence on the matter  You didnt address this point  It is Enron
       Corp policy to include such language and I would like to limit the
       Terms
       of Business in the same manner
    
       Can you call me at 9 am Houston time on Tuesday Sept 11  or suggest a
       different time  I am not trying to belabor execution of the the
       remaining
       documents
    
       Thanks
    
       Sara Shackleton
       Enron Wholesale Services
       1400 Smith Street EB3801a
       Houston TX  77002
       Ph  713 8535620
       Fax 713 6463490
    
    
           Original Message
          From   Harris Daniel DanielHarrisgscomENRON
    
    
    mailtoIMCEANOTES22Harris2C20Daniel22203CDaniel2EHarris40gs2Ecom
       3E40ENRONENRONcom
    
    
          Sent   Monday September 10 2001 123 AM
          To     Shackleton Sara
          Cc     DaniellaBodmanMorrisgscom Heard Marie
          Subject  RE ECT Investments Inc account with Goldman Sachs
                    International
    
          Sara
          Actually I believe we resolved these when we spoke Arbitration 
       more
          appropriate to general terms of business which principally
       contemplate
          the
          regulatory rules to which we are subject SFA rules In the event of
          inconsistency the terms of the PB agreement govern clause A3
    
          I also amended the OSLA by side letter which I sent over
    
          Kind regards
    
          Daniel
    
          Original Message
          From Shackleton Sara mailtoSaraShackletonENRONcom
          Sent 07 September 2001 2045
          To DanielHarrisgscom
          Cc DaniellaBodmanMorrisgscom Heard Marie
          Subject ECT Investments Inc account with Goldman Sachs
       International
    
    
          Daniel
    
          Thanks for finalizing the Prime Brokerage Agreement the Agreement
          with my colleague Angela Davis
    
          I have two points with respect to the Terms of Business Letter
       relating
          to the changes made to the Agreement which I believe we discussed but
          were not in a position to resolve at the time  These are
    
          1  Par 8 Arbitration which should conform to Clause J Par 14 of
          the Agreement  I recall that we were discussing the possible use of
          arbitration in the Agreement and existence of arbitration in the
       OSLA
          so that we would not need to amend this particular paragraph of the
          Terms of Business Letter  Since we ultimately agreed to English
       courts
          I think we need to conform the Terms of Business Letter which will
          prevail if in conflict with the Agreement
    
          2  Par 8 Arbitration which should be limited in the same manner
       as
          Clause J Par 11 as to limitation of liability  I believe that you
          and Angela agreed to the revisions in the Agreement  Why shouldnt
          these be mirrored in the Terms of Business Letter
    
          I look forward to hearing from you and completing the rest of the
          account documentation  Regards
    
          Sara Shackleton
          Enron Wholesale Services
          1400 Smith Street EB3801a
          Houston TX  77002
          Ph  713 8535620
          Fax 713 6463490
    
    
    
    
       
          This email is the property of Enron Corp andor its relevant
       affiliate
          and
          may contain confidential and privileged material for the sole use of
       the
          intended recipient s Any review use distribution or disclosure
       by
          others is strictly prohibited If you are not the intended recipient
       or
          authorized to receive for the recipient please contact the sender
       or
          reply
          to Enron Corp at enronmessagingadministrationenroncom and delete
          all
          copies of the message This email and any attachments hereto are
       not
          intended to be an offer or an acceptance and do not create or
       evidence
          a
          binding and enforceable contract between Enron Corp or any of its
          affiliates and the intended recipient or any other party and may
       not
          be
          relied on by anyone as the basis of a contract by estoppel or
       otherwise
          Thank you
    
       
    ..\maildir/heard-m/brokerage_agreements/26.
     MHEARD NonPrivilegedpst
    
    Please forward your email requests to the RAC group for opening this account  Thanks  Sara
    
     Original Message
    From 	Doukas Tom  
    Sent	Tuesday October 09 2001 742 AM
    To	Shackleton Sara
    Cc	Heard Marie
    Subject	Prime Account Needed
    Importance	High
    
    We urgently need to have a new prime account set up at Bear Stearns to facilitate moving the Anker Coal positions from ECI into a fair value entity
    
    I had forwarded this to Frank Sayre as both of you were out on Friday  This is background information on why we need to set up the prime account for ECT Europe Investments Inc
    
    We have been given until the 12th to move the Anker positions to the ECT Entity  The account will need to be fully open and funded before the positions can move
    
    As this is of the highest priority please let me know immediately if there will be any issue with completing paperwork by tomorrow
    
    Thanks
    
    Tom
    
     Original Message
    From 	Doukas Tom  
    Sent	Friday October 05 2001 1005 AM
    To	Sayre Frank
    Subject	FW Anker
    Importance	High
    
    FYI
    
     Original Message
    From 	Cooper Edmund  
    Sent	Monday October 01 2001 122 PM
    To	Doukas Tom
    Subject	Anker
    Importance	High
    
    Dear Tom
    
    As we discussed here are the stipulations we talked about regarding the entity to which Enron Credit Inc can transfer the Anker stock and bonds Those stipulations regarding the transferee entity are that it
    
    1 must be a US company this is for tax reasons
    2 must be a fair value entity under US GAAP accounting reasons
    3 must by an entity whose results roll up to Enron Europe Limited account reporting purposes
    4 must be able to hold stocks and bonds viz have a brokerage account
    
    Of existing Enron entities the only entity that meets 1 to 3 is ECT Europe Investments Inc Therefore this entity would need a brokerage account This would appear to be the easiest  way in which to meet all four conditions
    
    As we discussed even if we gave Bear Stearns a global Enron Corp guaranty I do not think that ECT Europe Investments Inc would be able to take advantage of Enron Credit Incs funding arrangements unless it had further 0 risk weighted transactions held in the account
    
    Thanks and regards
    
    Edmund
    ..\maildir/heard-m/brokerage_agreements/27.
     MHEARD NonPrivilegedpst
    
    Tom  There is no existing Enron company named ECT Europe Investments Inc  We need the legal name of the company  Also does that company have existing resolutions for opening brokerage accounts
    
     Original Message
    From 	Doukas Tom  
    Sent	Tuesday October 09 2001 742 AM
    To	Shackleton Sara
    Cc	Heard Marie
    Subject	Prime Account Needed
    Importance	High
    
    We urgently need to have a new prime account set up at Bear Stearns to facilitate moving the Anker Coal positions from ECI into a fair value entity
    
    I had forwarded this to Frank Sayre as both of you were out on Friday  This is background information on why we need to set up the prime account for ECT Europe Investments Inc
    
    We have been given until the 12th to move the Anker positions to the ECT Entity  The account will need to be fully open and funded before the positions can move
    
    As this is of the highest priority please let me know immediately if there will be any issue with completing paperwork by tomorrow
    
    Thanks
    
    Tom
    
     Original Message
    From 	Doukas Tom  
    Sent	Friday October 05 2001 1005 AM
    To	Sayre Frank
    Subject	FW Anker
    Importance	High
    
    FYI
    
     Original Message
    From 	Cooper Edmund  
    Sent	Monday October 01 2001 122 PM
    To	Doukas Tom
    Subject	Anker
    Importance	High
    
    Dear Tom
    
    As we discussed here are the stipulations we talked about regarding the entity to which Enron Credit Inc can transfer the Anker stock and bonds Those stipulations regarding the transferee entity are that it
    
    1 must be a US company this is for tax reasons
    2 must be a fair value entity under US GAAP accounting reasons
    3 must by an entity whose results roll up to Enron Europe Limited account reporting purposes
    4 must be able to hold stocks and bonds viz have a brokerage account
    
    Of existing Enron entities the only entity that meets 1 to 3 is ECT Europe Investments Inc Therefore this entity would need a brokerage account This would appear to be the easiest  way in which to meet all four conditions
    
    As we discussed even if we gave Bear Stearns a global Enron Corp guaranty I do not think that ECT Europe Investments Inc would be able to take advantage of Enron Credit Incs funding arrangements unless it had further 0 risk weighted transactions held in the account
    
    Thanks and regards
    
    Edmund
    ..\maildir/heard-m/brokerage_agreements/33.
     MHEARD NonPrivilegedpst
    
    Thresa
    
    Just FYI the items below are requests of all brokers and ENAECT Investments Inc provide our own format ie we do not use the brokers format
    Please forward the agreement when you receive  Thanks  Sara
    
     Original Message
    From 	Brogan Theresa T  
    Sent	Friday October 12 2001 433 PM
    To	Shackleton Sara
    Subject	Keefe Bruyette  Woods
    
    Sara
    	We are looking to restart the process of  establishing an executing broker agreement with Keefe Bruyette  Woods  We had temporarily put this on hold due to the WTC attack  We have been given a new contact  Sheila had previously already sent the memo to authorize going forward with the process  Below are their requirements for account set up
    
    1  Corporate Resolution
    2  Trading Authorization
    3  Settlement Instructions
    
    
    I am trying to get them to fax me their broker agreement  As soon as I receive some documentation I will forward to you  
    
    Thanks
    Theresa
    ..\maildir/heard-m/brokerage_agreements/35.
     MHEARD NonPrivilegedpst
    
    Please forward your email to the RAC group requesting the opening of this account  Thanks  Sara
    
    Original Message
    From Doukas Tom 
    Sent Tuesday October 02 2001 1045 AM
    To Shackleton Sara
    Subject FW services agreement
    
    
    This is the primebroker agreement that we need to fill out for CSFB  They are holding two trades hostage so this is a priority for us  Please let me know if you require any further information
    
    Tom
    
    Original Message
    From Keneally Kelly mailtokellykeneallycsfbcom
    Sent Tuesday October 02 2001 1034 AM
    To Doukas Tom
    Subject FW services agreement
    
    
    
    
      Original Message
     From 	Ramirez Jonathan  
     Sent	Thursday August 09 2001 1113 AM
     To	Keneally Kelly
     Subject	services agreement 
     
      primebrokeragepdf 
    
    This message is for the named persons use only  It may contain 
    confidential proprietary or legally privileged information  No 
    confidentiality or privilege is waived or lost by any mistransmission
    If you receive this message in error please immediately delete it and all
    copies of it from your system destroy any hard copies of it and notify the
    sender  You must not directly or indirectly use disclose distribute 
    print or copy any part of this message if you are not the intended 
    recipient CREDIT SUISSE GROUP and each of its subsidiaries each reserve
    the right to monitor all email communications through its networks  Any
    views expressed in this message are those of the individual sender except
    where the message states otherwise and the sender is authorised to state 
    them to be the views of any such entity
    Unless otherwise stated any pricing information given in this message is 
    indicative only is subject to change and does not constitute an offer to 
    deal at any price quoted
    Any reference to the terms of executed transactions should be treated as 
    preliminary only and subject to our formal written confirmation
    
    
    ..\maildir/heard-m/brokerage_agreements/36.
     MHEARD NonPrivilegedpst
    
    Please forward your email to the RAC group requesting the opening of this account  Thanks Sara
    
    Original Message
    From Doukas Tom 
    Sent Tuesday October 02 2001 1134 AM
    To Shackleton Sara
    Cc Heard Marie
    Subject FW Prime Brokerage Clearance Services Agreement
    
    
    I have another one for you guys  I do not anticipate any urgency on this but would still like it out of the way
    
    Thanks
    
    Tom
    
    Original Message
    From Grice Regan mailtoregangricefunbcom
    Sent Tuesday October 02 2001 1110 AM
    To Doukas Tom
    Subject Prime Brokerage Clearance Services Agreement
    
    
    Mr Doukas
    
    On September 27 we opened a prime brokerage account in the name Enron
    Credit Inc Bear Stearns is acting as Prime Broker on this account and
    First Union Securities Inc is executing your securities The SEC requires
    the Clearing Broker us to have the attached agreement on file for our
    prime brokerage clients
    
    If you would be so kind as to have this agreement signed I would greatly
    appreciate it If you have any questions you can call me at 7045937024
    Once it is completed it can be faxed back to my attention at 7045937032
    
    Thank you and best regards
    
    
    Regan Grice
    First Union Capital Markets Documentation
    Phone 18007351470 or 7045937278
    Fax 7045937032
    	
    
     Enron Credit 151doc 
    ..\maildir/heard-m/brokerage_agreements/4.
     MHEARD NonPrivilegedpst
    
    
    
     Original Message
    From 	Glover Sheila  
    Sent	Tuesday August 07 2001 503 PM
    To	Lowry Donna Shackleton Sara Bradford William S Schultz Cassandra
    Cc	Charania Aneela Hickerson Gary
    Subject	ISI International Strategy  Investement Group Executing Broker Agreement
    
    We are requesting an Executing Broker Agreement be put in place with ISI  We are using for regional research coverage and order flow  
    
    ISI utilizes Bear Stearns for clearing services  We are requesting for ECT Investments Inc and will name GS MSDW and BS as our clearing brokers
    
    Regards Sheila
    ..\maildir/heard-m/brokerage_agreements/52.
     MHEARD NonPrivilegedpst
    
    Ill touch base with you this afternoon  Thanks
    
    Sara Shackleton
    Enron North America Corp
    1400 Smith Street EB 3801a
    Houston Texas  77002
    7138535620 phone
    7136463490 fax
    sarashackletonenroncom
     Forwarded by Sara ShackletonHOUECT on 06062001 1141 AM 
    
    
    	Cheryl NelsonENRON Sent by Cheryl NelsonENRON 06062001 0915 AM 	   To Sara ShackletonHOUECTECT  cc Tom DoukasEnronEnronXGate  Subject Enron Credit Inc	
    
    
    Sara as you assumed this matter see attachments
    
    Cheryl Nelson
    Senior Counsel
    EB3816
    713 3454693
    httpgssenroncom
     Forwarded by Cheryl NelsonNAEnron on 06062001 0914 AM 
    
    
    	Anthony Capolongo ACapolongomorganstanleycom 06062001 0743 AM Please respond to ACapolongo 	   To cherylnelsonenroncom  cc Ketan Parekh KetanParekhmorganstanleycom Joseph Marovich JosephMarovichmorganstanleycom tomdoukasenroncom  Subject Enron Credit Inc	
    
    
    
    Cheryl
    
    As requested attached are the Master Securities Loan Agreement and Cash
    Letter needed to set up Enron Credit Inc on the enhanced product
    Please forward two sets of originals to my attention and I will forward
    a fully exected original copy to Enron
    
    Regards
    
    Anthony
    
      EnronCreditdoc 
      Arranged Cash Agreement LetterDOC 
    
    ..\maildir/heard-m/brokerage_agreements/73.
     MHEARD NonPrivilegedpst
    
    Please print and I need a file ECICSFB brokerage agreement
    
    Original Message
    From Doukas Tom 
    Sent Tuesday October 02 2001 1045 AM
    To Shackleton Sara
    Subject FW services agreement
    
    
    This is the primebroker agreement that we need to fill out for CSFB  They are holding two trades hostage so this is a priority for us  Please let me know if you require any further information
    
    Tom
    
    Original Message
    From Keneally Kelly mailtokellykeneallycsfbcom
    Sent Tuesday October 02 2001 1034 AM
    To Doukas Tom
    Subject FW services agreement
    
    
    
    
      Original Message
     From 	Ramirez Jonathan  
     Sent	Thursday August 09 2001 1113 AM
     To	Keneally Kelly
     Subject	services agreement 
     
      primebrokeragepdf 
    
    This message is for the named persons use only  It may contain 
    confidential proprietary or legally privileged information  No 
    confidentiality or privilege is waived or lost by any mistransmission
    If you receive this message in error please immediately delete it and all
    copies of it from your system destroy any hard copies of it and notify the
    sender  You must not directly or indirectly use disclose distribute 
    print or copy any part of this message if you are not the intended 
    recipient CREDIT SUISSE GROUP and each of its subsidiaries each reserve
    the right to monitor all email communications through its networks  Any
    views expressed in this message are those of the individual sender except
    where the message states otherwise and the sender is authorised to state 
    them to be the views of any such entity
    Unless otherwise stated any pricing information given in this message is 
    indicative only is subject to change and does not constitute an offer to 
    deal at any price quoted
    Any reference to the terms of executed transactions should be treated as 
    preliminary only and subject to our formal written confirmation
    
    
    ..\maildir/heard-m/brokerage_agreements/8.
     MHEARD NonPrivilegedpst
    
    Sheila
    
    Thanks  Marie and I will handle  Sara
    
     Original Message
    From 	Glover Sheila  
    Sent	Thursday September 06 2001 1238 PM
    To	Shackleton Sara Heard Marie Nelson Cheryl
    Cc	Brogan Theresa T Charania Aneela
    Subject	FW NEW ACCOUNT PAPERWORK
    
    Sara Cheryl and Marie
    This is the documentation for the Executing Broker relationship with Keefe Bruyette  Woods  This is for ECT Investments Inc only and should be for BS MSDW and GS as clearing brokers
    Thanks Sheila
    
     Original Message
    From 	Wallendorf JeanMarie JWallendorfkbwcomENRON mailtoIMCEANOTES22Wallendorf2C20JeanMarie22203CJWallendorf40kbw2Ecom3E40ENRONENRONcom 
    Sent	Thursday September 06 2001 1142 AM
    To	Glover Sheila
    Subject	NEW ACCOUNT PAPERWORK
    
    Sheila
    
    This is the account paperwork that we spoke about If something does not
    apply let me know The legal contact is Karen Jackson 2123238509
    If you have any questions please feel free to contact me 2123238450
     Corp Respdf  FREERIDING  DVPWORKSHEET  Full Trading
    Authorizationdoc
    
     Thank You
     Jeanmarie Wallendorf
    
    
    
     This communication is confidential and is intended solely for the
     addressee  It is not to be forwarded to any other person or copied
     without the permission of the sender  Please notify the sender in the
     event you have received this communication in error  This communication
     is not an offer to sell or a solicitation of an offer to buy any
     securities discussed herein  Keefe Bruyette  Woods Inc makes no
     representation as to the accuracy or completeness of information contained
     in this communication
    
    
    
    
      Corp Respdf  File Corp Respdf  
      FREERIDING  File FREERIDING  
      DVPWORKSHEET  File DVPWORKSHEET  
      Full Trading Authorizationdoc  File Full Trading Authorizationdoc  
    ..\maildir/heard-m/brokerage_agreements/87.
     MHEARD NonPrivilegedpst
    
    FYI
    
    Original Message
    From Su Ellen 
    Sent Thursday October 11 2001 1031 AM
    To Shackleton Sara Adams Laurel
    Subject Repo Documents for NBC
    
    
    Sara
    Can you please review this repo document from NBC  I would really appreciate it  It is not a high priority so there is no rush 
    
    Laurel
    Can you check if we have an ISDA master in place with this counterparty  Thanks
    
    Please call me if you have any questions
    
    Ellen
    
    Original Message
    From ELLEN SU ENRON CORP mailtoELLENSU1bloombergnet
    Sent Thursday October 11 2001 1018 AM
    To Su Ellen
    Subject Fwd FW Repurchase Agreement
    
    
     Original Msg from BILL SCOON NBC INTERNATIONAL U At 1011  812
    
    ELLENHERE ARE THE REPO DOCSCOULD YOU PLEASE FORWARD THEM TO YOU LEGAL
    DEPT FOR THEIR REVIEWTHANKSBILL
     Original Msg from Berberyan  AriBerberyantresbncca At 1011  856
    
    
    
     Original Message
     From	Digiacomo Silvana 
     Sent	Wednesday October 10 2001 416 PM
     To	hockeyprobloombergnet
     Cc	Berberyan Ari
     Subject	Repurchase Agreement
     
    
     As per your request
      REPOpdf 
    
     
     Silvana DiGiacomo
     National Bank of Canada
     1155 Metcalfe  First Floor
     Montreal Quebec H3B 5G2
     Email  silvanadigiacomotresbncca
     Telephone  514 3946181
     Fax  514 3946271
      REPOpdf 
    
    ..\maildir/heard-m/deleted_items/122.
     MHEARD NonPrivilegedpst
    
    I have an 830 am doctors appointment
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    ..\maildir/heard-m/deleted_items/134.
     MHEARD NonPrivilegedpst
    
    
    
     Original Message
    From 	Shah Rajen  
    Sent	Thursday October 25 2001 158 AM
    To	Shackleton Sara Doukas Tom
    Cc	Heard Marie Hemsley Simon Wall David Jordan Kevin D Drew Rachel
    Subject	RE Investment companies
    
    The company is ECT Merchant Investments Corp
    
     Original Message
    From 	Shackleton Sara  
    Sent	24 October 2001 1958
    To	Shah Rajen Doukas Tom
    Cc	Heard Marie
    Subject	RE Investment companies
    
    Tom  Please forward your requisite email request to Donna Lowry Rick Carson and Bill Bradford with your request and reasons
    
    This will entail new account application board resolution and authorized trader list
    
    By the way the Enron Corp corporate database does not list a company with the name Enron Capital Trade Merchant Investments
    
    Thanks  Sara
    
     Original Message
    From 	Shah Rajen  
    Sent	Wednesday October 24 2001 108 PM
    To	Hemsley Simon Doukas Tom Shackleton Sara
    Cc	Drew Rachel Jordan Kevin D Wall David
    Subject	RE Investment companies
    
    OK for tax
    
     Original Message
    From 	Hemsley Simon  
    Sent	24 October 2001 1840
    To	Doukas Tom Shackleton Sara
    Cc	Drew Rachel Shah Rajen Jordan Kevin D Wall David
    Subject	RE Investment companies
    
    Tom  I will try and get a USD accounting entity set up in SAP that rolls up to 
    ECTEF Inc for legal purposes 
    
    Im fairly sure we can do this as 54R ECTRL Global Divisions is a  accounting entity of 
    138 ECTRL a  entity
    
    Raj  do you have a problem from a tax perspective of having this set up
    
     Original Message
    From 	Doukas Tom  
    Sent	24 October 2001 1831
    To	Hemsley Simon Shackleton Sara
    Cc	Drew Rachel Shah Rajen Jordan Kevin D Wall David
    Subject	RE Investment companies
    Importance	High
    
    It will mean doing the paperwork all over again and closing the old account assuming ECTEF does not want an account  This is not a name change but actually a transfer from one entity to another  It will likely require resolutions to open brokerage accounts for ECTMI if they dont already have them in place
    
    Aside from looking foolish to Bear I anticipate no hassles from them  As far as our side it would be wise to have legal give their estimation of the effort and time frame needed to duplicate the effort that was made for ECTMI
    
    Sara how feasible would it be to prepare account paperwork for the ECTMI company to obtain a Bear account
    
    Thanks
    
    Tom
    
     Original Message
    From 	Hemsley Simon  
    Sent	Wednesday October 24 2001 1204 PM
    To	Shah Rajen Jordan Kevin D Doukas Tom Wall David
    Cc	Drew Rachel
    Subject	FW Investment companies
    
    I will investigate getting an accounting entity set up for ECTMI  does anyone know the number
    
    in the meantime and dont shout at me Tom how easy would it be to change the ECTEF
    broker account to have a new name  see company below
    
     Original Message
    From 	Wall David  
    Sent	26 September 2001 1545
    To	Hemsley Simon
    Subject	FW Investment companies
    
    We must move Anker as a matter of urgency  Lets discuss
    
    Thanks
    
     Original Message
    From 	Jordan Kevin D  
    Sent	26 September 2001 1542
    To	Wall David Seyfried Bryan
    Cc	Aiken Buddy Allen Melissa
    Subject	Investment companies
    
    After speaking with David Wall about which entity the Anker debt and equity should be moved to I placed a few quick phone calls  I understand that there may be some confusion about what is an investment company and which ones are available
    
    The ENA transaction support team suggested ECTMI Enron Capital Trade Merchant Investments as one possibility  There may be another US investment company and an investment company set up in the Netherlands  The people to follow up with would be Faith Killen andor Elaine Shields who work in ECT accounting
    
    I hope this helps you identify where to move the investments  Please keep me updated on the completion of that move  AA will be sensitive to us making that transfer into the investment company in the quarter that we have booked earnings on the investments as merchant investments  As discussed with David Wall that is consistent with Enrons corporate policy on merchant investments and with the Investment Company Audit Guide which governs our merchant investment accounting
    
    If you have any further questions or if I can be of further assistance please contact me at ext 35882
    
    Regards
    
    Kevin
    ..\maildir/heard-m/deleted_items/38.
     MHEARD NonPrivilegedpst
    
    Daniel
    
    What about the limitation of liability language which you suggested could be similar to the PB agreement your 92001 email
    
    Sara 
    
     Original Message
    From 	Harris Daniel DanielHarrisgscomENRON  
    Sent	Tuesday October 09 2001 151 AM
    To	Shackleton Sara DanielHarrisgscom
    Cc	karasaxongscom Heard Marie talyagordongscom Glover Sheila
    Subject	RE ECT Investments Inc account with Goldman Sachs International
    
    Please see attached
    
    Original Message
    From SaraShackletonenroncom mailtoSaraShackletonenroncom
    Sent 08 October 2001 2039
    To DanielHarrisgscom
    Cc karasaxongscom MarieHeardenroncom talyagordongscom
    SheilaGloverenroncom
    Subject RE ECT Investments Inc account with Goldman Sachs
    International
    
    
    Daniel  With respect to the Terms of Business Letter please email a copy
    of the proposed side letter to handle arbitration and limitation of
    liability  I just want to review the final product  We have all other
    documents ready for immediate execution  Sorry for the delay and I
    appreciate your patience  Regards
    
    Sara Shackleton
    Enron Wholesale Services
    1400 Smith Street EB3801a
    Houston TX  77002
    Ph  713 8535620
    Fax 713 6463490
    
    
        Original Message
       From   Harris Daniel DanielHarrisgscomENRON
    
    mailtoIMCEANOTES22Harris2C20Daniel22203CDaniel2EHarris40gs2Ecom
    3E40ENRONENRONcom
    
    
       Sent   Thursday September 20 2001 344 AM
       To     Shackleton Sara
       Cc     karasaxongscom Heard Marie talyagordongscom Glover
                 Sheila
       Subject  RE ECT Investments Inc account with Goldman Sachs
                 International
    
       Sara
    
       Arbitration  we will agree to English courts as per the language
       amending
       the osla I will prepare an amendment side letter
    
       Limitation of Liability  this is our standard position I propose the
       language agreeed to by you for the PB agreement
    
       I trust this will now close the open issues
    
       I look forward to hearing from you
    
       Kind regards
    
       Daniel
    
       Original Message
       From SaraShackletonenroncom mailtoSaraShackletonenroncom
       Sent 17 September 2001 2351
       To DanielHarrisgscom
       Cc karasaxongscom MarieHeardenroncom talyagordongscom
       SheilaGloverenroncom
       Subject RE ECT Investments Inc account with Goldman Sachs
       International
    
    
       Daniel
    
       Thank you for your response  Unfortunately the outstanding issues
       relating to the Terms of Business Letter impact our corporate policy
       If
       you insist upon arbitration it should be at either partys option and
       we
       can agree to arbitrate in accordance with the International Chamber of
       Commerce Rules  Also as you mentioned below there may be nonprime
       brokerage issues that relate to the terms of business and therefore
       are
       not adequately addressed in the terms of business letter  We do have
       other
       business relationships with GSI and again request inclusion of
       limitation
       of liability language in the terms of business letter  I propose
    
       Neither party shall have any liability arising from this Letter or from
       any obligations which relate to this Letter for any indirect special
       punitive exemplary incidental or consequential loss or damage
    
       Please reconsider the foregoing with explanation  I will be out of the
       office 91801 in the am
    
       All remaining documents have been completed and we will have them
       executed
       together with the terms of business letter
    
       Regards  Sara
    
       Sara Shackleton
       Enron Wholesale Services
       1400 Smith Street EB3801a
       Houston TX  77002
       Ph  713 8535620
       Fax 713 6463490
    
    
           Original Message
          From   Harris Daniel DanielHarrisgscomENRON
    
    
    mailtoIMCEANOTES22Harris2C20Daniel22203CDaniel2EHarris40gs2Ecom
       3E40ENRONENRONcom
    
    
          Sent   Tuesday September 11 2001 315 AM
          To     Shackleton Sara
          Subject  RE ECT Investments Inc account with Goldman Sachs
                    International
    
          Sara
    
          The terms of business are GSIs general terms and span your
       relationship
          with GSI generally There may be nonprime brokerage issues that
       relate
          to
          the terms of business Not everything in the TOBs intersects with the
       PB
          relationship certainly if you do other business with GSI
    
          Re the liability provision I think your concerns are adequately
          addressed
          in the documentation as drafted
    
          I would be grateful if you would come back to me as soon as possible
       so
          we
          can try to get this wrapped up today
    
          Kind regards
    
          Daniel
    
          Original Message
          From SaraShackletonenroncom mailtoSaraShackletonenroncom
          Sent 10 September 2001 2102
          To DanielHarrisgscom
          Subject RE ECT Investments Inc account with Goldman Sachs
          International
    
    
          Daniel
    
          Thanks for the message  It seems to me that the terms of the PB
          conflict
          because J14 conflicts with A3 that is i  J14 conflicts with Par8
          requiring the conclusion that English courts will not apply to the
       Terms
          of
          Business agreement and ii A3 requires that English courts prevail
          Are
          you agreeing with this analysis
    
          Also there is nothing in the Terms of Business agreement to conflict
          with
          the limitation of liability language of the PB applicable to the PB
          except
          for silence on the matter  You didnt address this point  It is
       Enron
          Corp policy to include such language and I would like to limit the
          Terms
          of Business in the same manner
    
          Can you call me at 9 am Houston time on Tuesday Sept 11  or
       suggest a
          different time  I am not trying to belabor execution of the the
          remaining
          documents
    
          Thanks
    
          Sara Shackleton
          Enron Wholesale Services
          1400 Smith Street EB3801a
          Houston TX  77002
          Ph  713 8535620
          Fax 713 6463490
    
    
              Original Message
             From   Harris Daniel DanielHarrisgscomENRON
    
    
    
    mailtoIMCEANOTES22Harris2C20Daniel22203CDaniel2EHarris40gs2Ecom
          3E40ENRONENRONcom
    
    
             Sent   Monday September 10 2001 123 AM
             To     Shackleton Sara
             Cc     DaniellaBodmanMorrisgscom Heard Marie
             Subject  RE ECT Investments Inc account with Goldman Sachs
                       International
    
             Sara
             Actually I believe we resolved these when we spoke Arbitration 
          more
             appropriate to general terms of business which principally
          contemplate
             the
             regulatory rules to which we are subject SFA rules In the event
       of
             inconsistency the terms of the PB agreement govern clause A3
    
             I also amended the OSLA by side letter which I sent over
    
             Kind regards
    
             Daniel
    
             Original Message
             From Shackleton Sara mailtoSaraShackletonENRONcom
             Sent 07 September 2001 2045
             To DanielHarrisgscom
             Cc DaniellaBodmanMorrisgscom Heard Marie
             Subject ECT Investments Inc account with Goldman Sachs
          International
    
    
             Daniel
    
             Thanks for finalizing the Prime Brokerage Agreement the
       Agreement
             with my colleague Angela Davis
    
             I have two points with respect to the Terms of Business Letter
          relating
             to the changes made to the Agreement which I believe we discussed
       but
             were not in a position to resolve at the time  These are
    
             1  Par 8 Arbitration which should conform to Clause J Par 14
       of
             the Agreement  I recall that we were discussing the possible use
       of
             arbitration in the Agreement and existence of arbitration in the
          OSLA
             so that we would not need to amend this particular paragraph of
       the
             Terms of Business Letter  Since we ultimately agreed to English
          courts
             I think we need to conform the Terms of Business Letter which will
             prevail if in conflict with the Agreement
    
             2  Par 8 Arbitration which should be limited in the same
       manner
          as
             Clause J Par 11 as to limitation of liability  I believe that
       you
             and Angela agreed to the revisions in the Agreement  Why
       shouldnt
             these be mirrored in the Terms of Business Letter
    
             I look forward to hearing from you and completing the rest of the
             account documentation  Regards
    
             Sara Shackleton
             Enron Wholesale Services
             1400 Smith Street EB3801a
             Houston TX  77002
             Ph  713 8535620
             Fax 713 6463490
    
    
    
    
    
       
             This email is the property of Enron Corp andor its relevant
          affiliate
             and
             may contain confidential and privileged material for the sole use
       of
          the
             intended recipient s Any review use distribution or
       disclosure
          by
             others is strictly prohibited If you are not the intended
       recipient
          or
             authorized to receive for the recipient please contact the
       sender
          or
             reply
             to Enron Corp at enronmessagingadministrationenroncom and
       delete
             all
             copies of the message This email and any attachments hereto
       are
          not
             intended to be an offer or an acceptance and do not create or
          evidence
             a
             binding and enforceable contract between Enron Corp or any of
       its
             affiliates and the intended recipient or any other party and may
          not
             be
             relied on by anyone as the basis of a contract by estoppel or
          otherwise
             Thank you
    
    
       
    
    
      Amendment to TOBsdoc  File Amendment to TOBsdoc  
    ..\maildir/heard-m/deleted_items/48.
     MHEARD NonPrivilegedpst
    
    
    
     Original Message
    From 	Harris Daniel DanielHarrisgscomENRON  
    Sent	Thursday October 11 2001 711 AM
    To	Shackleton Sara DanielHarrisgscom
    Cc	Gordon Talya
    Subject	RE ECT Investments Inc account with Goldman Sachs International
    
    please see attached
    if ok please sign and fax back to 44 20 7774 0457
    
    Original Message
    From SaraShackletonenroncom mailtoSaraShackletonenroncom
    Sent 09 October 2001 1952
    To DanielHarrisgscom
    Subject RE ECT Investments Inc account with Goldman Sachs
    International
    
    
    Sorry to not reply sooner  too many interruptions  see attached I tried
    to track language in PB  Sara
    
    See attached file Amendment to GSI TOBR1doc
    
        Original Message
       From   Harris Daniel DanielHarrisgscomENRON
       Sent   Tuesday October 09 2001 821 AM
       To     Shackleton Sara DanielHarrisgscom DanielHarrisgscom
       Cc     karasaxongscom Heard Marie talyagordongscom Glover
                 Sheila
       Subject  RE ECT Investments Inc account with Goldman Sachs
                 International
    
       please let me know which provision of the terms of business you think
       should
       be amended in that way
    
       Original Message
       From SaraShackletonenroncom mailtoSaraShackletonenroncom
       Sent 09 October 2001 1418
       To DanielHarrisgscom DanielHarrisgscom
       Cc karasaxongscom MarieHeardenroncom talyagordongscom
       SheilaGloverenroncom
       Subject RE ECT Investments Inc account with Goldman Sachs
       International
    
    
       Daniel
    
       What about the limitation of liability language which you suggested
       could
       be similar to the PB agreement your 92001 email
    
       Sara
    
           Original Message
          From   Harris Daniel DanielHarrisgscomENRON
          Sent   Tuesday October 09 2001 151 AM
          To     Shackleton Sara DanielHarrisgscom
          Cc     karasaxongscom Heard Marie talyagordongscom Glover
                    Sheila
          Subject  RE ECT Investments Inc account with Goldman Sachs
                    International
    
          Please see attached
    
          Original Message
          From SaraShackletonenroncom mailtoSaraShackletonenroncom
          Sent 08 October 2001 2039
          To DanielHarrisgscom
          Cc karasaxongscom MarieHeardenroncom talyagordongscom
          SheilaGloverenroncom
          Subject RE ECT Investments Inc account with Goldman Sachs
          International
    
    
          Daniel  With respect to the Terms of Business Letter please email a
          copy
          of the proposed side letter to handle arbitration and limitation of
          liability  I just want to review the final product  We have all
          other
          documents ready for immediate execution  Sorry for the delay and I
          appreciate your patience  Regards
    
          Sara Shackleton
          Enron Wholesale Services
          1400 Smith Street EB3801a
          Houston TX  77002
          Ph  713 8535620
          Fax 713 6463490
    
    
              Original Message
             From   Harris Daniel DanielHarrisgscomENRON
    
    
    
    mailtoIMCEANOTES22Harris2C20Daniel22203CDaniel2EHarris40gs2Ecom
          3E40ENRONENRONcom
    
    
             Sent   Thursday September 20 2001 344 AM
             To     Shackleton Sara
             Cc     karasaxongscom Heard Marie talyagordongscom
       Glover
                       Sheila
             Subject  RE ECT Investments Inc account with Goldman Sachs
                       International
    
             Sara
    
             Arbitration  we will agree to English courts as per the language
             amending
             the osla I will prepare an amendment side letter
    
             Limitation of Liability  this is our standard position I propose
          the
             language agreeed to by you for the PB agreement
    
             I trust this will now close the open issues
    
             I look forward to hearing from you
    
             Kind regards
    
             Daniel
    
             Original Message
             From SaraShackletonenroncom mailtoSaraShackletonenroncom
             Sent 17 September 2001 2351
             To DanielHarrisgscom
             Cc karasaxongscom MarieHeardenroncom talyagordongscom
             SheilaGloverenroncom
             Subject RE ECT Investments Inc account with Goldman Sachs
             International
    
    
             Daniel
    
             Thank you for your response  Unfortunately the outstanding
       issues
             relating to the Terms of Business Letter impact our corporate
       policy
             If
             you insist upon arbitration it should be at either partys option
          and
             we
             can agree to arbitrate in accordance with the International
       Chamber
          of
             Commerce Rules  Also as you mentioned below there may be
       nonprime
             brokerage issues that relate to the terms of business and
       therefore
             are
             not adequately addressed in the terms of business letter  We do
       have
             other
             business relationships with GSI and again request inclusion of
             limitation
             of liability language in the terms of business letter  I propose
    
             Neither party shall have any liability arising from this Letter
       or
          from
             any obligations which relate to this Letter for any indirect
          special
             punitive exemplary incidental or consequential loss or damage
    
             Please reconsider the foregoing with explanation  I will be out
       of
          the
             office 91801 in the am
    
             All remaining documents have been completed and we will have them
             executed
             together with the terms of business letter
    
             Regards  Sara
    
             Sara Shackleton
             Enron Wholesale Services
             1400 Smith Street EB3801a
             Houston TX  77002
             Ph  713 8535620
             Fax 713 6463490
    
    
                 Original Message
                From   Harris Daniel DanielHarrisgscomENRON
    
    
    
    
    mailtoIMCEANOTES22Harris2C20Daniel22203CDaniel2EHarris40gs2Ecom
             3E40ENRONENRONcom
    
    
                Sent   Tuesday September 11 2001 315 AM
                To     Shackleton Sara
                Subject  RE ECT Investments Inc account with Goldman Sachs
                          International
    
                Sara
    
                The terms of business are GSIs general terms and span your
             relationship
                with GSI generally There may be nonprime brokerage issues
       that
             relate
                to
                the terms of business Not everything in the TOBs intersects
       with
          the
             PB
                relationship certainly if you do other business with GSI
    
                Re the liability provision I think your concerns are
       adequately
                addressed
                in the documentation as drafted
    
                I would be grateful if you would come back to me as soon as
          possible
             so
                we
                can try to get this wrapped up today
    
                Kind regards
    
                Daniel
    
                Original Message
                From SaraShackletonenroncom
       mailtoSaraShackletonenroncom
                Sent 10 September 2001 2102
                To DanielHarrisgscom
                Subject RE ECT Investments Inc account with Goldman Sachs
                International
    
    
                Daniel
    
                Thanks for the message  It seems to me that the terms of the
       PB
                conflict
                because J14 conflicts with A3 that is i  J14 conflicts with
          Par8
                requiring the conclusion that English courts will not apply to
       the
             Terms
                of
                Business agreement and ii A3 requires that English courts
          prevail
                Are
                you agreeing with this analysis
    
                Also there is nothing in the Terms of Business agreement to
          conflict
                with
                the limitation of liability language of the PB applicable to
       the
          PB
                except
                for silence on the matter  You didnt address this point  It
       is
             Enron
                Corp policy to include such language and I would like to limit
          the
                Terms
                of Business in the same manner
    
                Can you call me at 9 am Houston time on Tuesday Sept 11  or
             suggest a
                different time  I am not trying to belabor execution of the
       the
                remaining
                documents
    
                Thanks
    
                Sara Shackleton
                Enron Wholesale Services
                1400 Smith Street EB3801a
                Houston TX  77002
                Ph  713 8535620
                Fax 713 6463490
    
    
                    Original Message
                   From   Harris Daniel DanielHarrisgscomENRON
    
    
    
    
    
    mailtoIMCEANOTES22Harris2C20Daniel22203CDaniel2EHarris40gs2Ecom
                3E40ENRONENRONcom
    
    
                   Sent   Monday September 10 2001 123 AM
                   To     Shackleton Sara
                   Cc     DaniellaBodmanMorrisgscom Heard Marie
                   Subject  RE ECT Investments Inc account with Goldman
       Sachs
                             International
    
                   Sara
                   Actually I believe we resolved these when we spoke
          Arbitration 
                more
                   appropriate to general terms of business which principally
                contemplate
                   the
                   regulatory rules to which we are subject SFA rules In the
          event
             of
                   inconsistency the terms of the PB agreement govern clause
          A3
    
                   I also amended the OSLA by side letter which I sent over
    
                   Kind regards
    
                   Daniel
    
                   Original Message
                   From Shackleton Sara mailtoSaraShackletonENRONcom
                   Sent 07 September 2001 2045
                   To DanielHarrisgscom
                   Cc DaniellaBodmanMorrisgscom Heard Marie
                   Subject ECT Investments Inc account with Goldman Sachs
                International
    
    
                   Daniel
    
                   Thanks for finalizing the Prime Brokerage Agreement the
             Agreement
                   with my colleague Angela Davis
    
                   I have two points with respect to the Terms of Business
       Letter
                relating
                   to the changes made to the Agreement which I believe we
          discussed
             but
                   were not in a position to resolve at the time  These are
    
                   1  Par 8 Arbitration which should conform to Clause J
       Par
          14
             of
                   the Agreement  I recall that we were discussing the
       possible
          use
             of
                   arbitration in the Agreement and existence of arbitration
       in
          the
                OSLA
                   so that we would not need to amend this particular paragraph
       of
             the
                   Terms of Business Letter  Since we ultimately agreed to
          English
                courts
                   I think we need to conform the Terms of Business Letter
       which
          will
                   prevail if in conflict with th48
























































































































































































