---
title: Analizzare i dati con Azure Machine Learning
description: Usare Azure Machine Learning per creare un modello di Machine Learning predittivo basato sui dati archiviati in Azure Synapse.
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
ms.openlocfilehash: daaa5e3a075eee19ab473818ae3bd84d4bd3b32b
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683674"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Analizzare i dati con Azure Machine Learning
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

In questa esercitazione viene usato Azure Machine Learning per creare un modello di Machine Learning predittivo basato sui dati archiviati in Azure Synapse. Nello specifico, verrà compilata una campagna di marketing mirata di Adventure Works, il negozio di biciclette, per stimare la probabilità che un cliente acquisti una bicicletta o meno.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Prerequisiti
Per eseguire questa esercitazione, è necessario:

* Un pool SQL in cui siano precaricati dati di esempio AdventureWorksDW. Per effettuarne il provisioning, vedere [Creare un pool SQL](create-data-warehouse-portal.md) e scegliere di caricare i dati di esempio. Se si ha già un data warehouse ma non i dati di esempio, è possibile [caricare manualmente i dati di esempio](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="1-get-the-data"></a>1. Ottenere i dati
I dati sono disponibili nella visualizzazione dbo.vTargetMail nel database AdventureWorksDW. Per leggere i dati:

1. Accedere ad [Azure Machine Learning Studio](https://studio.azureml.net/) e fare clic sugli esperimenti personali.
2. Fare clic su **+NEW** (Aggiungi nuovo) nella parte inferiore sinistra della schermata e selezionare **Blank Experiment** (Esperimento vuoto).
3. Immettere un nome per l'esperimento: Marketing mirato.
4. Trascinare il modulo **Import data** sotto **Data Input and output** dal riquadro moduli nel pannello Canvas.
5. Nel riquadro Proprietà, specificare i dettagli del pool SQL.
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

Eseguire l'esperimento facendo clic su **Esegui** sotto l'area di disegno dell'esperimento.

![Eseguire l'esperimento](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png)

Dopo aver concluso con successo l’esperimento, per visualizzare i dati importati fare clic sulla porta di output nella parte inferiore del modulo Reader e selezionare **Visualizza**.

![Visualizzare i dati importati](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png)

## <a name="2-clean-the-data"></a>2. Pulire i dati
Per pulire i dati, eliminare alcune colonne non rilevanti per il modello. A tale scopo, effettuare l'operazione seguente:

1. Trascinare il modulo **Select Columns in Dataset** in **Trasformazione dati < Manipolazione** nel pannello Canvas. Connettere questo modulo al modulo **Import Data**.
2. Fare clic su **Launch column selector** nel riquadro Proprietà per specificare le colonne da eliminare.

   ![Colonne progetto](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png)
3. Escludere due colonne: CustomerAlternateKey e GeographyKey.

   ![Rimuovere le colonne non necessarie](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png)

## <a name="3-build-the-model"></a>3. Compilare il modello
Si suddivideranno i dati 80-20: l'80% per il training di un modello di Machine Learning e il 20% per testare il modello. Per questo problema di classificazione binaria si useranno gli algoritmi "Two-Class".

1. Trascinare il modulo **Split** nell'area di disegno.
2. Immettere 0,8 per Fraction of rows nel primo set di dati di output nel riquadro Proprietà.

   ![Dividere i dati in set di traning e di test.](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png)
3. Trascinare il modulo **Two-Class Boosted Decision Tree** nell'area di disegno.
4. Trascinare il modulo **Train Model** nel pannello Canvas e specificare gli input connettendosi ai moduli **Albero delle decisioni con boosting a due classi** (algoritmo ML) e **Split** (dati per eseguire il training dell'algoritmo). 

     ![Connettere il modulo Train Model](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png)
5. Fare clic su **Launch column selector** nel riquadro Properties. Selezionare la colonna **BikeBuyer** come colonna da stimare.

   ![Selezionare la colonna da stimare](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png)

## <a name="4-score-the-model"></a>4. Assegnare un punteggio al modello
A questo punto si verificheranno le prestazioni del modello sui dati di test. L'algoritmo scelto verrà confrontato con un algoritmo diverso per verificare quale offre prestazioni migliori.

1. Trascinare il modulo **Score Model** nel pannello Canvas e connetterlo ai moduli **Train Model** e **Split data**.

   ![Assegnare un punteggio al modello](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png)
2. Trascinare il **Two-Class Bayes Point Machine** nell'area di disegno dell'esperimento. Si confronteranno le prestazioni di questo algoritmo rispetto a Two-Class Boosted Decision Tree.
3. Copiare e incollare i moduli Train Model e Score Model nell'area di disegno.
4. Trascinare il modulo **Evaluate Model** nell'area di disegno per confrontare i due algoritmi.
5. **Eseguire** l'esperimento.

   ![Eseguire l'esperimento](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png)
6. Fare clic con il pulsante destro del mouse sulla porta di output del modulo Evaluate Model e scegliere Visualize.

   ![Visualizzare i risultati della valutazione](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png)

La metrica fornita include curva ROC, diagramma di precisione/recupero e curva lift. Esaminando la metrica, si noterà che il primo modello fornisce prestazioni migliori rispetto al secondo. Per vedere quali sono state le previsioni del primo modello, fare clic sulla porta di output di Score Model e scegliere Visualizza.

![Visualizzare i risultati di punteggio](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png)

Verranno visualizzate altre due colonne aggiunte al set di dati di test.

* Scored Probabilities: la probabilità che un cliente sia un acquirente di biciclette.
* Scored Labels: la classificazione eseguita dal modello – acquirente di biciclette (1) o non acquirente (0). La soglia di probabilità per le etichette è impostata su 50% e può essere modificata.

Confrontando la colonna BikeBuyer (effettivo) con Scored Labels (stima), è possibile vedere il livello di prestazioni del modello. Successivamente, è possibile usare questo modello per eseguire stime per i nuovi clienti e pubblicare il modello come servizio Web o scrivere i risultati in Azure Synapse.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla creazione di modelli di apprendimento automatico predittivi, fare riferimento a [Introduzione a Machine Learning in Azure](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml).