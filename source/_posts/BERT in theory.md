---
title: BERT in theory
tags: [NLP]
---
In this article, I will share what I learn form BERT—A google new NLP transform learning model.

Anyway I start a paper every week challenge for 2019, I will read a NLP paper every weeek, and try to write down what I had learned from this paper, hope that I can keep it up^-^

Here is the paper [BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding](<https://arxiv.org/abs/1810.04805>)



## Summary

Learning from ELMO and GPT pretrianed model experience, BERT find another way to pretrain  model with bidirectional transformer architecture by learning marked word predicted and next sentence predict. This model learned from BERT can use for many NLP tasks by slightly modifying the input or fine tune pretrained model with target text corpus, then we will get some state of art result.



## Spark Ideas

Below I will share some valuable ideas I learn from the paper.



### How to use pretrained model 

Nowadays, there are two ways for using prtraiend model:

- Fine-tune

Just like what Restnet and Bert are used, we implyment the pretrianed model with model stucture and weights, inheriting mostly the config from pretrained model.In order to use the model for downstream task, we can add a classifier (like softmax layer) after the pretrianed model, then use downstream task data to learn new layer's weights and slightly change the weights of pretrained model with BP.

**Pros:** when we don't have enough labeled data for training, with tranfer learning by pretrained modle ,we can somehow increase our training model accuracy with little training data. And the weights that we will learn will not be too many with fine tuen mothod. 

**Cons:** downstream task's input embedding must be the same as the pretrained task, also, the task between downstream and pretraned taks can not be too different, otherise, what model have learned form pretrained task cannot apply to new task.

- Feature-base

Just like what W2v and ELMO are used, we treat the pretrianed result as a feature embedding for our downstream task, then we will use our full defined model(like: LSTM) to learn some abstract mapping knowledge from the embedding .

**Pros:** when we get enough learning data, its ok to learn from pretrained embedding, we have little restrict from pretraiend model ,since it just act like an input for the  whole model

**Cons:** we need to learn the model from scratch, and our model can  be only used for only one task



### Why BERT can learn more than ELMO and GPT

As ELMO and GPT try to learn a language model, what a language model does is to  tell you the next word giving a word or sentence before(or after).

The traditional way for training a language model, it to predict the next word from left to right or from right to left, or predict from left to right and predict from right to left separately,then merge the prediction together. 

The main target for language model is to predict next word, somehow , language model cannot fully used context info from before the word and after the word. Since language model can only predict next word from one direction. 

What BERT improve is that, it will not predict a word from before or after context, it use both sides of context, instead predicting next word, BERT predict the mark word in the sentences, so that, BERT can learn the relation from the whole sentence.

What's more ,BERT use transformer architecture for abstarcting context info, which has better performance than LSTM language model.



### Introduction for transformer of BERT

Transform has better performance for abstracting features, If you want to learn more about transformer, I recommand you to read [Attention Is All You Need](<https://arxiv.org/abs/1706.03762>) 

BERT uses transformer architecure for extracting features, in order to describe the transformer architecture, we will first define some terms, L: transformer layers, H: hidden layers's neuron number, A： self attenton heads.

BERT had trained 2 kind model for english, a base one , with L=12,H=768, A=12 and a large one with L=24, H=1024, A=16

Of course, the large one has better performance than the base one, since the large one had a deeper and larger transformer which can extacting more features from input

One the one hand, BERT use transformer as encoder for geting bidirection context info, one the other hand, BERT use transformer as decoder for generating text from one direction.



### What is the input for BERT model

BERT input is a concat result of token id embedding, segmentation embedding and mask word embedding.
The max length of all the embedding can not be bigger than 512, since the pretrained BERT model only take sentnece with 512 length(containing all the word tokens and the mask tokens), remember to do some sentence cropping if it is lonnger than 512.

- Token id embedding

In BERT, it use a method named "word piece tokenzier" for tokenizing text,  word piece tokenizer will tokenize text no only based on word level, but also sub-word level that seperate a  work into some piece of sub words, E.g. text = 'Jim Henson was a puppeteer', after word piece tokenized, reuslt = 'Jim Hen ##son was a puppet ##eer', with this method, BERT can get more info of words and will be good for some OOV problem. if you want to learn more about this tokenize meothod, I recommend you to read [Google's Neural Machine Translation System: Bridging the Gap between Human and Machine Translation](<https://arxiv.org/abs/1609.08144>)

After tokenizing the text, BERT will do some preprocess stuff the the  tokenized result, first , it will add a '[CLS]' mask at the front of the sentence, the [CLS] will act as a main features for classificaion task, for no-classification task, [CLS] token will work nothing .

Then , BERT will add a '[SEP]' at the end of the text, representing that this is the end of a sentence, for sentence based task like QA task , the [SEP] acts as a main feature, remember, in sentence based task there are two texts that merge into a sentence, so there are two [SEP] mark in the input sentences

After process from above,  we can use predefined vocabulary to map token into number

- Segmentation embedding

Since BERT take sentences comparing as one of the main training task, so that, BERT need to konw which sentence is the first one, which is the second one.

After word piece and adding [CLS] and [SEP] mask words, we can use 0 and 1 to represent the token belong to which sentence.

E.g. token sentence: '[CLS] I have a dream [SEP] The cat is white [SEP]', segmentation embedding: [0,0,0,0,0,1,1,1,1,1]

- Mask word embedding

BERT takes mask word predict as it another main training task, so it need to konw which word is masked.

We can ues 0 represents normal word, 1 represents mask word. For fine tuning BERT language model, this process will result into a embedding with 0 and 1. But, for fine tuning BERT classifier, we don't need to set some word as mark word, so that , in the senario, the mark word embedding is [0,0,0,…0] with a array with 0.



### About BERT masked LM  task

As descrbe before, BERT take two tasks for its trainning , one is marked LM training, another is next sentence predicting. Now, I will introduce how BERT train masked LM.

Different from tranditonal language model training task, BERT learn to predict some words in a sentence giving all the before and after context, this task looks like cloze procedure.

In order to set up this masked word predicted task, BERT will take 15% token for a sentence for masking.

In this 15% tokens, 80% will be replaced with '[MASK]', 10% will be replaced with a random word from vocabulary, 10% will not be replaced.

After making the training corpus, BERT will learn to predict the mask word, first, the whole sentence will pull into the transformer for encoding, then ,having the encoded result, will add a softmax layer at the end, predicting the mask word is what, the output array is the length of the vocabulary. It look like the process of training word2vec model with skip-gram method.

After the training, the model will learn to predict the word with before and after context.

Here, I will explain the differnt between traditional language model training method  and this masked word language model training method.

For the traditional one, it can only get context from one direction, and try to remake the whole sentence as its final target. But, for masked LM, it can use two direction context, since it only predict the word in the whole sentence, it will not try to remake the whole sentence as it task, as when we doing cloze procedure exercise.



### About BERT next sentence predicted task

BERT's training is a multi tasks training, not only it will learn relaiton for words, but also it will learn relaiton for sentences.

So BERT learn to predict if two sentences are related.

In order to set up this task, BERT will choose many sentence pairs from the input, the sentnece pair contains 2 sentences, 50% of the sentence pairs are related sentences which appears in the document one by one, 50% of the sentence pairs are not related, which the sentence are combined randomly.

After making the corpus, BERT will teat this as a binary classification task, predict if the sentence pairs are related.

The next sentence training task is very import for BERT, with this training, BERT can learn to konw relationship between  2 sentences which is helpful for some sentences based task like QA.























  