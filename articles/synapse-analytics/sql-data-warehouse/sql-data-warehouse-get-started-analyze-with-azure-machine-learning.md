---
title: Analizzare i dati con Azure Machine Learning
description: Usare Azure Machine Learning per creare un modello di apprendimento automatico predittivo basato sui dati archiviati in Azure Synapse.Use Azure Machine Learning to build a predictive machine learning model based on data stored in Azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 74a6d1aecfc83ea68b9e30453056d231f4bf3e65
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416181"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Analizzare i dati con Azure Machine Learning
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Questa esercitazione usa Azure Machine Learning per creare un modello di apprendimento automatico predittivo basato sui dati archiviati in Azure Synapse.This tutorial uses Azure Machine Learning to build a predictive machine learning model based on data stored in Azure Synapse. Nello specifico, verrà compilata una campagna di marketing mirata di Adventure Works, il negozio di biciclette, per stimare la probabilità che un cliente acquisti una bicicletta o meno.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Prerequisiti
Per eseguire questa esercitazione, è necessario:

* Un pool SQL precaricato con i dati di esempio AdventureWorksDW. A tale scopo, vedere [Creare un pool SQL](create-data-warehouse-portal.md) e scegliere di caricare i dati di esempio. Se si ha già un data warehouse, ma non i dati di esempio, è possibile [caricare manualmente i dati di esempio](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="1-get-the-data"></a>1. Ottenere i dati
I dati sono disponibili nella visualizzazione dbo.vTargetMail nel database AdventureWorksDW. Per leggere i dati:

1. Accedere ad [Azure Machine Learning Studio](https://studio.azureml.net/) e fare clic sugli esperimenti personali.
2. Fai clic su **NUOVO** in basso a sinistra dello schermo e seleziona **Esperimento vuoto.**
3. Immettere un nome per l'esperimento: Marketing mirato.
4. Trascinare il modulo **Importa dati** in Input dati **e output** dal riquadro dei moduli nell'area di disegno.
5. Specificare i dettagli del pool SQL nel riquadro Proprietà.
6. Specificare la **query** di database per leggere i dati di interesse.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Eseguire l'esperimento facendo clic su **Esegui** nell'area di disegno dell'esperimento.

![Eseguire l'esperimento](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png)

Dopo aver concluso con successo l’esperimento, per visualizzare i dati importati fare clic sulla porta di output nella parte inferiore del modulo Reader e selezionare **Visualizza**.

![Visualizzare i dati importati](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png)

## <a name="2-clean-the-data"></a>2. Pulire i dati
Per pulire i dati, eliminare alcune colonne non rilevanti per il modello. A tale scopo, effettuare l'operazione seguente:

1. Trascinare il modulo **Seleziona colonne nel set di dati** in **Trasformazione dati < Manipolazione** nell'area di disegno. Collegare questo modulo al modulo **Importa dati.**
2. Fare clic su **Launch column selector** nel riquadro Proprietà per specificare le colonne da eliminare.

   ![Project Columns](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png)
3. Escludere due colonne: CustomerAlternateKey e GeographyKey.

   ![Rimuovere le colonne non necessarie](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png)

## <a name="3-build-the-model"></a>3. Costruire il modello
Si suddivideranno i dati 80-20: 80% per il training di un modello di Machine Learning e 20% per testare il modello. Utilizzeremo gli algoritmi "Due classi" per questo problema di classificazione binaria.

1. Trascinare il modulo **Split** nell'area di disegno.
2. Nel riquadro delle proprietà immettere 0,8 per Frazione di righe nel primo set di dati di output.

   ![Dividere i dati in set di traning e di test.](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png)
3. Trascinare il modulo **Two-Class Boosted Decision Tree** nell'area di disegno.
4. Trascinare il modulo **Train Model** nell'area di disegno e specificare gli input collegandolo ai moduli **Albero decisionale potenziato** a due classi (algoritmo ML) e **Dividi** (dati su cui eseguire il training dell'algoritmo). 

     ![Connettere il modulo Train Model](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png)
5. Fare clic su **Launch column selector** nel riquadro Properties. Selezionare la colonna **BikeBuyer** come colonna da stimare.

   ![Selezionare una colonna da stimare](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png)

## <a name="4-score-the-model"></a>4. Segnare il modello
A questo punto si verificheranno le prestazioni del modello sui dati di test. L'algoritmo scelto verrà confrontato con un algoritmo diverso per verificare quale offre prestazioni migliori.

1. Trascinare il modulo Modello di **partitura** nell'area di disegno e connetterlo ai moduli **Modello di training** e **Dividi dati.**

   ![Assegnare un punteggio al modello](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png)
2. Trascinare il **Two-Class Bayes Point Machine** nell'area di disegno dell'esperimento. Si confronteranno le prestazioni di questo algoritmo rispetto a Two-Class Boosted Decision Tree.
3. Copiare e incollare i moduli Train Model e Score Model nell'area di disegno.
4. Trascinare il modulo **Evaluate Model** nell'area di disegno per confrontare i due algoritmi.
5. **Eseguire** l'esperimento.

   ![Eseguire l'esperimento](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png)
6. Fare clic con il pulsante destro del mouse sulla porta di output del modulo Evaluate Model e scegliere Visualize.

   ![Visualizzare i risultati della valutazione](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png)

Le metriche fornite sono la curva ROC, il diagramma di richiamo di precisione e la curva di sollevamento. Esaminando la metrica, si noterà che il primo modello fornisce prestazioni migliori rispetto al secondo. Per esaminare ciò che il primo modello ha previsto, fare clic sulla porta di output del modello di punteggio e fare clic su Visualizza.

![Visualizzare i risultati di punteggio](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png)

Verranno visualizzate altre due colonne aggiunte al set di dati di test.

* Scored Probabilities: la probabilità che un cliente sia un acquirente di biciclette.
* Scored Labels: la classificazione eseguita dal modello – acquirente di biciclette (1) o non acquirente (0). La soglia di probabilità per le etichette è impostata su 50% e può essere modificata.

Confrontando la colonna BikeBuyer (effettivo) con Scored Labels (stima), è possibile vedere il livello di prestazioni del modello. Successivamente, è possibile usare questo modello per eseguire stime per i nuovi clienti e pubblicare questo modello come servizio Web o scrivere i risultati in Azure Synapse.Next, you can use this model to make predictions for new customers and publish this model as a web service or write results back to Azure Synapse.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla creazione di modelli di apprendimento automatico predittivi, fare riferimento a [Introduzione a Machine Learning in Azure](https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/).