# RF-Classification
Abstract

A comparison of a Convolutional Neural Network and a Recurrent Neural Network was done to characterize Radio Frequency signal classifications for varying Signal-to-Noise-Ratios. The results show that a slight improvement can be achieved using an RNN model, but the reduction of hyperparameters was about 90% when compared to the CNN model. 

1. Introduction

The inspiration for this project is to implement Deep Learning algorithms into non-typical domains. We’ve seen Convolutional Neural Networks and Recurrent Neural Networks have been extensively used in image classification and natural language processing. But how about signal processing domain? There’s been worked done for Radio Frequency (RF) modulation classification [1]. Their focus was to create a matched filter Deep learning model capable of extracting features and testing it against signals with varying noise levels. This translate to the term Signal-To-Noise-Ratio (SNR), which is ratio of the desired signal vs the embedded noise. 

1.1. Radar Systems

 Radar transmits RF pulsed signal with a carrier frequency and at a short interval. The signal then reflects off the desired targets (which is not always true, since it can also reflect on any object in the environment) and is then received by the Radar. The received signal is embedded in noise. To extract the desired signal, the received signal goes through a matched filter, which maximizes SNR and mitigates the noise level. A well-known matched filter in radar systems is called pulse compression [5].

1.2.  Convolutional Neural Network

CNN architectures have been extensively used in image classification and natural language processing [1]. However, there may be other applications outside of these domains. In this project, CNN architectures are used to extract features from I/Q datasets. As a result, this model would then be tested against varying SNR levels, meaning a variation of noise levels across the data. The goal of the model is to identify, with a high accuracy, the top-1 modulation label. 
The CNN architecture used for this project was inspired by this paper [1]. However, there were changes to the architecture structure and a decrease in the number of filters, as well as, the number Fully Connected hidden layers to minimize the number of hyperparameters. As a result, my CNN architecture has half the hyperparameters than the original architecture and shows an improvement on difficult modulation labels, which will be discussed later. 

1.3. Long-Short Term Memory

LSTM is a kind of Recurrent Neural Network that are used to remember long-term dependencies [4]. As oppose to RNN, which are also used to remember dependencies, but up to a certain point. They have been used for a large variety problem, especially in natural language processing. In this case, this type of layer will strategically be inserted between a cascaded CNN architecture and a Fully Connected Network (FC) architecture. 
This type of architecture is referred as Convolutional, Long Short-Term Memory, Fully Connected Deep Neural Networks (CLDNN) [4]. Which has been shown to improve performance by passing CNN short term features along with long term features of a cascaded CNN into the LSTM. Then passing the LSTM output into an FC to improve output predictions [4].  
A CNN model and CLDNN model were put on to test against 10 RF signal classification labels.  The goal is to transform the CNN model into CLDNN model and compare results.

3. Discussion

The CNN model used for this project was influenced by the model used in this paper [1]. However, changes were made to the number of filters, filter sizes and hidden units for personal understanding. Since the data is relatively simple, if you plot the I/Q data it shows dots on a spatial map. The initial thought was to start with a small number of filters, since the complexity of the data was not of the same level as an image. However, the number of filters were then increased to improve validation loss. The CNN model was designed with the first layer having 64 filters, next layer having 80 filters, then passing the output of the CNN layer into a FC layer with 128 neurons and a second FC layer with 10 neurons. 

The filter sizes for each CNN layer were chosen to be have a width of 3. This was done to extract as much feature as possible without increasing the number hyperparameters. Additionally, the height of the filters was designed to be larger in the initial CNN layer and then smaller in the next layer. The original paper used the same filter size, but the larger filter was applied to the second CNN layer. The idea was to obtain simultaneous features from I and Q channels. As shown in original paper [1], there were more noticeable features in the weights from the (2x3) than from the (1x3) filter. Which led to use a (2x3) filter size at the beginning instead of a (1x3) filter size.

Multiple CNN models were tested against the data, most of the results were giving an overfit or underfit effect. Regularization, such as Dropout, was implemented into the model. However, other methods of regularization were used, such as Batch Normalization, but no improvements were found. Even using a combination of Dropout and Batch Norm was not effective in preventing overfitting or improving validation accuracy. Another issue encountered with Dropout was that it was causing the testing loss to always be lower than training loss. To mitigate this result, Dropout was removed from the FC layers. 
Most of the layers used a rectified linear (ReLu) activation function except for the output layer which used a Softmax function. Finally, a categorical cross entropy along with Adam optimizer was used to train the data. The use of cross entropy function is used for single label categorization, which is the goal of this project. Additionally, Adam was used over other optimizations since it outperforms in this type of dataset [1]. 

The CLDNN model was designed based on the architecture explained in this paper [4]. The authors show CLDNN architecture that has two CNN layers, followed by two LSTM layers and two FC layers. Using inspiration from this model. The designed of the new model used two CNN layers, like the CNN model, followed by a LSTM layer and two FC layers. If you think about it from high-level point of view, the CLDNN model is just the CNN model with an LSTM added between the CNN and FC layers. 

However, a slightly different designed was used for the CNN layers. The two layers are the same in every aspect, meaning number of filters and filter size are the same. This was done for both layers to have the same output size. The filter size of the CNN layers was increased to (1x5) since it shown to have a slightly better improvement in the validation loss. On the other hand, the number of filters were used based on the first CNN layer from the CNN model. Which means that both CNN layers had 64 filters and (1x5) filter size. Likewise, zero padding was used to maintain each output size the same as the 2x128 input size of the dataset. This was done to maintain features even at the edges. 

The next layer is what the authors considered a linear layer, or dimension reduction [4]. Concatenation was performed to combine the extracted features of both CNN into one output. This was done by only concatenating the data going across the width. Height and Channels were kept the same to have a similar dimensionality to the dataset. Furthermore, there needs to be a dimensionality reduction in order to pass it on to the LSTM layer. To do this, the output of the concatenated data was reshaped to have 2 dimensions. They found [4] that dimensionality reduction does not impact accuracy. 

The LSTM layer was kept simple and without adding internal regularization. The number of units and the number of timesteps were kept the same value for simplicity. The value for these two parameters were the same value as the number of filters (64) used in the CNN model. Keeping everything same size was beneficial when training the model and preventing overfitting. 
The output of the LSTM layer was passed to 2 FC layers, which were the discussed earlier. The total number of parameters was reduced by around 90%. Moreover, there was a slight improvement in validation accuracy, surpassing both the original model and the CNN model. While the original model was able to obtain a validation accuracy of 57.94%, CLDNN was able to obtain 58.03% validation accuracy. This improvement is rather miniscule, around 0.13% higher validation accuracy. The surprising part was how large of a reduction was done using CLDNN approach. The CLDNN model only used around 100,000 hyperparameters and was able to slightly outperform the original CNN model with 2 million hyperparameters [3] and the CNN model with 1.2 million. 

The results show how both models have low validation loss, but it seems that CNN model is prone to overfit the data if it ran for more than 50 epochs. On the other hand, the CLDNN model seems to be stable when going over 50 epochs. A test was done to see if the model would start to overfit given a high number of epochs. It turns out, that it never overfitted even after reaching 150 epochs. 
From the results shown, there’s a slight improvement in accuracy on some of the modulation labels, but a slight lower accuracy on others. An interesting finding is how CNN has a high accuracy in QAM16, as well as, a high confusion with QAM64, but CLDNN is the opposite. Later experimentation showed that using large filters extracted features that improved the accuracy for QAM16. 
Further experimentation is needed to improve the CLDNN model. Since it seems to show that maybe adding depth, instead of increasing the number of hyperparameters, can improve performance. As a result, reduce the size of the model, but not its complexity.
