# GenderDiscriminationDataSet_R_Analysis
Data in file Gender Discrimination.csv contain the information about gender, experience and annual salary in $ for some employees of a company. We want to evaluate whether the salary differs between males and females, given the experience. <br>

my.data <- read.csv('Gender_Discrimination.csv', sep=',') <br>

First look at the data <br>

my.data[1:3,] <br>
   Gender  Experience  Salary<br>
1  Female    15         78200<br>
2  Female    12         66400<br>
3  Female    15         61200<br>

dim(my.data)<br>
[1] 208 3<br>

summary(my.data)<br>

Variable Gender is a qualitative variable with 2 levels, Female and Male<br>

is.factor(my.data$Gender)<br>
[1] FALSE<br>

make it a factor<br>
my.data$Gender <- as.factor(my.data$Gender)<br>
levels(my.data$Gender)<br>
[1] "Female" "Male"<br>
table(my.data$Gender)<br>

Female    Male<br>
 140      68<br>
 
Initial description of the data.<br>
Histogram of the salary<br>

hist(my.data$Salary, prob=TRUE, main='Annual salary')<br>

![Histogram of Salary Rplot](https://github.com/adnantheanalyst/GenderDiscriminationDataSet_R_Analysis/assets/16821246/8b1cdf8f-8252-42d0-a54f-1ef9c582a9c0)

Boxplot of the salary<br>

boxplot(my.data$Salary, las=2, col='grey', main='Annual Salary')<br>

![Histogram of Salary Rplot](https://github.com/adnantheanalyst/GenderDiscriminationDataSet_R_Analysis/assets/16821246/3acf9aa6-99b8-4757-b67c-5e76ffd5d467)

We can evaluate if a logarithmic transformation makes the salary more "normal"<br>

hist(log(my.data$Salary), prob=TRUE, main='Logarithm of salary')<br>

![Logarithm of Salary Rplot](https://github.com/adnantheanalyst/GenderDiscriminationDataSet_R_Analysis/assets/16821246/689bd87e-0a65-426f-954b-153d89ee0b55)

boxplot(log(my.data$Salary), col='grey', main='Logarithm of salary')<br>

![Boxplot of Logarithm of Salary Rplot](https://github.com/adnantheanalyst/GenderDiscriminationDataSet_R_Analysis/assets/16821246/87f92f8e-fa1d-425b-986b-35c23afd1d13)

From hereon, consider the logarithm of the salary.<br>

my.data$lSalary <- log(my.data$Salary)<br>

In this way, the variable will be created directly inside the dataset.<br>
Gender distribution<br>

pie(table(my.data$Gender), labels=c('Female','Male'))<br>

![Pie Chart Gender RPlot](https://github.com/adnantheanalyst/GenderDiscriminationDataSet_R_Analysis/assets/16821246/98a40263-c9f5-404d-b4d3-5aea8614a62c)

Distribution of log salary given gender<br>
boxplot(my.data$Salary~my.data$Gender, main='Log Salary given Gender', col=c('pink','blue'), las=2, ylab='Salary', cex.axis=0.7)<br>

![Boxplot log Salary given Gender Rplot](https://github.com/adnantheanalyst/GenderDiscriminationDataSet_R_Analysis/assets/16821246/454a8364-bb96-4b2c-9903-59ccc0e4fc93)

Distribution of experience given gender<br>

boxplot(my.data$Experience~my.data$Gender, main='Experience given Gender', col=c('pink','blue'), las=2, ylab='Experience', cex.axis=0.7)<br>

![Boxplot Experience given Gender Rplot](https://github.com/adnantheanalyst/GenderDiscriminationDataSet_R_Analysis/assets/16821246/3ba35707-28c7-4241-bfa8-a952692f11c6)

Dispersion plot of log salary and experience<br>
plot(my.data$Experience, my.data$lSalary, main='Log Salary vs Experience', xlab='Experience', ylab='Salary', las=2, cex.axis=0.7)<br>

![Dispersion plot Log Salary given Experience Rplot](https://github.com/adnantheanalyst/GenderDiscriminationDataSet_R_Analysis/assets/16821246/5f0b3527-967a-47ed-86e3-f7e5eabb55fb)

Dispersion plot of log salary and experience distinguishing by gender<br>
plot(my.data$Experience, my.data$lSalary, main='Log Salary vs Experience', xlab='Experience', ylab='Log Salary', las=2, cex.axis=0.7)<br>
points(my.data$Experience[my.data$Gender == 'Female'], my.data$lSalary[my.data$Gender == 'Female'], col='pink', pch=19)<br>
points(my.data$Experience[my.data$Gender == 'Male'], my.data$lSalary[my.data$Gender == 'Male'], col='blue', pch=19)<br>
legend('topleft', pch=c(19,19), c('Female','Male'), col=c('pink','blue'), bty='n')<br>

![Boxplot Experience given Gender Rplot](https://github.com/adnantheanalyst/GenderDiscriminationDataSet_R_Analysis/assets/16821246/293399dc-2adf-4151-8a7f-f121d2434285)

Estimate a multiple linear with covariates Gender and Experience. Consider that Gender is codified so that it assumes value 0 if Gender=Female and value 1 if Gender=Male (R follows the alphabetical order; it can be changed). The model is<br>
&Tab;                           lSalary = β0 + β1Gender + β2Experience + ε<br>
or<br>
&Tab;                      lSalary = β0 + β1 I(Gender=Male) + β2Experience + ε<br>
if we want to explicit that Gender has an associated binary/indicator variable (dummy variable). Thus, if Gender=Female, the model is<br>
&Tab;                                lSalary = β0 + β2Experience + ε,<br>
while if Gender=Male, the model is<br>
&Tab;                             lSalary = β0 + β1 + β2Experience + ε,<br>
                              
model <- lm(lSalary ~ Gender + Experience, data=my.data)<br>
summary(model)<br>

Note that in the summary we have the estimate of β1, the parameter in case gender is male. Female level is considered as reference level. The linear regression fit for females iŝ lSalary = 10.9953234 + 0.0170923 ∗ Experience, while that for males iŝ lSalary = 10.9953234 + 0.1712362 + 0.0170923 ∗ Experience = 11.1665596 + 0.0170923 ∗ Experience.<br>

Graphical visualization of the estimated model<br>

plot(my.data$Experience, my.data$lSalary, main='Log Salary vs Experience', xlab='Experience', ylab='Log Salary', las=2, cex.axis=0.7)<br>
points(my.data$Experience[my.data$Gender == 'Female'], my.data$lSalary[my.data$Gender == 'Female'], col='pink', pch=19)<br>
points(my.data$Experience[my.data$Gender == 'Male'], my.data$lSalary[my.data$Gender == 'Male'], col='blue', pch=19)<br>
legend('topleft', pch=c(19,19), c('Female','Male'), col=c('pink','blue'), bty='n')<br>
abline(coef(model)[1], coef(model)[3], col='pink')<br>
abline(coef(model)[1]+coef(model)[2], coef(model)[3], col='blue')<br>

![Dispersion plot Log Salary given Experience by Gender rRplot](https://github.com/adnantheanalyst/GenderDiscriminationDataSet_R_Analysis/assets/16821246/7df67a38-5e51-47c8-a451-bbf67fc29e14)

Model with interaction between Gender and Experience<br>

model2 <- lm(lSalary ~ Gender * Experience, data=my.data)<br>
summary(model2)<br>

What can we infer from the model?<br>


Graphical inspection of the model<br>

plot(my.data$Experience, my.data$lSalary, main='Log Salary vs Experience', xlab='Experience', ylab='Log Salary', las=2, cex.axis=0.7)<br>
points(my.data$Experience[my.data$Gender == 'Female'], my.data$lSalary[my.data$Gender == 'Female'], col='pink', pch=19)<br>
points(my.data$Experience[my.data$Gender == 'Male'], my.data$lSalary[my.data$Gender == 'Male'], col='blue', pch=19)<br>
legend('topleft', pch=c(19,19), c('Female','Male'), col=c('pink','blue'), bty='n')<br>
abline(coef(model2)[1], coef(model2)[3], col='pink')<br>
abline(coef(model2)[1]+coef(model2)[2], coef(model2)[3]+coef(model2)[4], col='blue')<br>

![Visualization of estimated model2 Rplot](https://github.com/adnantheanalyst/GenderDiscriminationDataSet_R_Analysis/assets/16821246/23da481e-83bc-4ae3-bc1c-5b534e8dce81)

Does it make sense to include a polynomial term associated to Experience?<br>

let's try with the square of Experience<br>
model3 <- update(model2, . ~ . + I(Experience^2))<br>
summary(model3)<br>

We still need the residual analysis of model2.<br>
par(mfrow=c(2,2))<br>
plot(model2)<br>


![Residual analysis of polynomial model Rplot](https://github.com/adnantheanalyst/GenderDiscriminationDataSet_R_Analysis/assets/16821246/11c2bb71-006e-437a-9ec8-73b8dea36114)

How can we comment on the plot?<br>
Using model2 we can predict the salary for a male and a female with 20 years of experience<br>

exp(predict(model2, newdata=data.frame(list(Experience=20, Gender='Male'))))<br>
1<br>
106198.4<br>
exp(predict(model2, newdata=data.frame(list(Experience=20, Gender='Female'))))<br>
1<br>
78444.41<br>
without using predict()<br>

prediction for male<br>
exp(coef(model2)[1]+ coef(model2)[2]+coef(model2)[3]*20+coef(model2)[4]*20)<br>
(Intercept)<br>
106198.4<br>
prediction for female<br>
exp(coef(model2)[1]+ coef(model2)[3]*20)<br>
(Intercept)<br>
78444.41<br>

Add the curve (on the original scale) to the data.<br>
plot(my.data$Experience, my.data$Salary, main='Salary vs Experience', xlab='Experience', ylab='Salary')<br>
points(my.data$Experience[my.data$Gender == 'Female'], my.data$Salary[my.data$Gender == 'Female'], col='pink')<br>
points(my.data$Experience[my.data$Gender == 'Male'], my.data$Salary[my.data$Gender == 'Male'], col='blue')<br>
legend('topleft', pch=c(1,1), c('Female','Male'), col=c('pink','blue'), bty='n')<br>
curve(exp(coef(model2)[1]+ coef(model2)[3]*x), col='pink', add=TRUE)<br>
curve(exp(coef(model2)[1]+coef(model2)[2]+ coef(model2)[3]*x+coef(model2)[4]*x), col='blue', add=TRUE)<br>

![Visualization of estimated model2(20yrExperience) with Curve Rplot](https://github.com/adnantheanalyst/GenderDiscriminationDataSet_R_Analysis/assets/16821246/55dae6da-33fa-4514-8168-17464105105e)
