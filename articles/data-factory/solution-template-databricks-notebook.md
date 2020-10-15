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
ms.date: 04/27/2020
ms.openlocfilehash: f9dc11bd046bdc3a8913b4b05f1b68b84c9736c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89438450"
---
# <a name="transformation-with-azure-databricks"></a>Trasformazione con Azure Databricks

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In questa esercitazione si creerà una pipeline end-to-end che contiene le attività di **convalida**, **copia dei dati**e **notebook** in Azure Data Factory.

- La **convalida** garantisce che il set di dati di origine sia pronto per l'utilizzo downstream prima di attivare il processo di copia e analisi.

- **Copy Data** Duplica il set di dati di origine nell'archivio sink, montato come DBFS nel notebook di Azure Databricks. In questo modo, il set di dati può essere utilizzato direttamente da Spark.

- **Notebook** avvia il notebook di databricks che trasforma il set di dati. Aggiunge anche il set di dati a una cartella elaborata o ad Azure sinapsi Analytics (in precedenza SQL Data Warehouse).

Per semplicità, nel modello di questa esercitazione non viene creato un trigger pianificato. È possibile aggiungerne uno, se necessario.

![Diagramma della pipeline](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Prerequisiti

- Un account di archiviazione BLOB di Azure con un contenitore denominato `sinkdata` da usare come sink.

  Prendere nota del nome dell'account di archiviazione, del nome del contenitore e della chiave di accesso. Questi valori saranno necessari in un secondo momento nel modello.

- Area di lavoro Azure Databricks.

## <a name="import-a-notebook-for-transformation"></a>Importare un notebook per la trasformazione

Per importare un notebook di **trasformazione** nell'area di lavoro di databricks:

1. Accedere all'area di lavoro di Azure Databricks, quindi selezionare **Importa**.
       ![Comando di menu per l'importazione di un'area di lavoro ](media/solution-template-Databricks-notebook/import-notebook.png) il percorso dell'area di lavoro può essere diverso da quello visualizzato, ma ricordarlo per un momento successivo.
1. Selezionare **Importa da: URL**. Nella casella di testo immettere `https://adflabstaging1.blob.core.windows.net/share/Transformations.html` .

   ![Selezioni per l'importazione di un notebook](media/solution-template-Databricks-notebook/import-from-url.png)

1. A questo punto è possibile aggiornare il notebook di **trasformazione** con le informazioni sulla connessione di archiviazione.

   Nel notebook importato passare al **comando 5** , come illustrato nel frammento di codice seguente.

   - Sostituire `<storage name>` e `<access key>` con le informazioni di connessione di archiviazione.
   - Usare l'account di archiviazione con il `sinkdata` contenitore.

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
   1. Nell'area di lavoro di databricks selezionare l'icona del profilo utente nell'angolo in alto a destra.
   1. Selezionare **impostazioni utente**.
    ![Comando di menu per le impostazioni utente](media/solution-template-Databricks-notebook/user-setting.png)
   1. Selezionare **genera nuovo token** nella scheda **token di accesso** .
   1. Selezionare **Genera**.

    ![Pulsante "genera"](media/solution-template-Databricks-notebook/generate-new-token.png)

   *Salvare il token di accesso* per usarlo in seguito per la creazione di un servizio collegato databricks. Il token di accesso ha un aspetto simile a `dapi32db32cbb4w6eee18b7d87e45exxxxxx` .

## <a name="how-to-use-this-template"></a>Come usare questo modello

1. Passare alla **trasformazione con Azure Databricks** modello e creare nuovi servizi collegati per le connessioni seguenti.

   ![Impostazione connessioni](media/solution-template-Databricks-notebook/connections-preview.png)

    - **Connessione BLOB di origine** : per accedere ai dati di origine.

       Per questo esercizio, è possibile usare l'archivio BLOB pubblico che contiene i file di origine. Per la configurazione, fare riferimento alla schermata seguente. Usare l' **URL SAS** seguente per connettersi all'archiviazione di origine (accesso in sola lettura):

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![Selezioni per il metodo di autenticazione e l'URL SAS](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **Connessione BLOB di destinazione** : consente di archiviare i dati copiati.

       Nella finestra **nuovo servizio collegato** selezionare il BLOB di archiviazione del sink.

       ![Sink archiviazione BLOB come nuovo servizio collegato](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks** : per connettersi al cluster databricks.

        Creare un servizio collegato databricks usando la chiave di accesso generata in precedenza. È possibile scegliere se si dispone di un *cluster interattivo* . Questo esempio usa l'opzione **nuovo cluster di processi** .

        ![Selezioni per la connessione al cluster](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Selezionare **Usa questo modello**. Verrà visualizzata una pipeline creata.

    ![Creare una pipeline](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>Introduzione e configurazione della pipeline

Nella nuova pipeline la maggior parte delle impostazioni viene configurata automaticamente con i valori predefiniti. Esaminare le configurazioni della pipeline e apportare le modifiche necessarie.

1. Nel **flag di disponibilità**dell'attività di **convalida** , verificare che il valore del **set di dati** di origine sia impostato su `SourceAvailabilityDataset` creato in precedenza.

   ![Valore del set di dati di origine](media/solution-template-Databricks-notebook/validation-settings.png)

1. Nel **file di attività di** **copia dei dati** , selezionare le schede **origine** e **sink** . Se necessario, modificare le impostazioni.

   - **Source** ![ Scheda origine scheda origine](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - **Scheda sink scheda sink** ![](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. Nella **trasformazione**attività **notebook** esaminare e aggiornare i percorsi e le impostazioni in base alle esigenze.

   Il **servizio collegato databricks** deve essere pre-popolato con il valore di un passaggio precedente, come illustrato: ![ valore popolato per il servizio collegato databricks](media/solution-template-Databricks-notebook/notebook-activity.png)

   Per verificare le impostazioni del **notebook** :
  
    1. Selezionare la scheda **Impostazioni** . Per **percorso notebook**, verificare che il percorso predefinito sia corretto. Potrebbe essere necessario esplorare e scegliere il percorso corretto del notebook.

       ![Percorso notebook](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. Espandere il selettore **parametri di base** e verificare che i parametri corrispondano a quanto illustrato nello screenshot seguente. Questi parametri vengono passati al notebook di databricks da Data Factory.

       ![Parametri di base](media/solution-template-Databricks-notebook/base-parameters.png)

1. Verificare che i **parametri della pipeline** corrispondano a quanto illustrato nello screenshot seguente: ![ parametri della pipeline](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Connettersi ai set di impostazioni.

    >[!NOTE]
    >Nei set di impostazioni seguenti il percorso del file è stato specificato automaticamente nel modello. Se sono necessarie modifiche, assicurarsi di specificare il percorso per il **contenitore** e la **directory** in caso di errore di connessione.

   - **SourceAvailabilityDataset** : per verificare che i dati di origine siano disponibili.

     ![Selezioni per il servizio collegato e il percorso del file per SourceAvailabilityDataset](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset** : per accedere ai dati di origine.

       ![Selezioni per il servizio collegato e il percorso del file per SourceFilesDataset](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset** : per copiare i dati nel percorso di destinazione del sink. Usare i valori seguenti:

     - **Servizio collegato**  -  `sinkBlob_LS` , creato in un passaggio precedente.

     - **Percorso del file**  -  `sinkdata/staged_sink` .

       ![Selezioni per il servizio collegato e il percorso del file per DestinationFilesDataset](media/solution-template-Databricks-notebook/destination-dataset.png)

1. Selezionare **debug** per eseguire la pipeline. Per informazioni dettagliate sui log Spark, vedere il collegamento ai log di databricks.

    ![Collegamento ai log di databricks dall'output](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    È anche possibile verificare il file di dati usando Azure Storage Explorer.

    > [!NOTE]
    > Per la correlazione con Data Factory esecuzioni di pipeline, in questo esempio l'ID di esecuzione della pipeline viene aggiunto dalla data factory alla cartella di output. Questo consente di tenere traccia dei file generati da ogni esecuzione.
    > ![ID esecuzione pipeline accodata](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al servizio Azure Data Factory](introduction.md)
