# Cross-Lingual-Document-Translator
<p align ="center"> <b>Shristi Kumari (BITS Pilani) </p>
This project is part of an Assignement of the course Information Retrieval. The aim of this project is to develop a translator which could translate sentences from English to Dutch and vice versa. The translator has been developed using IBM Model 1.

# Data
The dataset for this project consists of Dutch sentences and their corresponding English translations. Both the files can be downloaded from https://drive.google.com/open?id=1WfFiH2x1du4Ctqdw0lVa-t6S_EX-ZHjE. 

# Approach Used
Machine Translation uses software techniques for translating text or speech from one language to another. Statistical Machine Translation is a part of Machine Translation paradigm which uses statistical models where the parameters are set based on a training corpora. In this project IBM model 1 which is a part of Statistical Machine Translation has been used.

### IBM Model 1
IBM model 1 uses the Expectation Maximization algorithm. It starts with assuming random transition probabilities and updates the probability of transition in each iteration. The updated transition probability forms the base probability for the next iteration. The above 2 steps are repeated till the transition probability values converge. The diagram below illustrates the steps of the algorithm.

![image](https://user-images.githubusercontent.com/27685757/68961093-85b88380-07f7-11ea-83a8-ed1c764b1a41.png)

# Assumptions
1. A constant MIN_PROB is defined which limits the probability of english word given foreign word otherwise the english vocabulary is too large to maintain accuracy while translation and it will not give good results. 
2. A sample size of 0.1 is taken to train the model as dataset was very large. Random Sampling is used.
3. In IBM Model1 only 1 NULL word is assumed to be added explicitly in source.

# PreProcessing
English and Dutch Datasets are taken and are pre-processed as follows:
1. Digits and characters other than letters are replaced with empty string 
2. End part of the sentences are trimmed.
3. Empty sentences formed due to preprocessing are removed.
4. A list of pair of corresponding English and Dutch sentences is made.
5. Random Sampling with sample size of 0.1 times is taken and output obtained after this step is returned as the final corpus.
   
 # Code
train method is called initially which internally calls train_iter_helper. In train_iter_helper the count dictionary is made which is used to update the translation model. The translation model gets updated using E-M Method. Model generates Translation table which ultimately is improvised with given number of iterations.

Then translation method translate_sentence is called for given trained model which ultimately gives us translated document. It tokenizes the source sentence and gets the corresponding translated word (assuming they map continuously with no distortion) and returns final sentence.Model generates Translation table which ultimately is improvised with given number of iterations.

Then translation method translate_sentence is called for given trained model which ultimately gives us translated document. It tokenizes the source sentence and gets the corresponding translated word (assuming they map continuously with no distortion) and returns final sentence.and in this section detailed explanation of the different modules present is provided.
 
### Modules
1.	 get_corpus:  English and Dutch datasets are taken and firstly pre-processed as following:
•	Replacing digits and characters other than letters with empty string 
•	Trimming the end part of the sentences.
•	If sentence is empty after preprocessing it is removed as well.
A list of pairs is made i.e. A list of pair of corresponding English and Dutch sentences (CORPUS)
Random Sampling is used to train our dataset. Sample size of 0.1 times is taken as random and returned as final corpus.
2.	train: takes a corpus and number of iterations and calls train_iteration that many times.
 
 ### Algorithm Flow

