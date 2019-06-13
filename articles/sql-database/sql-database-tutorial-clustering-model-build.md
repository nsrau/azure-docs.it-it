---
title: 'Esercitazione: Creare un modello di clustering in R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Nella seconda parte di questa serie di esercitazioni in tre parti verrà creato un modello K-means per eseguire il clustering in R con Machine Learning Services del database SQL di Azure (anteprima).
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
ms.date: 05/17/2019
ms.openlocfilehash: 12738b63be92420c5f3afea6c133522cbd97f849
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420229"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Esercitazione: Creare un modello di clustering in R con Machine Learning Services del database SQL di Azure (anteprima)

Nella seconda parte di questa serie di esercitazioni in tre parti verrà creato un modello K-means per eseguire il clustering in R con Machine Learning Services del database SQL di Azure (anteprima).

L'articolo spiega come:

> [!div class="checklist"]
> * Definire il numero di cluster per un algoritmo K-means
> * Eseguire il clustering
> * Analizzare i risultati

Nella [prima parte](sql-database-tutorial-clustering-model-prepare-data.md) si è appreso come preparare i dati di un database SQL di Azure per eseguire il clustering in R.

Nella [terza parte](sql-database-tutorial-clustering-model-deploy.md) si è appreso come creare una stored procedure in un database SQL di Azure in grado di eseguire il clustering in base ai nuovi dati.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Per la seconda parte di questa esercitazione è necessario aver completato la [**prima parte**](sql-database-tutorial-clustering-model-prepare-data.md) e i relativi prerequisiti.

## <a name="define-the-number-of-clusters"></a>Definire il numero di cluster

Per inserire in cluster i dati dei clienti, si userà l'algoritmo di clustering **K-means**, uno dei modi più semplici e noti per raggruppare dati.
Per altre informazioni su K-means, vedere la [guida completa all'algoritmo di clustering K-means](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

L'algoritmo accetta due input: I dati stessi e un numero predefinito "*k*" che rappresenta il numero di cluster da generare.
L'output è costituito da *k* cluster con i dati di input ripartiti tra i cluster.

Per determinare il numero di cluster che l'algoritmo deve usare, usare un tracciato della somma dei quadrati all'interno dei gruppi, per numero di cluster estratti. Il numero appropriato di cluster da usare è indicato in corrispondenza dell'incrocio a gomito del tracciato.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Grafico a gomito](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

In base al grafico, sembra che *k = 4* sia un valore appropriato per effettuare un tentativo. Con il valore *k* i clienti verranno raggruppati in quattro cluster.

## <a name="perform-clustering"></a>Eseguire il clustering

Nello script R seguente si userà la funzione **rxKmeans**, che corrisponde alla funzione K-means nel pacchetto RevoScaleR.

```r
# Output table to hold the customer group mappings.
# This is a table where the cluster mappings will be saved in the database.
return_cluster = RxSqlServerData(table = "return_cluster", connectionString = connStr);
# Set the seed for the random number generator for predictability
set.seed(10);
# Generate clusters using rxKmeans and output key / cluster to a table in SQL database
# called return_cluster
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters=4,
                   outFile=return_cluster,
                   outColName="cluster",
                   extraVarsToWrite=c("customer"),
                   overwrite=TRUE);

# Read the customer returns cluster table from the database
customer_cluster <- rxDataStep(return_cluster);
```

## <a name="analyze-the-results"></a>Analizzare i risultati

A questo punto, dopo aver eseguito il clustering con K-means, il passaggio successivo consiste nell'analizzare il risultato e verificare se le informazioni sono utili dal punto di vista pratico.

L'oggetto **clust** contiene i risultati del clustering K-means.

```r
#Look at the clustering details to analyze results
clust
```

```results
Call:
rxKmeans(formula = ~orderRatio + itemsRatio + monetaryRatio + 
    frequency, data = customer_returns, outFile = return_cluster, 
    outColName = "cluster", extraVarsToWrite = c("customer"), 
    overwrite = TRUE, numClusters = 4)
Data: customer_returns
Number of valid observations: 37336
Number of missing observations: 0 
Clustering algorithm:  

K-means clustering with 4 clusters of sizes 31675, 671, 2851, 2139
Cluster means:
   orderRatio   itemsRatio monetaryRatio frequency
1 0.000000000 0.0000000000    0.00000000  0.000000
2 0.007451565 0.0000000000    0.04449653  4.271237
3 1.008067345 0.2707821817    0.49515232  1.031568
4 0.000000000 0.0004675082    0.10858272  1.186068
Within cluster sum of squares by cluster:
         1          2          3          4
    0.0000  1329.0160 18561.3157   363.2188
```

Per specificare i quattro cluster, vengono usate le variabili definite nella [prima parte](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers):

* *orderRatio* = rapporto ordini restituiti (numero totale di ordini parzialmente o totalmente restituiti rispetto al numero totale di ordini)
* *itemsRatio* = rapporto articoli restituiti (numero totale di articoli restituiti rispetto al numero di articoli acquistati)
* *monetaryRatio* = rapporto importi restituiti (totale importo monetario di articoli restituiti rispetto all'importo di articoli acquistati)
* *frequency* = frequenza di restituzione

Per il data mining con K-means sono spesso richiesti un'ulteriore analisi dei risultati, nonché ulteriori passaggi per comprendere meglio ogni cluster, ma tale approccio consente di ottenere alcuni lead validi.
Ecco alcuni modi in cui è possibile interpretare questi risultati:

* Il cluster 1 (quello più grande) sembra corrispondere a un gruppo di clienti che non sono attivi (tutti i valori sono pari a zero).
* Il cluster 3 sembra essere un gruppo che si distingue in termini di comportamento di restituzione.

## <a name="clean-up-resources"></a>Pulire le risorse

***Se non si intende proseguire con questa esercitazione***, eliminare il database tpcxbb_1gb dal server del database SQL di Azure.

Seguire questa procedura nel portale di Azure:

1. Dal menu a sinistra nel portale di Azure selezionare **Tutte le risorse** o **Database SQL**.
1. Nel campo **Filtra per nome** immettere **tpcxbb_1gb** e selezionare la sottoscrizione.
1. Selezionare il database **tpcxbb_1gb**.
1. Nella pagina **Panoramica** selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Nella seconda parte di questa serie di esercitazioni sono stati completati questi passaggi:

* Definire il numero di cluster per un algoritmo K-means
* Eseguire il clustering
* Analizzare i risultati

Per distribuire il modello di Machine Learning creato, seguire la terza parte di questa serie di esercitazioni:

> [!div class="nextstepaction"]
> [Esercitazione: Distribuire un modello di clustering in R con Machine Learning Services del database SQL di Azure (anteprima)](sql-database-tutorial-clustering-model-deploy.md)