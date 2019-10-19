---
title: "Giới thiệu về Computer Vision (Phần 2)"
date: 2019-10-18T13:34:08+07:00
draft: false
authors: ["phatnt"]
categories:
  - computer vision
tags:
  - introductory
  - vision
  - classical
slug: gioi-thieu-CV-2
toc: true
---

Qua bài trước, bạn đã tìm hiểu được CV là gì. Ở bài này, chúng ta sẽ tìm hiểu thêm một số khía cạnh khác của CV nhé.

{{% toc %}}

# 4. Computer Vision không dễ

CV là một lĩnh vực thú vị, tuy nhiên còn tồn tại nhiều thách thức, từ những thách thức đơn giản tới những thách thức rất tinh tế! 

## 4.1. Mơ hồ về nội dung

<center><img src="https://scontent.fsgn5-5.fna.fbcdn.net/v/t1.15752-9/72565820_464746427717943_5353728901136777216_n.png?_nc_cat=100&_nc_oc=AQmH1BPjk3-Ct2uy9KNrNxSYrGfXGP_YiUap9V_oEi32AWgQN8BidW1zxQ3lXWRSiVY&_nc_ht=scontent.fsgn5-5.fna&oh=f483c445bd0acb549097177b1962fb1c&oe=5E1F2D4C" width=400></center>
<center><b> Ảnh 17. </b> Chai coca thật hay giả? <i> [4] </i></center>
## 4.2. Vật thể chồng chéo

<center><img src='https://scontent.fsgn5-7.fna.fbcdn.net/v/t1.15752-9/72967144_398954134116112_4459523268524638208_n.png?_nc_cat=103&_nc_oc=AQmRe6x_nb8Veub7nFzIn9_SOXpGHzwM6x2jGRsoAzyq1k8ATAN2hU4vOpeURCjG_uE&_nc_ht=scontent.fsgn5-7.fna&oh=59e679428ddb8efbd7fcfd3bee40486b&oe=5E37E0E6' width=400></center>
<center><b> Ảnh 18. </b> Cái cây..hay là cái gì khác? <i> [4] </i></center>
## 4.3. Sư đa dạng về ngoại hình

Kể cả các sự vật cùng loại cũng có **intra-class variance** lớn (tạm hiểu là khác biệt rất lớn về ngoại hình) 

<center> <img src="https://scontent.fsgn5-6.fna.fbcdn.net/v/t1.15752-9/72329140_2359449911036802_9204249356394299392_n.png?_nc_cat=106&_nc_oc=AQkRmxTPfpccsHjtpdUkCxaXZbFGzcuU3ed1FJzpYLYpxrnlHGUC5suw4mm4c7ndjOY&_nc_ht=scontent.fsgn5-6.fna&oh=ee6f2e73bfa1edb8b72224547e72ecce&oe=5E20F75D" width=400> </center>
<center><b> Ảnh 13. </b> Shape variation (Khác nhau về hình dáng) - <i> [4] </i></center>
<center><img src="https://scontent-hkg3-1.xx.fbcdn.net/v/t1.15752-9/72547969_382958022581658_5081595091747864576_n.png?_nc_cat=109&_nc_oc=AQk46lhPXoGyMMaBtqNIcKZjS9h4IoFfdE2RhZVwbBL4gr6M675AMd5CWVR2skILdX8&_nc_ht=scontent-hkg3-1.xx&oh=08b78db0ae08179207bb15dd8c9ab64a&oe=5E26D1CF" width=400></center>
<center><b> Ảnh 14. </b> Occlusion (Bị che khuất) - <i> [4] </i></center>
<center><img src='https://scontent.fsgn5-5.fna.fbcdn.net/v/t1.15752-9/72529808_2469499526479112_4046001098551459840_n.png?_nc_cat=100&_nc_oc=AQkwyueIlKGvvYnpopKEf1LHsMpwDsY7HpzSCjP3mUznt98aQDl_ZrD6cMEvRF5rY-M&_nc_ht=scontent.fsgn5-5.fna&oh=3e5617e8eb175aada4c24e638f98a1fc&oe=5E397B2D' width =400></center>
<center><b> Ảnh 19. </b> background clutter (ảnh nền lộn xộn)<i> [4] </i></center>
## 4.4. Mơ hồ về chiều sâu

<center> <img src="https://scontent.fsgn5-6.fna.fbcdn.net/v/t1.15752-9/72082174_544757772966431_6821665344709459968_n.png?_nc_cat=106&_nc_oc=AQkCc0q78k74o-MMZhIhd42qlZ4raxUHXOpzGghrE7AK52I9SnK1F1gqTeCJwBF5R2U&_nc_ht=scontent.fsgn5-6.fna&oh=afb1c276a0658201abdeea8780591c7e&oe=5E2E98FD" width=400></center>
<center><b> Ảnh 20. </b> Người đàn ông đang đứng trước hay kế tháp Pisa? <i> [7] </i></center>
Hoho bằng kinh nghiệm dày dặn của mình bạn đủ thông minh để biết rằng anh này không thể đứng kế tháp Pisa được đúng không? Nhưng còn bức ảnh này thì sao:

<center><img src="https://scontent.fsgn5-5.fna.fbcdn.net/v/t1.15752-9/72398935_770809700005510_1908022715688681472_n.png?_nc_cat=100&_nc_oc=AQmP4PqUZCQvo8ccndF3SdX4Xdj6p5LshPBaWWinw75X9m7qtz7E4R1Mozzjgvk53AM&_nc_ht=scontent.fsgn5-5.fna&oh=d94f8b8132e14cd659e37539ca8db0aa&oe=5E62C950" width=400> </center>
<center><b> Ảnh 21. </b> Quả cà chua khổng lồ! <i> [7] </i></center>
## 4.5. Ngữ nghĩa cao

Có những ảnh mang ý nghĩa trực tiếp mà bạn có thể hiểu ngay được, nhưng cũng có những ảnh mang hàm ý rất sâu xa, đòi hỏi cao về kiến thức của người đọc nó.

<center><img src="https://i.redd.it/ejun3n7qxrc11.png" width=300></center>
<center><b> Ảnh 22. </b> Ảnh meme<i> [7] </i></center>
Khoảng cách giữa ảnh meme và ý nghĩa thật sự ẩn chứa trong nó là .. một dải ngân hà. Ảnh meme có thể biễu diễn một vài sự vật hiện tượng, nhưng chủ thể chính mà nó muốn nhắc đến hoàn tòan khác trong hình. Hiện nay đã có một bài báo [8] của các nghiên cứu sinh Stanford sử dụng Deep Neural Networks để tạo ra cả meme, công nghệ giờ thật là "chanh xả"! 

<center><img src="http://www.extracatchy.net/wp-content/uploads/2017/04/meaningful-pictures.jpg" width=400></center>
<center><b> Ảnh 23. </b> Ảnh châm biếm - <i> extracatchy </i></center>
Ngoài ra, ta còn có ảnh châm biếm, mỗi tấm mang hàm ý sâu xa không kém gì ảnh meme.

# 5. Một số chủ đề tiêu biểu trong Computer Vision

Nhắc đến CV là không thể không nhắc đến hội nghị CVPR. [12] CVPR là một trong 3 hội nghĩ top đầu của CV trên thế giới. Vào năm 2019, có tổng số 1300 bài báo được thông qua trong số 5165 bài báo được nộp vào hội nghĩ.

<center> <img src='https://scontent.fsgn5-6.fna.fbcdn.net/v/t1.15752-9/73342373_503354376909863_3683605295851896832_n.png?_nc_cat=106&_nc_oc=AQlUf-81SexZZCyTHg8z9UwTmzWVrScviSg9iqx2DaZI4LXz_7nJELPGgqRxU99BxEA&_nc_ht=scontent.fsgn5-6.fna&oh=22623ff8c008194d73e4f60fa3646cb9&oe=5E18DE0D' width=500></center>
<center><b> Ảnh 24. </b> Các hướng nghiên cứu CVPR 2019 <i> [12] </i></center>
Không ngạc nhiên khi gần như toàn bộ các nghiên cứu đều hướng về Deep Learning. Trong đó một số bài toán nổi bật gồm có:

- Học sâu (Deep learning)
- Nhận dạng: phát hiện, phân loại, truy vấn (Detection, Categorization, Retrieval)
- Nhận dạng mặt, cử chỉ và dáng người (Face, Gesture, and Pose)
- Thị giác cấp thấp (Low-level vision)
- Phát sinh, tổng hợp hình ảnh và video (Image and Video Synthesis)
- Phân đoạn ảnh (Image Segmentation)
- Kết hợp thị giác và ngôn ngữ (Vision + )
- ...

## 5.1. Object Detection

Object Detection là một bài toán kinh điển của CV. Với đầu vào là một bức ảnh và đầu ra là các "hộp" bo xung quanh các đối tượng và cho biết đối tượng đó là gì. 

<center><img src='https://miro.medium.com/max/768/1*VXZ8CamGG2Z0M0N4t0Fmng.jpeg' width=400></center>
<center><b> Ảnh 25. </b> Phát hiện và nhận dạng vật thể (Object Detection) -  <i> towardsdatascience</i></center>
## 5.2. Image Synthesis

Gần đây với sự xuất hiện của ứng dụng "Sketch-to-Image" của Nvidia, hướng nghiên cứu này đã trở nên phát triển nhiều hơn. Với đầu vào là hình vẽ tay các vật bằng các loại "cọ đối tượng khác nhau", đầu ra là một bức ảnh phát sinh dựa trên đó.

<center><img src='https://cqf.io/ImageSynthesis/teaser.png' width=400></center>
<center><b> Ảnh 26. </b> Tổng hợp và phát sinh hình ảnh [13] <i> </i></center>
## 5.3. Pose & Gesture Detection

Ngôn ngữ con người không chỉ xoay quanh những lời nói, mà còn bao gồm cử chỉ nữa. Bằng cử chỉ, chúng ta có thể phân tích được "phong cách" của người nói là gì.

<center><img src='https://scontent.fsgn5-7.fna.fbcdn.net/v/t1.15752-9/72462682_417543185623500_5821216935752237056_n.png?_nc_cat=103&_nc_oc=AQl1vnyEXKeu59DkaxuiTK5_UBsYgeTPaqUZ1sAi25ZhVhEVyW0y1e74bg0y3Wyg8TM&_nc_ht=scontent.fsgn5-7.fna&oh=d2b691b559cce3aafd3e3386463a3eec&oe=5E2CC23F' width=400></center>
<center><b> Ảnh 27. </b> Tìm ra phong cách một người thông qua cử chỉ giao tiếp [14] <i> </i></center>
## 5.4. Image segmentation

[15] Ở trên mình có giới thiệu về Object Detection, tuy nhiên thực tế bounding box không biểu thị được **đúng hình dạng** của vật thể mà mình quan tâm. Trong bài toán segmentation, với đầu vào là bức ảnh, đầu ra cho biết các **đường bao** các vị trí mà đối tượng đó xuất hiện trên ảnh đó theo đúng hình dạng của đối tượng. 

<center><img src='https://divamgupta.com/assets/images/posts/imgseg/teaser.png' width=400></center>
<center><b> Ảnh 28. </b> Phân đoạn hình ảnh  <i> - divamgupta </i></center>
<center>

## 5.5. Vision & Language

Một trong những hướng nghiên cứu cũng rất thú vị là **Image Captioning**. Từ một hình ảnh, phát sinh ra một câu mô tả tương ứng với hình ảnh đó. Cái khó là làm sao cho câu được phát sinh ra tự nhiên hết sức có thể. Đây là hướng nghiên cứu mà có cả sự kết hợp của **CV** và **Mô hình hóa ngôn ngữ** (Language Modelling)

<img src='https://miro.medium.com/max/4435/1*6BFOIdSHlk24Z3DFEakvnQ.png' width=400></center>

<center><b> Ảnh 29. </b> Phát sinh câu mô tả thông qua hình ảnh  <i> - towardsdatascience </i></center>

# 6. Tổng kết

Như vậy, thông qua bài giới thiệu về CV trên, bạn đã một phần nào hiểu được CV là gì: 

- CV là một nhánh nghiên cứu trong Khoa học Máy tính để làm máy tính **hiểu** được các thông tin thi giác như con người
- CV không đơn thuần là xử lý ảnh, mà là **hiểu** bức ảnh
- CV đã, đang và sẽ hỗ trợ con người trong rất nhiều khía cạnh, góp phần nâng cao chất lượng và đưa con người tới tương lai 
- Các bài toán trong CV cũng có rất nhiều thách thức
- CV có rất nhiều hướng khác nhau để các bạn có thể tham gia nghiên cứu, và có vẻ như hướng nào cũng thú vị 😁

Qua bài viết này, mình hi vọng một có thể giúp các bạn có nhiều cảm hứng hơn để học CV và bạn có thể tự hào rằng Việt Nam có một cộng đồng CV Việt Nam lớn và luôn sẵn sàng hỗ trợ bạn! 😁

# Tham khảo

[1] [A Gentle Introduction to Computer Vision - Machine Learning Mastery](https://machinelearningmastery.com/what-is-computer-vision/)

[2] [Computer Vision - Wikipedia](https://en.wikipedia.org/wiki/Computer_vision#cite_note-Ballard-Brown-1982-1)

[3] [Introduction to Computer Vision - Georgia Tech on Udacity](https://www.udacity.com/course/introduction-to-computer-vision--ud810)

[4] [CS4670 Cornell's Introduction to Computer Vision](http://www.cs.cornell.edu/courses/cs4670/2018sp/lec01_intro.pdf)

[5] [Checker shadow illusion - Wikipedia](https://en.wikipedia.org/wiki/Checker_shadow_illusion)

[6] [Stereoscope - wikipedia](https://en.wikipedia.org/wiki/Stereoscope)

[7] [Introduction to Computer Vision - Toyota Technological Institute at Chicago (TTIC)](https://www.docsity.com/en/depth-ambiguity-introduction-to-computer-vision-lecture-12-computer-science/57838/)

[8] [Dank Learning: Generating Memes Using DeepNeural Network](https://arxiv.org/pdf/1806.04510.pdf)

[9] [Cooper Smith, "Facebook Users Are Uploading 350 Million New Photos Each Day - Insider"](https://www.businessinsider.com/facebook-350-million-photos-each-day-2013-9)

[10] [Xiaoyi Jiang, et. al., Biomedical Imaging: A Computer Vision Perspective](https://link.springer.com/chapter/10.1007/978-3-642-40261-6_1)

[11] [ Yaroslav Kuflinski, "Medical Image Analysis - Can a computer diagnose diseases?](https://www.technologynetworks.com/informatics/articles/medical-image-analysiscan-a-computer-diagnose-diseases-318849)"

[12] [Latest Computer Vision Trends from CVPR 2019](https://towardsdatascience.com/latest-computer-vision-trends-from-cvpr-2019-c07806dd570b)

[13] [Photographic Image Synthesis with Cascaded Refinement Networks](https://cqf.io/papers/Photographic_Image_Synthesis_ICCV2017.pdf)

[14] [Shiry Ginosar et. Al., "Learning Individual Styles of Conversational Gesture"](http://openaccess.thecvf.com/content_CVPR_2019/html/Ginosar_Learning_Individual_Styles_of_Conversational_Gesture_CVPR_2019_paper.html)   

[15] [nttuan8, Image Segmentation với U-Net](https://nttuan8.com/bai-12-image-segmentation-voi-u-net/)