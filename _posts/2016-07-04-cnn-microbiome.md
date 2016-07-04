---
published: true
layout: post
title: Convolutional Neural Networks and Microbiome&#58; An exploratory analysis
modified: Mon Jul 04 2016
tags: 
  - microbiome
  - biomedical research
  - diagnostics
  - deep learning
image: 
  feature: "abstract-3.jpg"
  credit: dargadgetz
  creditlink: "http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/"
---



This is a proof-of-concept post based on the TensorFlow library developed by Google. To the best of my knowledge, it is the first ever attempt to apply Convolutional Neural Networks on microbiome data.


<figure>
<center>
<img src="/images/cnn-colah.png" alt="Convolutional Neural Networks" width="475">
</center>
<figcaption>
A typical Convolutional Neural Network with convolutional layer, max-pooling and fully-connected layer. Image credit: http://colah.github.io/posts/2014-07-Conv-Nets-Modular/
</figcaption>
</figure>

## Copyright Declaration
The author asserts his copyright over this file and all files written by him containing links to this copyright declaration under the terms of the copyright laws in force in the country you are reading this work in.

**This work is copyright © Ali A. Faruqi 2016. All rights reserved.**


## Introduction

In a previous post, [I discussed about the microbiome, its market capitalization and its potential to be used in diagnosis of diseases](http://alifar76.github.io/diagnostic-platform/). I further demonstrated how even a simple deep-learning algorithm, as implemented in [TensorFlow](https://github.com/tensorflow/tensorflow), can be applied on a given microbiome dataset and provide not-so-bad accuracy.

Being fairly new to the field of neural networks and being completely self-taught, I decided to move ahead and apply a more sophisticated model on the same dataset. I chose convolutional neural networks. [Convolutional neural networks are a type of artificial neural network that are inspired from the biological connectivity of neurons of the animal visual cortex](https://en.wikipedia.org/wiki/Convolutional_neural_network). 

There is extensive literature on convolutional neural networks (CNN) and it is the beyond the scope of this post to do an extensive survey on CNNs. In this post, I will provide a description of applying CNNs to microbiome data as a proof-of-concept exercise. This is the first time, to the best of my knowledge, that CNNs have been applied to microbiome data. So, without further ado, let’s dive right into CNNs.


## Technical Details

### Reading data
The input file of microbiome data comes as an [OTU](http://www.drive5.com/usearch/manual/otu_definition.html) table. The individual (sample) on which microbiome data is obtained is actually a vector of OTUs. In the [Lozupone et al](http://www.ncbi.nlm.nih.gov/pubmed/24034618) dataset, we have information on 267 OTUs. Our input for CNN, therefore, is a 267x1 vector for each sample.

### Steps of CNN
Convolution layers and max pooling are the main elements of CNNs. In the [simple Python script I’ve written](https://github.com/alifar76/TFMicrobiome/blob/master/src/cnn.py), the method proceeds as shown in the following figure:

<figure>
<center>
<img src="/images/cnn-microbiome.png" alt="CNN Workflow" width="75" height="50">
</center>
<figcaption>
Microbiome input data flow in CNN from bottom to top.
</figcaption>
</figure>

To elaborate the figure a bit more:

1. The OTU table is input for the first convolutional layer followed by max-pooling.
2. The output of first max-pooling is input to second convolutional layer followed by max-pooling.
3. The output of second max-pooling is input to densely connected layer.
4. Dropout is performed on the output of densely connected layer.
5. The output of dropout is finally passed to the readout layer, which performs simple softmax regression.

### Input reshaping

In traditional image classification datasets, such as [MNIST](https://www.tensorflow.org/versions/r0.9/tutorials/mnist/download/index.html), one would like to reshape the flattened image pixel vector back to the original 2d image array. 

[tf.nn.conv2d method in TensorFlow library](https://www.tensorflow.org/versions/r0.9/api_docs/python/nn.html#conv2d), which computes a 2-D convolution given 4-D input and filter tensors can be used for this purpose. Unlike the MNIST dataset, in which the in_height and in_width of the input tensor can be set to 28x28, in the microbiome dataset, the in_height and in_width are set to 1 and 267 respectively, where 267 is the number of OTUs (features) in the dataset.

## CNN Results

Even though CNN model is more sophisticated than basic softmax regression, we get a much lower accuracy (of about 0.772) for our dataset using CNN. I have tried playing around with different variables and parameters. Following are some of the variables I have tried to manipulate.

1. Train-to-test split ratio: The highest accuracy I got was with train to test ratio of 0.55.
2. Max-pooling: Whether one does max-pooling or not, it did not seem to have an impact on accuracy with the current microbiome dataset.
3. Dropout: Like  max-pooling, doing or not doing dropout does not seem to be impacting the accuracy.
4. Gradient descent optimizers: AdadeltaOptimizer with learning_rate = 0.1, rho = 0.95 and epsilon = 1e-02 seems to be giving accuracy of 0.77. I have also tried AdamOptimizer and it seems to be giving comparable results. I haven’t tried other optimizer.
5. Stride for conv2d and maxpool: The highest accuracy is when the stride of the sliding window is 1 for both conv2d and maxpool at the start of the script.

Few variables that I have not explored so far include:

1. Changing the number of convolutional layers
2. Changing the output channel values of convolutional layers
3. Randomizing the training and test datasets
4. Changing the amount of noise used to initialize weights for the model

Summing up, even though CNN has not performed as well compared to basic softmax regression, there is potential for further fine tuning this model. Larger microbiome datasets can be used for this purpose and along with the suggestions provided above.