---
# Display name
title: Xia Yu(夏雨)
#subtitle: 
# Is this the primary user of the site?
superuser: true

# Role/position/tagline
role: Junior in Microelectronic Engineering
# Organizations/Affiliations to show in About widget
organizations:
  - name: Huazhong University of Science and Technology(Wuhan, China)
    url: http://english.hust.edu.cn/
## Contact

# Short bio (displayed in user profile at end of posts)
bio: Although my major is related to semiconductors, it does not prevent me from having interests in many areas including my original profession, AI and so on.

# Interests to show in About widget
Interests:
  - Convolutional Neural Networks, 
  - Recurrent Neural Networks
  - single image super resolution
  - FPGA Programming

# Education to show in About widget
education:
  courses:
    - course:  Graduate Academic Preparation Program
      institution: University of California, Santa Barbara(Santa Barbara, CA)
      year: Sep. 2022~Jun. 2023(Expected)
    - course:  B.E. in Microelectronic Engineering
      institution: Huazhong University of Science and Technology(Wuhan, China)
      year: Sep. 2019~present
# Social/Academic Networking
# For available icons, see: https://wowchemy.com/docs/getting-started/page-builder/#icons
#   For an email link, use "fas" icon pack, "envelope" icon, and a link in the
#   form "mailto:your-email@example.com" or "/#contact" for contact widget.

social:
  - icon: square-envelope
    icon_pack: fas
    link: "mailto:RainHsiaqwq@gmail.com"
  - icon: github
    icon_pack: fab
    link: https://github.com/RainyDayqwq
  - icon: cv
    icon_pack: ai
    link: uploads/cv.pdf
# Link to a PDF of your resume/CV.
# To use: copy your resume to `static/uploads/resume.pdf`, enable `ai` icons in `params.toml`,
# and uncomment the lines below.
# - icon: cv
#   icon_pack: ai
#   link: uploads/cv.pdf

# Enter email to display Gravatar (if Gravatar enabled in Config)
email: ''

# Highlight the author in author lists? (true/false)
highlight_name: true
---
{{< icon name="download" pack="fas" >}} Download my {{< staticref "uploads/cv.pdf" "newtab" >}}CV{{< /staticref >}}, {{< staticref "uploads/academicrecord.pdf" "newtab" >}}Academic Record{{< /staticref >}}, {{< staticref "uploads/成绩单.pdf" "newtab" >}}成绩单{{< /staticref >}}. The Academic Record and its Chinese edition can be verified on [this platform.](http://verify.hust.edu.cn/#/pdf-verify)

I am currently a junior at [Huazhong University of Science and Technology](http://english.hust.edu.cn/)(you can see Chinese Universities Ranking [here](https://www.shanghairanking.com/rankings/bcur/2022)), pursuing a major in Microelectronic Engineering. This September, I am going to the [University of California, Santa Barbara](https://www.ucsb.edu/) to complete my final year of study because I have been granted admission to its International Program. In my first three years of undergraduate study, I chose to be diligent and earned a 3.96/4.0 cumulative GPA, which ensured that I had a relatively solid foundation in mathematics, physics and my subject area.

Here illustrates some of my key courses together with their grades: Calculus (I)(A)(97), Linear Algebra(92), Physics (I)(93), Signal and Linear System(97), Quantum Mechanics(II)(86), Semiconductor Physics(I)(96), Solid State Physics(94), Microelectronic Materials(96).

My previous research and project experience lie in various areas including Convolutional Neural Networks, Recurrent Neural Networks, single image super resolution, FPGA Programming (all learned by myself). I learned about these fields through the research projects and competitions I attended, which are elaborated in the **Experience section** of my homepage. 

Through these research experiences, I encountered obstacles as well as gradually organized my thoughts at the same time. For example, during my research to [NILM Problem](https://rainhsia.netlify.app/project/nilm/), I attempted to connect the output of CNN to the input of the RNN. Generally, the ordinary [MaxPooling2D Layer](https://keras.io/zh/layers/pooling/) in CNN could only deal with the tensor in the shape of *(Batch_size, Rows, Cols, Channels)* from the previous layers, while the input of [GRU](https://keras.io/zh/layers/recurrent/#gru) in RNN usually looked like *(Batch_size, Lookback, Input data)*. Plus, the MaxPooling2D Layer's lower three dimensions, representing the information extracted from single image, could be easily turned into one dimension with the Flatten Layer as the Input data dimension of GRU. However, it was infeasible to simply use GRU Units in conjunction with MaxPooling2D Layers because the tensor flowing in the latter missed one dimension in the time axis. I had been stuck with this problem for a long time. Whether I went online or turned to my teachers and friends, they all didn't know how to solve this problem. Just when I was about to give up, I came across the [Timedistributed Layer](https://keras.io/zh/layers/wrappers/#timedistributed) by chance, which seemed to add an additional time dimension to the topmost level of the tensor. I applied it to package some layers in CNN, and then the net could operate correctly. While the problem was addressed, I obtained a better comprehension about the shape of the tensor as well as the different meaning of tensor's each dimension in either CNN or RNN. 

I am fortunate enough to work out a significant problem I encountered, but I know the truth is, there could be a lot of difficult, even intractable or unsolvable problems in research. Once you get caught up in these issues, you may easily lose your way, even confidence. Now that there could be many frustrating things if you dig into one domain, how do you still drive yourself forward?

In my opinion, trying to foray into unfamiliar areas, gradually figuring out the rules behind them, and finally coming up with your own small ideas and insights to solve problems, this series of processes in itself could be such a fulfilling thing to encourage you. When you go deeper into one field, the field will occupy a part of your mind where you can stroll at any time or place, which would give you a sense of satisfaction. This satisfaction could bring you equanimity together with security, which is quite dissimilar to the temporary satisfaction gained from playing computer games or watching entertaining movies.

Meanwhile, I am interested in and want to give a try to more areas such as GAN for the computer vision field, DQN for game AI, Brain-inspired Computing and Devices and so on. 

I’m applying for a graduate program or a research internship.✨

