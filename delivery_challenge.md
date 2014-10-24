# Delivery_Challenge
ww44ss  
Oct  2014  


```r
#if getwd()=="delivery_challenge" {} else {setwd("delivery_challenge")}

delivery_data<-read.csv("delivery_data.csv")

##clean data
        ##data frame it
        delivery_data<-as.data.frame(delivery_data)
        #eliminate NA zipcodes
        delivery_data<-delivery_data[!is.na(delivery_data$pickup_zipcode),]
        ##ensure pruchase prices are finite
        delivery_data<-delivery_data[!is.na(delivery_data$purchase_price),]
        delivery_data<-delivery_data[delivery_data$purchase_price>0,]

##take a look at the structure and header of the data 

str(delivery_data)
```

```
## 'data.frame':	57139 obs. of  6 variables:
##  $ pickup_zipcode    : int  94102 94110 94110 94109 94114 94114 94107 94115 94109 94117 ...
##  $ purchase_price    : num  79.16 9.72 9.65 24.74 11.9 ...
##  $ customer_uuid     : Factor w/ 13969 levels "0004fea6-6c44-41aa-8276-d496aa16b6c5",..: 3507 5439 1565 7947 3035 13807 9447 13809 1865 6529 ...
##  $ pickup_uuid       : Factor w/ 3154 levels "00199bb9-1b4e-4510-b1f2-7fb73f801034",..: 35 778 1137 3051 671 2541 2252 995 1882 1076 ...
##  $ avg_courier_rating: Factor w/ 9 levels "4.3","4.4","4.5",..: 6 6 5 8 8 5 6 8 6 8 ...
##  $ Status            : Factor w/ 1 level "Delivery_Complete": 1 1 1 1 1 1 1 1 1 1 ...
```

```r
head(delivery_data)
```

```
##   pickup_zipcode purchase_price                        customer_uuid
## 1          94102          79.16 3FA8C674-DAF0-4442-8F4B-AB8BE12A4DF8
## 2          94110           9.72 643C6802-D4DC-45AB-BF82-F3B0B76CE7BD
## 3          94110           9.65 1cbdc825-4dda-4865-8960-2e7e0e82e47e
## 4          94109          24.74 9239d697-ed75-4ded-b41f-99d76d43546a
## 5          94114          11.90 370fa764-e910-43f7-a98b-8f3f80fcc0fd
## 6          94114          39.71 fc956b21-2c60-4753-9eb6-891762beee91
##                            pickup_uuid avg_courier_rating
## 1 02886d08-b306-45c7-8f1c-a18ad9a1f607                4.8
## 2 407e4bed-9278-48d7-b096-f51cac9ed4c8                4.8
## 3 5f095be6-fc3d-4acc-9816-729d698aecb6                4.7
## 4 f80d0f48-43f6-4ea1-a2a6-7caa61d53ca1                  5
## 5 36c897e6-2a26-43bd-9b05-3933a1d13877                  5
## 6 d01c7cbf-dca3-4d5b-b8a9-1c14c9c4b96e                4.7
##              Status
## 1 Delivery_Complete
## 2 Delivery_Complete
## 3 Delivery_Complete
## 4 Delivery_Complete
## 5 Delivery_Complete
## 6 Delivery_Complete
```


You have data on 57139 transactions.  

###Frequency of Deliveries

First plot the number of deliveries by zipcode.  


```r
require(xtable)
```

```
## Loading required package: xtable
```

```r
require(ggplot2)
```

```
## Loading required package: ggplot2
```

```r
#find frequencies

        deliveries <- as.data.frame(table(delivery_data$pickup_zipcode))

        #rename columns descriptively
        colnames(deliveries)<-c("pickup_zipcode", "freq")

        deliveries$pickup_zipcode <- as.character(deliveries$pickup_zipcode)


   
##plot the data
        P1 <- ggplot(deliveries, aes(x=factor(pickup_zipcode), y=freq))+geom_point()
        P1<- P1+xlab("zipcode")+ylab("deliveries")+ggtitle("Deliveries versus Zipcode")
        P1<-P1+theme(axis.text.x = element_text(angle = 90, vjust = 0.5, hjust=1))

        print(P1)
```

<img src="delivery_challenge_files/figure-html/frequency of deliveries.png" title="plot of chunk frequency of deliveries" alt="plot of chunk frequency of deliveries" style="display: block; margin: auto;" />
The above shows the number of deliveries by zipcode. There's obviously substantial variation with the mean 1731.4848 deliveries per zipcode and a standard deviation of 2287.8665.  

The top zipcodes for deliveries are:  


```r
        ##create table output of top fifteen zip codes

        ##sort data by deliveries
        deliv_sorted<-deliveries[with(deliveries, order(-freq)),]        
        ##create html table
        delivtable<-xtable(t(deliv_sorted[1:15,]))
        ##print it
        print(delivtable, type="html", include.colnames=FALSE, html.table.attributes='class=mytable')
```

<!-- html table generated in R 3.1.1 by xtable 1.7-3 package -->
<!-- Fri Oct 24 12:22:41 2014 -->
<TABLE class=mytable>
  <TR> <TD align="right"> pickup_zipcode </TD> <TD> 94110 </TD> <TD> 94103 </TD> <TD> 94123 </TD> <TD> 94109 </TD> <TD> 94115 </TD> <TD> 94102 </TD> <TD> 94117 </TD> <TD> 94107 </TD> <TD> 94114 </TD> <TD> 94133 </TD> <TD> 94118 </TD> <TD> 94111 </TD> <TD> 94105 </TD> <TD> 94122 </TD> <TD> 94108 </TD> </TR>
  <TR> <TD align="right"> freq </TD> <TD> 8566 </TD> <TD> 6395 </TD> <TD> 6358 </TD> <TD> 5625 </TD> <TD> 4240 </TD> <TD> 3869 </TD> <TD> 3568 </TD> <TD> 3069 </TD> <TD> 2982 </TD> <TD> 1864 </TD> <TD> 1745 </TD> <TD> 1618 </TD> <TD> 1508 </TD> <TD> 1455 </TD> <TD>  987 </TD> </TR>
   </TABLE>

```r
        ##the next 15
        delivtable<-xtable(t(deliv_sorted[15:30,]))
        ##print it
        print(delivtable, type="html", include.colnames=FALSE, html.table.attributes='class=mytable')
```

<!-- html table generated in R 3.1.1 by xtable 1.7-3 package -->
<!-- Fri Oct 24 12:22:41 2014 -->
<TABLE class=mytable>
  <TR> <TD align="right"> pickup_zipcode </TD> <TD> 94108 </TD> <TD> 94132 </TD> <TD> 94121 </TD> <TD> 94131 </TD> <TD> 94116 </TD> <TD> 94104 </TD> <TD> 94158 </TD> <TD> 94112 </TD> <TD> 94127 </TD> <TD> 94124 </TD> <TD> 94539 </TD> <TD> 94903 </TD> <TD> 94129 </TD> <TD> 94134 </TD> <TD> 94101 </TD> <TD> 94199 </TD> </TR>
  <TR> <TD align="right"> freq </TD> <TD> 987 </TD> <TD> 986 </TD> <TD> 383 </TD> <TD> 371 </TD> <TD> 297 </TD> <TD> 272 </TD> <TD> 246 </TD> <TD> 243 </TD> <TD> 217 </TD> <TD> 129 </TD> <TD>  39 </TD> <TD>  30 </TD> <TD>  22 </TD> <TD>  16 </TD> <TD>  15 </TD> <TD>  11 </TD> </TR>
   </TABLE>

<style>
table.mytable {
  border: 10px;
  width: 80%;
  font-size: 14px;
  line-height: 20px;
  font-family: 'Trebuchet MS';
  font-weight: normal;
  color: black;
}
</style>

###Purchase Price by Zipcode

<img src="delivery_challenge_files/figure-html/mean deliveries1.png" title="plot of chunk mean deliveries" alt="plot of chunk mean deliveries" style="display: block; margin: auto;" />

```
## Loading required package: plyr
```

<img src="delivery_challenge_files/figure-html/mean deliveries2.png" title="plot of chunk mean deliveries" alt="plot of chunk mean deliveries" style="display: block; margin: auto;" />




