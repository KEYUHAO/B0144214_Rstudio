糖尿病預測模型
================

資料前處理
----------

### 資料讀取

此資料來源為UCI Machine Learning Repository。

``` r
if (!require('mlbench')){
    install.packages("mlbench")
}
 
 library(mlbench)
 data(PimaIndiansDiabetes)
 head(PimaIndiansDiabetes)
```

    ##   pregnant glucose pressure triceps insulin mass pedigree age diabetes
    ## 1        6     148       72      35       0 33.6    0.627  50      pos
    ## 2        1      85       66      29       0 26.6    0.351  31      neg
    ## 3        8     183       64       0       0 23.3    0.672  32      pos
    ## 4        1      89       66      23      94 28.1    0.167  21      neg
    ## 5        0     137       40      35     168 43.1    2.288  33      pos
    ## 6        5     116       74       0       0 25.6    0.201  30      neg

``` r
PimaIndiansDiabetesC<-PimaIndiansDiabetes[complete.cases(PimaIndiansDiabetes),]
c(nrow(PimaIndiansDiabetes),nrow(PimaIndiansDiabetesC))
```

    ## [1] 768 768

### 將資料隨機分為訓練組與測試組

隨機將2/3的資料分到訓練組（Test==F），剩下1/3為測試組（Test==T)

``` r
 PimaIndiansDiabetes$Test<-F
 PimaIndiansDiabetes[sample(1:nrow(PimaIndiansDiabetes),nrow(PimaIndiansDiabetes)/3),]$Test<-T
 c(sum(PimaIndiansDiabetes$Test==F),sum(PimaIndiansDiabetes$Test==T))
```

    ## [1] 512 256

可得訓練組案例數為512 測試組案例數為256

預測模型建立
------------

### 模型建立

感覺用回歸作起來比較好看

``` r
fit<-glm(diabetes~., PimaIndiansDiabetes[PimaIndiansDiabetes$Test==F,],family="binomial")
library(MASS)
finalFit<-stepAIC(fit,direction = "both",trace = F)
summary(finalFit)$coefficients
```

    ##                 Estimate  Std. Error   z value     Pr(>|z|)
    ## (Intercept) -9.289668028 0.957134092 -9.705712 2.850783e-22
    ## pregnant     0.137710159 0.039274160  3.506381 4.542449e-04
    ## glucose      0.037429946 0.004627988  8.087736 6.078408e-16
    ## pressure    -0.016049116 0.006721084 -2.387876 1.694606e-02
    ## insulin     -0.001365676 0.000954437 -1.430871 1.524673e-01
    ## mass         0.107074892 0.019210362  5.573809 2.492286e-08
    ## pedigree     1.098105767 0.370145605  2.966686 3.010283e-03
    ## age          0.020066081 0.011267061  1.780951 7.492049e-02

由上述參數可知，由懷孕、葡萄糖、遺傳等等，以邏輯迴歸建立模型預測是否有糖尿病。

預測模型驗證
------------

``` r
##PimaIndiansDiabetesPred<-predict(finalFit,newdata = PimaIndiansDiabetes[PimaIndiansDiabetes$Test==T,])
##PimaIndiansDiabetesAns<-ifelse(PimaIndiansDiabetesPred<0.5,"P","N") #<0.5: Level 1
##PimaIndiansDiabetesAns<-factor(PimaIndiansDiabetesAns,levels = c("P","N"))
##install.packages("caret")
##library(caret)
##sensitivity(PimaIndiansDiabetesAns,PimaIndiansDiabetes[PimaIndiansDiabetes$Test==T,]$diabetes)
##specificity(PimaIndiansDiabetesAns,PimaIndiansDiabetes[PimaIndiansDiabetes$Test==T,]$diabetes)
##posPredValue(PimaIndiansDiabetesAns,PimaIndiansDiabetes[PimaIndiansDiabetes$Test==T,]$diabetes)
##negPredValue(PimaIndiansDiabetesAns,PimaIndiansDiabetes[PimaIndiansDiabetes$Test==T,]$diabetes)
```
