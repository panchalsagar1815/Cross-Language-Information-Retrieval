# Cross-Language-Information-Retrieval

Overview
This project aims to build a cross-language information retrieval system (CLIR) which, given a query in German, will be capable of searching text documents written in English and displaying the results in German.

We're going to use machine translation, and information retrieval using a vector space model, and then assess the performance of the system using IR evaluation techniques.

Parts of the project are explained as we progress.

Data Used
bitext. (en, de): A sentence-aligned, parallel German-English corpus, sourced from the Europarl corpus (which is a collection of debates held in the EU parliament over several years). We'll use this to develop word-alignment tools and build a translation probability table.

newest. (en, de): A separate, smaller parallel corpus for evaluation of the translation system.

devel. (docs, queries,qrel): A set of documents in English (sourced from Wikipedia), queries in German, and relevance judgement scores for each query-document pair.

The files are available to check out in the data/clir directory of the repo.

Housekeeping: File encodings and tokenisation
Since the data files we use is utf-8 encoded text, we need to convert the strings into ASCII by escaping the special symbols.

As mentioned earlier, we're going to build a CLIR engine consisting of information retrieval and translation components, and then evaluate its accuracy.

The CLIR system will:

translate queries from German into English (because our searcheable corpus is in English), using word-based translation, a rather simplistic approach as opposed to the sophistication you might see in, say, Google Translate.
search over the document corpus using the Okapi BM25 IR ranking model, a variation of the traditional TF-IDF model.
evaluate the quality of ranked retrieval results using the query relevance judgements.
Information Retrieval using Okapi BM25
We'll start by building an IR system, and give it a test run with some English queries.

Here's an overview of the tasks involved:

Loading the data files, and tokenizing the input.
Preprocessing the lexicon by stemming, removing stopwords.
Calculating the TF/IDF representation for all documents in our wikipedia corpus.
Storing an inverted index to efficiently documents, given a query term.
Implementing querying with BM25.
Test runs.
So for our first task, we'll load the devel.docs file, extract and tokenize the terms, and store them in a python dictionary with the document ids as keys.
