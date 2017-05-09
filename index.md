---
title       : WK4Project
subtitle    : Diamond price estimation based on carat weight
author      : blitz95
job         : 
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
---


<style type = "text/css">

code.r{
    font-size: 9px;
}

</style>



## Data Products  - WK4 Project   
&nbsp;
#### This project provides a very simply cost estimator for diamonds based on carat weight alone.  While there are other factors that contribute to the cost of a diamond this estimator uses only carat weight.

- The cost is estimated in Singapore dollars.
- The estimator uses a Shiny application with a slider selector. 
- The data is from the UsingR packages, "diamonds" data set.
- The data is fit with a simple linear model.

#### When the user selects a carat weight, the plot updates showing the carat weight/price point of the carat weight selected.  The esimated price in Singapore dollars is shown below the plot. If carat weights higher than 

--- .class #id 

## User Interface Code (ui.R) code  

##### The user interface code controls the user interface


```r
library(shiny)
library(UsingR, quietly = TRUE)
data(diamonds)

# Define UI for application that predicts the price of a diamond based on weight
shinyUI(fluidPage(
  # Application title
  titlePanel("Predict price of diamond based on weight of diamond in carats"),
  # Sidebar with a slider input for carats
  sidebarLayout(
    sidebarPanel(
      sliderInput("sliderCarat","What is the weight of the diamond in carats?", 0.20, 5.01,value = 2.5)
    ),
    # Show a plot of Price verses Weight in carats"
    mainPanel(
        h4("This program fits a linear model to a data set of prices verses carat weights of diamonds"),
        h4("The user selects a carat weight, the using the model, a prediction of Singapore dollar cost is presented"),
        h5("Due to the size of the data set, the refresh may take a few seconds."),  
      plotOutput("plot"),
      h4("Predicted price in Singapore Dollars (SIN$) from Model Fit:"),
      textOutput("pred")
        )
    )
))
```

---. class #id

## Server Code (server.R) code

#### The server code controls what the app does.  For this application, the server code fits a simple linear model to the diamonds data, takes the input from the slider, uses a predict function to estimate a prediction, plots the result, and provides a prediction of price in Singapore dollars.


```r
library(UsingR, quietly = TRUE)
data(diamonds)
library(shiny)


# Define server logic required to predict cost of diamond from based on weight input"
shinyServer(function(input, output) {
    model <- lm(price ~ carat, data = diamonds)
    
    modelpred <- reactive({
        caratInput <- input$sliderCarat
        predict(model, newdata = data.frame(carat = caratInput))
    })    

    output$plot <- renderPlot({
        CaratInput <- input$sliderCarat
        
        plot(diamonds$carat, diamonds$price, xlab = "Weight (Carats)", 
             ylab = "Price (SIN $)", bty = "n", pch = 16, col=rgb(0,0,0,alpha=0.1),
             xlim = c(0,5), ylim = c(300,35000))

        abline(model, col = "red", lwd = 2)
            
        legend(25, 250, c("Model Prediction"), pch = 16,
               col = c("red"), bty = "n", cex = 1.2)
        points(CaratInput, modelpred(), col = "red", pch = 10, cex = 2.5)
    })

    output$pred <- renderText({
        paste("SIN $", format(round(modelpred(), 2), big.mark = ","))
    })
})
```

---. class #id

## Resulting application for use

#### The prediction model plot covers carat weights from 0 to 5.01 carats.  However, due to a lack of data for  less than 0.3 carats or greater than 3 carats, the uncertainty in estimates from this simple linear model increases for carat weights less than 0.3 carats and greater than 3 carats. 
&nbsp;
#### The interactive model can be accessed at the link below.
&nbsp;
&nbsp;
#### [WK4Project- Diamond price based on carat weight](https://blitz95.shinyapps.io/WK4Project/)


