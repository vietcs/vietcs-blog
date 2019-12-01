---
title: "Giới thiệu về Computer Vision (Phần 1)"
date: 2019-10-13T13:34:08+07:00
draft: false
authors: ["phatnt"]
categories:
  - computer vision
tags:
  - introductory
  - vision
  - classical
slug: gioi-thieu-CV-1
---

Có thể nói rằng, chúng ta thật may mắn khi được sinh ra trong một khoảng thời gian thế giới có thật nhiều sự đổi mới, công nghệ bắt đầu phát triển nhanh hơn bao giờ hết. Những công nghệ trước giờ chỉ có trên những thước phim Hollywood, giờ đây lại càng trở nên gần gũi với đời sống, trở thành một "cánh tay nối dài" của con người. Điển hình nhất là các ứng dụng Trí tuệ Nhân tạo mà làm mưa làm gió như hiện tại: Xe tự hành, robotics, nhà thông minh,.. Mà trong đó, **Computer Vision** (Thị giác Máy tính) là một lĩnh vực đóng không nhỏ trong đó, với mong muốn làm máy tính có khả năng **nhìn** được như con người. Nghe thật vi diệu phải không? Vậy thì chúng ta sẽ đi tìm hiểu xem chúng là gì nhé!

{{% toc %}}



<center><img src="https://miro.medium.com/max/1200/1*s9raSe9mLeSSuxE3API-ZA.gif" width=400></center>
<center> <b>Ảnh 1</b> Minh họa Object Detection - <i>towardsdatascience </i></center>
Như vậy, theo bạn, **Computer Vision** (CV)  là gì?  

Đây có vẻ như là một khái niệm mà người không biết thì nghĩ mình biết rất rõ, còn người nghĩ mình không biết thì lại biết rất rõ 🤣. Mình có đi thử hỏi một số bạn thì xem các bạn đó nghĩ CV là thứ gì trên đời? Mình sẽ gom các câu trả lời thành một số dạng sau đây:

- "CV là Image Processing (Xử lý ảnh)"

- "CV là nhận dạng Object Detection (Nhận dạng vật thể)"
- "CV là dạy cho máy tính hiểu các thông tin thị giác (như ảnh và video) như con người"
- "CV là các hệ thống camera sử dụng trí thông minh của máy để giải quyết các công việc của người"
- "CV là liên quan tới việc tìm kiếm các quy luật để khai thác thông tin thị giác từ ảnh và video"

Hmm có vẻ như ai cũng có lý cả đúng không? Theo bạn, theo bạn ai đúng ai sai?

# 1. Computer Vision là gì?
## 1.1 Mỗi bức ảnh là một câu chuyện

<center><img src="https://upload.wikimedia.org/wikipedia/commons/a/ab/Accident_de_chemin_de_fer_%C3%A1_la_gare_Montparnasse_en_1895.jpg" width=300></center>
<center> <b>Ảnh 2.</b> L'Accident de la Gare Montparnasse</center>
Nhìn bức ảnh bạn thấy gì:

- Một tai nạn xe lửa thảm khốc ?
- Một bức tranh về xưa cũ về tàu hỏa ?
- Mọi người đang xây dựng hiện trường để đóng phím (có cái thang và dây) ?
- Một công viên giải trí (theme park) để mọi người chụp hình ?

<center><img src = "https://scontent.fsgn5-6.fna.fbcdn.net/v/t1.15752-9/71948514_691248641361382_6832124930105016320_n.png?_nc_cat=106&_nc_oc=AQnwU005seafSaNukp2Ck5Tbwoidxpy0g0NweiTWEI6ktgnjhcV86SbQU6EBTRvdnmU&_nc_ht=scontent.fsgn5-6.fna&oh=006b29806b8a53ef374e1502112146bc&oe=5E29D8CB" width=400></center>
<center> <b>Ảnh 3</b>. Martin Luther King, "I Have A Dream" </center>
<center> Bạn thấy điều gì ở bức ảnh này? </center>
Nhiều bạn sẽ có cách hiểu hình ảnh trên khác nhau, nhưng các bạn sẽ nhận ra rằng não bạn đã đưa ra **câu chuyện** (story) khác nhau cho các bức ảnh, và não bạn làm điều này một cách rất **tự nhiên** bởi ví chúng được sinh ra để làm những việc như thế. 

Nhưng "story" ở đây là gì? Tùy thuộc vào việc bạn muốn làm gì với nó! Ví dụ như là:

- Vị trí các vật trong ảnh
- Cảm xúc của bức ảnh
- Độ sâu các vật thể trong bức ảnh
- Hàm ý của bức ảnh
- ..

Qua đó, **CV** là một lĩnh vực nghiên cứu để khiến máy tính hiểu được các thông tin thị giác tương tự như con người. 

`The goal of computer vision is to write computer programs that can interpret images ` [3]

## 1.2 Định nghĩa 

<center><img src="https://i0.wp.com/static1.businessinsider.com/image/535edec0ecad04c0741f732f/construction_google_car.gif" width=400> </center>
<center> <b>Ảnh 4.</b> Thị giác trong xe tự hành - <i>informationstrategyrsm</i> </center>
Bây giờ chúng ta sẽ coi thêm một định nghĩa tổng quát và "xịn xò" hơn nhé. Theo định nghĩa của Dana H. Ballard và Christopher M. Brown trong cuốn sách "Computer Vision" xuất bản năm 1982:

`Computer vision is an interdisciplinary scientific field that deals with how computers can be made to gain high-level understanding from digital images or videos. From the perspective of engineering, it seeks to automate tasks that the human visual system can do` [1]

Như vậy là **CV** sẽ quan tâm tới việc làm thế nào để khiến máy tính có thể **hiểu** được các thông tin thị giác như ảnh và videos. Nhiệm vụ chính của **CV** là làm cầu nối (bridging) cho khoảng cách từ giữa trí thông minh thị giác của con người với trí thông minh thị giác của máy tính. Ở khía cạnh kĩ thuật thì **mục tiêu** của **CV** là **tự động hóa** các tác vụ liên quan tới thị giác giống như con người: Detection (nhận dạng), Image Captioning (Nhìn hình mô tả), Visual question answering (Hỏi đáp thị giác),..

# 2. Computer Vision không phải là Image Processing

Khác với Image Processing (xử lý ảnh): 

- **Đẩu vào**: Ảnh 
- **Đầu ra**: Ảnh

Với đầu vào là ảnh, CV hướng tới đầu ra là .. "**hiểu**" bức ảnh. Việc **hiểu** này có vẻ rất đơn giản đối với chúng ta, bởi lẽ, khi bạn sinh ra, ban đầu bạn sẽ thấy mờ mờ một chút, rồi đột nhiên, bạn thấy và hiểu mọi thứ tự nhiên đến lạ! Trong các ví dụ tiếp theo, chúng ta cùng tìm hiểu xem việc **hiểu** là như thế nào nhé.

## 2.1. Hiệu ứng Đổ bóng bàn cờ Adelson

[5] Hiệu ứng Đổ bóng bàn cờ (Checker-shadow effect) là một nghiên cứu về ảo ảnh thị giác được công bố bởi Edward H. Adelson, giáo sư về Khoa học Thị giác tại MIT vào năm 1995. 

<center><img src='https://www.illusionsindex.org/images/illusions/Adelson-Checkershadow/33_adelson_main.jpg' width=400></center>
<center><b> Ảnh 5. </b> Hiệu ứng đổ bóng bàn cờ Adelson</center>
Bức ảnh này gồm một bàn cờ, phần lớn được đổ bóng bằng một khối hình trụ.

**Câu hỏi:** Theo bạn, A và B vùng nào tối hơn? 

... Có vẻ như là B có màu nhạt hơn A.

Ok, giả sử bạn có một cái photometer (thước đo ánh sáng) và bạn áp vào thì sẽ như thế nào:

<center><img src='https://upload.wikimedia.org/wikipedia/commons/thumb/2/21/Grey_square_optical_illusion_proof2.svg/2000px-Grey_square_optical_illusion_proof2.svg.png' width=400></center>
<center><b> Ảnh 6. </b> Hiệu ứng đổ bóng bàn cờ với photometer</center>
Voila! Photometer sẽ cho bạn thấy rằng A và B đều có độ sáng như nhau. Như vậy là A và B có màu hoàn toàn giống nhau sao? 

Đúng vậy, bức hình này tạo ra một ảo ảnh thị giác rằng ô vuông A có vẻ như có màu đậm hơn ô vuông B... hoặc là do não của bạn nghĩ rằng đó là một ô sáng nhưng chả qua nó chỉ được đổ bóng thôi? Nhưng chắc chắn 1 điều rằng, đối với máy tính, vùng A và B có màu giống nhau (Vì chúng có cùng intensity mà!)

## 2.2.  Hiệu ứng đổ bóng Kersten

[3] Ở ví dụ trước, 2 ô vuông A và B có **độ lớn màu** (color intensity) hoàn toàn giống nhau, tuy nhiên não của bạn biết chắc chắn ô B là 1 ô màu sáng, chả qua là nó bị đổ bóng thôi. Như vậy, **Nhìn thấy** (seeing) không chỉ đơn thuần là **một thông số có thể đo lường được** (property of measurement), **nhìn thấy** là xây dựng một **tri giác** (percept) hoặc một **câu mô tả** (description) về các **thông tin thị giác** (visual information) mà bạn nhìn thấy. Ở trong não của bạn, điều này diễn ra rất tự nhiên. 

Ở ví dụ này, chúng ta cùng tìm hiểu thêm về một hiện tượng tương tự:

<center><img src='https://upload.wikimedia.org/wikipedia/commons/5/5d/Kersten%27s_shadow_effect_-_increasing_height.gif' width=400'></center>
<center><b> Ảnh 7. </b> Lifting shadow</center>
<center><img src='https://upload.wikimedia.org/wikipedia/commons/thumb/7/76/Kersten%27s_shadow_effect_-_same_height.gif/800px-Kersten%27s_shadow_effect_-_same_height.gif' width=400></center>
<center><b> Ảnh 8. </b> Flat-moving shadow</center>
Ở 2 bức hình này, ta thấy 1 chiếc hộp di chuyển theo 2 hướng khác nhau trên cùng 1 mặt phẳng: 

- Một chiếc hộp đi từ trái qua phải và hướng lên trên
- Một chiếc hộp đi xéo về hướng góc phải của mặt phẳng

Nếu bạn để ý kĩ, hình ảnh 2 chiếc hộp đều nằm ở vị trí giống nhau, chỉ cái bóng là thay đổi. Não chúng ta, với kinh nghiệm hiểu biết về ánh sáng và đổ bóng dày dặn từ cha sinh mẹ đẻ tới giờ, não ta đang giả định rằng có một nguồn sáng phía trên chiếc hộp, và ở hình đầu tiên chiếc hộp được nhấc bổng lên, còn hình bên dưới đi đi với góc phải của mặt phẳng. 

Một lần nữa,  **Nhìn thấy** không chỉ đơn thuần là **một thông số có thể đo lường được** . **Nhìn** là để **hiểu**.

## 2.3. Kính lập thể

<center><img src='https://foundlingmuseum.org.uk/wp-content/uploads/2015/12/Stereoscope-in-The-Fallen-Woman-848x400.jpg' width=400></center>
<center><b> Ảnh 9. </b> Stereoscope - <i> Wikipedia </i></center>
**Kính lập thể** (stereoscope) là một loại thiết bị dùng để xem một cặp ảnh riêng biệt ở dạng lập thể hay dạng ảnh nổi, với nguyên tắc mắt phải và mắt trái nhìn vào cùng một cảnh trên hai ảnh. [6] Về phần stereo vision mình sẽ chia sẻ trong các số tiếp theo 😀.

<center><img src='https://media1.giphy.com/media/qETVHTMHfkhOM/giphy.gif' width=400></center>
<center><b> Ảnh 10. </b> Một bức hình stereoscope của một phụ nữ đang sử dụng stereoscope - <i> tumblr </i></center>
[4] Mắt chúng ta, nhờ sự chênh lệch (difference) giữa 2 bức hình, đã làm cho bạn biết rằng: cô gái đó ngồi phía sau cái bàn và trước một kệ sách.

## 2.4. Sự mơ hồ

[3] Như vậy, qua 3 ví dụ trên, các bạn có thể tự đặt cho mình một câu hỏi: Phải chăng não chúng ta bằng một cách nào đó đang hoạt động một cách sai lệch? Cái hộp trong *hình 7* và *hình 8* có lẽ đã không di chuyển theo hướng của chúng ta nghĩ? 

Nhưng thật ra, có một sự mơ hồ về việc chiếc hộp đang di chuyển theo hướng nào, và não của bạn đã viết nên một câu chuyện để giải thích hiện tượng đó. Cuối cùng, sự khác nhau giữa Xử lý ảnh và Thị giác Máy tính là **xây dựng câu chuyện** đó.

# 3. Tại sao học Computer Vision?

Vậy các bạn đã biết computer vision là gì, tại sao chúng ta phải học computer vision?

## 3.1. Để "hiểu" con người

<img src= 'https://scontent.fsgn5-6.fna.fbcdn.net/v/t1.15752-9/72652338_446169346004881_1174998495501746176_n.png?_nc_cat=106&_nc_oc=AQmlkyLq9mVqKAKCFtd9HT-AJWE_b21yUUMJjApVpWU_UxtZ8eBu47RG2ZjghDmqqXA&_nc_ht=scontent.fsgn5-6.fna&oh=2e1c4179dacdbb0be0804412f509d66e&oe=5E65053C' width=400>

<center><b> Ảnh 11. </b> Tốc độ đăng tải ảnh như "tên lửa" trên mạng xã hội [4]</center>
Theo Business nsider (2013) [9], mỗi ngày có hơn **350 triệu tấm ảnh** được đăng lên Facebook. Tương đương với việc: trong 1.15 triệu người dùng của Facebook, thì mỗi người đăng tải trung bình 217 tấm mỗi ngày, con số này thật **kinh khủng**! Đó là chưa tính tới số lượng ảnh được đăng tải trên Instagram.

<center><img src='https://cdn.semrush.com/blog/static/media/33/05/3305aff9c92790e235b80be2a3febc54/resize/885x-/1header.png' width=300> </center>
<center><b> Ảnh 12. </b> Ảnh minh họa về  Social Listening - Semrush's blog </center>
Như vậy, nếu như bằng cách nào đó chúng ta có thể **hiểu** những thông tin trong bức ảnh này thì chúng ta có thể **hiểu** được con người. Từ đó, chúng ta có thể đưa ra nhiều **lựa chọn** và **trải nghiệm** phù hợp hơn cho người sử dụng. 

**Ví dụ**: 

- Một anh chàng hay chia sẻ ảnh chơi game thì có thể quảng cáo anh này mua đĩa game
- Một cô gái thích đi du lịch thì có thể

Thực tế, các mạng xã hội và các công cụ tìm kiếm hiện nay đều có sử dụng Computer Vision (và cả NLP nữa) để "lắng nghe" người dùng **(social listening)**

## 3.2. Để hỗ trợ con người

"Nếu chúng ta dạy máy tính biết cách suy nghĩ, trước hết chúng ta phải dạy chúng cách nhìn" - Fei-fei Li 

Hiện nay, các ứng dụng trong Trí tuệ Nhân Tạo đều ít nhiều có sự góp mặt của **CV**. Ví dụ điển hình nhất là cửa hàng Amazon Go, đây là một cửa hàng được lắp đặt các hệ thống camera thông minh để giám sát người mua hàng check-in ra vào.

<center><img src='https://cdn.vox-cdn.com/thumbor/ggSkZPfsAM8ITnEbvendtwajXDo=/0x0:2040x1360/1200x800/filters:focal(901x509:1227x835)/cdn.vox-cdn.com/uploads/chorus_image/image/61868725/nstatt_181019_3042_8781.0.jpg' width=400></center>
<center><b> Ảnh 13. </b> Amazon Go, Siêu thị không người bán của Amazon - The Verge</center>
Như vậy, trước khi ra bất kì quyết định gì về người mua hàng, hệ thống phải **nhìn** thông minh trước đã.

<center><img src='https://d3i6fh83elv35t.cloudfront.net/static/2018/10/AdobeStock_1469331961-1200x800.jpeg' width=400></center>
<center><b> Ảnh 14. </b> Xe tự hành - pbs.org</center>
Ngoài các cảm biến, GPS, xe tự hành còn sử dụng Computer Vision để hỗ trợ điều hướng.



## 3.3. Để cứu người 

[11] Vào năm 2017, các nghiên cứu sinh ở Stanford đã làm nghiên cứu thành công một thiết bị sử dụng CV để chẩn đoán những dấu hiệu bất thường trên da, để phát hiện ra dấu hiệu của ung thư da giai đoạn đầu.

<center><img src="https://news-media.stanford.edu/wp-content/uploads/2017/01/20153422/IMG_6861.jpg" width=400></center>
<center><b> Ảnh 15. </b> Dermatoscope - Thiết bị chẩn đoán bệnh da liễu cầm tay của các nghiên cứu sinh Stanford  [4]</center>
Đây không phải là lần đầu tiên CV xuất hiện trong lĩnh vực y tế, và chắc chắn cũng không phải là lần cuối. Ngoài ra các CV còn được dùng để hỗ trợ chẩn đoán trên ảnh siêu âm (ultrasound), ảnh MRI và cả ảnh CT nữa!

<center><img src='https://abm-website-assets.s3.amazonaws.com/rdmag.com/s3fs-public/embedded_image/2017/04/lung%20cancer.jpg' width=400></center>
<center><b> Ảnh 16. </b> Chẩn đoán ung thư phổi trên ảnh X-quang [4]</center>
Qua phần 1, bạn đã hiểu được cơ bản Computer Vision là gì. Mời bạn xem tiếp <a href='../gioi-thieu-CV-2'> phần 2 </a> để tìm hiểu thêm về Computer Vision nhé!

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