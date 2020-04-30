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
ms.date: 07/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ebea6117420ee6de67025dfd4cfba71e905cb9ec
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81453098"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Esercitazione: Creare un modello di clustering in R con Machine Learning Services del database SQL di Azure (anteprima)

Nella seconda parte di questa serie di esercitazioni in tre parti verrà creato un modello K-means in R per eseguire il clustering. Nella parte successiva di questa serie questo modello verrà distribuito in un database SQL con Machine Learning Services di Database SQL di Azure (anteprima).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

In questo articolo si apprenderà come:

> [!div class="checklist"]
> * Definire il numero di cluster per un algoritmo K-Means
> * Eseguire il clustering
> * Analizzare i risultati

Nella [prima parte](sql-database-tutorial-clustering-model-prepare-data.md) si è appreso come preparare i dati di un database SQL di Azure per eseguire il clustering.

Nella [terza parte](sql-database-tutorial-clustering-model-deploy.md) si apprenderà come creare una stored procedure in un database SQL di Azure in grado di eseguire il clustering in base ai nuovi dati.

## <a name="prerequisites"></a>Prerequisiti

* Per la seconda parte di questa esercitazione è necessario aver completato la [**prima parte**](sql-database-tutorial-clustering-model-prepare-data.md) e i relativi prerequisiti.

## <a name="define-the-number-of-clusters"></a>Definire il numero di cluster

Per raggruppare i dati dei clienti si userà l'algoritmo di clustering **K-Means**, una delle modalità di raggruppamento dei dati più semplici e note.
Per altre informazioni su K-Means, vedere la [guida completa all'algoritmo di clustering K-Means](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

L'algoritmo accetta due input: i dati stessi e un numero predefinito "*k*" che rappresenta il numero di cluster da generare.
L'output è *k* cluster con i dati di input partizionati tra i cluster.

Per determinare il numero di cluster che l'algoritmo deve usare, usare un tracciato della media della somma dei quadrati all'interno dei gruppi, per numero di cluster estratti. Il numero appropriato di cluster da usare è quello in corrispondenza della curva o del "gomito" del tracciato.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Grafico a gomito](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

In base al grafico, *k = 4* sembrerebbe un buon valore da provare. Questo valore di *k* raggruppa i clienti in quattro cluster.

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

Le medie dei quattro cluster si ottengono usando le variabili definite nella [parte uno](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers):

* *orderRatio* = rapporto ordini di reso (numero totale di ordini parzialmente o completamente resi rispetto al numero totale di ordini)
* *itemsRatio* = rapporto articoli resi (numero totale di articoli resi rispetto al numero di articoli acquistati)
* *monetaryRatio* = rapporto importi resi (importo monetario totale di articoli resi rispetto all'importo di articoli acquistati)
* *frequency* = frequenza dei resi

Il data mining con K-Means spesso richiede un'ulteriore analisi dei risultati e ulteriori passaggi per comprendere meglio ogni cluster, ma può fornire alcune indicazioni valide.
Ecco alcuni modi in cui possono essere interpretati i risultati:

* Il cluster 1 (quello più grande) sembra corrispondere a un gruppo di clienti che non sono attivi (tutti i valori sono pari a zero).
* Il cluster 3 sembra essere un gruppo che si distingue per il comportamento relativo ai resi.

## <a name="clean-up-resources"></a>Pulire le risorse

***Se non si intende proseguire con questa esercitazione***, eliminare il database tpcxbb_1gb dal server del database SQL di Azure.

Seguire questa procedura nel portale di Azure:

1. Dal menu a sinistra nel portale di Azure selezionare **Tutte le risorse** o **Database SQL**.
1. Nel campo **Filtra per nome** immettere **tpcxbb_1gb** e selezionare la sottoscrizione.
1. Selezionare il database **tpcxbb_1gb**.
1. Nella pagina **Panoramica** selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Nella seconda parte di questa serie di esercitazioni sono stati completati i passaggi seguenti:

* Definire il numero di cluster per un algoritmo K-Means
* Eseguire il clustering
* Analizzare i risultati

Per distribuire il modello di Machine Learning creato, seguire la terza parte di questa serie di esercitazioni:

> [!div class="nextstepaction"]
> [Esercitazione: Distribuire un modello di clustering in R con Machine Learning Services del database SQL di Azure (anteprima)](sql-database-tutorial-clustering-model-deploy.md)