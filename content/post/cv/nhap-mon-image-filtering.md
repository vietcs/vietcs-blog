---
title: "Nhap Mon Image Filtering"
date: 2019-10-25T22:36:42+07:00
draft: false
authors: ["khaild"]
categories: 
  - computer vision
tags:
  - introductory
  - vision
slug: nhap-mon-image-filtering
---

## 1. Giới thiệu
Camera 360 là kẻ thù của cánh đàn ông, vì nhờ nó mà các chị em phụ nữ hóa lung linh xinh đẹp hơn. Một trong những công cụ thần thánh trong việc làm đẹp thời 4.0 chính là **bộ lọc (filter)**. Bài viết hôm nay không mang tính chất cà khịa việc này, mục đích của tui tác giả chỉ muốn cho các bạn hiểu rõ nguyên lý, ứng dụng và cách sử dụng filter.

Filter (hay tiếng Việt là bộ lọc) được dùng rất nhiều trong thị giác máy tính, nhằm loại bỏ nhiễu (**denoising**), làm mờ ảnh (**blurring**), làm mượt (**smoothing**), cân bằng các giá trị pixel trong ảnh (**equalization**), nhận diện viền (**edge detection**), trích xuất đặc trưng (**feature extraction**), so khớp mẫu (**template matching**),... thông qua tương quan chéo hoặc tích chập giữa **kernel** với ảnh hoặc ngược lại.

**Gợi ý: Độc giả không có nhu cầu hiểu mấu chốt các thuật toán có thể kéo thẳng xuống phần 3. Tóm tắt kiến thức**

## 2.1. Bộ lọc tương quan (Correlation filter)
Để áp dụng một bộ lọc vào ảnh, ta cần **kernel**. Kernel có thể được hiểu như lõi, là một ma trận mang tính chất đặc trưng quyết định đầu ra của ảnh thông qua thuật toán lọc ảnh. Ở bài tiếp theo, chúng ta sẽ bàn kỹ về tích chập ảnh với kernel và so sánh với tương quan chéo.

Giả sử một cửa sổ con có kích thước $k\times k$, với *k* là số lẻ, ta tính được **tương quan (correlation)** dựa trên công thức:

<center>$I[i,j] = \frac{1}{k^2}\sum_{u=-(k-1)/2}^{(k-1)/2}\sum_{v=-(k-1)/2}^{(k-1)/2}I_0[i+u,j+v]$ (1) 

Để thực hiện công thức (1), ta lần lượt cho *u* và *v* chạy từ *$-\frac{k-1}{2}$* đến *$\frac{k-1}{2}$* rồi cộng các kết quả của từng $I_0[i+u,j+v]$ lại. Tham số $\frac{1}{k^2}$ được gọi là **trọng số mặc định (uniform weights)**.

Bây giờ hãy thử thay các trọng số riêng rẻ bằng một ma trận chứa nhiều trọng số nào! Ta gọi ma trận đó là $K$ nhé!

<center>$I[i,j] = \sum_{u=-(k-1)/2}^{(k-1)/2}\sum_{v=-(k-1)/2}^{(k-1)/2}K[u,v]I_0[i+u,j+v]$ (2)

Công thức (2) được biết đến là **tương quan chéo (cross-correlation)**. Nếu xem $K[u,v]$ là một **kernel** (kernel vốn là ma trận chứa các trọng số) và $I_0[i+u,j+v]$ là ảnh gốc thì ta có ngay và luôn công thức tương quan chéo kernel với ảnh. Thuật toán này hoạt động rất đơn giản: cho lần lượt *u*, *v* chạy từ *$-\frac{k-1}{2}$* đến *$\frac{k-1}{2}$*, ta nhân hệ số tại vị trí $[u,v]$ của ma trận $K$ với hệ số tại vị trí $[i+u,j+v]$ của ma trận ảnh gốc $I_0$ rồi cộng các kết quả lại với nhau. Ê zét!

Trong thư viện Scipy có sẵn hàm [scipy.ndimage.correlate](https://docs.scipy.org/doc/scipy/reference/generated/scipy.ndimage.correlate.html#scipy.ndimage.correlate), bạn đọc có thể tham khảo đường link để áp dụng luôn sau này.

## 2.2. Nhiễu Gauss (Gaussian noise)
Trong thực tế có 3 loại nhiễu thường gặp nhất: **nhiễu đốm (speckle noise)**, **nhiễu muối tiêu (pepper-and-salt noise)** và **nhiễu Gauss (Gaussian noise)**. Nhiễu đốm xuất hiện khi các sóng tới (sóng ánh sáng, siêu âm, điện từ,...) giao thoa với nhau tạo nên các điểm cực đại hoặc cực tiểu. Nhiễu muối tiêu xuất hiện do sự gián đoạn việc truyền/nhận tín hiệu giữa nguồn phát và cảm biến. **Nhiễu Gauss**, được đặt theo tên nhà toán học người Đức Gauß (hoặc Gauss), là một loại nhiễu có **mật độ xác suất (probability density)** tương ứng với **phân phối chuẩn (normal distribution)**. Hay nói cách khác, biểu đồ mà nhiễu Gauss tạo nên cũng có hình dạng cái chuông.
<center><img src="https://raw.githubusercontent.com/leduckhai/VietCS-LeDucKhai/master/Representation%20Data/Gaussian%20distribution.png" width=400>

<center> Hình 1: Hình dạng cái chuông của **phân phối Gauss (Gaussian distribution)**

Công thức mật độ xác suất Gauss:

<center>$f(x) = \frac{1}{\sigma\sqrt{2\pi}}e^{-\frac{(x-\mu)^2}{2\sigma^2}}$ (3)
    
trong đó:

$\sigma$: độ lệch chuẩn (standard deviation)

$\mu$: số trung bình

$x$: vị trí tọa độ

Số trung bình $\mu$ thường mặc định là 0. Vì thế tham số quyết định hình dạng của biểu đồ Gauss là độ lệch chuẩn $\sigma$. Suy rộng ra trong không gian 2D, hàm số Gauss là tích của 2 hàm số Gauss trên trục x và y, viết gọn sẽ là:
<center>$f(x,y) = f(x)f(y) = \frac{1}{2\pi\sigma^2}e^{-\frac{x^2+y^2}{2\sigma^2}}$ (4) 

<img src='https://raw.githubusercontent.com/leduckhai/VietCS-LeDucKhai/master/Representation%20Data/2D%20Gauss.png' width = 400>

<center> Hình 2: Phân bố Gauss trong không gian 2D 
    
Gọi $I_{noisy}$, $I_{0}$, $A_{noise}$ lần lượt là ma trận ảnh bị nhiễu Gauss, ma trận ảnh gốc và ma trận nhiễu Gauss thì ta sẽ có công thức:

<center>$I_{noisy}{[i,j]} = I_{0}{[i,j]} + A_{noise}{[i,j]}$ (5)

Việc tạo nhiễu Gauss thường dùng để thử bộ lọc, vì thế đây là một thuật toán tuy đơn giản nhưng rất quan trọng sau này. Với tinh thần ứng dụng thực tiễn, ta dùng hẵn hàm [skimage.util.random_noise](https://scikit-image.org/docs/dev/api/skimage.util.html#skimage.util.random_noise) có sẵn luôn nha.


```python
# Load các thư viện cần thiết
import numpy as np
from matplotlib import pyplot as plt
from skimage.util import random_noise

image = plt.imread('Data/CT_Brain.jpg') # Đọc ảnh đầu vào
img = random_noise(image, var=0.05) # Hàm random_noise dùng để tạo nhiễu Gauss
plt.figure(figsize=(10,5)) # Tạo cửa sổ hiển thị
plt.subplot(121); plt.imshow(image, cmap='gray'); plt.title('Original') # Hiển thị ảnh gốc
plt.subplot(122); plt.imshow(img, cmap='gray'); plt.title('Noisy image') # Hiển thị ảnh bị nhiễu
```




    <matplotlib.text.Text at 0x7efddc333828>




![png](output_3_1.png)


## 2.3. Bộ lọc trung bình (Mean filter)
Bất kỳ ai cũng đã từng bị mụn đúng không? Hãy tưởng tượng một sớm thức dậy thấy khuôn mặt ta chẳng khác gì một bãi mìn. Nhưng rồi thằng/con ghệ nó nhắn tin bảo selfie cho nó một tấm để đỡ nhớ nhung. Trời má ơi luôn, làm sao có thể che đi những vết mụn đáng ghét này nhỉ? À phải rồi, filter thần thánh có thể buff +5 điểm nhan sắc mà. Vậy thì chúng ta cùng tạo filter với python thử. Bắt đầu từ bộ lọc trung bình nha!

Kernel của bộ lọc trung bình là một ma trận kích thước $k\times k$ ($k$ là số lẻ) với các phần tử bằng 1. Đầu ra của bộ lọc sẽ là giá trị trung bình của tổng các phần tử nằm trong kernel nơi nó đi qua. Hại não quá hả? Thôi giải thích bằng hình ảnh cho dễ hiểu nha!

<img src='https://raw.githubusercontent.com/leduckhai/VietCS-LeDucKhai/master/Representation%20Data/How%20mean%20filter%20works.png' width = 400>

Các ma trận ở hàng trên là ma trận ảnh gốc, hàng dưới là ảnh xuất ra và vùng xám là kernel. Ta lấy ví dụ hình bên trái. Theo quy tắc tương quan chéo kernel với ảnh ở (2) thì ta có:

$(1\times10)+(1\times41)+(1\times9)+(1\times232)+(1\times186)+(1\times79)+(1\times1)+(1\times43)+(1\times56)=657$

Sau đó ta chia trung bình giá trị này, tức chia 9:

$\frac{657}{9}=73$

Đầu ra của kernel là một giá trị nằm ở tâm kernel đó. Bạn hãy để ý vị trí của số 186 màu đỏ ở trên và 73 ở dưới. Cứ thế, ta cho kernel chạy qua tất cả vị trí trong ảnh thì ta sẽ được một ảnh lọc hoàn chỉnh.

Có bạn thắc mắc: vậy đối với giá trị 10, tức vị trí $[1,1]$ của ma trận ảnh thì tính toán thế nào? Quả thật tại giá trị 10, kernel bị "lòi trĩ" ra ngoài nên không thể áp dụng nhân tương quan. Để giải quyết việc này, ta sẽ **đệm (padding)** ma trận ảnh. Hình dưới ta gọi kỹ thuật này là **zero padding**, tức đệm số 0.

<img src='https://raw.githubusercontent.com/leduckhai/VietCS-LeDucKhai/master/Representation%20Data/Zero%20padding.png' width = 400>

Ảnh gốc và ảnh xuất chỉ có kích thước $4\times4$ thôi nhưng khi ta "đệm" số 0 vào các hàng và cột thì ta sẽ có ảnh gốc kích thước $6\times6$. Nhìn hình thì đủ hiểu rồi ha! Thật ra còn nhiều kỹ thuật đệm khác như **đệm đối xứng (symmetric padding)**, **đệm phản chiếu (reflect padding)**, **đệm hằng số (constant padding)**, **đệm rìa (edge padding)** nhưng tui không tiện diễn giải vì chúng không tạo quá nhiều sự khác biệt với nhau khi xuất ảnh.

Hạ Thảo đang "đèn đỏ" nên nổi mụn rất nhiều. Chúng ta cùng giúp cô ấy "cà mụn" để tự tin hơn trước bạn trai nhé!


```python
# Load các thư viện cần thiết
import numpy as np
from matplotlib import pyplot as plt
from scipy import ndimage

# Đọc ảnh đầu vào và hiển thị
img = plt.imread('Data/HaThao_1.jpg') # Đọc ảnh
plt.figure(figsize=(10,5)) # Tạo cửa sổ hiển thị
plt.subplot(121), plt.imshow(img),plt.title('Original') # Hiển thị ảnh gốc

# Tạo hàm mean_filter 
def mean_filter(img,n):
    kernel = np.ones((n,n,1))/n**2 # Tạo ma trận kernel
    img_filt = ndimage.correlate(img,kernel) # Tương quan chéo kernel với ảnh
    return img_filt # xuất ra ảnh đã lọc

img_filt = mean_filter(img,9) # Gọi hàm số mean_filter và thực hiện tính toán
plt.subplot(122), plt.imshow(img_filt),plt.title('Mean filter') # Hiển thị ảnh xuất
```




    (([], <a list of 0 Text xticklabel objects>),
     ([], <a list of 0 Text yticklabel objects>))




![png](output_5_1.png)


Chu choa mẹ ơi, trong ảnh gốc, dễ thấy da mặt Hạ Thảo lấm tấm nhiều nốt mụn đỏ (mà thực ra do tui dùng Paint) nhưng sau khi qua bộ lọc thì chúng đã mờ đi rất nhiều. Trong thực tế, các ứng dụng selfie và photoshop dùng bộ lọc trung bình dưới dạng vùng khoanh nhỏ thay vì áp dụng vào toàn bộ hình như trên.

## 2.4. Bộ lọc Gauss (Gaussian filter)
Bộ lọc Gauss được dùng nhiều hơn bộ lọc trung bình vì những ưu điểm vượt trội của nó. Bộ lọc Gauss tuân thủ phân bố ... Gauss nhưng dưới thể rời rạc. Một kernel kích thước $(5\times5)$ với một tham số $\sigma$ cụ thể sẽ nhìn giống thế này:
<img src='https://raw.githubusercontent.com/leduckhai/Computer-Vision-Tutorial/master/Representation%20Data/Gaussian%20Kernel%205x5.gif' width=200>

Bây giờ ta bắt tay vào viết code luôn. (Xin lỗi, vì sự yếu kém của bản thân mà tui chỉ tìm thấy duy nhất thư viện astropy có thể tạo ra Gaussian 2D kernel để từ đó tương quan chéo với ảnh gốc như phần 2.3. Tuy nhiên thư viện này không có sẵn trên Anaconda, sẽ rất khó khăn cho các bạn tân binh tự cài đặt. Vì thế tui xin mạn phép dùng thẳng hàm [skimage.filters.gaussian](https://scikit-image.org/docs/dev/api/skimage.filters.html#skimage.filters.gaussian) luôn)


```python
import numpy as np
from matplotlib import pyplot as plt
from skimage.filters import gaussian
import cv2 

img = plt.imread('Data/HaThao_1.jpg')
plt.figure(figsize=(10,5))
plt.subplot(121), plt.imshow(img), plt.title('Original')

img_filt = gaussian(img,sigma=2.2, multichannel=True)
#img_filt = cv2.GaussianBlur(img, ksize=(7,7),sigmaX=2.2) # OpenCV hỗ trợ tùy chọn kích thước kernel, Scikit-image thì không
plt.subplot(122), plt.imshow(img_filt), plt.title('Gaussian filter')
```




    (<matplotlib.axes._subplots.AxesSubplot at 0x7efd50954940>,
     <matplotlib.image.AxesImage at 0x7efd57d650f0>,
     <matplotlib.text.Text at 0x7efd5090a588>)




![png](output_8_1.png)


So sánh với bộ lọc trung bình, các bạn hãy để ý thấy rằng phần tóc, chân mày và con ngươi của Hạ Thảo không bị "quá mờ" so với làn da và nền. Trong thị giác máy tính, viền (ví dụ biên giới giữa tóc và da) đóng vai trò quan trọng trong thuật toán nhận diện vật thể và tách ảnh. Vì vậy, việc loại bỏ nhiễu nhưng vẫn giữ lại viền được khuyến nghị. Đó cũng chính là ưu điểm của bộ lọc Gauss: bảo tồn viền (**edge preserving**).

## 2.5. Bộ lọc trung vị (Median filter)
Bộ lọc trung vị hoạt động giống như bộ lọc trung bình, nhưng thay vì xuất ra giá trị trung bình, nó xuất ra trung vị của các phần tử trong cửa sổ mà nó đi qua. Giả sử ta có một kernel kích thước $3\times3$:

<img src='https://raw.githubusercontent.com/leduckhai/VietCS-LeDucKhai/master/Representation%20Data/Median%20Filter%20Kernel.png' width=400>

Các phần tử trong kernel là: 124, 126, 127, 120, 150, 125, 115, 119, 123. Trong đó trung vị là: 124. Vậy tại vị trí tâm của kernel, tức vị trí giá trị 150 sẽ xuất ra trung vị 124.

Bộ lọc trung vị loại bỏ nhiễu đốm và nhiễu muối tiêu tốt hơn bộ lọc trung bình và Gauss. Ta hãy cùng thử một bài toán thực tế về xử lý ảnh y khoa nhé! Hàm tạo bộ lọc trung vị được dùng sẽ là [skimage.filters.median](https://scikit-image.org/docs/dev/api/skimage.filters.html#skimage.filters.median)


```python
from matplotlib import pyplot as plt
from skimage.color import rgb2gray
from skimage.util import random_noise
from skimage.filters import median
from skimage.morphology import rectangle

img = plt.imread('Data/CT_Brain.jpg')
img = rgb2gray(img)
plt.figure(figsize=(10,5))
plt.subplot(131), plt.imshow(img, cmap='gray'), plt.title('Original')

img_n = random_noise(img, var=0.01)
plt.subplot(132), plt.imshow(img_n, cmap='gray'), plt.title('Noisy image')
kernel = rectangle(5,5) # Tạo kích thước kernel 5x5
img_filt = median(img_n,selem=kernel)
plt.subplot(133), plt.imshow(img_filt, cmap='gray'), plt.title('Median filter')
```

    /opt/conda/lib/python3.5/site-packages/skimage/util/dtype.py:135: UserWarning: Possible precision loss when converting from float64 to uint8
      .format(dtypeobj_in, dtypeobj_out))





    (<matplotlib.axes._subplots.AxesSubplot at 0x7efd416430b8>,
     <matplotlib.image.AxesImage at 0x7efd415df198>,
     <matplotlib.text.Text at 0x7efd415bd3c8>)




![png](output_11_2.png)


## 2.6. So khớp mẫu bằng bộ lọc (template matching with filters)
Hãy tưởng tượng bạn lạc mất người yêu sau khi cô ấy nhờ bạn đi mua kem. Trước mắt bạn là một rừng người và mắt bạn không thể suy xét chọn lựa từng cá nhân để xem đó có phải ghệ mình không. **Template matching** sẽ giúp bạn trong trường hợp này.

Trước hết bạn phải tìm một bức ảnh chụp người yêu của bạn, gọi là **mẫu (template)**, để đối chiếu với ảnh gốc (tức ảnh chụp cả rừng người dưới đây. Ô màu xanh lá là ô xác định tìm thấy người yêu bạn sau khi chạy thuật toán **so khớp mẫu**.
<img src='https://raw.githubusercontent.com/leduckhai/VietCS-LeDucKhai/master/Representation%20Data/Finding%20Template.PNG' width=500>

Từ công thức (2), ta thay $K[u,v]$ bằng $T[u,v]$, viết tắt cho template và rút gọn ký hiệu $\sum$:
<center> $I[i,j] = \sum_{u,v=-(k-1)/2}^{(k-1)/2}T[u,v]I_0[i+u,j+v]$ (6)

Bây giờ ta chuẩn hóa phương trình (6):
<center> $I[i,j] = \frac{\sum_{u,v=-(k-1)/2}^{(k-1)/2}T[u,v]I_0[i+u,j+v]}{\sqrt{\sum_{u,v=-(k-1)/2}^{(k-1)/2}T[u,v]^2
\sum_{u,v=-(k-1)/2}^{(k-1)/2}I_0[i+u,j+v]^2}}$ (7)
    
Phương trình (7) được gọi là **tương quan chéo chuẩn hóa (normalized cross-correlation)**. Áp dụng (7) ta sẽ ra kết quả như sau:

<img src='https://raw.githubusercontent.com/leduckhai/VietCS-LeDucKhai/master/Representation%20Data/Template%20matching.PNG' width=500>

Nhìn vào ảnh đã được so khớp mẫu, bạn có thấy chấm màu trắng trong vùng khoanh tròn không? Đó là điểm có độ sáng (**intensity**) lớn nhất và cũng là tâm của mẫu được xác định. Phương trình (7) đạt giá trị lớn nhất chỉ khi giá trị các phần tử của mẫu đúng bằng cửa sổ con.

Bây giờ ta thực hành luôn nhé! Ta sẽ sử dụng hàm [skimage.feature.match_template](https://scikit-image.org/docs/dev/api/skimage.feature.html#skimage.feature.match_template) 


```python
import numpy as np
import matplotlib.pyplot as plt
from skimage import data
from skimage.feature import match_template

image = data.coins() # Load ảnh các đồng xu từ dữ liệu có sẵn của scikit-image
coin = image[170:220, 75:130] # Cắt ảnh một đồng xu ra để làm mẫu

result = match_template(image, coin) # Khâu so khớp mẫu
ij = np.unravel_index(np.argmax(result), result.shape) # Xác định vị trí chấm sáng nhất
x, y = ij[::-1]

# Hiển thị các ảnh và đánh dấu mẫu đã được khớp
fig = plt.figure(figsize=(10, 5))
ax1 = plt.subplot(1, 3, 1)
ax2 = plt.subplot(1, 3, 2)
ax3 = plt.subplot(1, 3, 3, sharex=ax2, sharey=ax2)

ax1.imshow(coin, cmap=plt.cm.gray)
ax1.set_axis_off()
ax1.set_title('template')

ax2.imshow(image, cmap=plt.cm.gray)
ax2.set_axis_off()
ax2.set_title('image')

hcoin, wcoin = coin.shape
rect = plt.Rectangle((x, y), wcoin, hcoin, edgecolor='r', facecolor='none')
ax2.add_patch(rect)

ax3.imshow(result)
ax3.set_axis_off()
ax3.set_title('`match_template`\nresult')

ax3.autoscale(False)
ax3.plot(x, y, 'o', markeredgecolor='r', markerfacecolor='none', markersize=10)

# In kết quả
print('Vị trí chấm sáng nhất là: (i,j) = (%d,%d)' %(x,y))
```

    /opt/conda/lib/python3.5/site-packages/scipy/signal/signaltools.py:375: FutureWarning: Conversion of the second argument of issubdtype from `complex` to `np.complexfloating` is deprecated. In future, it will be treated as `np.complex128 == np.dtype(complex).type`.
      complex_result = (np.issubdtype(in1.dtype, complex) or
    /opt/conda/lib/python3.5/site-packages/scipy/signal/signaltools.py:376: FutureWarning: Conversion of the second argument of issubdtype from `complex` to `np.complexfloating` is deprecated. In future, it will be treated as `np.complex128 == np.dtype(complex).type`.
      np.issubdtype(in2.dtype, complex))


    Vị trí chấm sáng nhất là: (i,j) = (75,170)



![png](output_13_2.png)


Trong so khớp mẫu, tui phải nhắc đến 2 điều. Điều thứ nhất: ngoài phương trình **normalized cross-correlation (NCC)**, người ta còn dùng phương trình **tổng các hiệu số tuyệt đối (sum of absolute differences)**, vì e các bạn "tẩu hỏa nhập ma" nên tui sẽ đề cập đến khi VietCS đủ lớn. Điều thứ hai: trong các bài toán thực tế, bạn sẽ không có một mẫu giống y chang trong ảnh gốc đâu. Mẫu bạn có sẽ như thế này:
<img src='https://raw.githubusercontent.com/leduckhai/VietCS-LeDucKhai/master/Representation%20Data/non%20identical%20template%20matching.PNG' width=400>
Rõ ràng vật thể trong mẫu và ảnh gốc khác nhau mặc dù chúng đều là xe. Vì thế thuật toán so khớp mẫu cần phải cải tiến thêm, cụ thể là phải ứng dụng các **mạng thần kinh tích chập (convolution neural network)**. Ta cũng sẽ bàn đến cái này sau.

## 3. Tóm tắt kiến thức
<u> 1) Bộ lọc tương quan (Correlation filter) </u>

Phương trình tương quan chéo giữa ảnh và kernel:
<center>$I[i,j] = \sum_{u=-(k-1)/2}^{(k-1)/2}\sum_{v=-(k-1)/2}^{(k-1)/2}K[u,v]I_0[i+u,j+v]$ (2)

trong đó:

$I[i,j]$: ma trận ảnh đầu ra

$I_0[i,j]$: ma trận ảnh gốc

$K[u,v]$: ma trận kernel

$k$: kích thước kernel

Sử dụng [scipy.ndimage.correlate](https://docs.scipy.org/doc/scipy/reference/generated/scipy.ndimage.correlate.html#scipy.ndimage.correlate) để tính tương quan chéo ảnh với kernel

<u> 2) Nhiễu Gauss (Gaussian noise) </u>

Hàm số Gauss trong không gian 2D:
<center>$f(x,y) = f(x)f(y) = \frac{1}{2\pi\sigma^2}e^{-\frac{x^2+y^2}{2\sigma^2}}$ (4)

Sử dụng [skimage.util.random_noise](https://scikit-image.org/docs/dev/api/skimage.util.html#skimage.util.random_noise) để tạo nhiễu Gauss

<u> 3) Bộ lọc trung bình (Mean filter) </u>

Sử dụng [skimage.filters.rank.mean](https://scikit-image.org/docs/dev/api/skimage.filters.rank.html#skimage.filters.rank.mean) để áp bộ lọc trung bình vào ảnh

<u> 4) Bộ lọc Gauss (Gaussian filter) </u>

Sử dụng [skimage.filters.gaussian](https://scikit-image.org/docs/dev/api/skimage.filters.html#skimage.filters.gaussian) để áp bộ lọc Gauss vào ảnh

<u> 5) Bộ lọc trung vị (Median filter) </u>

Sử dụng [skimage.filters.median](https://scikit-image.org/docs/dev/api/skimage.filters.html#skimage.filters.median) áp bộ lọc trung vị vào ảnh

<u> 6) So khớp mẫu (Template matching) </u>

Sử dụng [skimage.feature.match_template](https://scikit-image.org/docs/dev/api/skimage.feature.html#skimage.feature.match_template) để so ảnh mẫu xem khớp với ảnh gốc tại vị trí nào

<u> 7) Phân loại bộ lọc theo ứng dụng: </u>

Tăng cường ảnh (**Image enhancement**): bộ lọc trung bình, trung vị, Gauss,...

Nhận diện viền (**Edge detection**): bộ lọc Sobel, Prewitt, Roberts cross, Deriche,...

Tách ảnh (**Image segmentation**): bộ lọc Gabor, Log Gabor,...

Nhận diện điểm nhỏ (**Blob detection**): bộ lọc Laplacian of Gaussian (LoG), ...

## 4. Bài tập
* Hãy thử viết chương trình "bộ lọc trung bình" bằng Numpy
* Hãy thử tạo ma trận nhiễu Gauss trong không gian 2D bằng Numpy
* Hãy thử viết chương trình "so khớp mẫu" bằng Numpy

**Gợi ý: Độc giả có thể tìm code trên mạng, miễn là bạn hiểu nguyên lý hoạt động của nó**

## 5. Lời ngỏ
Nhân loại hiện nay có 4 cấp bậc công nghệ: sao chép (copy), cải tiến (upgrade), sáng tạo (innovate) và sáng tạo đổi mới (radical innovate). Phương châm viết của tui là hướng bạn đọc đến mức độ *cải tiến*, tức có khả năng tinh chỉnh các thuật toán có sẵn sao cho phù hợp với từng loại yêu cầu công nghiệp. Đối với các thuật toán mà tui "nghĩ" không cần phải tinh chỉnh thì tui sẽ chú trọng nguyên lý làm việc của nó và cách sử dụng luôn các hàm trong Skimage mà bỏ qua bước viết code diễn giải chi tiết (gọi là implement from scratch). Tất nhiên điều này sẽ không áp dụng với các thuật toán có nhiều biến thể đòi hỏi các bạn cần có code mẫu để phát triển các biến thể gần tương tự.

Tui rất trông chờ ý kiến từ các bạn đọc nhằm cải thiện các bài viết và đem đến nhiều kiến thức cũng như cảm xúc cho các bạn hơn. Hãy comment tẹt ga ở dưới nhé!

## Nguồn tham khảo:
1. [Wikipedia](https://vi.wikipedia.org/wiki/Trang_Ch%C3%ADnh)
2. [Wiki Uni Göttingen](http://wiki.awf.forst.uni-goettingen.de/)
3. [Stackoverflow](https://stackoverflow.com/)
4. [Deeplizard](https://deeplizard.com/)
5. [Udacity](https://udacity.com/)


```python

```