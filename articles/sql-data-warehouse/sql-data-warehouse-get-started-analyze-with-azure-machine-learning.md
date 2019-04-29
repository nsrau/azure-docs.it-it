---
title: Analizzare i dati con Azure Machine Learning | Documentazione di Microsoft
description: Usare Azure Machine Learning per creare un modello predittivo di apprendimento automatico basato sui dati archiviati in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 03/22/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 7f9500adc6871c4c9f81c32bf456bc36cf91db4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61439978"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Analizzare i dati con Azure Machine Learning
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Questa esercitazione usa Azure Machine Learning per creare un modello predittivo di apprendimento automatico basato sui dati archiviati in Azure SQL Data Warehouse. Nello specifico, verrà compilata una campagna di marketing mirata di Adventure Works, il negozio di biciclette, per stimare la probabilità che un cliente acquisti una bicicletta o meno.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Prerequisiti
Per eseguire questa esercitazione, è necessario:

* Un'istanza di SQL Data Warehouse in cui sia precaricato il database di esempio AdventureWorksDW. Per effettuarne il provisioning, vedere [Creare un Azure SQL Data Warehouse][Create a SQL Data Warehouse] e scegliere di caricare i dati di esempio. Se si ha già un data warehouse, ma non i dati di esempio, è possibile [caricare manualmente i dati di esempio][load sample data manually].

## <a name="1-get-the-data"></a>1. Ottenere i dati
I dati sono disponibili nella visualizzazione dbo.vTargetMail nel database AdventureWorksDW. Per leggere i dati:

1. Accedere ad [Azure Machine Learning Studio][Azure Machine Learning studio] e fare clic sugli esperimenti personali.
2. Fare clic su **+ nuovo** nella parte inferiore sinistra della schermata e selezionare **esperimento vuoto**.
3. Immettere un nome per l'esperimento: Marketing mirato.
4. Trascinare il **Importa dati** modulo **dati di Input e output** dal riquadro dei moduli nell'area di disegno.
5. Nel riquadro Properties specificare i dettagli del database SQL Data Warehouse.
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
![Eseguire l'esperimento][1]

Dopo aver concluso con successo l’esperimento, per visualizzare i dati importati fare clic sulla porta di output nella parte inferiore del modulo Reader e selezionare **Visualizza**.
![Visualizzare i dati importati][3]

## <a name="2-clean-the-data"></a>2. Pulire i dati
Per pulire i dati, eliminare alcune colonne non rilevanti per il modello. A tale scopo, effettuare l'operazione seguente:

1. Trascinare il **Select Columns in Dataset** modulo **trasformazione dei dati < manipolazione** nell'area di disegno. Connettere questo modulo per il **Import Data** modulo.
2. Fare clic su **Launch column selector** nel riquadro Proprietà per specificare le colonne da eliminare.
   ![Project Columns][4]
3. Escludere due colonne: CustomerAlternateKey e GeographyKey.
   ![Rimuovere le colonne non necessarie][5]

## <a name="3-build-the-model"></a>3. Compilare il modello
Si suddivideranno i dati 80-20: l'80% per il training di un modello di Machine Learning e il 20% per testare il modello. Per questo problema di classificazione binaria si useranno gli algoritmi "Two-Class".

1. Trascinare il modulo **Split** nell'area di disegno.
2. Nel riquadro proprietà, immettere 0,8 per Fraction of rows nel primo set di dati di output.
   ![Dividere i dati in set di traning e di test][6]
3. Trascinare il modulo **Two-Class Boosted Decision Tree** nell'area di disegno.
4. Trascinare il **Train Model** modulo nell'area di disegno e specificare gli input tramite la connessione al **Two-Class Boosted Decision Tree** (algoritmo di Machine Learning) e **Split** (dati per il training di moduli di algoritmi su). 
     ![Connettere il modulo Train Model][7]
5. Fare clic su **Launch column selector** nel riquadro Properties. Selezionare la colonna **BikeBuyer** come colonna da stimare.
   ![Selezionare una colonna da stimare][8]

## <a name="4-score-the-model"></a>4. Assegnare un punteggio al modello
A questo punto si verificheranno le prestazioni del modello sui dati di test. L'algoritmo scelto verrà confrontato con un algoritmo diverso per verificare quale offre prestazioni migliori.

1. Trascinare **Score Model** modulo nell'area di disegno e connetterlo al **Train Model** e **Split Data** moduli.
   ![Il modello di punteggio][9]
2. Trascinare il **Two-Class Bayes Point Machine** nell'area di disegno dell'esperimento. Si confronteranno le prestazioni di questo algoritmo rispetto a Two-Class Boosted Decision Tree.
3. Copiare e incollare i moduli Train Model e Score Model nell'area di disegno.
4. Trascinare il modulo **Evaluate Model** nell'area di disegno per confrontare i due algoritmi.
5. **Eseguire** l'esperimento.
   ![Eseguire l'esperimento][10]
6. Fare clic con il pulsante destro del mouse sulla porta di output del modulo Evaluate Model e scegliere Visualize.
   ![Visualizzare i risultati della valutazione][11]

La metrica fornita include curva ROC, curva di precisione/recupero e curva di accuratezza. Esaminando la metrica, si noterà che il primo modello fornisce prestazioni migliori rispetto al secondo. Per vedere i quali sono state le previsioni del primo modello, fare clic sulla porta di output di Score Model e scegliere Visualize.
![Visualizzare i risultati di punteggio][12]

Verranno visualizzate altre due colonne aggiunte al set di dati di test.

* Scored Probabilities: la probabilità che un cliente sia un acquirente di biciclette.
* Scored Labels: la classificazione eseguita dal modello – acquirente di biciclette (1) o non acquirente (0). La soglia di probabilità per le etichette è impostata su 50% e può essere modificata.

Confrontando la colonna BikeBuyer (effettivo) con Scored Labels (stima), è possibile vedere il livello di prestazioni del modello. Come passaggi successivi è possibile usare questo modello per eseguire stime per i nuovi clienti e pubblicare il modello come un servizio Web o scrivere i risultati in SQL Data Warehouse.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla creazione di modelli di apprendimento automatico predittivi, fare riferimento a [Introduzione a Machine Learning in Azure][Introduction to Machine Learning on Azure].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2-visualize-new.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png


<!--Article references-->
[Azure Machine Learning studio]:https://studio.azureml.net/
[Introduction to Machine Learning on Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[load sample data manually]: sql-data-warehouse-load-sample-databases.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
