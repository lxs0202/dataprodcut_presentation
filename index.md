---
title       : Presetation on Iris prediction application
subtitle    : 
author      : Stuart Lyu
job         : Data Analyst
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
---

## Idea behind the application

1. Predict iris species based on sepal length, sepal width, petal legth,petal width.
2. Based on the R default Iris dataset.
3. Utilize Randomforest for predictive modelling.

--- .class #id 

## Building model

1. break data set into training set and test set.
2. apply randomforest on the training set 


```r
set.seed(1234)
ind <- sample(2, nrow(iris), replace=TRUE, prob=c(0.7, 0.3))
trainData <- iris[ind==1,]
testData <- iris[ind==2,]
library(randomForest)
rf <- randomForest(Species ~ ., data=trainData, ntree=100, proximity=TRUE)
```

--- .class #id 

## model performance

we apply the model to the test dataset and see the performance is pretty good.


```
##             
## irisPred     setosa versicolor virginica
##   setosa         10          0         0
##   versicolor      0         12         2
##   virginica       0          0        14
```

--- .class #id 

## link model to web input

we then link the web input to our built model to show the predicting result



```r
library(shiny)
predictiris <- function(sepal_length, sepal_width, petal_length,petal_width,model=rf){
  data <- data.frame(sepal_length)
  data$"Sepal.Width" <- sepal_width
  data$"Petal.Length" <- petal_length
  data$"Petal.Width" <- petal_width
  names(data)[1] <-"Sepal.Length";
  irisPred <- predict(rf, data)
  return(as.character(irisPred))
}


shinyServer(
  function(input, output) {
    output$inputValue <- renderPrint({paste(input$sepal_length,input$sepal_width,input$petal_length,input$petal_width, sep=",")})
    output$prediction <- renderPrint({predictiris(input$sepal_length,input$sepal_width,input$petal_length,input$petal_width)})
  }
  )
```


