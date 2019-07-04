---
layout: post
comments: true
mathjax: true
priority: 440000
title: “Introduction to ad-hoc information retrieval”
tags: [information retrieval]
excerpt: information retrieval
img: ir.png
date: 2019-06-07 12:00:00
---

## Task overview:

Given a query and a corpus, find the relevant items
* query: textual description of information need
* corpus: a collection of textual documents
* relevance: satisfaction of the user’s information need

## Basic Information Retrieval Process
<div class="imgcap">
<img src="https://user-images.githubusercontent.com/22668421/59152815-f8882c00-8a19-11e9-8671-9c0cc09ab061.png" style="border:none;width:80%">
</div>

## Document Representation 
* Document representation = deciding which concepts should go in the index
* Option 1 (controlled vocabulary): a set a manually constructed concepts that describe the major topics covered in the collection
* Option 2 (free-text indexing): the set of individual terms that occur in the collection


## Controlled vocab
* Controlled vocabulary: a set of well-defined concepts
* Assigned to documents by annotators (or automatically) example: MeSH

## free-text indexing
* Represent documents using terms within the document
* Which terms? Only the most descriptive terms? Only the
unambiguous ones? All of them?
* Usually, all of them (a.k.a. full-text indexing)
* The user will use term-combinations to express higher level concepts
* Query terms will hopefully disambiguate each other (e.g., “volkswagen golf”)

## free-text indexing preprocess
* Step 1: mark-up removal: remove html tags
* Step 2: down-casing
* Step 3: tokenization
* Step 4: stopword removal
* Step 5: create an index using the union of all remaining terms

## Boolean Retrieval
* Assumption: the user wants to find all the relevant documents and only the relevant documents
* evaluation metric: precision and recall
* Query is explicit using AND, OR. example: "Trump AND Xi"

## Most Basic View of a Search Engine
* It uses an index to quickly locate the relevant documents
* Index: a list of concepts and pointers to documents that discuss them

## Bit-map index
<div class="imgcap">
<img src="https://user-images.githubusercontent.com/22668421/59160950-26b54c80-8aaa-11e9-8505-10505e26334a.png" style="border:none;width:80%">
</div>

* 1 = the word appears in the document at least once
* 0 = the word does not appear in the document
* Does not represent word frequency, order, or location information
* the matrix is very sparse
* example of bit-map query and search process:

<div class="imgcap">
<img src="https://user-images.githubusercontent.com/22668421/59160971-6c721500-8aaa-11e9-8c62-e642305f83e1.png" style="border:none;width:80%">
</div>
<div class="imgcap">
<img src="https://user-images.githubusercontent.com/22668421/59161010-eaceb700-8aaa-11e9-8649-4a63c79a2860.png" style="border:none;width:80%">
</div>

* Alternative: represent only the 1’s: df = number of documents in which the term appears at least once
* all documents need to be sorted by docid
* search process(AND):
  - If docids are equal, add docid to results and increment both pointers
  - If docids are not equal, increment pointer with lowest docid
  - Repeat until (1) end of one list and (2) docid from other list is greater (early stopping)

<div class="imgcap">
<img src="https://user-images.githubusercontent.com/22668421/59161074-c1625b00-8aab-11e9-8e47-44e868b7831e.png" style="border:none;width:80%">
</div>

* search process(OR):
  - If docids are equal, add docid to results and increment both pointers
  - If docids are not equal, add lowest docid and increment its pointer
  - Repeat until end of both lists

<div class="imgcap">
<img src="https://user-images.githubusercontent.com/22668421/59198145-085e5800-8b61-11e9-83ee-4e8125e162b9.png" style="border:none;width:80%">
</div>

* Retrieval Model 1: Unranked Boolean
  - Retrieves the set of documents that match the boolean query (an “exact-match” retrieval model)
  - Returns results in no particular order

* Retrieval Model 2: Ranked Boolean
  - consider tf = term frequency (# of times the term appears in the document)
  - At each step, keep a list of documents that match the query and their scores (a.k.a. a “priority queue”)
  - Score computation:
    * A AND B: adjust the document score based on the minimum frequency/score associated with expression A and expression B
    * A OR B: adjust the document score based on the sum of frequencies/scores associated with expression A and expression B


<div class="imgcap">
<img src="https://user-images.githubusercontent.com/22668421/59198314-6db24900-8b61-11e9-9d07-1586d389e900.png" style="border:none;width:80%">
</div>

<div class="imgcap">
<img src="https://user-images.githubusercontent.com/22668421/59198346-8589cd00-8b61-11e9-9c9e-cf4725c0626a.png" style="border:none;width:80%">
</div>

<div class="imgcap">
<img src="https://user-images.githubusercontent.com/22668421/59322996-cc350f80-8ca6-11e9-88cf-e6bdab73281f.png" style="border:none;width:80%">
</div>



  



