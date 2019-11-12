---
title: "Nhập môn Image Filtering"
date: 2019-10-25T22:36:42+07:00
draft: false
authors: ["khaild"]
categories: 
  - computer vision
tags:
  - introductory
  - vision
  - image-processing
  - filter
  - template-matching
slug: nhap-mon-image-filtering
---

{{% toc %}}

## <span style="color:blue">1. Giới thiệu </span>

Khi nhận được ảnh từ camera, hệ thống sẽ không xuất ngay mà phải qua các giai đoạn xử lý, một trong số đó là giai đoạn **áp bộ lọc (image filtering)**. 

Filter (hay tiếng Việt là bộ lọc) được dùng rất nhiều trong thị giác máy tính, nhằm loại bỏ nhiễu (**denoising**), làm mờ ảnh (**blurring**), làm mượt (**smoothing**), cân bằng các giá trị pixel trong ảnh (**equalization**), nhận diện viền (**edge detection**), trích xuất đặc trưng (**feature extraction**), so khớp mẫu (**template matching**),... thông qua tương quan chéo hoặc tích chập giữa **kernel** với ảnh hoặc ngược lại.

**Gợi ý: Độc giả không có nhu cầu hiểu mấu chốt các thuật toán có thể kéo thẳng xuống phần 3. Tóm tắt kiến thức. Code đầy đủ dưới dạng Notebook tại [đây]( [https://github.com/leduckhai/My-Data-Container/blob/master/VietCS%20Blog/VietCS%20code/Nh%E1%BA%ADp%20m%C3%B4n%20Image%20filtering.ipynb](https://github.com/leduckhai/My-Data-Container/blob/master/VietCS Blog/VietCS code/Nhập môn Image filtering.ipynb) )**

## <span style="color:blue">2. Bài giảng  </span>

## <span style="color:blue">2.1. Bộ lọc tương quan (Correlation filter)  </span>
Để áp dụng một bộ lọc vào ảnh, ta cần **kernel**. Kernel có thể được hiểu như lõi, là một ma trận mang tính chất đặc trưng quyết định đầu ra của ảnh thông qua thuật toán lọc ảnh [1]. Ở bài tiếp theo, chúng ta sẽ bàn kỹ về tích chập ảnh với kernel và so sánh với tương quan chéo.

Giả sử một cửa sổ con có kích thước $k \times k$ , với  $k$  là số lẻ, ta tính được **tương quan (correlation)** dựa trên công thức:
$$
I[i,j] = \frac{1}{k^2}\sum\_{u=-\frac{k-1}{2}}^{\frac{k-1}{2}}\sum\_{v=-\frac{k-1}{2}}^{\frac{k-1}{2}}I_0[i+u,j+v]
\tag{1}
$$

với:

$i$, $j$ : tọa độ (hàng, cột) của ma trận tương quan $I$

$I_0$: ma trận gốc

Để thực hiện công thức (1), ta lần lượt cho $u$ và $v$ chạy từ *$-\frac{k-1}{2}$* đến *$\frac{k-1}{2}$* rồi cộng các kết quả của từng $I_0[i+u,j+v]$ lại. Tham số $\frac{1}{k^2}$ được gọi là **trọng số mặc định (uniform weights)**.

Bây giờ hãy thử thay các trọng số riêng rẻ bằng một ma trận chứa nhiều trọng số nào! Ta gọi ma trận đó là $K$.

$$
I[i,j] = \sum\_{u=-\frac{k-1}{2}}^{\frac{k-1}{2}}\sum\_{v=-\frac{k-1}{2}}^{\frac{k-1}{2}}K[u,v]I_0[i+u,j+v]
\tag{2}
$$

Công thức (2) được biết đến là **tương quan chéo (cross-correlation)**. Ở bài **Image as a function** ta đã biết được ảnh vốn là một hàm số rời rạc với 2 biến "cột" và "hàng". Nếu xem $K[u,v]$ là một **kernel** (kernel vốn là ma trận chứa các trọng số) và $I_0[i+u,j+v]$ là ảnh gốc thì ta có ngay và luôn công thức tương quan chéo kernel với ảnh. Thuật toán này hoạt động rất đơn giản: cho lần lượt $u$, $v$ chạy từ *$-\frac{k-1}{2}$* đến *$\frac{k-1}{2}$*, ta nhân từng hệ số tại vị trí $[u,v]$ của ma trận $K$ với hệ số tại vị trí $[i+u,j+v]$ của ma trận ảnh gốc $I_0$ rồi cộng các kết quả lại với nhau. 

Trong thư viện Scipy có sẵn hàm [scipy.ndimage.correlate](https://docs.scipy.org/doc/scipy/reference/generated/scipy.ndimage.correlate.html#scipy.ndimage.correlate), bạn đọc có thể tham khảo đường link để áp dụng luôn sau này.

## <span style="color:blue">2.2. Nhiễu Gauss (Gaussian noise) </span>
Trong thực tế có 3 loại nhiễu thường gặp nhất: **nhiễu đốm (speckle noise)**, **nhiễu muối tiêu (pepper-and-salt noise)** và **nhiễu Gauss (Gaussian noise)**. Nhiễu đốm xuất hiện khi các sóng tới (sóng cơ, sóng điện từ,...) giao thoa với nhau tạo nên các điểm cực đại hoặc cực tiểu [2]. Nhiễu muối tiêu xuất hiện do sự gián đoạn việc truyền/nhận tín hiệu hoặc lỗi chuyển đổi tín hiệu tương tự thành tín hiệu số [3]. **Nhiễu Gauss**, được đặt theo tên nhà toán học người Đức **Gauß** (hoặc Gauss), là một loại nhiễu có **mật độ xác suất (probability density)** tương ứng với **phân phối chuẩn (normal distribution)** [4]. Hay nói cách khác, biểu đồ mà nhiễu Gauss tạo nên cũng có hình dạng cái chuông.
<center><img src="https://raw.githubusercontent.com/leduckhai/My-Data-Container/master/VietCS%20Blog/Nh%E1%BA%ADp%20m%C3%B4n%20Image%20filtering/Gaussian%20distribution.png" width=400> </center>
<center> Hình dạng cái chuông của phân phối Gauss (Gaussian distribution) </center>
Công thức mật độ xác suất Gauss:
$$
f(x|\mu, \sigma^2) = \frac{1}{\sigma\sqrt{2\pi}}e^{-\frac{(x-\mu)^2}{2\sigma^2}}
\tag{3}
$$
trong đó:

$\sigma$: độ lệch chuẩn (standard deviation)

$var = \sigma^2$: phương sai (variance)

$\mu$: số trung bình (mean value)

$x$: vị trí tọa độ

Số trung bình $\mu$ thường mặc định là 0. Vì thế tham số quyết định hình dạng của biểu đồ Gauss trong các thuật toán thường là độ lệch chuẩn $\sigma$ hoặc phương sai $var$. Suy rộng ra trong không gian 2D, hàm số Gauss là tích của 2 hàm số Gauss trên trục x và y, với $\mu = 0$ thì viết gọn sẽ là:
$$
f(x,y) = f(x)f(y) = \frac{1}{2\pi\sigma^2}e^{-\frac{x^2+y^2}{2\sigma^2}}
\tag{4}
$$
<center><img src='https://raw.githubusercontent.com/leduckhai/My-Data-Container/master/VietCS%20Blog/Nh%E1%BA%ADp%20m%C3%B4n%20Image%20filtering/Gaussian%202D%20distribution.png'width=400></center>
<center> Phân bố Gauss trong không gian 2D </center>
Gọi $I\_{noisy}$, $I\_{0}$, $A\_{noise}$ lần lượt là ma trận ảnh bị nhiễu Gauss, ma trận ảnh gốc và ma trận nhiễu Gauss thì ta sẽ có công thức:
$$
I\_{noisy}{[i,j]} = I\_{0}{[i,j]} + A\_{noise}{[i,j]}
\tag{5}
$$

Việc tạo nhiễu Gauss thường dùng để thử bộ lọc, vì thế đây là một thuật toán tuy đơn giản nhưng rất quan trọng sau này. Với tinh thần ứng dụng thực tiễn, ta dùng hẵn hàm [skimage.util.random_noise](https://scikit-image.org/docs/dev/api/skimage.util.html#skimage.util.random_noise) có sẵn luôn.

```python
# Nạp các thư viện cần thiết
from matplotlib import pyplot as plt
from skimage.util import random_noise

image = plt.imread('Data/CT_Brain.jpg') # Đọc ảnh đầu vào
img = random_noise(image, var=0.05) # Hàm random_noise dùng để tạo nhiễu Gauss
```



<center><img src='https://raw.githubusercontent.com/leduckhai/My-Data-Container/master/VietCS%20Blog/Nh%E1%BA%ADp%20m%C3%B4n%20Image%20filtering/Gaussian%20noise%20output.PNG' width=400></center>
<center> Kết quả thuật toán </center>
## <span style="color:blue">2.3. Bộ lọc trung bình (Mean filter) </span>

**Bộ lọc trung bình (mean filter)** được xếp vào loại **bộ lọc tuyến tính (linear filter)**. *Độc giả được khuyến khích đọc bài **Linearity and Convolution** để biết thêm cách phân loại bộ lọc tuyến tính/phi tuyến*. Kernel của bộ lọc trung bình là một ma trận kích thước $k\times k$ ($k$ là số lẻ) với các phần tử bằng 1. Đầu ra của bộ lọc sẽ là giá trị trung bình của tổng các phần tử nằm trong kernel nơi nó đi qua [5]. Hại não quá hả? Thôi giải thích bằng hình ảnh cho dễ hiểu nha!

<center><img src='https://raw.githubusercontent.com/leduckhai/My-Data-Container/master/VietCS%20Blog/Nh%E1%BA%ADp%20m%C3%B4n%20Image%20filtering/Mean%20filter%20calculation.png' width=400></center>
Các ma trận ở hàng trên là ma trận ảnh gốc, hàng dưới là ảnh xuất ra và vùng xám là kernel. Ta lấy ví dụ hình bên trái. Theo quy tắc tương quan chéo kernel với ảnh ở (2) thì ta có:

$$
(1\times10)+(1\times41)+(1\times9)+(1\times232)+(1\times186)+(1\times79)+(1\times1)+(1\times43)+(1\times56)=657
$$
Sau đó ta chia trung bình giá trị này, tức chia 9:
$$
\frac{657}{9}=73
$$
Đầu ra của kernel là một giá trị nằm ở tâm kernel đó. Bạn hãy để ý vị trí của số 186 màu đỏ ở trên và 73 ở dưới. Cứ thế, ta cho kernel chạy qua tất cả vị trí trong ảnh thì ta sẽ được một ảnh lọc hoàn chỉnh.

Có bạn thắc mắc: vậy đối với giá trị 10, tức vị trí $[1,1]$ của ma trận ảnh thì tính toán thế nào? Quả thật tại giá trị 10, kernel bị "thừa" ra ngoài nên không thể áp dụng nhân tương quan. Để giải quyết việc này, ta sẽ **đệm (pad)** ma trận ảnh. Hình dưới ta gọi kỹ thuật này là **zero padding**, tức đệm số 0.

<center><img src='https://raw.githubusercontent.com/leduckhai/My-Data-Container/master/VietCS%20Blog/Nh%E1%BA%ADp%20m%C3%B4n%20Image%20filtering/Zero%20padding.png' width=300></center>
Ảnh gốc và ảnh xuất chỉ có kích thước $4\times4$ thôi nhưng khi ta "đệm" số 0 vào các hàng và cột thì ta sẽ có ảnh gốc kích thước $6\times6$.  Thật ra còn nhiều kỹ thuật đệm khác như **đệm đối xứng (symmetric padding)**, **đệm phản chiếu (reflect padding)**, **đệm hằng số (constant padding)**, **đệm rìa (edge padding)** [6] nhưng tôi không tiện diễn giải vì chúng không tạo quá nhiều sự khác biệt với nhau khi xuất ảnh.

Hàm bộ lọc trung bình sẽ được viết thế này, bằng việc thay đổi kernel bạn sẽ có thể tự tạo các phiên bản bộ lọc khác cho riêng mình:


```python
import numpy as np
from scipy import ndimage
# Tạo hàm mean_filter 
def mean_filter(img,n):
    kernel = np.ones((n,n,1))/n**2 # Tạo ma trận kernel
    img_filt = ndimage.correlate(img,kernel) # Tương quan chéo kernel với ảnh
    return img_filt # Xuất ra ảnh đã lọc
```

<center><img src='https://raw.githubusercontent.com/leduckhai/My-Data-Container/master/VietCS%20Blog/Nh%E1%BA%ADp%20m%C3%B4n%20Image%20filtering/Mean%20filter%20output.PNG' width=400></center>
<center> Kết quả thuật toán </center>
Trong ảnh gốc, da mặt Hạ Thảo lấm tấm nhiều nốt mụn đỏ (mà thực ra do tôi dùng Paint chấm lên) nhưng sau khi qua bộ lọc thì chúng đã mờ đi rất nhiều. Trong thực tế, các ứng dụng selfie và photoshop dùng bộ lọc trung bình dưới dạng vùng khoanh nhỏ thay vì áp dụng vào toàn bộ khuôn hình như trên.

## <span style="color:blue">2.4. Bộ lọc Gauss (Gaussian filter) </span>
**Bộ lọc Gauss (Gaussian filter)** được dùng nhiều hơn bộ lọc trung bình vì những ưu điểm vượt trội của nó. Bộ lọc Gauss tuân thủ phân bố ... Gauss nhưng dưới thể rời rạc. Kernel kích thước $3\times3$ và $5\times5$ với một tham số $\sigma$ cụ thể sẽ nhìn giống thế này:

<center><img src='https://github.com/leduckhai/My-Data-Container/blob/master/VietCS%20Blog/Nh%E1%BA%ADp%20m%C3%B4n%20Image%20filtering/Gaussian%20kernel.PNG?raw=true' width=300></center>
 Vì xác xuất phân bố nhiễu Gauss tập trung như hình chuông nên như hình trên ta thấy giá trị ở trung tâm bộ lọc sẽ mang trọng số lớn nhất, càng ra xa rìa thì trọng số càng giảm. Tổng các phần tử trong kernel bằng 1. Nếu tổng lớn hơn 1, giá trị trung bình của ảnh sau tương quan chéo sẽ tăng mạnh khiến ảnh bị lóa. Nếu tổng bé hơn 1, ảnh sẽ tối đi. 

Bây giờ ta bắt tay vào viết code luôn. Thư viện scikit-image có hỗ trợ hàm [skimage.filters.gaussian](https://scikit-image.org/docs/dev/api/skimage.filters.html#skimage.filters.gaussian) để lọc Gauss.

```python
from skimage.filters import gaussian
img_filt = gaussian(img,sigma=2.2, multichannel=True)
```



<center><img src='https://raw.githubusercontent.com/leduckhai/My-Data-Container/master/VietCS%20Blog/Nh%E1%BA%ADp%20m%C3%B4n%20Image%20filtering/Gaussian%20filter%20output.PNG' width=400></center>
<center>Kết quả thuật toán</center>
So sánh với bộ lọc trung bình, các bạn hãy để ý thấy rằng phần tóc, chân mày và đôi mắt của Hạ Thảo không bị "quá mờ" so với làn da và nền. Trong thị giác máy tính, viền (ví dụ biên giới giữa tóc và da) đóng vai trò quan trọng trong thuật toán nhận diện vật thể và tách ảnh. Vì vậy, việc loại bỏ nhiễu nhưng vẫn giữ lại viền được khuyến nghị. Đó cũng chính là ưu điểm của bộ lọc Gauss: **bảo tồn viền (edge preserving)** [10].

## <span style="color:blue">2.5. Bộ lọc trung vị (Median filter) </span>
**Bộ lọc trung vị (median filter)** được xếp vào loại **bộ lọc phi tuyến (non-linear filter)**. Bộ lọc trung vị hoạt động giống như bộ lọc trung bình, nhưng thay vì xuất ra giá trị trung bình, nó xuất ra trung vị của các phần tử trong cửa sổ mà nó đi qua [7]. Giả sử ta có một kernel kích thước $3\times3$:

<center><img src='https://raw.githubusercontent.com/leduckhai/My-Data-Container/master/VietCS%20Blog/Nh%E1%BA%ADp%20m%C3%B4n%20Image%20filtering/Median%20filter%20how%20it%20works.gif' width=400></center>
Các phần tử trong kernel là: 124, 126, 127, 120, 150, 125, 115, 119, 123. Trong đó trung vị là: 124. Vậy tại vị trí tâm của kernel, tức vị trí giá trị 150 sẽ xuất ra trung vị 124.

Bộ lọc trung vị loại bỏ nhiễu đốm và nhiễu muối tiêu tốt hơn bộ lọc trung bình và Gauss [8]. Ta hãy cùng thử một bài toán thực tế về xử lý ảnh y khoa nhé! Hàm tạo bộ lọc trung vị được dùng sẽ là [skimage.filters.median](https://scikit-image.org/docs/dev/api/skimage.filters.html#skimage.filters.median)

```python
from skimage.filters import median
from skimage.morphology import rectangle
kernel = rectangle(5,5) # Tạo kích thước kernel 5x5
img_filt = median(img_n,selem=kernel)
```



<center><img src='https://raw.githubusercontent.com/leduckhai/My-Data-Container/master/VietCS%20Blog/Nhập%20môn%20Image%20filtering/Median%20filter%20output.PNG' width=600></center>
<center>Kết quả thuật toán</center>
## <span style="color:blue">2.6. So khớp mẫu bằng bộ lọc (template matching with filters) </span>
Hãy tưởng tượng bạn muốn tìm người quen giữa rừng người mênh mông. **Template matching** sẽ giúp bạn trong trường hợp này.

Trước hết bạn phải tìm một bức ảnh chụp người ấy, gọi là **mẫu (template)**, để đối chiếu với ảnh gốc (tức ảnh chụp cả rừng người dưới đây. Ô màu xanh lá là ô xác định tìm thấy người quen bạn sau khi chạy thuật toán **so khớp mẫu**.

<center><img src='https://raw.githubusercontent.com/leduckhai/My-Data-Container/master/VietCS%20Blog/Nh%E1%BA%ADp%20m%C3%B4n%20Image%20filtering/Template%20matching%20example.PNG' width=500></center>
Từ công thức (2), ta thay $K[u,v]$ bằng $T[u,v]$, viết tắt cho template và rút gọn ký hiệu $\sum$:
$$
I[i,j] = \sum_{u,v=-\frac{k-1}{2}}^{\frac{k-1}{2}}T[u,v]I_0[i+u,j+v]
\tag{6}
$$

Bây giờ ta chuẩn hóa phương trình (6) bằng việc chia cho tích độ lệch chuẩn của ảnh và mẫu:
$$
I[i,j] = \frac{\sum\_{u,v=-\frac{k-1}{2}}^{\frac{k-1}{2}}T[u,v]I_0[i+u,j+v]}{\sqrt{\sum\_{u,v=-\frac{k-1}{2}}^{\frac{k-1}{2}}T[u,v]^2
\sum\_{u,v=-\frac{k-1}{2}}^{\frac{k-1}{2}}I_0[i+u,j+v]^2}}
\tag{7}
$$

Phương trình (7) được gọi là **tương quan chéo chuẩn hóa (normalized cross-correlation)**. $I[i,j]$ khi đó chỉ còn chạy từ 0 đến 1. Bây giờ ta hãy thử so sánh một chút giữa (6) và (7) để hiểu lý do tôi áp dụng (7) cho thuật toán. Khi trượt mẫu trên ảnh, nếu ảnh rơi vào vùng gam màu trắng (tức mỗi pixel có giá trị gần 255), kết quả sẽ đạt giá trị max theo phương trình (6). Và nếu ảnh rơi vào vùng gam màu đen (tức mỗi pixel có giá trị gần 0), kết quả sẽ đạt giá trị min. Điều đó thật khó để ta xác định được đâu là vùng ảnh mà mẫu "nên" rơi vào. Đối với phương trình (7) thì mọi chuyện không xảy ra như vậy. Với vùng gam màu đen, kết quả vẫn giữ giá trị min, nhưng nếu rơi vào vùng gam màu trắng, giá trị này sẽ bị "chế ngự" bởi tích độ lệch chuẩn dưới mẫu số khiến nó không thể max được. Giá trị lớn nhất xảy ra khi các giá trị pixel của mẫu xấp xỉ với vùng ảnh được xác định, tức khi mẫu số thấp nhất. 

<center><img src='https://raw.githubusercontent.com/leduckhai/My-Data-Container/master/VietCS%20Blog/Nh%E1%BA%ADp%20m%C3%B4n%20Image%20filtering/Template%20matching%20result.PNG'width=500></center>
Nhìn vào ảnh đã được so khớp mẫu, bạn có thấy chấm màu trắng trong vùng khoanh tròn không? Đó là điểm có độ sáng (**intensity**) lớn nhất và cũng là tâm của mẫu được xác định. Đó cũng là giá trị lớn nhất của phương trình (7).

Bây giờ ta thực hành luôn nhé! Ta sẽ sử dụng hàm [skimage.feature.match_template](https://scikit-image.org/docs/dev/api/skimage.feature.html#skimage.feature.match_template) 

```python
from skimage import data
from skimage.feature import match_template

image = data.coins() # Load ảnh các đồng xu từ dữ liệu có sẵn của scikit-image
coin = image[170:220, 75:130] # Cắt ảnh một đồng xu ra để làm mẫu

result = match_template(image, coin) # Khâu so khớp mẫu
# Xác định vị trí chấm sáng nhất
ij = np.unravel_index(np.argmax(result), result.shape) 
x, y = ij[::-1]
print('Vị trí chấm sáng nhất là: (i,j) = (%d,%d)' %(x,y))
```

```
Vị trí chấm sáng nhất là: (i,j) = (75,170)
```



<center><img src='https://raw.githubusercontent.com/leduckhai/My-Data-Container/master/VietCS%20Blog/Nh%E1%BA%ADp%20m%C3%B4n%20Image%20filtering/Template%20matching%20output.PNG'width=400></center>
<center>Kết quả thuật toán</center>
Trong so khớp mẫu, tôi phải nhắc đến 2 điều. Điều thứ nhất: ngoài phương trình **normalized cross-correlation (NCC)**, người ta còn dùng phương trình **tổng các hiệu số tuyệt đối (sum of absolute differences)** [9], tôi sẽ đề cập sau này. Điều thứ hai: trong các bài toán thực tế, bạn sẽ không có một mẫu giống y chang trong ảnh gốc. Mẫu bạn có sẽ như thế này:

<center><img src='https://raw.githubusercontent.com/leduckhai/My-Data-Container/master/VietCS%20Blog/Nh%E1%BA%ADp%20m%C3%B4n%20Image%20filtering/Non%20identical%20template%20matching.PNG'width=350></center>
Tuy hai chiếc xe khác nhau về hình dáng, chủng loại, nhưng chúng có cùng một số đặc điểm chung như khung xe màu trắng và bánh xe màu đen. Vì thế thuật toán nói trên vẫn hoạt động hiệu quả. Trong một trường hợp khác, hai chiếc xe có các vùng sáng tối, góc chụp hoàn toàn khác nhau thì thuật toán trên sẽ không nhận diện được. Ta cần cải thiện thêm thuật toán này, cụ thể là phải ứng dụng các **mạng thần kinh tích chập (convolution neural network)**. Ta cũng sẽ bàn đến cái này sau.

## <span style="color:blue">3. Tóm tắt kiến thức </span>
Như vậy bạn đã hiểu được phần nào cơ chế của các bộ lọc và ứng dụng của chúng, tôi xin tóm tắt lại kiến thức làm hành trang cho bạn sử dụng sau này.

<u> 1) Bộ lọc tương quan (Correlation filter) </u>

Phương trình tương quan chéo giữa ảnh và kernel (dùng để áp kernel vào ảnh tạo nên bộ lọc ảnh):
$$
I[i,j] = \sum\_{u=-\frac{k-1}{2}}^{\frac{k-1}{2}}\sum\_{v=-\frac{k-1}{2}}^{\frac{k-1}{2}}K[u,v]I_0[i+u,j+v]
\tag{2}
$$

trong đó:

$I[i,j]$: ma trận ảnh đầu ra

$I_0[i,j]$: ma trận ảnh gốc

$K[u,v]$: ma trận kernel

$k$: kích thước kernel

Hàm tính tương quan chéo ảnh với kernel: [scipy.ndimage.correlate](https://docs.scipy.org/doc/scipy/reference/generated/scipy.ndimage.correlate.html#scipy.ndimage.correlate) 

<u> 2) Nhiễu Gauss (Gaussian noise) </u>

Hàm số Gauss trong không gian 2D:
$$
f(x,y) = f(x)f(y) = \frac{1}{2\pi\sigma^2}e^{-\frac{x^2+y^2}{2\sigma^2}}
\tag{4}
$$

Nhiễu Gauss được tạo ra để thử bộ lọc. Hàm tạo nhiễu Gauss: [skimage.util.random_noise](https://scikit-image.org/docs/dev/api/skimage.util.html#skimage.util.random_noise) 

<u> 3) Bộ lọc trung bình (Mean filter) </u>

Đầu ra của bộ lọc trung bình là giá trị trung bình của tổng các phần tử nằm trong kernel nơi nó đi qua. Hàm áp bộ lọc trung bình vào ảnh: [skimage.filters.rank.mean](https://scikit-image.org/docs/dev/api/skimage.filters.rank.html#skimage.filters.rank.mean) 

<u> 4) Bộ lọc Gauss (Gaussian filter) </u>

Ưu điểm của bộ lọc Gauss: bảo tồn viền (edge preserving). Hàm áp bộ lọc Gauss vào ảnh: [skimage.filters.gaussian](https://scikit-image.org/docs/dev/api/skimage.filters.html#skimage.filters.gaussian) 

<u> 5) Bộ lọc trung vị (Median filter) </u>

Bộ lọc trung vị loại bỏ nhiễu đốm và nhiễu muối tiêu tốt. Hàm áp bộ lọc trung vị vào ảnh: [skimage.filters.median](https://scikit-image.org/docs/dev/api/skimage.filters.html#skimage.filters.median) 

<u> 6) So khớp mẫu (Template matching) </u>

Trong so khớp mẫu, điểm có độ sáng (intensity) lớn nhất là tâm của mẫu được xác định. Hàm so ảnh mẫu xem khớp với ảnh gốc tại vị trí nào: [skimage.feature.match_template](https://scikit-image.org/docs/dev/api/skimage.feature.html#skimage.feature.match_template) 

<u> 7) Phân loại bộ lọc theo ứng dụng: </u>

Tăng cường ảnh (**Image enhancement**): bộ lọc trung bình, trung vị, Gauss,...

Nhận diện viền (**Edge detection**): bộ lọc Sobel, Prewitt, Roberts cross, Deriche,...

Tách ảnh (**Image segmentation**): bộ lọc Gabor, Log Gabor,...

Nhận diện điểm nhỏ (**Blob detection**): bộ lọc Laplacian of Gaussian (LoG), ...



## <span style="color:red">Nguồn tham khảo </span>

Nguồn ảnh:

1. [Wikipedia](https://vi.wikipedia.org/wiki/Trang_Ch%C3%ADnh)
2. [Wiki Uni Göttingen](http://wiki.awf.forst.uni-goettingen.de/)
3. [Stackoverflow](https://stackoverflow.com/)
4. [XRDS: The ACM Magazine for Students]( https://blog.xrds.acm.org/ )
5. [University of Edinburg: School of Informatics]( https://homepages.inf.ed.ac.uk/rbf/HIPR2/median.htm)
6. [Deeplizard](https://deeplizard.com/)
7. [Udacity](https://udacity.com/)
8. [Scikit-image: Template matching example codes]( https://scikit-image.org/docs/dev/auto_examples/features_detection/plot_template.html#sphx-glr-auto-examples-features-detection-plot-template-py )

Nguồn tài liệu:

[1]: [Wiki: Kernel (image processing)]( https://en.wikipedia.org/wiki/Kernel_(image_processing))

[2]: [Wiki: Speckle (interference)]( https://en.wikipedia.org/wiki/Speckle_(interference) ) 

[3]: [Wiki: Salt and pepper](https://en.wikipedia.org/wiki/Image_noise#Salt-and-pepper_noise) 

[4]: [Wiki: Gaussian noise](https://en.wikipedia.org/wiki/Gaussian_noise)

[5]: [Wiki Uni Göttingen: Arithmetic mean filter](http://wiki.awf.forst.uni-goettingen.de/wiki/index.php/Arithmetic_mean_filter) 

[6]: [SciPy: Padding](https://docs.scipy.org/doc/numpy/reference/generated/numpy.pad.html) 

[7], [8]: [ScienceDirect: Median filter](https://www.sciencedirect.com/topics/computer-science/median-filter) 

[9]: [Wiki: Template matching - SAD](https://en.wikipedia.org/wiki/Template_matching#Template-based_matching_explained_using_cross_correlation_or_sum_of_absolute_differences)

[10]: [Wiki: Gaussian blur](https://en.wikipedia.org/wiki/Gaussian_blur)

(1), (2), (5): [Udacity: Introduction to Computer Vision]( https://classroom.udacity.com/courses/ud810 )

(3), (4): [Wiki: Gaussian filter]( https://en.wikipedia.org/wiki/Gaussian_filter )

(6), (7): [OpenCV: Template matching]( https://docs.opencv.org/2.4/modules/imgproc/doc/object_detection.html?highlight=matchtemplate#matchtemplate)
