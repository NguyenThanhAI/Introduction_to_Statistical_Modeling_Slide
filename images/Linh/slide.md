---
marp: true
---

# 11.5 Link Function

---

`Link function` dạng `log` là một trong những `link functions` phổ biến cho mô hình GLMs với phân phối `Gamma` và phân phối `inverse Gaussian` để đảm bảo  $\mu > 0$ và cho mục đích diễn giải

Trong `R`, đối với phân phối `Gamma` và phân phối `inverse Gaussian`, các `link function` được sử dụng là `log`, `identity` và `inverse` (mặc định cho phân phối `Gamma`). Hàm `link = "1/mu^2"` cũng được sử dụng cho phân phối `inverse Gaussian`.

---

<em> Ví dụ 1 </em>

Data small-leaved lime trong dataset `lime`

```{r}
library(GLMsData); data(lime); str(lime)
```

```{r}
'data.frame':	385 obs. of  4 variables:
 $ Foliage: num  0.1 0.2 0.4 0.6 0.6 0.8 1 1.4 1.7 3.5 ...
 $ DBH    : num  4 6 8 9.6 11.3 13.7 15.4 17.8 18 22 ...
 $ Age    : int  38 38 46 44 60 56 72 74 68 79 ...
 $ Origin : Factor w/ 3 levels "Coppice","Natural",..: 2 2 2 2 2 2 2 2 2 2 ...
```

Trong đó

`Foliage` là khối lượng tán lá tính bằng `kg`

`DBH` là đường kính cây tính bằng `cm`

`Age` là tuổi cây tính bằng năm

`Origin` là nguồn gốc của cây: `Coppice`, `Natural` và `Planted`

---

#### Sử dụng phân phối `Gamma` với các `link functions` khác nhau.

Với `link function` dạng `log`

```{r}
lime.log <- glm(Foliage ~ log(DBH), family=Gamma(link="log"),data=lime)
lime.log$coefficients
```

```{r}
(Intercept)          OriginNatural          OriginPlanted 
-4.6289255              0.3245216             -1.5284910 
  log(DBH) OriginNatural:log(DBH) OriginPlanted:log(DBH) 
  1.8431878             -0.2040329              0.5767826
```

---

Với `inverse link function`

```{r}
lime.inv <- update(lime.log, family=Gamma(link="inverse"))
```

`R` đưa ra cảnh báo

```{r}
Warning in log(ifelse(y == 0, 1, y/mu)) : NaNs produced
```

`R` không thể tìm được các điểm bắt đầu phù hợp với mô hình, chúng ta có thể cung cấp cho hàm `glm()` các điểm bắt đầu sử dụng `mustart` (cho thang đo của data) hoặc `etastart` cho thang dự đoán tuyến tính

```{r}
lime.inv <- update(lime.log, family=Gamma(link="inverse"), mustart=fitted(lime.log))
```

`R` tiếp tục báo lỗi như trên vậy nên chúng ta không xem xét mô hình này nữa. Hoàn toàn tương tự với `link function` `identity`

---

log-link model

<img src="model.png">

---

Một vài hàm `link function` dạng log

<img src="log-link-1.png" style="height:100%; width:100%">

---

<img src="log-link-2.png">

---

# 11.6 Ước lượng tham số phân tán

---

## 11.6.1 Ước lượng tham số phân tán cho phân bố Gamma

Với phân bố Gamma, ước lượng hợp lý cự đại (`MLE`) của tham số phân tán $\phi$ không có dạng gần đúng mà `MLE` cho $\phi$ là nghiệm của phương trình

$$D(y, \hat{\mu}) = -2 \sum_{i=1}^n w_i \log\phi - w_i \log w_i + w_i \psi(w_i / \phi)$$

Trong đó $D(y, \hat{\mu})$ là phần dư và $n$ là cỡ mẫu, $\psi(x) = \Gamma(x)'/\Gamma(x)$.

Phương trình này được giải bằng phương pháp lặp, chính vì vậy ước lượng Pearson và ước lượng deviance thường được sử dụng nhiều hơn.

---

Ước lượng deviance 
$$\tilde{\phi} = \dfrac{D(y, \hat{\mu})}{n - p'}$$
thường có ý nghĩa cho các giá trị $y_i$ rất bé trong phân phối `Gamma` nên ta có thể ưu tiên dùng ước lượng Pearson
$$\bar\phi = \dfrac{1}{n - p'} \sum_{i = 1}^n \dfrac{w_i}{\hat\mu_i^2}$$
khi còn nghi ngờ độ chính xác của các giá trị nhỏ

---

<em> Ví dụ 11.5 </em>

Xét mô hình gamma `lime.log` đã được fit trong ví dụ 1 với tập dữ liệu `lime`

Ước lượng cho $\phi$

```{r}
phi.md <- deviance(lime.log)/df.residual(lime.log)
phi.pearson <- summary(lime.log)$dispersion
c("Mean Deviance"=phi.md, "Pearson"=phi.pearson)
```

```{r}
Mean Deviance       Pearson 
    0.4028747     0.5443774  
```

Sử dụng ước lượng hợp lý cực đại `MLE`, kết quả thu được là `0.3736`

---

<em> Ví dụ 11.6 </em>

Kiểm định anova

```{r}
round(anova(lime.log, test="F"), 3)
```

```{r}
Df Deviance Resid. Df Resid. Dev       F Pr(>F)    
NULL                              384     508.48                   
Origin           2    19.89       382     488.59  18.272 <2e-16 ***
log(DBH)         1   328.01       381     160.58 602.535 <2e-16 ***
Origin:log(DBH)  2     7.89       379     152.69   7.247  0.001 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```

Mặc định `R` sử dụng ước lượng Pearson của $\phi$ và `F-test` để tính toán

---

Ngoài ra cũng có thể ước lượng $\phi$ theo cách khác

```{r}}
round(anova(lime.log,test="F", dispersion=phi.md), 3)
```

```{r}
                Df Deviance Resid. Df Resid. Dev       F    Pr(>F)    
NULL                              384     508.48                      
Origin           2    19.89       382     488.59  24.690 < 2.2e-16 ***
log(DBH)         1   328.01       381     160.58 814.165 < 2.2e-16 ***
Origin:log(DBH)  2     7.89       379     152.69   9.793 < 2.2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```

Kết luận khá tương đồng với ước lượng khác

---

Các hệ số được ước lượng:

```{r}
printCoefmat(coef(summary(lime.log)), 3)
```

```{r}
                       Estimate Std. Error t value Pr(>|t|)    
(Intercept)              -4.629      0.276  -16.79   <2e-16 ***
OriginNatural             0.325      0.388    0.84   0.4037    
OriginPlanted            -1.528      0.573   -2.67   0.0079 ** 
log(DBH)                  1.843      0.102   18.15   <2e-16 ***
OriginNatural:log(DBH)   -0.204      0.143   -1.42   0.1554    
OriginPlanted:log(DBH)    0.577      0.209    2.76   0.0061 ** 
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```

Có sự khác nhau một chút giữa loại cây tự nhiên và cây được trồng

---

## 11.6.2 Ước lượng tham số phân tán cho phân bố Inverse Gaussian

Đối với phân bố `inverse Gaussian` thì `MLE` cho tham số phân tán là

$$\hat{\phi} = \dfrac{D(y, \hat{\mu})}{n}$$

Nhìn chung thì đây là 1 ước lược chệch của $\phi$. Ước lượng devience trung bình:

$$\tilde{\phi} = \dfrac{D(y, \hat{\mu})}{n - p'}$$

giống với dạng `MLE` gần như không chệch

Ước lượng devience trung bình nhìn chung là tốt về mặt lý thuyết và được dùng khi dữ liệu có chất lượng tốt

---

Pearson estimation cho $\phi$

$$\bar{\phi} = \dfrac{1}{n - p'} \sum_{i = 1}^{n} \dfrac{w_i (y_i - \hat{\mu}_i)}{\hat{\mu}_i^3}$$

Trong phân phối `inverse Gaussian` thì độ lệch khá 'nhạy cảm' với lỗi làm tròn với những giá trị $y_i$ nhỏ nên ước lượng Pearson sẽ tốt hơn là ước lượng độ lệch trung bình khi giá trị của $y$ là nhỏ.

---

<em> Ví dụ 11.7 </em>

```{r}
lime.iG <- glm( Foliage ~ Origin * log(DBH), family=inverse.gaussian(link="log"), data=lime)
```

```{r}
phi.iG.mle <- deviance(lime.iG)/length(lime$Foliage) # ML estimate
phi.iG.md <- deviance(lime.iG)/df.residual(lime.iG) # Mean dev
phi.iG.pearson <- summary( lime.iG )$dispersion # Pearson
c("MLE"=phi.iG.mle, "Mean dev."=phi.iG.md, "Pearson"=phi.iG.pearson)
```

Kết quả thu được cho ước lượng của $\phi$ với các loại ước lượng `MLE`, `mean deviation`, `Pearson`

```{r}
      MLE Mean dev.   Pearson 
 1.056659  1.073387  1.255992
```

---

Akaike’s An Information Criterion
nhắc lại

$$AIC = -2 \times l(\hat{\beta}_0,...,\hat{\beta}_p, \phi; y) + 2p' $$

trong đó $l$ là hàm $log$ likelihood

Giá trị càng nhỏ thì model càng tốt.

```{r}
c("Gamma:"=AIC(lime.log), "inv. Gauss.:"=AIC(lime.iG))
```

```{r}
  Gamma: inv. Gauss.: 
750.3267    1089.5297
```

Ta nhận thấy rằng mô hình gamma GLM tốt hơn so với inverse Gaussian GLM
