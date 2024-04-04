# Quantile_Regression_Forest
Regresi Hutan kuantil (QRF)  merupakan generalisasi dari hutan acak (random forest) yang sifatnya kekar, tidak linear dan non-parametrik yang bertujuan untuk menduga kuantil bersyarat (conditional quantile). Diberikan kuantil ke- $\tau$ 
 dari $Y$ dengan syarat $X=x$  yang dinotasikan $q_\tau(Y|X=x)$ dengan $\tau\in(0,1)$ . Fungsi sebaran bersyarat untuk $X=x$, $F(Y|X=x)$  merupakan peluang lebih kecil atau sama dengan $Y \in R$ seperti pada persamaan berikut
	$$F(Y|X=x)=P(Y \leq y|X=x)$$
Fungsi sebaran ini digunakan untuk menkonstruksi kuantil. Secara umum persamaan regresi kuantil forest dapat diformulasikan sebagai berikut
	$$q_\tau(Y|X=x)=inf\{y:F(y|X=x)\geq\tau\}$$
Untuk menduga fungsi sebaran bersyarat maka digunakan sebaran tertimbang dari peubah respon yakni dengan 
	$$\hat{F}(Y|X=x)=\sum\limits_{i=1}^n w_i(x)1_{Y_i \leq y\} $$
dengan $w_i(x)=\frac{1}{k}\sum\limits_{i=1}^k w_i(x,\theta_t)$
