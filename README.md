# Cross-Lingual-Document-Translator
<p align="center"> <b>Shristi Kedia (BITS Pilani) </b> </p>

This project is part of an Assignement of the course Information Retrieval. The aim of this project is to develop a translator which could translate sentences from English to Dutch and vice versa. The translator has been developed using IBM  Model 1.

# Data
The dataset for this project consists of Dutch sentences and their corresponding English translations. Both the files can be downloaded from https://drive.google.com/drive/folders/1ZWZrds7AaYatiyyIB8jBAEfZ7g03Atlc?usp=sharing.

# Approach Used
Machine Translation uses software techniques for translating text or speech from one language to another. Statistical Machine Translation is a part of Machine Translation paradigm which uses statistical models where the parameters are set based on a training corpora. In this project IBM model 1 which is a part of Statistical Machine Translation has been used.

### **IBM Model 1**
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
The execution begins with the ***train method*** which internally calls ***train_iter_helper***. In ***train_iter_helper*** the count dictionary is made which is used to update the translation model. The translation model gets updated using Expectation Maximization (EM) Method. The model generates the Translation table which ultimately is improvised upon further iterations.After the Translation Table is made, ***translate_sentence*** method is called which ultimately gives us the translated document. It tokenizes the source sentence and gets the corresponding translated word (assuming they map continuously with no distortion) and returns final sentence. The different modules used are described in detail below.
 
### **Modules**
1.	***get_corpus:***  This method returns the preprocessed dataset as described above from the original dataset.
2.	***train:*** It takes a corpus and number of iterations and calls train_iteration that many times.
3. ***set_initial_probabilities:*** The initial probabilities in translation table are set assuming uniform distribution with probability as 1/(length of English vocabulary)
4. ***init_vocab:*** Generates English and Dutch vocabulary
5. ***max_lex_transl_probab:*** It updates the translation the translation table of each iteration by calculation total count of each target and source word(with normalization).
6. ***get_total_count:*** This method returns sum of all possible alignment of target word t with all source word s.
7. ***translate_sentence:*** It generates translation of each Dutch sentence. It calls ***get_translation*** to get translation of each word in the translation table and returns it without reordering (property of IBM Model 2) as it is.
8. ***get_translation:*** It gets the maximum probability i.e. p(e|f) for each English word and takes it as the translated word.
 
# Results
The result obtained is summarized in the table below. The results are obtained by testing on a sample of **Europal English-Dutuch Dataset**.

![Performance](https://user-images.githubusercontent.com/27685757/68962399-7f77d680-07fa-11ea-8e35-bfda24614f8a.png)

# Innovation
IBM Model 1 is weak in terms of conducting reordering or adding and dropping words. In most cases, words that follow each other in one language would have a different order after translation, but IBM Model 1 treats all kinds of reordering as equally possible. Due to the limitations of IBM Model 1, I have tried 2 innovative techniques which when applied to the existing code improves the accuracy of the model by 25%. The idea behind the innovation techniques is explained in detail below.

**Innovation 1 A**

A major issue of IBM model 1 is due to its hypothesis of only 1 NULL word per sentence. The issue which occurs due to this is that Model 1 seems to align too few target words to the NULL source word. There is also another non-structural problem associated with Model 1 according to which rare words get aligned to too many words in the target language. To tackle this situation a 2 step approach is used. In the first step a function is used which smoothes the translation probability estimates by adding virtual counts according to a uniform probability distribution over all target words. This helps in tackling the issue of rare words. For carrying out this task a parameter n is defined and its value is set to 0.005 and then we obtain the smoothed
probability by  

![image](https://user-images.githubusercontent.com/27685757/68961464-771e9c00-07f8-11ea-8481-c6308d535277.png)

where |V| is the size of the target vocabulary. For handling the NULL words a fixed number of extra null words (q0 = 2) are added to address the lack of sufficient alignments of target words to the NULL source word.

**Innovation 1 B**

The second innovation carried out is Ensemble Learning which generally improves the result by combining several models and thereby improves the result as compared to the result of a single model. From the same corpus, m different samples are obtained and each of the sample os trained by IBM Model 1. Hence, m different models are obtained and their corresponding
translation probability matrices are produced.
To obtain the resultant translation probabilities, **2 approaches** are considered:

**First Approach**

The word translations from the translation probability matrix for each model are taken, by taking the word corresponding to the maximum translation probability. Thus, m or less word translations for each source language word in the original corpus are obtained. Finally, the majority voting of all the potential translation options for each source word is taken which generates the resultant word translations.

**Limitations of First Approach**

Since this approach considers the word translations of each model for obtaining the resultant word translations and leaves out the probabilities of translations altogether, correct result may not always be obtained. It was observed that for cases where highly confident word translations are overshadowed due to greater number of considerably lower confident word translations. For example, if 15 models are trained on a corpus, and 6 of them are giving ‘huis’ as the translation of ‘house’ with a probability of around 0.8 each while 8 of them are giving ‘weg’ as the translation of ‘house’ with a probability of around 0.3 each and 1 of them gives some random translation. In this particular case, it can be seen that the translation of ‘house’ should be ‘huis’ but the above approach would result in ‘weg’ as the translation.

**Second Approach**

In addition to the first approach, each and every word translation probability is incorporated to get the resultant word translation, in contrast to the first approach where the word translations from the models were only considered to get the resultant translations.
After obtaining the translation probability matrices from the models, the average of probabilities of translation from the source word into a particular target word is taken. Finally, the word translations are obtained from the average probabilities just as in the case of IBM Model 1. The limitation with the first approach is resolved here and can be illustrated with the same example as before. This approach would give the translation probability of ‘house’ to ‘huis’ to be around 0.8 in contrast to 0.3 for translation from ‘house’ to ‘weg’ and the higher probability would result in the correct translation.
