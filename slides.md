---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background:
# apply any windi css classes to the current slide
class: 'text-center'
position: 'center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: true
---

# Nhập môn mô hình hóa thống kê
## Nhóm 2
## Chương 11
Thành viên: - Nguyễn Chí Thanh
            - Đoàn Đại Thanh Long
            - Nguyễn Hữu Tuấn Nghĩa
            - Nguyễn Mạnh Linh

<style>
h1 {
  background-color: #2B90B6;
  font-size: 12px;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

---

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

# MỤC LỤC

## 11.1 Giới thiệu và tổng quan
## 11.2 Mô hình hóa dữ liệu liên tục dương
## 11.3 Phân phối Gamma
## 11.4 Phân phối Inverse Gaussian
## 11.5 Link function
## 11.6 Ước lượng tham số phân tán
## 11.7 Case Study
## 11.8 Các hàm R liên quan
## 11.9 Tổng kết


<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>


---

# 11.1 Giới thiệu và tổng quan

| Các đề mục | Người trình bày|
|-----|-----|
|11.2 giới thiệu quá trình mô hình hóa dữ liệu liên tục dương| Thanh|
|11.3 bàn luận về phân phối Gamma|Thanh|
|11.4 bàn luận về phân phối Inverse Gaussian|Thanh|
|11.5 Lựa chọn link function cho các phân phối trên|Linh|
|11.6 Ước lượng $\phi$ của các phân phối|Linh
|11.7 trình bày một số Case Study|Long (Case Study 1) - Nghĩa (Case Study 2)|
|11.8 tổng hợp một số hàm trong R để fit GLM với hai phân phối Gamma và Inverse Gaussian|Nghĩa|
|11.9 Tổng kết| Nghĩa|

---

# 11.1 Giới thiệu và tổng quan

- Mô hình hóa cho dữ liệu liên tục dương. Ví dụ: Các đại lượng vật lý: nhiệt độ (tuyệt đối), áp suất,...
- Hai phân phối phổ biến được chọn để mô hình hóa dữ liệu liên tục dương là phân phối Gamma và phân phối Inverse Gaussian

---

# 11.2 Mô hình hóa dữ liệu liên tục dương

- Trong nhiều ứng dụng, biến phản hồi liên tục và dương. Các biến này thường lệch phải, ranh giới giới giới hạn 0 là đuôi bên trái của phân phối

- Hệ quả ranh giới giới hạn tại 0:
$\sigma \rightarrow 0$ khi $\mu \rightarrow 0$

- Dữ liệu liên tục dương thường có mối quan hệ trung bình - phương sai tăng. Ví dụ các hàm phương sai:

$V(\mu)=\mu^2$ hoặc $V(\mu)=\mu^3$


### Ví dụ 1: Một tập dữ liệu nằm trong chuỗi các nghiên cứu về sinh khối thực vật ở lục địa Á - Âu là dữ liệu nghiên cứu về lá của cây chanh. Dataset ```lime``` trong package ```GLMsData```

- Tập dữ liệu trên nghiên cứu tính khối lượng của tán lá dựa trên đường kính của tán lá

- Ngoài ra còn thông tin tuổi của cây

---

# 11.2 Mô hình hóa dữ liệu liên tục dương

- Khối lượng của tán lá tỷ lệ với diện tích bề mặt của tán lá

- Đường kính của tán lá có liên quan đến đường kính của tán lá

- Một tán lá có thể xem như dạng gần một quả cầu. $\mu$ tỷ lệ thuận với $4\pi (\dfrac{d}{2})^2=4\pi d^2 \Rightarrow \log y \propto \log \pi + 2 \log d$

- Code:

```python
library(GLMsData); data(lime); str(lime)
```

```python
'data.frame': 385 obs. of 4 variables:
$ Foliage: num 0.1 0.2 0.4 0.6 0.6 0.8 1 1.4 1.7 3.5 ...
$ DBH : num 4 6 8 9.6 11.3 13.7 15.4 17.8 18 22 ...
$ Age : int 38 38 46 44 60 56 72 74 68 79 ...
$ Origin : Factor w/ 3 levels "Coppice","Natural",..: 2 2 2 2 2 2 2
2 2 2 ...
```

---
layout: two-cols
---

# 11.2 Mô hình hóa dữ liệu liên tục dương

<img src="images/lime.png" width="400" height="350">

::right::

- Biến phản hồi (khối lượng tán lá) luôn luôn dương

- Phương sai của biến phản hồi tăng khi trung bình tăng

- Có mối liên hệ giữa khối lượng tán lá và DBH, khối lượng tán lá và tuổi


- Không có sự khác nhau nhiều về dữ liệu khối lượng tán lá theo nguồn gốc cây

---

# 11.3 Phân phối Gamma

- Phân phối Gamma là một phân phối thuộc họ phân phối xác suất có hai tham số.

- Phân phối Gamma có ba dạng

  - Tham số định dạng $\alpha$ và tham số phạm vi $\beta$:

  $$\mathcal{P}\big(y;\alpha,\beta\big)=\dfrac{y^{\alpha-1}\exp(-y/\beta)}{\Gamma(\alpha)\beta^{\alpha}}$$


  $$\mathrm{E}\lbrack y \rbrack=\alpha\beta$$
  $$\mathrm{var}\lbrack y \rbrack=\alpha \beta^2$$

  - Tham số định dạng $k=\alpha$ và tham số phạm vi ngược $\theta = \dfrac{1}{\beta}$


  $$\mathcal{P}\big(y;k,\theta\big)=\dfrac{y^{k-1}\theta^{k}\exp(-y\theta)}{\Gamma(k)}$$

---

# 11.3 Phân phối Gamma

<img src="images/gamma_1.png">


---

# 11.3 Phân phối Gamma

- Theo trung bình $\mu$ và tham số phân tán $\phi$

$$\alpha=\dfrac{1}{\phi}, \beta=\mu\phi$$

$$\mathcal{P}\big(y;\mu,\phi\big)=\Bigg(\dfrac{y}{\phi \mu} \Bigg)^{1/\phi}\dfrac{1}{y}\exp\Bigg(-\dfrac{y}{\phi\mu}\Bigg)\dfrac{1}{\Gamma(1/\phi)}$$

$$\mathcal{P}\big(y;\mu,\phi\big)=\Bigg(\dfrac{y}{\phi}\Bigg)^{1/\phi}\dfrac{1}{y\Gamma(1/\phi)}\exp\Bigg(-\dfrac{y}{\phi\mu} - \dfrac{1}{\phi}\log \mu\Bigg)$$

$$\Rightarrow \begin{cases} \theta = -\dfrac{1}{\mu} \\ \kappa(\theta) = \log \mu=\log(-\dfrac{1}{\theta}) \end{cases}$$

$$\begin{cases} d(y, \mu) = 2 \lbrace t(y, y) - t(y, \mu) \rbrace = 2  \Bigg\lbrace -1 - \log y - (-\dfrac{y}{\mu} - \log \mu) \Bigg\rbrace =2\Bigg\lbrace - \log \dfrac{y}{\mu} + \dfrac{y - \mu}{\mu} \Bigg\rbrace \\ V(\mu)=\mu^2\end{cases}$$

<style>
  .katex{font-size: 0.9em;}
</style>

---

# 11.3 Phân phối Gamma

<img src="images/gamma_2.png">

---

# 11.3 Phân phối Gamma

- Hàm link function tiêu chuẩn

$$\eta = -\dfrac{1}{\mu}$$
Thực tế hàm link function không được sử dụng do không đảm bảo $\mu > 0$ thay vào đó là hàm $\log \mu$
- Hàm phương sai:

$$V(\mu)=\mu^2$$

  - Hệ số phương sai:

  $$\dfrac{\mathrm{var}}{\mathrm{mean}^2}$$

  - Phân phối Gamma có hệ số phương sai là hằng số

---

# 11.3 Phân phối Gamma

### Ví dụ 2: Sử dụng tập dữ liệu ```lime``` ở ví dụ 1, dữ liệu được chia thành các nhóm theo tuổi cây và nguồn gốc cây, trung bình và phương sai của mỗi nhóm được tính

```python
 # Define age *groups*
> lime$AgeGrp <- cut(lime$Age, breaks=4 )
> # Now compute means and variances of each origin/age group:
> vr <- with( lime, tapply(Foliage, list(AgeGrp, Origin), "var" ) )
> mn <- with( lime, tapply(Foliage, list(AgeGrp, Origin), "mean" ) )
> # Plot
> plot( log(vr) ~ log(mn), las=1, pch=19,
xlab="log(group means)", ylab="log(group variance)")
> mf.lm <- lm( c(log(vr)) ~ c(log(mn)) )
> coef( mf.lm )
(Intercept) c(log(mn))
-0.165002 1.706453
> abline( coef( mf.lm ), lwd=2)
```

---
layout: two-cols
---

# 11.3 Phân phối Gamma

<img src="images/lime_1.png">

::right::

- Hệ số góc của đường thẳng giữa $\log(vr)$ và $\log(mn)$ khoảng 1.7 gần với 2. Vì vậy có thể xem:

$$\log(\mathrm{group} \thickspace \mathrm{variance}) \propto 2 \log(\mathrm{group} \thickspace \mathrm{mean})$$

- Với từng nhóm, phương sai của từng nhóm tỷ lệ với bình phương trung bình của nhóm $V(\mu)\approx\mu^2$ tương ứng với phân phối Gamma

---

# 11.4 Phân phối Gamma

- Độ lệch phần dư:

$$D(y, \hat{\mu})=\sum_{i=1}^nw_id(y_, \hat{\mu}_i)$$

- Trường hợp đặc biệt, trong trường hợp mô hình được học dùng MLE (giải một cách chính xác) và link function là hàm $\log$ thì:

$$D(y, \hat{\mu})=\sum_{i=1}^n 2 w_i \Big \lbrace -\log \dfrac{y_i}{\hat{\mu}_i} + \dfrac{y_i - \hat{\mu}_i}{\hat{\mu}_i} \Big \rbrace=\sum_{i=1}^n 2 w_i \Big \lbrace -\log \dfrac{y_i}{\hat{\mu}_i} \Big \rbrace$$


<style>
  .katex{font-size: 0.9em;}
</style>


---

# 11.3 Phân phối Gamma

- Chứng minh:

$$U(\hat{\bold{\beta}})=\dfrac{1}{\phi}X^TWM(\bold{y}-\bold{\mu})$$

$$W_{ii}=\dfrac{w_i}{V(\mu_i)\Big(\dfrac{d\eta_i}{d\hat{\mu}_i}\Big)^2}$$

$$M_{ii}=\dfrac{d\eta_i}{d\hat{\mu}_i}$$

$$U(\hat{\beta}_1)=\dfrac{1}{\phi}\sum_{i=1}^n \dfrac{w_i}{V(\mu_i)\Big(\dfrac{d\eta_i}{d\hat{\mu}_i}\Big)^2}\dfrac{d\eta_i}{d\hat{\mu}_i}(y_i - \hat{\mu}_i)=0$$

$$U(\hat{\beta}_1)=\dfrac{1}{\phi}\sum_{i=1}^n \dfrac{w_i}{\hat{\mu}_i^2\Big(\dfrac{1}{\hat{\mu}_i}\Big)^2}\dfrac{y_i - \hat{\mu}_i}{\hat{\mu}_i}=\dfrac{1}{\phi}\sum_{i=1}^n\dfrac{w_i(y_i - \hat{\mu}_i)}{\hat{\mu}_i}=0$$

---

# 11.3 Phân phối Gamma

- $D(y, \hat{\mu}) \sim \chi_{n-p'}^2$ 
nếu điều kiện xấp xỉ hàm yên ngựa được thỏa mãn. Đối với phân phối Gamma điều kiện xấp xỉ hàm yên ngựa được thỏa mãn khi $\phi \leq \dfrac{1}{3}$

- Xét:
$$\mathcal{P}\big(y;\mu,\phi\big)=\Bigg(\dfrac{y}{\phi}\Bigg)^{1/\phi}\dfrac{1}{y\Gamma(1/\phi)}\exp\Bigg(-\dfrac{y}{\phi\mu} - \dfrac{1}{\phi}\log \mu\Bigg)\\=\Bigg(\dfrac{y}{\phi}\Bigg)^{1/\phi}\dfrac{1}{y\Gamma(1/\phi)}\exp\Big(\dfrac{t(y, y)}{\phi}\Big)\exp\Bigg(-\dfrac{d(y, \mu)}{2\phi}\Bigg)\\=\Bigg(\dfrac{y}{\phi}\Bigg)^{1/\phi}\dfrac{1}{y\Gamma(1/\phi)}\exp\Big(\dfrac{-1 - \log y}{\phi}\Big)\exp\Bigg(-\dfrac{d(y, \mu)}{2\phi}\Bigg)$$

- Khi $\phi \leq 1/3$:

$$\Gamma(1 / \phi) \approx \sqrt{2\pi \phi}\Bigg(\dfrac{1}{\phi e}\Bigg)^{1/\phi}$$

<style>
  .katex{font-size: 0.9em;}
</style>

---

# 11.3 Phân phối Gamma

- Thay vào ta được:

$$\mathcal{P}\big(y;\mu,\phi\big)\approx\Bigg(\dfrac{y}{\phi}\Bigg)^{1/\phi}\dfrac{1}{y\sqrt{2\pi \phi}\Bigg(\dfrac{1}{\phi e}\Bigg)^{1/\phi}}\Bigg(\dfrac{1}{e}\Bigg)^{1/\phi}y^{-1/\phi}\exp\Bigg(-\dfrac{d(y, \mu)}{2\phi}\Bigg)\\=\dfrac{1}{\sqrt{2\pi\phi y^2}}\exp\Bigg(-\dfrac{d(y, \mu)}{2\phi}\Bigg)=\dfrac{1}{\sqrt{2\pi\phi V(y)}}\exp\Bigg(-\dfrac{d(y, \mu)}{2\phi}\Bigg)=\tilde{\mathcal{P}}(y;\mu, \phi)$$

- $\phi$ không biết trước được ước lượng ở mục 11.6 và được kiểm định likelihood ratio test dựa F-tests

---
layout: two-cols
---

# 11.3 Phân phối Gamma

- Mối quan hệ với phân phối Poisson:

  - Với các sự kiện xảy ra tuân theo phân phối Poisson, khoảng thời gian giữa hai sự kiện liên tiếp xảy ra tuân theo phân phối Gamma

  - Gọi một sự kiện xảy ra theo phân phối Poisson trong chu kỳ $T$ với tần suất $\lambda$ trong một đơn vị thời gian. Khoảng thời gian $y$ cần để chờ $r$ sự kiện xảy ra tuân theo phân phố Gamma với trung bình là $\dfrac{r}{\lambda}$ và phương sai là $\dfrac{r}{\lambda^2}$



 
::right::

<img src="images/poisson.png">

- Ví dụ: Một sự kiện xảy ra với tần suất $\lambda=0.2$ lần trên giây. Độ dài thời gian $y$ để đợi $r=10$ sự kiện xảy ra tuân theo phân phối Gamma với trung bình là $\dfrac{r}{\lambda}=\dfrac{10}{0.2}=50(s)$ và phương sai là $\dfrac{r}{\lambda^2}=\dfrac{r}{\lambda^2}=\dfrac{10}{0.2^2}=250(s^2)$ 


---

# 11.4 Phân phối Inverse Gaussian

- Phân phối Inverse Gaussian cũng là một phân phối phù hợp để mô hình hóa dữ liệu liên tục dương

- Hàm mật độ xác suất của phân phối Inverse Gaussian:

$$\mathcal{P}(y;\mu,\phi)=\Big(2\pi y^3 \phi\Big)^{-1/2}\exp \Big \lbrace - \frac{1}{2\phi} \frac{(y-\mu)^2}{y\mu^2} \Big \rbrace$$

với

$$\begin{cases} y > 0 \\ \mu > 0 \\ \phi > 0 \end{cases}$$

- Khi $\mu \rightarrow \infty$, phân phối Inverse Gaussian trở thành phân phối Levy ($\mathrm{var} \rightarrow \infty$):

$$\mathcal{P}(y;\phi)=\dfrac{1}{\sqrt{2\pi y^3\phi}}\exp \Big \lbrace -\dfrac{1}{2\phi y} \Big \rbrace$$


---

# 11.4 Phân phối Inverse Gaussian

- Tìm $\theta$ và $\kappa(\theta)$
$$\mathcal{P}(y;\mu,\phi)=\Big(2\pi y^3 \phi\Big)^{-1/2}\exp \Big \lbrace - \frac{1}{2\phi} \frac{(y-\mu)^2}{y\mu^2} \Big \rbrace\\=\dfrac{1}{\sqrt{2\pi y^3\phi}}\exp\Big\lbrace - \dfrac{y^2 - 2y\mu + \mu^2}{2\phi y\mu^2} \Big\rbrace\\=\dfrac{1}{\sqrt{2\pi y^3\phi}}\exp\Big\lbrace -\dfrac{1}{2\phi y} \Big\rbrace \exp \Big \lbrace - \dfrac{y}{2\phi\mu^2} + \dfrac{1}{\phi \mu} \Big \rbrace $$

$$\begin{cases} \theta = - \dfrac{1}{2\mu^2} \\ \kappa(\theta)=-\dfrac{1}{\mu} \end{cases}$$

$$\begin{cases}V(\mu)=\mu^3 \\ d(y, \mu)=2 \Big \lbrace -\dfrac{y}{2 y^2} + \dfrac{1}{y} - \Big(-\dfrac{y}{2\mu^2} + \dfrac{1}{\mu}\Big) \Big \rbrace=\dfrac{(y - \mu)^2}{y\mu^2} \end{cases} $$

---

# 11.4 Phân phối Inverse Gaussian

- Hàm link function tiêu chuẩn:

$$\eta = \dfrac{1}{\mu^2}$$

- Hàm phương sai:

$$V(\mu)=\mu^3$$

- Đối với phân phối Inverse Gaussian, hàm mật độ xác suất chính xác bằng hàm yên ngựa:


$$\mathcal{P}(y;\mu,\phi)=\dfrac{1}{\sqrt{2\pi y^3\phi}}\exp\Big\lbrace -\dfrac{1}{2\phi y} \Big\rbrace \exp\Big\lbrace \dfrac{t(y, y)}{\phi} \Big\rbrace \exp \Big \lbrace - \dfrac{d(y, \mu)}{2\phi} \Big \rbrace\\=\dfrac{1}{\sqrt{2\pi \phi y^3}}\exp \Big \lbrace - \dfrac{d(y, \mu)}{2\phi} \Big \rbrace=\dfrac{1}{\sqrt{2\pi \phi V(y)}}\exp \Big \lbrace - \dfrac{d(y, \mu)}{2\phi} \Big \rbrace=\tilde{\mathcal{P}}(y; \mu, \phi)$$


$\Rightarrow$ Hàm độ lệch đơn vị $d(y, \mu)$ tuân theo phân phối $\chi_1^2$

- $\phi$ không biết trước được ước lượng ở mục 11.6 và được kiểm định likelihood ratio test dựa F-tests

<style>
  .katex{font-size: 0.9em;}
</style>

---


# 11.4 Phân phối Inverse Gaussian


- Mối quan hệ giữa phân phối Inverse Gaussian và phân phối Gaussian:

  - Chuyển động Brown: Là một chuyển động ngẫu nhiên của phân tử trong không khí theo thời gian

  - Phân phối Inverse Gaussian miêu tả phân phối của khoảng thời gian cần thiết $y$ để đạt đến một điểm cách một khoảng $\delta$ cố định từ vị trí hiện tại.

  - Ngược lại, phân phối chuẩn miêu tả phân phối khoảng cách $\delta$ từ vị trí hiện tại trong khoảng thời gian $\tau$ cố định cho trước.


---

11.4 Phân phối Gaussian


<img src="images/invGauss_1.png" width="500" height="3600">

- Hình 1 miêu tả liên hệ giữa khoảng thời gian và khoảng cách đến điểm gốc của một phân tử trong một phép thử

- Phân phối khoảng thời gian cần để phân tử đạt đến một điểm cách $\delta=5$

- Cho trước khoảng thời gian $\tau=20$, qqplot của khoảng cách tính từ điểm gốc mà phân tử đi được


---

# PHỤ LỤC

- Chứng minh công thức độ lệch phần dư trang 436:

$$\mathcal{P}\big(y;\mu,\phi\big)=\Bigg(\dfrac{y}{\phi \mu} \Bigg)^{1/\phi}\dfrac{1}{y}\exp\Bigg(-\dfrac{y}{\phi\mu}\Bigg)\dfrac{1}{\Gamma(1/\phi)}$$

$$\mathcal{P}\Bigg(y_i;\mu_i,\dfrac{\phi}{w_i}\Bigg)=\Bigg(\dfrac{w_iy_i}{\phi \mu_i} \Bigg)^{w_i/\phi}\dfrac{1}{y}\exp\Bigg(-\dfrac{w_iy_i}{\phi_i\mu_i}\Bigg)\dfrac{1}{\Gamma(w_i/\phi)}$$

$$\log\mathcal{P}\Bigg(y_i;\mu_i,\dfrac{\phi}{w_i}\Bigg)=\dfrac{w_i}{\phi}\log\Bigg(\dfrac{w_iy_i}{\phi \mu_i}\Bigg)-\log y - \dfrac{w_iy_i}{\phi \mu_i} - \log \Gamma(w_i/\phi)$$

$$\dfrac{\partial \log\mathcal{P}\Bigg(y_i;\mu_i,\dfrac{\phi}{w_i}\Bigg)}{\partial \phi}=-\dfrac{w_i}{\phi^2}\log\Bigg(\dfrac{w_iy_i}{\phi \mu_i}\Bigg) - \dfrac{w_i}{\phi^2} + \dfrac{w_iy_i}{\mu_i\phi^2} + \dfrac{w_i}{\phi^2}\dfrac{\Gamma'(w_i/\phi)}{\Gamma(w_i/\phi)}=0$$

$$\Rightarrow -w_i\log\Bigg(\dfrac{w_iy_i}{\phi \mu_i}\Bigg) - w_i + \dfrac{w_iy_i}{\mu_i} + w_i\dfrac{\Gamma'(w_i/\phi)}{\Gamma(w_i/\phi)}=0$$

$$\Rightarrow -w_i \log\Bigg(\dfrac{y_i}{\mu_i}\Bigg) + w_i \dfrac{y_i - \mu_i}{\mu_i}=-w_i\log(\phi) + w_i\log(w_i) - w_i\dfrac{\Gamma'(w_i/\phi)}{\Gamma(w_i/\phi)}$$

$$\Rightarrow 2\Bigg\lbrace-w_i \log\Bigg(\dfrac{y_i}{\mu_i}\Bigg) + w_i \dfrac{y_i - \mu_i}{\mu_i}\Bigg\rbrace=2 \Bigg \lbrace -w_i\log(\phi) + w_i\log(w_i) - w_i\dfrac{\Gamma'(w_i/\phi)}{\Gamma(w_i/\phi)} \Bigg \rbrace$$

$$\Rightarrow d(y_i, \mu_i)=2 \Bigg \lbrace -w_i\log(\phi) + w_i\log(w_i) - w_i\dfrac{\Gamma'(w_i/\phi)}{\Gamma(w_i/\phi)} \Bigg \rbrace$$

$$\Rightarrow D(y_i, \hat{\mu}_i)=2\sum_{i=1}^n -w_i\log(\phi) + w_i\log(w_i) - w_i\dfrac{\Gamma'(w_i/\phi)}{\Gamma(w_i/\phi)}$$

<style>
  .katex{font-size: 0.5em;}
</style>