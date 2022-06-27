---
title: Solving Non‑intrusive Load Monitoring Problem Utilizing the Combination of CNN and RNN
summary: An example of using the in-built project page.
tags:
  - Non‑intrusive Load Monitoring
date: '2016-04-27T00:00:00Z'

# Optional external URL for project (replaces project detail page).
external_link: ''

image:
  caption: Photo by rawpixel on Unsplash
  focal_point: Smart

links:
  - icon: twitter
    icon_pack: fab
    name: Follow
    url: https://twitter.com/georgecushen
url_code: ''
url_pdf: ''
url_slides: ''
url_video: ''

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
slides: example
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

### MODEL

I chose the artificial neural network to solve NILM Problem because it has been implemented in many areas with excellent outcomes. 

To begin with, I considered that the running mode of an appliance is relevant in the time axis, which is shown in Figure 1.

<img src="https://s2.loli.net/2022/06/27/7wtMkAENrSgHuWV.png" alt="image-20220627111732394" style="zoom:80%;" />

For example, when a refrigerator works, it may follow the process of cooling and thermally insulating. Then I drew the Time-Active Power diagram of the refrigerator as shown in Figure 2. based on the Low-Frequency Data read from UK-DALE. From Figure 2. We can see the working states show a periodicity from which RNN may extract some valuable information.

<img src="https://s2.loli.net/2022/06/27/9bJrPhtBFaAkj3x.png" alt="image-20220627125149273" style="zoom: 67%;" />

Therefore I chose to use RNN for settling NILM Problem. I prepared the Low-Frequency Active Power Data in the appropriate shape to feed into the RNN network.

Secondly, I noticed that most previous works hardly used the High-Frequency Data, which may involve abundant details contributing to identifying what appliances are running. Though UK-DALE provided me with enough data, It was challenging to come up with an appropriate method to feed this part of the data into the neural network. Finally, I was inspired by the project's name, Research on Power Load Identification based on V-I Curve, decided by my advisor, to transfer the sequence data into the form of V-I curves. Then I wrote the python code to draw V-I curves. Drawing one V-I curve would use continuous 6 seconds' High-Frequency Data, totally 96K voltage and current points respectively. The reason why I chose a six-second window to draw the V-I curve was to match the Low-Frequency Data's sample rate. In Figure 3. We can see the various V-I curves at different times with different combinations of appliances in operation. CNN has been successfully being used in extracting the details of images thus I chose to use CNN to deal with these High-Frequency Data.![image-20220627133743445](https://s2.loli.net/2022/06/27/k4zvKmOjEGVpZ2Y.png)

However, the RNN was advantageous in dealing with time series while CNN was superior in processing images. Consequently, I combined these two types of neural networks and built my CNN-RNN mixed model.

My model had two branches, one is simple RNN and another is CNN+RNN. These two branches were concatenated by the *Concatenate Layer* in Keras.

![image-20220627140856282](https://s2.loli.net/2022/06/27/FQhNZXdExuovIsR.png)

### INPUT DATA

From Figure 4. We can see the model has two inputs——input_1 and input_2. Input_1 was the High-Frequency Data (having been transformed to V-I curve form) while Input_2 was the Low-Frequency Data(the sum of the whole-house's active power). These two types of input data were both prepared into appropriate shape $Batch \times Lookback \times Input\ Dim$ to be feed into the model as Figure 5. shows.

<img src="https://s2.loli.net/2022/06/27/VM3t5zB1w8ZSQEr.png" alt="image-20220627142349105" style="zoom:80%;" />

