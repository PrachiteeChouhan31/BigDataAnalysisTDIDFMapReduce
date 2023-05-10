# BigDataAnalysisTDIDFMapReduce
This is an assignment for big data analysis in which we have to compute theTFIDF values for textcorpora dataset using Hadoop.

Overview
For this labb the first part of document processing: computing a TF-IDF measure for the terms in the document corpus (already in HDFS). 
This lab implemented a (Streaming) Hadoop pipeline to compute TFIDF. • Input – a directory in HDFS containing a document corpus
• Output – triples of the form (term, document_id, tfidf_number), where the tfidf_number is a measurement of how “important” the term is in the specified document.
It is very common to solve problems using a sequence of MapReduce steps. RFor computing TFIDF we will use these steps
• Step 1: doc/term counts
First convert a word to a term. Second, the key is a pair (document_id, term). 
In this step, the MapReduce key, use the string “+” to send to the reducer, so if mapper is processing the document with ID “shakespeare-hamlet” and is processing the term “father”, it would emit the key “shakespere-hamlet+father”. Sample output for this and the other steps is below.
different order.
# hdfs dfs -cat /output/1-term-count/* | head -n 5 austen-emma+a 3073
austen-emma+abbey 23
austen-emma+abbeymill 7
Example Output / Hint
austen-emma+abbeyoh 1
austen-emma+abbots 1

• Step 2: term count per document
This step reads the document corpus again and just calculates the number of terms per document.  Output for this step is a stream of tuples of the form (doc_id, term_count) and examples are below.
# hdfs dfs -cat /output/2-term-count-document/* | head -n 5 austen-emma 158128
austen-persuasion 83259
austen-sense 118620
bible-kjv 790029
blake-poems 6817

• Step 3: split key from Step 1
In Step 1, we needed a composite key with both doc_id and term. However, to compute Term Frequency we need to join the doc/term count in Step 1 with the document count in Step 2. So this Step 3 will be a map-only job that will simply take the tuples from Step 1 of the form (<doc_id>+<term>, count) and produce three- tuples of the form (doc_id, term, count). By map-only job I mean that the reducer just writes out the tuples it gets from the mapper without modifying them. Output is
# hdfs dfs -cat /output/3-split-doc-term/* | head -n 5 austen-emma freak 1
austen-emma free 5
austen-emma freed 2
austen-emma freedom 3
austen-emma freeze 1

• Step 4: compute Document Frequency (DF)
in this the MapReduce job will go back to the documents, but this time the mapper will construct tuples of the form (term, doc_id) and the reducer will emit tuples of the form (term, unique_doc_id_count).
# hdfs dfs -cat
a 18
aaron 2
aaronites 1
aarons 2
ab 1
  
• Step 5: compute TF-IDF
You have three streams (HDFS directories) available:
– (doc_id, term_count) from Step 2
– (doc_id, term, count) from Step 3
– (term, unique_doc_id_count) from Step
  # hdfs dfs -cat /output/5-tfidf/* | head -n 5
austen-emma abdys 6.323990691085703
