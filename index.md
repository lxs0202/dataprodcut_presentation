---
title       : Presetation on Iris prediction application
subtitle    : 
author      : Stuart Lyu
job         : Data Analyst
framework   : html5slides       # {io2012, html5slides, shower, dzslides, ...}
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

--- .class #id 


## web front end

our web front end is very straight forward. Just inputs of 4 Iris attributes and a submit button



```r
library(shiny)
shinyUI(
  pageWithSidebar(
    # Application title
    headerPanel("Iris prediction"),
    sidebarPanel(
      numericInput('sepal_length', 'Sepal Length', 1, min = 0.1, max =10 , step = 0.1),
      numericInput('sepal_width', 'Sepal Width', 1, min = 0.1, max =10 , step = 0.1),
      numericInput('petal_length', 'Petal Length', 1, min = 0.1, max =10 , step = 0.1),
      numericInput('petal_width', 'Petal Width', 1, min = 0.1, max =10 , step = 0.1),
      submitButton('Submit')
    ),
    mainPanel(
      h3('Results of prediction'),
      h4('You entered'),
      verbatimTextOutput("inputValue"),
      h4('Which resulted in a prediction of '),
      verbatimTextOutput("prediction"),
      h4('User Guide'),
      verbatimTextOutput("instruction")
    )
  )
)
```

<!--html_preserve--><div class="container-fluid">
<div class="row-fluid">
<div class="span12" style="padding: 10px 0px;">
<h1>Iris prediction</h1>
</div>
</div>
<div class="row-fluid">
<div class="span4">
<form class="well">
<label for="sepal_length">Sepal Length</label>
<input id="sepal_length" type="number" value="1" min="0.1" max="10" step="0.1"/>
<label for="sepal_width">Sepal Width</label>
<input id="sepal_width" type="number" value="1" min="0.1" max="10" step="0.1"/>
<label for="petal_length">Petal Length</label>
<input id="petal_length" type="number" value="1" min="0.1" max="10" step="0.1"/>
<label for="petal_width">Petal Width</label>
<input id="petal_width" type="number" value="1" min="0.1" max="10" step="0.1"/>
<div>
<button type="submit" class="btn btn-primary">Submit</button>
</div>
</form>
</div>
<div class="span8">
<h3>Results of prediction</h3>
<h4>You entered</h4>
<pre id="inputValue" class="shiny-text-output"></pre>
<h4>Which resulted in a prediction of </h4>
<pre id="prediction" class="shiny-text-output"></pre>
<h4>User Guide</h4>
<pre id="instruction" class="shiny-text-output"></pre>
</div>
</div>
</div><!--/html_preserve-->

