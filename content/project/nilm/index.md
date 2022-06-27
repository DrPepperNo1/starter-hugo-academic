---
title: Solving Non‑intrusive Load Monitoring Problem Utilizing the Combination of CNN and RNN.
summary: One of my research experiences🔬.
tags:
  - Non‑intrusive Load Monitoring
date: '2022-06-27T00:00:00Z'

# Optional external URL for project (replaces project detail page).
external_link: ''

image:
  caption: 
  focal_point: Smart

#links:
#  - icon: twitter
#    icon_pack: fab
#    name: Follow
#    url: https://twitter.com/georgecushen
url_code: ''
url_pdf: ''
url_slides: ''
url_video: ''

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
#slides: example
---

## BACKGROUND

### INTRODUCTION

The recent increase in installations of smart meters in households makes it possible to provide more specific information about appliance usage in a family. If we can decide what kinds of appliances are running now just through the data gleaned by the smart meters? This is what Non‑intrusive Load Monitoring (NILM) focuses on.

NILM Problem has practical significance in many aspects. For electricity suppliers, they could ameliorate their supplying strategies by utilizing the data generated from the NILM algorithm. For domestic users, they may save on electricity costs by getting useful information about appliance usage and their consumption behavior.

I proposed an approach based on the combination of CNN and RNN to solve NILM Problem.

### PUBLIC DATASETS

There are many available public datasets, such as UK-DALE, REDD, LAWE, AMPDS and so on. 

I choose [UK-DALE](https://www.nature.com/articles/sdata20157/), which is an open-access dataset from the UK recording Domestic Appliance-Level Electricity.

Data involved:

- High-Frequency Data: The whole-house's voltage and current data at a 16 kHz sample rate
- Low-Frequency Data: Individual appliances' active power data at a 1/6 Hz sample rate

### OPEN-SOURCE TOOLKIT

[NILMTK](https://nilmtk.github.io/) is a toolkit designed to help researchers evaluate the accuracy of NILM algorithms and import data from public datasets. 

In my work, I use NILMTK to read the data from dataset files in .h5 format.

## MY WORK

### Network Architecture

I chose the artificial neural network to solve NILM Problem because it has been implemented in many areas with excellent outcomes. 

To begin with, I considered that the running mode of an appliance is relevant in the time axis, which is shown in Figure 1.

<img src="https://s2.loli.net/2022/06/27/7wtMkAENrSgHuWV.png" alt="image-20220627111732394" style="zoom:80%;" />

For example, when a refrigerator works, it may follow the process of cooling and thermally insulating. Then I drew the Time-Active Power diagram of the refrigerator as shown in Figure 2. based on the Low-Frequency Data read from UK-DALE. From Figure 2. We can see the working states show a periodicity from which RNN may extract some valuable information.

<img src="https://s2.loli.net/2022/06/27/9bJrPhtBFaAkj3x.png" alt="image-20220627125149273" style="zoom: 50%;" />

Therefore I chose to use RNN for settling NILM Problem. I prepared the Low-Frequency Active Power Data in the appropriate shape to feed into the RNN network.

Secondly, I noticed that most previous works hardly used the High-Frequency Data, which may involve abundant details contributing to identifying what appliances are running. Though UK-DALE provided me with enough data, It was challenging to come up with an appropriate method to feed this part of the data into the neural network. Finally, I was inspired by the project's name, Research on Power Load Identification based on image encoding, decided by my advisor, to transfer the sequence data into the form of V-I curves. Then I wrote the python code to draw V-I curves. Drawing one V-I curve would use continuous 6 seconds' High-Frequency Data, totally 96K voltage and current points respectively. The reason why I chose a six-second window to draw the V-I curve was to match the Low-Frequency Data's sample rate. In Figure 3. We can see the various V-I curves at different times with different combinations of appliances in operation. CNN has been successfully being used in extracting the details of images thus I chose to use CNN to deal with these High-Frequency Data.![image-20220627133743445](https://s2.loli.net/2022/06/27/k4zvKmOjEGVpZ2Y.png)

However, the RNN was advantageous in dealing with time series while CNN was superior in processing images. Consequently, I combined these two types of neural networks and built my CNN-RNN mixed model.

My model had two branches, one is simple RNN and another is CNN+RNN. These two branches were concatenated by the *Concatenate Layer* in Keras.

![model](https://s2.loli.net/2022/06/27/gQ8fXphRSdENOCK.png)

<img src="https://s2.loli.net/2022/06/27/hvOWwCJaD3erSjl.png" alt="image-20220627144213825" style="zoom: 80%;" />

### INPUT DATA

From Figure 4. We can see the model has two inputs——input_1 and input_2. Input_1 was the High-Frequency Data (having been transformed to V-I curve form) while Input_2 was the Low-Frequency Data(the sum of the whole-house's active power). These two types of input data were both prepared into the appropriate shape *Batch × Lookback × Input Dim* to be fed into the model as shown in Figure 5. 

<img src="https://s2.loli.net/2022/06/27/VM3t5zB1w8ZSQEr.png" alt="image-20220627142349105" style="zoom:80%;" />

- Batch Size: One batch of data per input into the model

- Lookback: Time window of 120 seconds

- Input Dim: Active power and V-I curve every 6 seconds

Besides, Figure 6. shows how *Lookback* was arranged under the *Batch* dimension.

<img src="C:\Users\99259\AppData\Roaming\Typora\typora-user-images\image-20220627150209383.png" alt="image-20220627150209383" style="zoom:80%;" />

> P.S. 
>
> The resolution of the V-I curve is 32×32, which is enough for practical applications. Plus, the V-I curve has been converted to a binary image instead of the common RGB image because it is the shape of the curve, rather than its color that matters in this problem.

### CREATE LABELS

NILM Problem is supervised learning thus It is necessary to create labels. In my model, I would predict 5 appliances involving Fridge freezer, Kettle, Microwave, Television, and Washer dryer. The label was a numpy list looking like [1, 0, 0, 1, 0] every 6 seconds. With each dimension in the label standing for an appliance, the label was "1" when the corresponding appliance reached its active power threshold set by me otherwise the label would be "0". Figure 7. demonstrates the label.

<img src="https://s2.loli.net/2022/06/27/rZ74OM9zutbnjLo.png" alt="image-20220627154747602" style="zoom:80%;" />

### PLATFORM

[TensorFlow](https://www.tensorflow.org/) is an open-source deep learning library. [Keras](https://www.guru99.com/keras-tutorial.html) is an Open Source Neural Network library written in Python that runs on top of Tensorflow.

I used python 3.8 + Keras + Tensorflow2.0 to build my network and a lot of ancillary scripts such as Generator, input data reshaper, the script converting High-Frequency Data to V-I curves and so on.

All codes have been updated to my [Github repository](https://github.com/RainyDayqwq/NILM).

### TRAINING DETAILS

I exported nine weeks of data (a total of 907,200 V-I curves) and trained on part of them. I don't remember exactly what data I trained on, I just remember that the training lasted over ten hours on my Laptop with Intel i5-11400H CPU and Nvdia RTX3050ti GPU.

### OUTCOMES

I tested the performance of the model in identifying the operation of the fridge freezer with 42.67 hours of continuous data in untrained data and achieved a good outcome as shown in Figure 8.

<img src="https://s2.loli.net/2022/06/27/GBpOLYUmISsx85b.png" alt="image-20220627163214706" style="zoom:80%;" />

Then I calculated some indicators from the confusion matrix: 


{{< math >}}
$$
\left\{
	\begin{aligned}
		Acc&=0.8057 \\
        Precision&=0.7096 \\
        Recall&=0.7758 \\
        Specificity&=0.8225 \\
        F1\ score &= 0.7412
	\end{aligned}
\right.
\tag{1}
$$
{{< /math >}}

The F1 score states the equilibrium between the precision and the recall thus it can well reflect the performance of the model. 

I compared my result with others' work.

In [Sun's work](https://kns.cnki.net/kcms/detail/detail.aspx?dbcode=CMFD&dbname=CMFD201802&filename=1018121837.nh&uniplatform=NZKPT&v=8D4Qlp0uzIKpLsk8uFJ4JUIqZOO5jUZ-u2ePc7aA-oQVqKn9llj9b-eIBE8GH_vJ), he used the FHMM algorithm and got the *F1 score = 0.528* on a refrigerator. My results improved by 20 percent over his on the refrigerator.

In [Jack Kelly and William Knottenbelt‘s work](https://arxiv.org/pdf/1507.06594.pdf), they used LSTM to solve NILM Problem getting the *F1 score = 0.74* on a refrigerator. Though My result is similar to theirs, it is worth mentioning that my work has more practical meaning. That is because their input data is simply a combination of five appliances while my input data is the whole house's total data involving dozens of appliances. 

However, my model achieved poor results on other appliances. For instance, the result of microwave is illustrated in Figure 9.

<img src="https://s2.loli.net/2022/06/27/G4bZPC9De1UwV5p.png" alt="image-20220627194750713" style="zoom:80%;" />

I also calculated some indicators from the confusion matrix: 


{{< math >}}
$$
\left\{
	\begin{aligned}
		Acc&=0.9831 \\
        Precision&=0.3389 \\
        Recall&=0.3849 \\
        Specificity&=0.9906 \\
        F1\ score &= 0.3604
	\end{aligned}
\right.
\tag{2}
$$
{{< /math >}}

As we can see in (2), compared with the very high accuracy, the F1 score is terrible. This is because the dataset is extremely imbalanced. 

I wrote a python script to observe the percentage of running time for different appliances. The refrigerator runs for 40% of a day while the value of the microwave is only 0.007%.

To solve the problem of imbalanced data, I have been attempting to substitute the previous Binary Cross Entropy loss function to Weighted Cross Entropy together with selecting some appliances with longer operating time for prediction. I also wanted to use data augmentation to populate the data of appliances with shorter run times.

Related work is still in progress...

## GROUPMEMBERS' WORK

My reliable group member, Wei Tang has deployed the model at the edge based on Raspberry Pi 4B.

He wrote the GUI in PYQT as shown in Figure 10. He also built the power collection section.

<img src="https://s2.loli.net/2022/06/27/OZMkDcPS1XhKoyG.png" alt="image-20220627202229689" style="zoom:80%;" />

## LINKS

ALL codes, model and data mentioned above are available!

- V-I curve: 
- codes: 
- Demo Video: 

