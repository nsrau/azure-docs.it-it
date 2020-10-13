---
title: Analizzare i dati con Azure Machine Learning
description: Usare Azure Machine Learning per creare un modello di Machine Learning predittivo basato sui dati archiviati in Azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 9cf65b2fdeb7faa03b950593db86dd32a4ef91a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86495739"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Analizzare i dati con Azure Machine Learning

Questa esercitazione usa la [finestra di progettazione di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-designer) per creare un modello di apprendimento automatico predittivo. Il modello è basato sui dati archiviati in sinapsi di Azure. Lo scenario per l'esercitazione consiste nel prevedere se è probabile che un cliente acquisti una bicicletta o meno, quindi Adventure Works, il Bike Shop, può creare una campagna di marketing mirata.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questa esercitazione, è necessario:

* un pool SQL pre-caricato con i dati di esempio AdventureWorksDW. Per eseguire il provisioning di questo pool SQL, vedere [creare un pool SQL](create-data-warehouse-portal.md) e scegliere di caricare i dati di esempio. Se si dispone già di un data warehouse ma non si dispone di dati di esempio, è possibile [caricare manualmente i dati di esempio](load-data-from-azure-blob-storage-using-polybase.md).
* un'area di lavoro di Azure Machine Learning. Seguire [questa esercitazione](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) per crearne uno nuovo.

## <a name="get-the-data"></a>Ottenere i dati

I dati usati si trova nella vista dbo. vTargetMail in AdventureWorksDW. Per usare l'archivio dati in questa esercitazione, i dati vengono prima esportati nell'account Azure Data Lake Storage perché la sinapsi di Azure attualmente non supporta i set di dati. Azure Data Factory può essere usato per esportare i dati dal data warehouse a Azure Data Lake Storage usando l' [attività di copia](https://docs.microsoft.com/azure/data-factory/copy-activity-overview). Utilizzare la query seguente per l'importazione:

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

Una volta che i dati sono disponibili in Azure Data Lake Storage, per [connettersi ai servizi di archiviazione di Azure](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)vengono usati gli archivi dati in Azure Machine Learning. Attenersi alla procedura seguente per creare un archivio dati e un set di dati corrispondente:

1. Avviare Azure Machine Learning Studio da portale di Azure o accedere a [Azure Machine Learning Studio](https://ml.azure.com/).

1. Fare clic su **archivi dati** nel riquadro a sinistra nella sezione **Gestisci** e quindi fare clic su **nuovo archivio dati**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/datastores-tab.png" alt-text="Screenshot del riquadro sinistro dell'interfaccia Azure Machine Learning":::

1. Specificare un nome per l'archivio dati, selezionare il tipo come ' archiviazione BLOB di Azure ', specificare la posizione e le credenziali. quindi scegliere **Crea**.

1. Fare quindi clic su **set di impostazioni** nel riquadro a sinistra nella sezione **Asset** . Selezionare **Crea set di dati** con l'opzione **dall'archivio dati**.

1. Specificare il nome del set di dati e selezionare il tipo da **tabulare**. Quindi, fare clic su **Avanti** per spostarsi in avanti.

1. Nella **sezione selezionare o creare un archivio dati**selezionare l'opzione data **Store creato in precedenza**. Selezionare l'archivio dati creato in precedenza. Fare clic su Avanti e specificare il percorso e le impostazioni del file. Assicurarsi di specificare l'intestazione di colonna se i file ne contengono uno.

1. Infine, fare clic su **Crea** per creare il set di dati.

## <a name="configure-designer-experiment"></a>Configurare l'esperimento di progettazione

Eseguire quindi i passaggi seguenti per la configurazione della finestra di progettazione:

1. Fare clic sulla scheda **finestra di progettazione** nel riquadro a sinistra nella sezione **autore** .

1. Selezionare i **moduli predefiniti di facile utilizzo** per creare una nuova pipeline.

1. Nel riquadro impostazioni a destra specificare il nome della pipeline.

1. Selezionare anche un cluster di calcolo di destinazione per l'intero esperimento nel pulsante impostazioni per un cluster di cui è stato effettuato il provisioning in precedenza. Chiudere il riquadro Impostazioni.

## <a name="import-the-data"></a>Importare i dati

1. Selezionare i **set di impostazioni** sottoscheda nel riquadro sinistro sotto la casella di ricerca.

1. Trascinare il set di dati creato in precedenza nell'area di disegno.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/import-dataset.png" alt-text="Screenshot del riquadro sinistro dell'interfaccia Azure Machine Learning":::

## <a name="clean-the-data"></a>Eseguire la pulizia dei dati

Per pulire i dati, eliminare le colonne che non sono rilevanti per il modello. Seguire la procedura descritta di seguito:

1. Selezionare i **moduli** sottoscheda nel riquadro sinistro.

1. Trascinare il modulo **Select Columns in Dataset** in **Trasformazione dati < Manipolazione** nel pannello Canvas. Connettere questo modulo al modulo **DataSet** .

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-in.png" alt-text="Screenshot del riquadro sinistro dell'interfaccia Azure Machine Learning" lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-out.png":::

1. Per aprire il riquadro proprietà, fare clic sul modulo. Fare clic su Modifica colonna per specificare le colonne che si desidera eliminare.

1. Escludere due colonne: CustomerAlternateKey e GeographyKey. Fare clic su **Save** (Salva).

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/drop-columns.png" alt-text="Screenshot del riquadro sinistro dell'interfaccia Azure Machine Learning":::

## <a name="build-the-model"></a>Creare il modello

I dati sono suddivisi 80-20:80% per eseguire il training di un modello di apprendimento automatico e il 20% per testare il modello. In questo problema di classificazione binaria vengono usati algoritmi a due classi.

1. Trascinare il modulo **Split data** nell'area di disegno.

1. Nel riquadro Proprietà immettere 0,8 per **la frazione di righe nel primo set di dati di output**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/split-data.png" alt-text="Screenshot del riquadro sinistro dell'interfaccia Azure Machine Learning":::

1. Trascinare il modulo **Two-Class Boosted Decision Tree** nell'area di disegno.

1. Trascinare il modulo **Train Model** nell'area di disegno. Specificare gli input connettendosi all' **albero delle decisioni con boosting a due classi** (algoritmo ml) e **suddividere i dati** , ovvero i dati per il training dell'algoritmo nei moduli.

1. Per Train Model Model selezionare Modifica colonna nell'opzione **colonna etichetta** nel riquadro proprietà. Selezionare la colonna **BikeBuyer** come colonna da stimare e selezionare **Salva**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/label-column.png" alt-text="Screenshot del riquadro sinistro dell'interfaccia Azure Machine Learning":::

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/train-model.png" alt-text="Screenshot del riquadro sinistro dell'interfaccia Azure Machine Learning":::

## <a name="score-the-model"></a>Assegnare un punteggio al modello

A questo punto, testare il modo in cui il modello viene eseguito sui dati di test. Verranno confrontati due algoritmi diversi per vedere quali prestazioni sono migliori. Seguire la procedura descritta di seguito:

1. Trascinare il modulo **Score Model** nel pannello Canvas e connetterlo ai moduli **Train Model** e **Split data**.

1. Trascinare la **media perceptron a due classi Bayes** nell'area di disegno dell'esperimento. Si confronterà il modo in cui questo algoritmo viene eseguito rispetto all'albero delle decisioni con boosting Two-Class.

1. Copiare e incollare i moduli **Train Model** e **Score Model** nell'area di disegno.

1. Trascinare il modulo **Evaluate Model** nell'area di disegno per confrontare i due algoritmi.

1. Fare clic su **Invia** per impostare l'esecuzione della pipeline.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-in.png" alt-text="Screenshot del riquadro sinistro dell'interfaccia Azure Machine Learning" lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-out.png":::

1. Al termine dell'esecuzione, fare clic con il pulsante destro del mouse sul modulo **Evaluate Model** e scegliere **Visualizza risultati valutazione**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/result-visualize-zoomed-out.png" alt-text="Screenshot del riquadro sinistro dell'interfaccia Azure Machine Learning":::

La metrica fornita include curva ROC, diagramma di precisione/recupero e curva lift. Esaminare queste metriche per verificare che il primo modello sia stato eseguito in modo migliore rispetto al secondo. Per esaminare la stima del primo modello, fare clic con il pulsante destro del mouse sul modulo Score Model e scegliere Visualizza set di dati con punteggio per visualizzare i risultati previsti.

Verranno visualizzate altre due colonne aggiunte al set di dati di test.

* Scored Probabilities: la probabilità che un cliente sia un acquirente di biciclette.
* Scored Labels: la classificazione eseguita dal modello – acquirente di biciclette (1) o non acquirente (0). La soglia di probabilità per le etichette è impostata su 50% e può essere modificata.

Confrontare la colonna BikeBuyer (Actual) con le etichette con Punteggio (stima), per verificare l'esecuzione del modello. Successivamente, è possibile utilizzare questo modello per eseguire stime per i nuovi clienti. È possibile [pubblicare questo modello come servizio Web](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy) o scrivere i risultati in sinapsi di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Machine Learning, vedere [Introduzione a Machine Learning in Azure](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml).

Per informazioni sul Punteggio predefinito, vedere la data warehouse, [qui](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest).