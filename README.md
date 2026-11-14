# Stata code
Stata coding project based on a 2016 paper titled: Abetter vision for development: Eyeglasses and academic performance in rural primary schools in China





*Estimating the Linear Probability Model
*y is binary variable so its linear probabaility

reg received eye female prevglas headteacher headleader zavetest

*example
*a unit increase in eye aquitity, holding everytihg else constant the proabiliy of receiving eye glasses decrease by 69%
*

*fitted line

predict receivedhat, xb
sort eye
twoway (scatter received eye) (line receivedhat eye) (scatter receivedhat eye)


*Predicted Probability for a Specific Student: Female
*this is regresion
reg received eye female prevglas headteacher headleader zavetest
*matrix tells you your coefficents (row, colum)
matrix coef=e(b)
matrix list coef

*probabiulity of receiving eye glasses as eye aquity increases
*female
gen receivedhat2f=coef[1,7]+coef[1,1]*eye + coef[1,2]*1 + coef[1,3]*0 +coef[1,4]*0 +coef[1,5]*0 +coef[1,6]*0

twoway (scatter received eye) (connect receivedhat2f eye, lc(blue)) 


*now for males all the same except female=0
gen receivedhat2M=coef[1,7]+coef[1,1]*eye + coef[1,2]*0 + coef[1,3]*0 +coef[1,4]*0 +coef[1,5]*0 +coef[1,6]*0


*now you have 2 graphs one for male and one for female
twoway (scatter received eye) (line receivedhat2M eye, lc(blue)) (line receivedhat2f eye, lc(red))



*****use adjust so stata does the work ***********

****** linear probabaility model graph *********

*use adjust so its not manual
*stata now does this prediction
*no need to work with coefficents just tell it what values to plug in
*by means use everyones eye aquity not specific values
reg received eye female prevglas headteacher headleader zavetest

adjust female=0 prevglas =0 headteacher=0 headleader=0 zavetest=0, by(eye) generate(receivedhat3m)

adjust female=1 prevglas =0 headteacher=0 headleader=0 zavetest=0, by(eye) generate(receivedhat3f)

twoway (scatter received eye) (line receivedhat3m eye, lc(blue)) (line receivedhat3f eye, lc(red))


**use the average values of all the other variables
*find the graph for the relationship between eye aquity and reciving eye glasses
*no need to plug in number


adjust female prevglas headteacher headleader zavetest, by(eye) generate(receivedhat4)
*this is receivedhat4

twoway (scatter received eye) (line receivedhat4 eye)



************************ LOGIT ********************************
*Because received is a binary outcome, a logit model provides a nonlinear alternative 


logit received eye female prevglas headteacher headleader zavetest

*slope at average values
mfx compute

adjust female prevglas headteacher headleader zavetest, by(eye) pr generate(recievedhatlogit)
*this is recievedhatlogit 
sort eye

twoway (scatter received eye) (connect recievedhatlogit eye) (line receivedhat4 eye)



*can not comment of r squared unless you compare it to another pseudo R sqaured
*variations in X can only be talkned about if its linear OLS



log close

