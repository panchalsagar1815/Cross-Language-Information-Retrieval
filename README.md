# Cross-Language-Information-Retrieval

Overview
This project aims to build a cross-language information retrieval system (CLIR) which, given a query in German, will be capable of searching text documents written in English and displaying the results in German.

We're going to use machine translation, and information retrieval using a vector space model, and then assess the performance of the system using IR evaluation techniques.

Parts of the project are explained as we progress.

Data Used
bitext. (en, de): A sentence-aligned, parallel German-English corpus, sourced from the Europarl corpus (which is a collection of debates held in the EU parliament over several years). We'll use this to develop word-alignment tools and build a translation probability table.

newest. (en, de): A separate, smaller parallel corpus for evaluation of the translation system.

devel. (docs, queries, rel): A set of documents in English (sourced from Wikipedia), queries in German, and relevance judgement scores for each query-document pair.

The files are available to check out in the data/clir directory of the repo.

Housekeeping: File encodings and tokenisation
Since the data files we use are utf-8 encoded text, we need to convert the strings into ASCII by escaping the special symbols.

As mentioned earlier, we're going to build a CLIR engine consisting of information retrieval and translation components, and then evaluate its accuracy.

The CLIR system will:

translate queries from German into English (because our searchable corpus is in English), using word-based translation, a rather simplistic approach as opposed to the sophistication you might see in, say, Google Translate.
search over the document corpus using the Okapi BM25 IR ranking model, a variation of the traditional TF-IDF model.
evaluate the quality of ranked retrieval results using the query relevance judgements.
Information Retrieval using Okapi BM25
We'll start by building an IR system, and give it a test run with some English queries.

Here's an overview of the tasks involved:

Loading the data files, and tokenizing the input.
Preprocessing the lexicon by stemming, and removing stopwords.
Calculating the TF/IDF representation for all documents in our Wikipedia corpus.
Storing an inverted index to efficiently document, given a query term.
Implementing querying with BM25.
Test runs.

So for our first task, we'll load the devel. docs file, extract and tokenize the terms, and store them in a Python dictionary with the document IDs as keys.
We took the default values for k1 and b (1.5 and 0.5), which seemed to give good results. Although these parameters may be altered depending on the type of data being dealt with.

Now we create a method to retrieve the query component and another method that will use the previous ones and retrieve the relevant documents for a query, sorted based on their ranks.

The information retrieval engine has worked quite well in this case. The top-ranked document for the query is a snippet of the Wikipedia article for Manchester United Football Club.

On further inspection, we can see that the documents ranked lower are, for example, for The University of Manchester, or even just articles with the words 'Manchester' or 'United' in them.

Now we can begin translating the German queries to English.

Query Translation:
For translation, we'll implement a simple word-based translation model in a noisy channel setting. This means that we'll use both a language model over English and a translation model.

We'll use a unigram language model for decoding/translation, but also create a model with trigram to test the performance improvement).

We aim to find the string, 
 which maximises, given the English output string and the German input string.

Language Model:
From Wikipedia: A statistical language model is a probability distribution over sequences of words. Given such a sequence, say of length m, it assigns a probability P(w1,....,wm) to the whole sequence.

The models will be trained on the 'bitext. en' file, and tested on 'newest. en'.

As we train the model on different files, it's obvious that we'll run into words (unigrams) and trigrams that we hadn't seen in the file we trained the model on. To account for this unknown information, we'll use add-k or Laplace smoothing for the unigram and Katz-Backoff smoothing for the trigram model.

Let's start with calculating the unigram, bigram and trigram counts (we need the bigram counts for trigram smoothing). The sentences are also converted appropriately by adding sentences at the start and end of sentences.

For the unigram language model, the perplexity for different values of k was as follow:

k	Perplexity
0.0001	613.92
0.01	614.03
0.1	628.82
1	823.302
For the tri-gram model, Katz-Backoff smoothing was chosen as it takes a discounted probability for things only seen once, and backs off to a lower level n-gram for unencountered n-grams.

Compared with the trigram model, the perplexity was as follows:

Model	Perplexity
Unigram (Best K)	613.92
Trigram (Katz Backoff)	461.65
As can be seen, the trigram model with 'Katz Backoff' smoothing seems to perform better than the best unigram model (with k = 0.0001). Thus we can say that this model is better for predicting the sequence of a sentence than unigram, which should is obvious if you think about it.

Translation model
Next, we'll estimate translation model probabilities. For this, we'll use IBM1 from the NLTK library. IBM1 learns word-based translation probabilities using expectation maximisation.

We'll use both 'bitext.de' and 'bitext. en' files for this purpose; extract the sentences from each, and then use IBM1 to build the translation tables.

Translations obtained through Google Translate are better. It's interesting to note that our translation engine works well if a 'word-word' translation is considered, and if the word-pair has been encountered enough times in the bi-lingual corpora.

Google Translate also seems to perform better as it considers phrase-based translation, which is more sophisticated and accurate than word-word translation.

Our engine also seems to work better for function words rather than content words as those would have been the ones encountered a lot in the bi-corpora and are better aligned.

The alignments were combined by taking the intersection of the forward and reverse alignments in this case. Combining the two alignments improved things in the sense that the intersection got rid of all the extra 'noise' in the alignments so that the most likely ones remained (that existed both in the forward and reverse direction).

Combining, and Evaluation
For the final bit, we'll create a function that translates a query and retrieves the relevant documents for it.

Then, to evaluate the results of our CLIR engine, we'll use the Mean Average Precision to judge the performance of the CLIR system. MAP is a standard evaluation metric used in IR.

With that, our basic CLIR system is complete. Improvements could be made, especially in the translation component by using a phrase-based model. Or we could use Google to translate the queries for us, and see how well the IR system performs. But that's another area of exploration.
