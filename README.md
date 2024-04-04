# Quantile_Regression_Forest
Regresi Hutan kuantil (QRF)  merupakan generalisasi dari hutan acak (_random forest_) yang sifatnya kekar, tidak linear dan non-parametrik yang bertujuan untuk menduga kuantil bersyarat (_conditional quantile_). Diberikan kuantil ke- $\tau$ 
 dari $Y$ dengan syarat $X=x$  yang dinotasikan $q_\tau(Y|X=x)$ dengan $\tau\in(0,1)$ . Fungsi sebaran bersyarat untuk $X=x$, $F(Y|X=x)$  merupakan peluang lebih kecil atau sama dengan $Y \in R$ seperti pada persamaan berikut
	$$F(Y|X=x)=P(Y \leq y|X=x)$$
Fungsi sebaran ini digunakan untuk menkonstruksi kuantil. Secara umum persamaan regresi kuantil forest dapat diformulasikan sebagai berikut
	$$q_\tau(Y|X=x)=inf\{y:F(y|X=x)\geq\tau\}$$
Untuk menduga fungsi sebaran bersyarat maka digunakan sebaran tertimbang dari peubah respon yakni dengan 
	$$\hat{F}(Y|X=x)=\sum\limits_{i=1}^n w_i(x)1_{Y_i \leq y\} $$
dengan $w_i(x)=\frac{1}{k}\sum\limits_{i=1}^k w_i(x,\theta_t)$
Sebagai ilustrasi, kami menggunakan data pendapatan (tersedia oleh author). Untuk menjalankan regresi hutan kuantil di RStudio, lakukan penginstalan paket (package) _quantregForest_ dari CRAN dengan kode berikut:
```r
install.packages("quantregForest")
```
Selanjutnya, silahkan ketikkan kode berikut:
```r
library(quantregForest)
Data(pendapatan)
train_ind <- sample.split(pendapatan1, SplitRatio = 0.8)
data_train <- subset(pendapatan,train_ind == TRUE)
data_test <- subset(pendapatan,train_ind == FALSE)
xtrain <- as.matrix(data_train[,-1])
ytrain <- as.vector(data_train[,1])
xtest <- as.matrix(data_test[,-1])
ytest <- as.vector(data_test[,1])
set.seed(1515)
nrow(data_train)
nrow(data_test)

#Estimasi qrf 
qrfpendapatan <- quantregForest(xtrain,ytrain, ntree = 10000)
qrfpendapatan$importance
quantiles <- c(0.05,0.5,0.95)

#Prediksi qrf
quant <- predict(qrfpendapatan,quantiles=quantiles,newdata=xtest)

#Kepentingan Peubah
importance(qrfpendapatan,quantiles=quantiles)
importance(qrfpendapatan,quantiles=0.5)

#Coverage Plot
z <- quant[,3]-quant[,1]
or <- order(z)
ynew <- ytest
n <- length(ynew)

# center and order the quantiles
med <- quant[or,2]-quant[or,2]
upp <- quant[or,3]-quant[or,2]
low <- quant[or,1]-quant[or,2]
ytrain1 <- ynew[or]-quant[or,2]
plot(1:n,ynew[or]-quant[or,2],pch=20,xlab="ordered samples",
       ylab="observed response and prediction intervals(centred)",type="n",main="95% prediction intervals of QRF", ylim=c(-5,10))
dist <- 0.01
for (i in 1:n){
  polygon( c(i-dist,i+dist,i+dist,i-dist),
             c(upp[i],upp[i],low[i],low[i]) ,col=rgb(1,1,1) ,border=TRUE)
  }
for (i in 1:n){
  lines(c(i-dist,i+dist) , c(upp[i],upp[i]) )
  lines(c(i-dist,i+dist) , c(low[i],low[i]) )
  }
inpred <- (ytrain1<= upp) & (ytrain1>=low)
for (i in 1:n) points(i,ynew[or[i]]-quant[or[i],
                                          2],col=as.numeric(inpred)[i]+2,pch=20)
```
Contoh hasil prediksi interval dari model QRF dengan $\tau=(0,05;0,01)$ untuk data pendapatan dapat dilihat pada gambar berikut
![https://github.com/asrirawan/Quantile-Regression-Forest/issues/1#issue-2226526870](https://private-user-images.githubusercontent.com/113370906/319775256-ff7c1126-88eb-461c-b92a-f8ae7433e620.jpg?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTIyNjU5MjUsIm5iZiI6MTcxMjI2NTYyNSwicGF0aCI6Ii8xMTMzNzA5MDYvMzE5Nzc1MjU2LWZmN2MxMTI2LTg4ZWItNDYxYy1iOTJhLWY4YWU3NDMzZTYyMC5qcGc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNDA0JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDQwNFQyMTIwMjVaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1kZmRiOGQwYjM2NjAxYjcwOWI2NmUzYmQ1ZWE5YjI5M2JjYWY4MWVlMDUzNGZmMjI5YjQyNWY1YmQ1ZTBiZjM2JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.RT2GtB-l3gxRDNNzgAZj0A6_v0_v9enC3A46-kmfOPk))
