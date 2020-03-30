---
title: Trasformazione con Azure Databricks
description: Informazioni su come usare un modello di soluzione per trasformare i dati usando un notebook di Databricks in Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/03/2020
ms.openlocfilehash: 9a05b09f958d741fa56c586fbc7f5c5908dbbce6
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384382"
---
# <a name="transformation-with-azure-databricks"></a>Trasformazione con Azure Databricks

In questa esercitazione viene creata una pipeline end-to-end contenente le attività **Convalida**, **Copia dati**e **Blocco appunti** in Azure Data Factory.

- **La convalida** garantisce che il set di dati di origine sia pronto per l'utilizzo downstream prima di attivare il processo di copia e analisi.

- **Copia dati** duplica il set di dati di origine nell'archivio sink, che viene montato come DBFS nel blocco appunti di Azure Databricks.Copy data duplicates the source dataset to the sink storage, which is mounted as DBFS in the Azure Databricks notebook. In this way, the dataset can be directly consumed by Spark.

- **Blocco appunti** attiva il blocco appunti Databricks che trasforma il set di dati. Aggiunge inoltre il set di dati a una cartella elaborata o a Azure SQL Data Warehouse.

Per semplicità, il modello in questa esercitazione non crea un trigger pianificato. Se necessario, è possibile aggiungerne uno.

![Diagramma della pipeline](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Prerequisiti

- Un account di archiviazione BLOB `sinkdata` di Azure con un contenitore chiamato per l'uso come sink.

  Prendere nota del nome dell'account di archiviazione, del nome del contenitore e della chiave di accesso. Questi valori saranno necessari più avanti nel modello.

- Area di lavoro di Azure Databricks.An Azure Databricks workspace.

## <a name="import-a-notebook-for-transformation"></a>Importare un blocco appunti per la trasformazione

Per importare un blocco appunti **di Trasformazione** nell'area di lavoro Databricks:

1. Accedere all'area di lavoro di Azure Databricks e quindi selezionare **Importa**.
       ![Comando di menu](media/solution-template-Databricks-notebook/import-notebook.png) per l'importazione di un'area di lavoro Il percorso dell'area di lavoro può essere diverso da quello visualizzato, ma ricordarlo in un secondo momento.
1. Selezionare **Importa da: URL**. Nella casella di `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`testo immettere .

   ![Selezioni per l'importazione di un blocco appunti](media/solution-template-Databricks-notebook/import-from-url.png)

1. A questo punto è possibile aggiornare il blocco appunti **di trasformazione** con le informazioni di connessione di archiviazione.

   Nel blocco appunti importato passare al **comando 5** come illustrato nel frammento di codice seguente.

   - Sostituire `<storage name>` `<access key>` e con le proprie informazioni di connessione di archiviazione.
   - Usare l'account `sinkdata` di archiviazione con il contenitore.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1. Generare un **token di accesso di Databricks** per consentire a Data Factory di accedere a Databricks.
   1. Nell'area di lavoro Databricks, seleziona l'icona del tuo profilo utente in alto a destra.
   1. Selezionare **Impostazioni utente**.
    ![Comando di menu per le impostazioni utente](media/solution-template-Databricks-notebook/user-setting.png)
   1. Selezionare **Genera nuovo token** nella scheda Token di **accesso.**
   1. Selezionare **Genera**.

    ![Pulsante "Genera"](media/solution-template-Databricks-notebook/generate-new-token.png)

   *Salvare il token* di accesso per un utilizzo successivo nella creazione di un servizio collegato Databricks.Save the access token for later use in creating a Databricks linked service. Il token di `dapi32db32cbb4w6eee18b7d87e45exxxxxx`accesso è simile a .

## <a name="how-to-use-this-template"></a>Come utilizzare questo modello

1. Passare al modello **Trasformazione con Azure Databricks** e creare nuovi servizi collegati per le connessioni seguenti.

   ![Impostazione delle connessioni](media/solution-template-Databricks-notebook/connections-preview.png)

    - **Connessione BLOB di origine:** per accedere ai dati di origine.

       Per questo esercizio, è possibile usare l'archiviazione BLOB pubblica che contiene i file di origine. Fare riferimento alla schermata seguente per la configurazione. Usare l'URL di accesso condiviso seguente per connettersi all'archiviazione di origine (accesso di sola lettura):Use the following **SAS URL** to connect to source storage (read-only access):

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![Selezioni per il metodo di autenticazione e l'URL della chiamata in base alla chiamata in stato di condiviso](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - Connessione BLOB di **destinazione:** per archiviare i dati copiati.

       Nella finestra **Nuovo servizio collegato** selezionare il BLOB di archiviazione sink.

       ![BLOB di archiviazione sink come nuovo servizio collegatoSink storage blob as a new linked service](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks:** per connettersi al cluster Databricks.Azure Databricks - to connect to the Databricks cluster.

        Creare un servizio collegato a Databricks utilizzando la chiave di accesso generata in precedenza. È possibile scegliere di selezionare un *cluster interattivo,* se necessario. In questo esempio viene utilizzata l'opzione **Nuovo cluster di processi.**

        ![Selezioni per la connessione al cluster](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Selezionare **Usa questo modello**. Verrà creata una pipeline.

    ![Creare una pipeline](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>Introduzione e configurazione della pipeline

Nella nuova pipeline, la maggior parte delle impostazioni viene configurata automaticamente con i valori predefiniti. Esaminare le configurazioni della pipeline e apportare le modifiche necessarie.

1. Nel flag **Disponibilità**attività di **convalida** verificare che `SourceAvailabilityDataset` il valore del set di **dati** di origine sia impostato su quello creato in precedenza.

   ![Valore del set di dati di origineSource dataset value](media/solution-template-Databricks-notebook/validation-settings.png)

1. In **Copia** attività dati **da file a BLOB**selezionare le schede **Origine** e **Sink** . Se necessario, modificare le impostazioni.

   - **Scheda** ![Origine Scheda Origine](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - Scheda ![ **Lavello** della scheda Lavello](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. In **Trasformazione dell'attività** **Blocco appunti** esaminare e aggiornare i percorsi e le impostazioni in base alle esigenze.

   **Databricks servizio collegato** deve essere pre-popolato con il ![valore di un passaggio precedente, come mostrato: Populated value for the Databricks linked service](media/solution-template-Databricks-notebook/notebook-activity.png)

   Per verificare le impostazioni **del blocco appunti:**
  
    1. Selezionare la scheda **Impostazioni.** Per **Percorso blocco appunti**, verificare che il percorso predefinito sia corretto. Potrebbe essere necessario sfogliare e scegliere il percorso corretto del blocco appunti.

       ![Percorso blocco appunti](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. Espandere il selettore **Parametri di base** e verificare che i parametri corrispondano a quelli visualizzati nella schermata seguente. Questi parametri vengono passati al blocco appunti Databricks da Data Factory.

       ![Parametri di base](media/solution-template-Databricks-notebook/base-parameters.png)

1. Verificare che i **parametri della pipeline** corrispondano a quelli illustrati nella schermata seguente: Parametri della pipelineVerify that the Pipeline Parameters match what is shown in the following screenshot: ![Pipeline parameters](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Connettersi ai set di dati.

   - **SourceAvailabilityDataset** - per verificare che i dati di origine sono disponibili.

     ![Selezioni per il servizio collegato e il percorso del file per SourceAvailabilityDataset](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset** - per accedere ai dati di origine.

       ![Selezioni per il servizio collegato e il percorso del file per SourceFilesDataset](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset** - per copiare i dati nel percorso di destinazione del sink. Usare i valori seguenti:

     - **Servizio collegato**`sinkBlob_LS`, creato in un passaggio precedente. - 

     - **Percorso file** - `sinkdata/staged_sink`.

       ![Selezioni per il servizio collegato e il percorso del file per DestinationFilesDataset](media/solution-template-Databricks-notebook/destination-dataset.png)

1. Selezionare **Debug** per eseguire la pipeline. È possibile trovare il collegamento ai registri di Databricks per i registri Spark più dettagliati.

    ![Collegamento ai registri di Databricks dall'output](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    È anche possibile verificare il file di dati usando Esplora archivi di Azure.You can also verify the data file by using Azure Storage Explorer.

    > [!NOTE]
    > Per la correlazione con le esecuzioni della pipeline di Data Factory, in questo esempio viene aggiunto l'ID di esecuzione della pipeline dalla data factory alla cartella di output. In questo modo è possibile tenere traccia dei file generati da ogni esecuzione.
    > ![ID esecuzione pipeline aggiunto](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione a Data factory di Azure](introduction.md)
