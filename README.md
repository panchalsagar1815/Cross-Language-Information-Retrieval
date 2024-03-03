# Cross-Language-Information-Retrieval

Overview
This project aims to build a cross-language information retrieval system (CLIR) which, given a query in German, will be capable of searching text documents written in English and displaying the results in German.

We're going to use machine translation, and information retrieval using a vector space model, and then assess the performance of the system using IR evaluation techniques.

Parts of the project are explained as we progress.

Data Used
bitext. (en, de): A sentence-aligned, parallel German-English corpus, sourced from the Europarl corpus (which is a collection of debates held in the EU parliament over a number of years). We'll use this to develop word-alignment tools, and build a translation probability table.

newstest.(en, de): A separate, smaller parallel corpus for evaulation of the translation system.

devel.(docs,queries,qrel): A set of documents in English (sourced from Wikipedia), queries in German, and relevance judgement scores for each query-document pair.

The files are available to check out in the data/clir directory of the repo.

Housekeeping: File encodings and tokenisation
Since the data files we use is utf-8 encoded text, we need to convert the strings into ASCII by escaping the special symbols.
