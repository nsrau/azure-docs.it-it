---
title: 'Esercitazione: Procedura guidata di assegnazione di punteggi al modello di Machine Learning per i pool SQL dedicati'
description: Esercitazione su come usare la procedura guidata di assegnazione di punteggi a modelli di Machine Learning nei pool SQL dedicati.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: f5c5edc067b3f7b525fd129462c48ca50fdafc8f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314045"
---
# <a name="tutorial-machine-learning-model-scoring-wizard-for-dedicated-sql-pools"></a>Esercitazione: Procedura guidata di assegnazione di punteggi al modello di Machine Learning per i pool SQL dedicati

Informazioni su come arricchire facilmente i dati nei pool SQL dedicati con modelli predittivi di Machine Learning.  I modelli creati dai data scientist sono ora facilmente accessibili ai professionisti dei dati per l'analisi predittiva. Un professionista dei dati in Synapse può semplicemente selezionare un modello dal registro di modelli di Azure Machine Learning per la distribuzione nei pool Synapse SQL e avviare le previsioni per arricchire i dati.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> - Eseguire il training di un modello di Machine Learning e registrarlo nel registro di modelli di Azure Machine Learning
> - Usare la procedura guidata di assegnazione di punteggi di SQL per avviare previsioni nel pool SQL dedicato

Se non si ha una sottoscrizione di Azure, creare un [account gratuito prima di iniziare](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

- [Area di lavoro di Synapse Analytics](../get-started-create-workspace.md) con un account di archiviazione di ADLS Gen2 configurato come archiviazione predefinita. È necessario essere il **collaboratore dei dati del BLOB di archiviazione** del file system di ADLS Gen2 che si vuole usare.
- Pool SQL dedicato nell'area di lavoro di Synapse Analytics. Per informazioni dettagliate, vedere [Creare un pool SQL dedicato](../quickstart-create-sql-pool-studio.md).
- Servizio collegato di Azure Machine Learning nell'area di lavoro di Synapse Analytics. Per i dettagli, vedere [Creare un servizio collegato di Azure Machine Learning in Synapse](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/)

## <a name="train-a-model-in-azure-machine-learning"></a>Eseguire il training di un modello in Azure Machine Learning

Prima di iniziare, verificare che la versione in uso di **sklearn** sia 0.20.3.

Prima di eseguire tutte le celle del notebook, controllare se l'istanza di calcolo è in esecuzione.

![Verificare l'istanza di calcolo di Azure Machine Learning](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00b.png)

1. Passare all'area di lavoro di Azure Machine Learning.

1. Scaricare il file [Predict NYC Taxi Tips.ipynb](https://go.microsoft.com/fwlink/?linkid=2144301).

1. Avviare l'area di lavoro di Azure Machine Learning in [Azure Machine Learning Studio](https://ml.azure.com).

1. Passare a **Notebook** e fare clic su **Carica file**, quindi selezionare il file "Predict NYC Taxi Tips.ipynb" scaricato e caricarlo.
   ![Caricare il file](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00a.png)

1. Dopo aver caricato e aperto il notebook, fare clic su **Esegui tutte le celle**.

   Una delle celle potrebbe generare un errore e chiedere di eseguire l'autenticazione con Azure. Controllare gli output delle celle per verificare questa occorrenza, quindi eseguire l'autenticazione nel browser seguendo il collegamento e immettendo il codice. Quindi eseguire di nuovo il notebook.

1. Il notebook eseguirà il training di un modello ONNX e lo registrerà con MLFlow. Passare a **Modelli** per verificare se il nuovo modello è stato registrato correttamente.
   ![Modello nel registro](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00c.png)

1. L'esecuzione del notebook comporta anche l'esportazione dei dati di test in un file CSV. Scaricare il file CSV nel sistema locale. Più avanti il file CSV verrà importato nel pool SQL dedicato e i dati verranno usati per testare il modello.

   Il file CSV viene creato nella stessa cartella del file del notebook. Se non è immediatamente visibile, fare clic su "Aggiorna" in Esplora file.

   ![File CSV](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00d.png)

## <a name="launch-predictions-with-sql-scoring-wizard"></a>Avviare le previsioni con la procedura guidata di assegnazione di punteggi di SQL

1. Aprire l'area di lavoro di Synapse con Synapse Studio.

1. Passare a **Dati** -> **Collegato** -> **account di archiviazione**. Caricare `test_data.csv` nell'account di archiviazione predefinito.

   ![Caricare i dati](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00a.png)

1. Passare a **Sviluppo** -> **Script SQL**. Creare un nuovo script SQL per caricare `test_data.csv` nel pool SQL dedicato.

   > [!NOTE]
   > Aggiornare l'URL del file in questo script prima di eseguirlo.

   ```SQL
   IF NOT EXISTS (SELECT * FROM sys.objects WHERE NAME = 'nyc_taxi' AND TYPE = 'U')
   CREATE TABLE dbo.nyc_taxi
   (
       tipped int,
       fareAmount float,
       paymentType int,
       passengerCount int,
       tripDistance float,
       tripTimeSecs bigint,
       pickupTimeBin nvarchar(30)
   )
   WITH
   (
       DISTRIBUTION = ROUND_ROBIN,
       CLUSTERED COLUMNSTORE INDEX
   )
   GO
   
   COPY INTO dbo.nyc_taxi
   (tipped 1, fareAmount 2, paymentType 3, passengerCount 4, tripDistance 5, tripTimeSecs 6, pickupTimeBin 7)
   FROM '<URL to linked storage account>/test_data.csv'
   WITH
   (
       FILE_TYPE = 'CSV',
       ROWTERMINATOR='0x0A',
       FIELDQUOTE = '"',
       FIELDTERMINATOR = ',',
       FIRSTROW = 2
   )
   GO
   
   SELECT TOP 100 * FROM nyc_taxi
   GO
   ```

   ![Caricare i dati nel pool SQL dedicato](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00b.png)

1. Passare a **Dati** -> **Area di lavoro**. Aprire la procedura guidata di assegnazione di punteggi di SQL facendo clic con il pulsante destro del mouse sulla tabella Pool SQL dedicato. Scegliere **Machine Learning** -> **Enrich with existing model** (Arricchisci con il modello esistente).

   > [!NOTE]
   > L'opzione Machine Learning viene visualizzata solo se è stato creato un servizio collegato per Azure Machine Learning. Vedere **Prerequisiti** all'inizio di questa esercitazione.

   ![Opzione Machine Learning](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00c.png)

1. Selezionare un'area di lavoro collegata di Azure Machine Learning nella casella di riepilogo a discesa. Viene caricato un elenco di modelli di Machine Learning dal registro di modelli dell'area di lavoro di Azure Machine Learning scelta. Attualmente sono supportati solo modelli ONNX, quindi verranno visualizzati solo questi.

1. Selezionare il modello di cui è stato eseguito il training e quindi fare clic su **Continua**.

   ![Selezionare il modello di Azure Machine Learning](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00d.png)

1. Eseguire quindi il mapping delle colonne della tabella agli input del modello e specificare gli output del modello. Se il modello viene salvato nel formato MLFlow e la firma del modello viene popolata, il mapping verrà eseguito automaticamente usando una logica basata sulla somiglianza dei nomi. L'interfaccia supporta anche il mapping manuale.

   Fare clic su **Continua**.

   ![Mapping tra tabelle e modello](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00e.png)

1. Il codice T-SQL generato viene sottoposto a wrapping all'interno di una stored procedure. Questo è il motivo per cui è necessario fornire un nome per la stored procedure. Il file binario del modello che include i metadati (versione, descrizione e così via) verrà copiato fisicamente da Azure Machine Learning in una tabella del pool SQL dedicato. Quindi, è necessario specificare la tabella in cui salvare il modello. È possibile scegliere "Usa una tabella esistente" oppure "Crea nuova tabella". Al termine, fare clic su **Distribuisci modello + Apri editor** per distribuire il modello e generare uno script di previsione T-SQL.

   ![Creare la procedura](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00f.png)

1. Una volta generato lo script, fare clic su "Esegui" per eseguire l'assegnazione di punteggi e ottenere le previsioni.

   ![Eseguire stime](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00g.png)

## <a name="next-steps"></a>Passaggi successivi

- [Avvio rapido: Creare un nuovo servizio collegato di Azure Machine Learning in Synapse](quickstart-integrate-azure-machine-learning.md)
- [Funzionalità di Machine Learning in Azure Synapse Analytics (aree di lavoro in anteprima)](what-is-machine-learning.md)
