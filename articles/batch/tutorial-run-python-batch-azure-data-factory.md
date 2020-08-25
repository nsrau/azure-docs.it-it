---
title: Eseguire script Python tramite Data Factory
description: 'Esercitazione: Informazioni su come eseguire gli script Python come parte di una pipeline tramite Azure Data Factory usando Azure Batch.'
author: mammask
ms.devlang: python
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: komammas
ms.custom: mvc, devx-track-python
ms.openlocfilehash: f4c71cffe00faa6dd8cc440c59f94b8c2d60f712
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185112"
---
# <a name="tutorial-run-python-scripts-through-azure-data-factory-using-azure-batch"></a>Esercitazione: Eseguire script Python tramite Azure Data Factory usando Azure Batch

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Eseguire l'autenticazione con gli account di archiviazione e Batch
> * Sviluppare ed eseguire uno script in Python
> * Creare un pool di nodi di calcolo per eseguire un'applicazione
> * Pianificare i carichi di lavoro Python
> * Monitorare una pipeline di analisi
> * Accedere ai file di log

L'esempio riportato di seguito esegue uno script Python che riceve il file di input CSV da un contenitore di archiviazione BLOB, esegue un processo di manipolazione dei dati e scrive l'output in un contenitore di archiviazione BLOB separato.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Una distribuzione [Python](https://www.python.org/downloads/) installata per i test locali.
* Il pacchetto di [Azure](https://pypi.org/project/azure/) `pip`.
* Il [set di dati iris.csv](https://www.kaggle.com/uciml/iris/version/2#Iris.csv)
* Un account Azure Batch e un account di archiviazione di Azure collegato. Per altre informazioni su come creare e collegare account Batch agli account di archiviazione, vedere [Creare un account Batch](quick-create-portal.md#create-a-batch-account).
* Un account di Azure Data Factory. Per altre informazioni su come creare una data factory tramite il portale di Azure, vedere [Creare una data factory](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).
* [Batch Explorer](https://azure.github.io/BatchExplorer/).
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="create-a-batch-pool-using-batch-explorer"></a>Creare un pool di Batch usando Batch Explorer

In questa sezione si userà Batch Explorer per creare il pool di Batch che verrà usato dalla pipeline di Azure Data Factory. 

1. Accedere a Batch Explorer con le credenziali di Azure.
1. Selezionare un account Batch
1. Creare un pool selezionando **Pool** nella barra laterale sinistra e quindi facendo clic sul pulsante **Aggiungi** sopra il modulo di ricerca. 
    1. Scegliere un ID e un nome visualizzato. Per questo esempio si userà `custom-activity-pool`.
    1. Impostare il tipo di scala su **A dimensione fissa** e impostare il numero di nodi dedicati su 2.
    1. In **Data science** selezionare **Dsvm Windows** come sistema operativo.
    1. Scegliere `Standard_f2s_v2` come dimensione della macchina virtuale.
    1. Abilitare l'attività di avvio e aggiungere il comando `cmd /c "pip install pandas"`. L'identità utente può rimanere l'**utente del pool** predefinito.
    1. Selezionare **OK**.

## <a name="create-blob-containers"></a>Creare contenitori BLOB

Verranno ora creati i contenitori BLOB in cui archiviare i file di input e output per il processo OCR di Batch.

1. Accedere a Storage Explorer con le credenziali di Azure.
1. Usando l'account di archiviazione collegato all'account Batch, creare due contenitori BLOB (uno per i file di input e uno per i file di output) seguendo i passaggi in [Creare un contenitore BLOB](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container).
    * In questo esempio, il contenitore di input sarà denominato `input` e il contenitore di output `output`.
1. Caricare [`iris.csv`](https://www.kaggle.com/uciml/iris/version/2#Iris.csv) nel contenitore di input `input` usando Storage Explorer seguendo la procedura descritta in [Gestione dei BLOB in un contenitore BLOB](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container)

## <a name="develop-a-script-in-python"></a>Sviluppare uno script in Python

Lo script Python seguente carica il set di dati `iris.csv` dal contenitore `input`, esegue un processo di manipolazione dei dati e salva i risultati nel contenitore `output`.

``` python
# Load libraries
from azure.storage.blob import BlockBlobService
import pandas as pd

# Define parameters
storageAccountName = "<storage-account-name>"
storageKey         = "<storage-account-key>"
containerName      = "output"

# Establish connection with the blob storage account
blobService = BlockBlobService(account_name=storageAccountName,
                               account_key=storageKey
                               )

# Load iris dataset from the task node
df = pd.read_csv("iris.csv")

# Subset records
df = df[df['Species'] == "setosa"]

# Save the subset of the iris dataframe locally in task node
df.to_csv("iris_setosa.csv", index = False)

# Upload iris dataset
blobService.create_blob_from_path(containerName, "iris_setosa.csv", "iris_setosa.csv")
```

Salvare lo script come `main.py` e caricarlo nel contenitore di **Archiviazione di Azure**. Assicurarsi di testare e convalidare la funzionalità localmente prima di caricarla nel contenitore BLOB:

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>Configurare una pipeline di Azure Data Factory

In questa sezione verrà creata e convalidata una pipeline con lo script Python.

1. Seguire i passaggi per creare una data factory nella sezione "Creare una data factory" di [questo articolo](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).
1. Nella casella delle **risorse della factory** selezionare il pulsante + (più) e quindi selezionare **Pipeline**.
1. Nella scheda **Generale** impostare il nome della pipeline su "Esegui Python".

    ![Nella scheda Generale impostare il nome della pipeline su "Esegui Python"](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. Nella casella **Attività** espandere **Servizio batch**. Trascinare l'attività personalizzata dalla casella degli strumenti **Attività** all'area di progettazione della pipeline.
1. Nella scheda **Generale** specificare **testPipeline** per il nome.

    ![Nella scheda Generale specificare testPipeline per il nome](./media/run-python-batch-azure-data-factory/create-custom-task.png)
1. Nella scheda **Azure Batch** aggiungere l'**account Batch** creato nei passaggi precedenti e selezionare **Test connessione** per assicurarsi che abbia esito positivo.

    ![Nella scheda Azure Batch aggiungere l'account Batch creato nei passaggi precedenti, quindi testare la connessione](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)

1. Nella scheda **Impostazioni** immettere il comando `python main.py`.
1. Per il **servizio collegato alle risorse**  aggiungere l'account di archiviazione creato nei passaggi precedenti. Testare la connessione per assicurarsi che abbia esito positivo.
1. Nel **percorso della cartella** selezionare il nome del contenitore di **Archiviazione BLOB di Azure** che contiene lo script Python e gli input associati. In questo modo i file selezionati vengono scaricati dal contenitore nelle istanze del nodo del pool prima dell'esecuzione dello script Python.

    ![In Percorso cartella selezionare il nome del contenitore di archiviazione BLOB di Azure](./media/run-python-batch-azure-data-factory/create-custom-task-py-script-command.png)
1. Fare clic su **Convalida** sulla barra degli strumenti della pipeline sopra l'area di disegno per convalidare le impostazioni della pipeline. Assicurarsi che la pipeline sia stata convalidata correttamente. Per chiudere l'output della convalida, selezionare il pulsante &gt;&gt; (freccia destra).
1. Fare clic su **Debug** per testare la pipeline e verificare che funzioni correttamente.
1. Fare clic su **Pubblica** per pubblicare la pipeline.
1. Fare clic su **Trigger** per eseguire lo script Python come parte di un processo batch.

    ![Fare clic su Trigger per eseguire lo script Python come parte di un processo batch](./media/run-python-batch-azure-data-factory/create-custom-task-py-success-run.png)

### <a name="monitor-the-log-files"></a>Monitorare i file di log

Nel caso in cui avvisi o errori vengano generati dall'esecuzione dello script, è possibile estrarre `stdout.txt` o `stderr.txt` per ottenere altre informazioni sull'output registrato.

1. Selezionare **Processi** dal lato sinistro di Batch Explorer.
1. Scegliere il processo creato dalla data factory. Supponendo di aver denominato il pool `custom-activity-pool`, selezionare `adfv2-custom-activity-pool`.
1. Fare clic sull'attività che ha restituito un codice di uscita di errore.
1. Visualizzare `stdout.txt` e `stderr.txt` per analizzare e diagnosticare il problema.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato analizzato un esempio che illustra come eseguire gli script Python come parte di una pipeline tramite Azure Data Factory usando Azure Batch.

Per altre informazioni su Azure Data Factory, vedere:

> [!div class="nextstepaction"]
> [Azure Data Factory](../data-factory/introduction.md)
> [Pipeline e attività ](../data-factory/concepts-pipelines-activities.md)
> [Attività personalizzate](../data-factory/transform-data-using-dotnet-custom-activity.md)
