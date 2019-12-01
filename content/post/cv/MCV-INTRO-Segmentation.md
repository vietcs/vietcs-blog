---
title: "Giới thiệu bài toán Phân Đoạn Ảnh"
date: 2019-11-30T13:34:08+07:00
draft: false
authors: ["danhnt"]
categories:
  - computer vision
tags:
  - introductory
  - vision
  - classical
slug: gioi-thieu-phan-doan-anh
---



## 1. Phân đoạn ảnh là gì?

Nếu như bài toán *Phân loại ảnh - Image Classification* trả lời cho câu hỏi "cái gì trong ảnh", bài toán *Phát hiện đối tượng - Object Detection* trả lời cho câu hỏi "cái gì trong ảnh và vị trí ở đâu?" thì bài toán *Phân đoạn ảnh - Image Segmentation* sẽ giúp chúng ta trả lời chính xác *điểm ảnh nào thuộc về đối tượng nào*.

Hình 1 đưa ra ví dụ minh họa sự khác biệt giữa ba bài toán trên:

<p>
	<center>
        <img align="center" src='https://raw.githubusercontent.com/danielthe1st/CS_Blog/master/classification.jpg' width=240 height=150>
    	<img align="center" src='https://github.com/danielthe1st/CS_Blog/blob/master/detection.jpg?raw=true' width=240 height=150>
     	<img align="center" src='https://github.com/danielthe1st/CS_Blog/blob/master/segment.jpg?raw=true' width=240 height=150>
    </center>
<center><caption>Hình 1: Phân loại ảnh - Phát hiện đối tượng - Phân đoạn ảnh</caption></center>
<center><caption><i>Nguồn: internet</i></caption></center>
</p>

Với ảnh đầu vào như hình trên, thuật toán *Phân loại ảnh* sẽ cho ta biết có một đối tượng là *"con chó"* trong hình. Tuy nhiên ta sẽ không biết chính xác vị trí của nó ở đâu. Trong khi đó, với thuật toán *Phát hiện đối tượng*, ta sẽ biết thêm được thông tin về vị trí tương đối của *"con chó"* trong hình thông qua một cửa sổ *(bounding-box)*. Trong khi đó một thuật toán *Phân đoạn ảnh* sẽ cho ta biết được một cách chính xác hơn những điểm ảnh nào thuộc về đối tượng *"con chó"*.

<p>
	<center>
        <img align="center" src='https://github.com/danielthe1st/CS_Blog/blob/master/segmentmap.jpg?raw=true' width=250 height=150>
    </center>
<center><caption>Hình 2: Dạng biểu diễn khác của bức ảnh sau khi được <i>Phân đoạn</i></caption></center>
</p>

Một cách tổng quát, sau khi *Phân đoạn ảnh*, ta được một dạng biểu diễn khác *(segment map - Hình 2)* của bức ảnh mà qua đó máy tính có thể dễ dàng ***hiểu*** được bức ảnh hơn! 

Về mặt kĩ thuật, *Phân đoạn ảnh* là quá trình ***gán nhãn cho từng điểm ảnh*** trong bức ảnh: các điểm ảnh có cùng tính chất sẽ được gán cùng một nhãn.

## 2. Ứng dụng của Phân đoạn ảnh

### 2.1 Y học

*Phân đoạn ảnh* được ứng dụng trong Y học để đọc các ảnh X-quang, ảnh siêu âm,... từ đó giúp các bác sĩ trong việc nhận diện bệnh hoặc phát hiện các bất thường trong những bức ảnh ấy. Một ứng dụng kinh điển của *Phân đoạn ảnh* trong lĩnh vực này chính là **phân đoạn tế bào ung thư**. Trong thực tế, hình dạng của các tế bào ung thư có tính đặc trưng và bằng việc phát hiện ra chúng từ sớm, căn bệnh ung thư có thể được điều trị với tỉ lệ thành công cao. 

*Phân đoạn ảnh* có thể trợ giúp trong việc nhận diện hình dạng của của những tế bào ung thư này thông qua việc phát hiện biên cạnh của chúng *(Hình 3).*

<p>
	<center>
        <img align="center" src='https://github.com/danielthe1st/CS_Blog/blob/master/breastcancer.gif?raw=true' width=250 height=150>
        <img align="center" src='https://github.com/danielthe1st/CS_Blog/blob/master/cellcancersegment.jpg?raw=true' width=250 height=150>
    </center>
<center><caption>Hình 3: Tế bào ung thư vú - Phân đoạn ảnh tìm ra biên cạnh các tế bào</caption></center>
    <center><caption><i>Nguồn: towardsdatascience - semanticscholar</i></caption></center>
</p>

### 2.2 Xe tự hành

Để xe tự hành có thể hoạt động, chúng cần nhận dữ liệu từ các cảm biến quanh xe như máy ghi hình, ra-đa,... để "*nhận biết*" thế giới xung quanh. Việc này được thực hiện thông qua các mô hình *Phát hiện vật thể và Phân đoạn ảnh*. Việc phân loại đến cấp độ điểm ảnh là cần thiết vì sẽ ảnh hưởng đến tính an toàn của hệ thống khi đưa ra sử dụng thực tế.

<p>
	<center>
        <img align="center" src='https://github.com/danielthe1st/CS_Blog/blob/master/autocar.gif?raw=true' width=250 height=150>
        <img align="center" src='https://github.com/danielthe1st/CS_Blog/blob/master/semseg.gif?raw=true' width=250 height=150>
    </center>
<center><caption>Hình 4: Phát hiện vật thể trong ảnh - Phân đoạn ảnh cho xe tự hành</caption></center>
    <center><caption><i>Nguồn: towardsdatascience - medium</i></caption></center>
</p>

### 2.3 Xử lý ảnh vệ tinh

Với sự phát triển của khoa học công nghệ, con người đã đưa vào không gian rất nhiều những vệ tinh nhân tạo, với mục đích thu thập hình ảnh nói riêng và các thông tin về Trái Đất nói chung. Để vẽ được bản đồ thế giới từ những dữ liệu ấy đòi hỏi con người phải xác định đâu là nhà cửa, cây cối, đường xá, công viên... Nếu công việc này được thực hiện thủ công, khối lượng dữ liệu cũng như thời gian phải bỏ ra là rất lớn.

*Phân đoạn ảnh* có thể giải quyết bài toán này nhằm giúp con người nhận biết từng vị trí cụ thể trên Trái Đất mà không tốn quá nhiều thời gian và công sức.

<p>
    <center>
    <img align="center" src='https://github.com/danielthe1st/CS_Blog/blob/master/Left-Original-satellite-image-Right-Semantic-segmentation-of-roads-buildings-and.png?raw=true' width=450>
    </center>
<center><caption>Hình 5: Ứng dụng phân đoạn ảnh vệ tinh để vẽ bản đồ</caption></center>
    <center><caption><i>Nguồn: researchgate</i></caption></center>
</p>

Bên cạnh những ứng dụng nổi bật kể trên, *Phân đoạn ảnh* còn có thể giải quyết nhiều bài toán khác trong đời sống. Với nhiều ứng dụng hữu ích như vậy chúng ta hãy cùng tìm hiểu xem, có những phương pháp nào để giúp chúng ta thực hiện được việc *Phân đoạn ảnh* nhé!

## 3. Các phương pháp Phân đoạn ảnh

Trong phạm vi của một bài Giới thiệu, chúng ta sẽ cùng nhau tìm hiểu về hai phương pháp cơ bản và đơn giản nhất của *Phân đoạn ảnh*, để từ đó có được cái nhìn rõ nét hơn về nó. Các phương pháp khác sẽ được đề cập ở nội dung bài sau.

### 3.1 Phân đoạn ảnh bằng cách đặt ngưỡng - Thresholding Method

Đúng với cái tên của nó, ý tưởng của việc phân đoạn ảnh bằng cách đặt ngưỡng là sử dụng giá trị của các điểm ảnh (giá trị ba kênh màu nếu là ảnh RGB; độ sáng nếu là ảnh mức xám) và tiến hành phân đoạn.

Để thực hiện phân đoạn, chúng ta sẽ chọn một giá trị ngưỡng - *threshold value*. Các điểm ảnh có giá trị dưới/ trên ngưỡng này sẽ được phân loại là vật thể/ nền. Phương pháp này còn có tên gọi khác là *thresholding segmentation*:

- Nếu phân đoạn ảnh thành hai vùng, ta sẽ chọn một ngưỡng toàn cục - *global threshold*.
- Nếu phân đoạn ảnh thành nhiều vùng, ta sẽ chọn nhiều ngưỡng. Mỗi ngưỡng này được gọi là ngưỡng cục bộ - *local threshold*.

Cùng cài đặt thử phương pháp phân đoạn này nhé, chúng ta xem xét ví dụ sau: *(tham khảo analyticsvidhya)*

<p>
    <center>
    <img align="center" src='https://github.com/danielthe1st/CS_Blog/blob/master/1.jpeg?raw=true' width=250>
    <img align="center" src='https://github.com/danielthe1st/CS_Blog/blob/master/1gray.jpg?raw=true' width=250>
    </center>
    <center><caption>Hình 6: Ảnh đầu vào gồm ảnh gốc (trái) và ảnh mức xám tương ứng được sử dụng làm đầu vào để phân đoạn ảnh (phải)</caption></center>
</p>

[***Demo source code***](https://colab.research.google.com/notebook#fileId=1sBsaZGH11JJcZmE2zhAwocXEWs30tTfl&offline=true&sandboxMode=true)

Với ảnh màu ban đầu, ta sẽ chuyển sang ảnh mức xám (Hình 6-phải) để làm đầu vào cho thuật toán phân đoạn ảnh sử dụng ngưỡng. Mục đích của việc chuyển đổi là để dễ dàng hình dung cách thuật toán này hoạt động mà thôi.

Chúng ta sẽ thử hai trường hợp: global threshold và local threshold. Đầu tiên, một vùng vật thể (biểu thị bởi giá trị 1 - màu đen) và nền (biểu thị bởi giá trị 0 - màu trắng). Để thực hiện, chúng ta sẽ đặt một ngưỡng *global threshold*, ở đây giá trị ngưỡng được chọn là trung bình của các giá trị điểm ảnh trên ảnh mức xám. Điểm ảnh có giá trị trên ngưỡng sẽ là vùng vật thể, dưới ngưỡng sẽ là vùng nền, kết quả được thể hiện ở *Hình 7*:

<p>
    <center>
    <img align="center" src='https://github.com/danielthe1st/CS_Blog/blob/master/2scales_Seg.png?raw=true' width=350>
    </center>
    <center><caption>Hình 7: Phân đoạn ảnh thành hai phần</caption></center>
</p>

Trường hợp hai, chúng ta sẽ thử phân đoạn ảnh này thành bốn phần: ba vùng vật thể và một vùng nền. Việc cần làm là bạn sẽ phải chọn ra các ngưỡng cục bộ - *local threshold*. Với *Source Code Demo* mình đã chọn các ngưỡng là 1.5, 1.25 và 1.0 lần giá trị trung bình các điểm ảnh. Kết quả của ảnh phân đoạn thể hiện ở *Hình 8*:

<p>
    <center>
    <img align="center" src='https://github.com/danielthe1st/CS_Blog/blob/master/3scales_Seg.png?raw=true' width=350>
    </center>
    <center><caption>Hình 8: Phân đoạn ảnh thành bốn phần</caption></center>
</p>

Các bạn có thể thử thêm nhiều trường hợp cũng như tùy chỉnh theo ý thích của riêng mình, tham khảo ở Source Code đính kèm bên trên nếu cần nhé!

Tổng kết lại *Phân đoạn ảnh* bằng cách đặt ngưỡng, chúng ta có một phương pháp với những ưu điểm: tính toán đơn giản, tốc độ xử lý nhanh, xử lý tốt chỉ khi các vùng vật thể và vùng nền có sự tương phản với nhau cao. Tuy nhiên, cách làm này còn nhiều hạn chế như chưa hoạt động tốt với những trường hợp giá trị các điểm ảnh không quá khác biệt, hoặc có sự trùng lặp giá trị điểm ảnh giữa các vật thể khác nhau cũng dẫn đến sự thiếu chính xác khi phân đoạn.

### 3.2 Phân đoạn ảnh dựa trên cạnh -  Edge-based Segmentation

Giữa những vật thể khác nhau trong một bức ảnh luôn tồn tại các đường biên phân tách. Tận dụng điều đó, ta tìm ra các biên cạnh và xem đó như là những đường biên của vật thể. Đây là ý tưởng chính của phương pháp này.

Câu hỏi đặt ra là: Làm sao chúng ta phát hiện được những đường biên đó? *Filter (bộ lọc)* và *Convolution (tích chập)* là các từ khóa để trả lời cho câu hỏi này.

Nguyên lý hoạt động của phương pháp này như sau:

1. Xác định hệ số của filter tùy theo mục đích
2. Thực hiện nhân element-wise filter đó với bức ảnh đang xét
3. Trượt filter theo một bước nhảy (stride) tùy ý, có phần đệm (padding) phù hợp để giữ nguyên kích thước bức ảnh
4. Thực hiện đến khi sử dụng hết tất cả các điểm ảnh

Để dễ hình dung, ta hãy xem ví dụ này để biết cách mà filter được áp lên bức ảnh. Để ý rằng nếu không có padding, kích thước ảnh sau khi qua bộ lọc sẽ bị giảm đi *(trong trường hợp này, ảnh đầu vào 5x5 sẽ giảm còn 3x3)*:

<p>
    <center>
    <img align="center" src='https://github.com/danielthe1st/CS_Blog/blob/master/convSobel.gif?raw=true' width=300>
    </center>
    <center><caption><i>Bước nhảy = 1, Kích thước bộ lọc 3x3, Kích thước ảnh 5x5</i> <br>Hình 9: Ví dụ áp dụng một bộ lọc cho một bức ảnh (không có padding)</br></caption></center>
</p>

Hãy cùng làm ví dụ đơn giản về việc trích xuất biên cạnh dọc (vertical) và ngang (horizontal) với giá trị các bộ lọc như sau:
$$
Horizontal = 
\left(\begin{array}{cc} 
1 &  2 & 1 \\
0 & 0 & 0 \\
-1 & -2 & -1
\end{array}\right)
;Vertical =
\left(\begin{array}{cc} 
-1 & 0 & 1 \\ 
-2 & 0 & 2 \\
-1 & 0 & 1
\end{array}\right)
$$
[***Demo Source Code***](https://colab.research.google.com/notebook#fileId=1NccDNF0D13iTHPaWNnXxnPummhavs7_k&offline=true&sandboxMode=true)

Với hình ảnh các ô vuông ở Hình 10, qua hai bộ lọc trên ta được kết quả dưới đây. Quan sát có thể thấy Hình 10-giữa có xu hướng giữ lại những nét ngang trong khi Hình 10-phải có xu hướng giữ lại các nét dọc.

<p>
    <center>
    <img align="center" src='https://github.com/danielthe1st/CS_Blog/blob/master/cross.png?raw=true' width=200 height = 200>
    <img align="center" src='https://github.com/danielthe1st/CS_Blog/blob/master/cross_horizon.png?raw=true' width=200 height = 200>
    <img align="center" src='https://github.com/danielthe1st/CS_Blog/blob/master/cross_vertical.png?raw=true' width=200 height = 200>
    </center>
    <center><caption>Hình 10: Ảnh minh họa phân đoạn dựa vào cạnh</caption></center>
</p>

Mỗi bộ lọc khác nhau sẽ cho phép ta phân đoạn bức ảnh của mình thành các phần khác nhau tùy theo mục đích, các bạn hãy tự tìm hiểu xem còn có những bộ lọc nào nữa nhé.

## 4. Tạm kết

Qua bài giới thiệu này, các bạn đã biết một cách tổng quan thế nào là *Phân đoạn ảnh* và một số ứng dụng của kỹ thuật này trong đời sống. Bên cạnh đó, bạn cũng được tự mình thử nghiệm hai phương pháp đơn giản của kỹ thuật này.

Với hai phương pháp đã đề cập, ta chỉ đơn thuần phân đoạn ảnh dựa trên màu sắc hay biên cạnh mà chưa quan tâm các phân đoạn có thực sự liên kết với nhau hay không. Ở những bài sau, ta sẽ cùng tìm hiểu về *Phân đoạn ảnh theo ngữ nghĩa - Semantic Image Segmentation* để giải quyết vấn đề đó.

Ở bài tiếp theo, chúng ta sẽ tìm hiểu về hai phương pháp khác là *Phân đoạn ảnh* dựa trên vùng (Region-based) và *Phân đoạn ảnh* dựa trên cụm (Clustering-based).

## 5. Tài liệu tham khảo

[1] https://www.analyticsvidhya.com/blog/2019/04/introduction-image-segmentation-techniques-python/ 

[2] https://nttuan8.com/bai-12-image-segmentation-voi-u-net/ 

[3] https://towardsdatascience.com/introduction-to-image-segmentation-with-k-means-clustering-83fd0a9e2fc3 