---
title: Replication of the SRLUT
summary: My research experiences🔭.
tags:
  - Single Image Super Resolution
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

I participated in the China College IC Competition on Mar. 2022. The task of my team was to design an Upsampling IP which could resize a low-resolution image (960 × 540) to high resolution image (3840 × 2160) with good performance in all of three metrics: PSNR, SISM and Lpips. I investigated several algorithms including [AMD FSR](https://www.amd.com/en/technologies/fidelityfx-super-resolution), [SelfExSR](https://www.cv-foundation.org/openaccess/content_cvpr_2015/papers/Huang_Single_Image_Super-Resolution_2015_CVPR_paper.pdf) and [SRLUT](https://openaccess.thecvf.com/content/CVPR2021/papers/Jo_Practical_Single-Image_Super-Resolution_Using_Look-Up_Table_CVPR_2021_paper.pdf). I tried to replicate the AMD FSR but failed because I lacked reference to its rationale. Though my algorithm could export 4K images, the quality of them was inferior. Then I found the SelfExSR, which was published on CVPR, and its source code. However, the source code was written for Windows without a Linux version, which means it cannot directly run on our advisor's server. After a series of attempts, I finally ran it on the Linux server as well as got high-quality output images. Nevertheless, SelfExSR was a traditional single image super-resolution algorithm thus it used various methods to extract information from the input image, which made the algorithm too complex to be deployed in FPGA. Finally, I came across SRLUT when I searched for algorithms on Google Scholar. I almost replicated the whole work of SRLUT.

## INTRODUCTION OF SRLUT

The author first built up a super-resolution CNN. This CNN hardly had any difference from other simple super-resolution CNN except for its small receptive field. The kernal size of the input convolutional layer was as small as 2×2. 

Then, the author trained the net on public datasets. Until now, there was nothing special.

After the net had been trained successfully, the author prepared all combinations of the input data and fed them into the net then got the corresponding output of 16 pixels. Consequently, the author transferred the net into a LUT called SRLUT by repeating the process mentioned above. Now let us estimate how large the SRLUT is. For practical usage, the author uniformly divided the original input space of {{< math >}}$2^8${{< /math >}} bins into {{< math >}}$$2^4+1$${{< /math >}} bins. The number of combinations entered is {{< math >}}$$(2^4+1)^4$${{< /math >}}, for a total of 83521. For each combination of inputs, the output is 16 values from 0 to 255. That is, 16 one-byte. Therefore, the size of the entire lookup table is {{< math >}}$$(2^4+1)^4 \times 16=1.274MB$${{< /math >}}. For RGB triple channel, the whole SRLUT's size is {{< math >}}$$3 \times 1.274MB=3.830MB$${{< /math >}}. 

With the small size and simple implementation, The SRLUT is appropriate for hardware platforms, such as FPGA. 

## MY WORK

I built SRLUT with Keras as well as wrote periphery scripts enabling the training process to operate automatically. Besides, I rewrote the code of someone else's web crawler to automatically crawl 4K and PNG format images on the Internet as the training dataset.

From this replication work, I have gained a deeper understanding of neural networks.

First, I have a deeper understanding of the dimensionality of the input and output of neural networks. Initially, I learned that the batch operation was averaging, and I was not sure what that meant. It was not until I wrote my own MSE loss function and found it has a large deviation from the result calculated by Tensorflow's official MSE loss, I went to line by line to observe the tensor in different positions of the net. Finally, I settled this problem by dividing the output of loss by batch size. Therefore, my conclusion is: it seems that the different channels of the *batch dimension* are independent of each other until they are fed into the loss function for calculation during the Keras operation. The data between the different batches is summed and averaged at the loss function.

Second, I figure out how to accelerate the speed of training and prediction by setting the size of the batch. At first, I set the batch to four and only input four 2x2 pixel areas at a time. In this circumstance, the training was very slow as one color channel of an image took even more than one hour to train. Then I turned up the batch size to 180*4 as memory would allow and found that the training time was dramatically reduced to a few minutes. I think one possible explanation for this phenomenon is: The process of calling Keras functions to perform operations takes a lot of time, even more time than computing the data been fed into the net. Therefore increasing the size of the batch appropriately could be beneficial while too large batch may lead to problems.

Third, I learn more about CNN for the single image super-resolution field. I wrote my own script to implement the function of [Pixel Shuffle Layer](https://pytorch.org/docs/stable/generated/torch.nn.PixelShuffle.html) based on the simple idea of resizing without any previous knowledge thus I comprehended how CNN could export upsampling images. I also learned the fact that the [BatchNormalization Layer](https://keras.io/api/layers/normalization_layers/batch_normalization/), which has yielded many results in other areas, is not applicable in a super-resolution CNN. BatchNormalization Layer effectively 'standardizes' the distribution of the output of the previous layer to be more efficiently processed by the subsequent layer. This 'standardizes' contributes to classification tasks because it can smooth out noise or improve generalization over other data. However, in super-resolution fields, there is no need for standardizing the data to a certain interval for the input images' distribution of the pixel value can be concentrated in any position from 0 to 255. When I trained my net, I attempted to insert BatchNormalization Layers between 2D-Convolutional Layers only to observe the performance of the net went bad.

Finally, the output image of my network was slightly strange, which I thought may be caused by the optimizer parameter settings was inappropriate or the batch size was too large. I also did not write the script that turns the network into a LUT. Next, I plan to complete these sections and collaborate with my team members to complete the FPGA hardware implementation.

## LINKS

ALL codes, model and data mentioned above are available.

- Codes: https://github.com/RainyDayqwq/SingleImageSuperResolution

- PPT: {{< staticref "uploads/SRLUT.pptx" "newtab" >}}download{{< /staticref >}}.
