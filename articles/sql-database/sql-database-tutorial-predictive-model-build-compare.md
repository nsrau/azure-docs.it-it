---
title: 'Esercitazione: Eseguire il training di modelli predittivi in R e confrontarli'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Nella seconda parte di questa serie di esercitazioni in tre parti verranno creati due modelli predittivi in R con i Machine Learning Services del database SQL di Azure (anteprima) e quindi si selezionerà il modello più accurato.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 07/26/2019
ms.openlocfilehash: 2c85a378dc219e8af1b6458344ee4dba0fa73e68
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596805"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Esercitazione: Creare ed eseguire un modello predittivo in R con i Machine Learning Services del database SQL di Azure (anteprima)

Nella seconda parte di questa serie di esercitazioni in tre parti verranno creati due modelli predittivi in R e quindi si selezionerà il modello più accurato. Nella parte successiva di questa serie questo modello verrà distribuito in un database SQL con Machine Learning Services di Database SQL di Azure (anteprima).

L'articolo spiega come:

> [!div class="checklist"]
> * Eseguire il training di due modelli di Machine Learning
> * Eseguire stime da entrambi i modelli
> * Confrontare i risultati per scegliere il modello più accurato

Nella [prima parte](sql-database-tutorial-predictive-model-prepare-data.md) si è appreso come importare un database di esempio e quindi preparare i dati da usare per il training di un modello predittivo in R.

Nelle [terza parte](sql-database-tutorial-predictive-model-deploy.md) si spiegherà come archiviare il modello in un database e quindi creare le stored procedure dagli script R sviluppati nella prima e nella seconda parte. Le stored procedure verranno eseguite in un database SQL per effettuare stime basate sui nuovi dati.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Per la seconda parte di questa esercitazione è necessario aver completato la [**prima parte**](sql-database-tutorial-predictive-model-prepare-data.md) e i relativi prerequisiti.

## <a name="train-two-models"></a>Eseguire il training di due modelli

Per trovare il modello migliore per i dati di noleggio di sci, creare due modelli diversi (regressione lineare e albero delle decisioni) e vedere quale esegue le stime nel modo più accurato. Si userà il frame di dati `rentaldata` creato nella prima parte di questa serie.

```r
#First, split the dataset into two different sets:
# one for training the model and the other for validating it
train_data = rentaldata[rentaldata$Year < 2015,];
test_data  = rentaldata[rentaldata$Year == 2015,];

#Use the RentalCount column to check the quality of the prediction against actual values
actual_counts <- test_data$RentalCount;

#Model 1: Use rxLinMod to create a linear regression model, trained with the training data set
model_linmod <- rxLinMod(RentalCount ~  Month + Day + WeekDay + Snow + Holiday, data = train_data);

#Model 2: Use rxDTree to create a decision tree model, trained with the training data set
model_dtree  <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = train_data);
```

## <a name="make-predictions-from-both-models"></a>Eseguire stime da entrambi i modelli

Usare una funzione di stima per stimare i conteggi dei noleggi con ogni modello con training.

```r
#Use both models to make predictions using the test data set.
predict_linmod <- rxPredict(model_linmod, test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

predict_dtree  <- rxPredict(model_dtree,  test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

#To verify it worked, look at the top rows of the two prediction data sets.
head(predict_linmod);
head(predict_dtree);
```

```results
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1         27.45858          42       2     11     4      0       0
2        387.29344         360       3     29     1      0       0
3         16.37349          20       4     22     4      0       0
4         31.07058          42       3      6     6      0       0
5        463.97263         405       2     28     7      1       0
6        102.21695          38       1     12     2      1       0
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1          40.0000          42       2     11     4      0       0
2         332.5714         360       3     29     1      0       0
3          27.7500          20       4     22     4      0       0
4          34.2500          42       3      6     6      0       0
5         645.7059         405       2     28     7      1       0
6          40.0000          38       1     12     2      1       0
```

## <a name="compare-the-results"></a>Confrontare i risultati

A questo punto si desidera vedere quale dei modelli offre le stime migliori. Un modo semplice e rapido per eseguire questa operazione è usare una funzione di tracciamento di base per visualizzare la differenza tra i valori effettivi nei dati di training e i valori stimati.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![Confronto tra i due modelli](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

Sembra che il modello di albero delle decisioni sia il più accurato dei due modelli.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende proseguire con questa esercitazione, eliminare il database TutorialDB dal server del database SQL di Azure.

Seguire questa procedura nel portale di Azure:

1. Dal menu a sinistra nel portale di Azure selezionare **Tutte le risorse** o **Database SQL**.
1. Nel campo **Filtra per nome...** immettere **TutorialDB** e selezionare la sottoscrizione.
1. Selezionare il proprio database TutorialDB.
1. Nella pagina **Panoramica** selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Nella seconda parte di questa serie di esercitazioni sono stati completati questi passaggi:

* Eseguire il training di due modelli di Machine Learning
* Eseguire stime da entrambi i modelli
* Confrontare i risultati per scegliere il modello più accurato

Per distribuire il modello di Machine Learning creato, seguire la terza parte di questa serie di esercitazioni:

> [!div class="nextstepaction"]
> [Esercitazione: Distribuire un modello predittivo in R con i Machine Learning Services del database SQL di Azure (anteprima)](sql-database-tutorial-predictive-model-deploy.md)
