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
ms.date: 12/10/2018
ms.openlocfilehash: 322f5306949b266958ded908e981ed530e8245c8
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227730"
---
# <a name="transformation-with-azure-databricks"></a>Trasformazione con Azure Databricks

In questa esercitazione si creerà una pipeline end-to-end contenente le attività di **convalida**, **copia**e **notebook** in data factory.

-   L'attività di **convalida** viene usata per garantire che il set di dati di origine sia pronto per l'utilizzo a valle, prima di attivare il processo di copia e analisi.

-   L'attività di **copia** copia il file o il set di dati di origine nell'archivio sink. L'archivio sink è montato come DBFS nel notebook di Databricks, in modo che il set di dati possa essere utilizzato direttamente da Spark.

-   L'attività dei **notebook di databricks** attiva il notebook di databricks che trasforma il set di dati e lo aggiunge a una cartella elaborata/SQL DW.

Per mantenere semplice questo modello, non viene creato un trigger pianificato. Se necessario, è possibile aggiungerlo.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Prerequisiti

1. Creare un **account di archiviazione BLOB** e un contenitore denominato `sinkdata` da usare come **sink**. Prendere nota del **nome dell'account di archiviazione**, del **nome del contenitore** e della **chiave di accesso**, che verranno usati più avanti nel modello.

2. Verificare di avere un'**area di lavoro di Azure Databricks** o crearne una nuova.

3. **Importare il notebook per la trasformazione**. 
    1. Nel Azure Databricks, fare riferimento alle schermate seguenti per importare un notebook di **trasformazione** nell'area di lavoro databricks. Non deve trovarsi nella stessa posizione seguente, ma ricordare il percorso scelto per un momento successivo.
   
       ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)    
    
    1. Selezionare "Importa da: **URL**" e immettere l'URL seguente nella casella di testo:
    
       * `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`
        
       ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)    

4. A questo punto è possibile aggiornare il notebook di **trasformazione** con le informazioni sulla connessione di archiviazione. Passare al **comando 5** (come illustrato nel frammento di codice seguente) nel notebook importato sopra e sostituire `<storage name>`e `<access key>` con le informazioni di connessione di archiviazione. Verificare che l'account corrisponda all'account di archiviazione creato in precedenza e contenga il contenitore `sinkdata`.

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

5.  Generare un **token di accesso di Databricks** per consentire a Data Factory di accedere a Databricks. **Salvare il token di accesso** per usarlo in seguito per la creazione di un servizio collegato databricks, simile a "dapi32db32cbb4w6eee18b7d87e45exxxxxx".

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="how-to-use-this-template"></a>Come usare questo modello

1.  Passare a **trasformazione con Azure Databricks** modello. Creare nuovi servizi collegati per le connessioni seguenti. 
    
    ![Impostazione connessioni](media/solution-template-Databricks-notebook/connections-preview.png)

    1.  **Connessione BLOB di origine** : per accedere ai dati di origine. 
        
        È possibile usare l'archivio BLOB pubblico contenente i file di origine per questo esempio. Fare riferimento alla schermata seguente per la configurazione. Usare l' **URL** di firma di accesso condiviso seguente per connettersi all'archiviazione di origine (accesso in sola lettura): 
        * `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Connessione BLOB di destinazione** : per la copia di dati in. 
        
        Nel servizio collegato sink selezionare una risorsa di archiviazione creata nel **prerequisito** 1.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Azure Databricks** : per la connessione al cluster databricks.

        Creare un servizio collegato databricks usando la chiave di accesso generata nel **prerequisito** 2. c. Se si ha un *cluster interattivo*, è possibile selezionarlo. Questo esempio usa l'opzione *New job cluster* (Nuovo cluster dei processi).

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

1. Selezionare **Usa questo modello**per visualizzare una pipeline creata come illustrato di seguito:
    
    ![Creare una pipeline](media/solution-template-Databricks-notebook/new-pipeline.png)   

## <a name="pipeline-introduction-and-configuration"></a>Introduzione e configurazione della pipeline

Nella nuova pipeline creata, la maggior parte delle impostazioni sono state configurate automaticamente con i valori predefiniti. Consultare le configurazioni e aggiornare laddove necessario per adattarle alle proprie impostazioni. Per informazioni dettagliate, è possibile controllare le istruzioni e le schermate di riferimento.

1.  Per eseguire una verifica della disponibilità dell'origine viene creato un **flag di disponibilità** dell'attività di convalida. *SourceAvailabilityDataset* creato nel passaggio precedente è selezionato come set di dati.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Viene creato un **file di attività di copia nel BLOB** per la copia del set di dati dall'origine al sink. Fare riferimento agli screenshot seguenti per le configurazioni dell'origine e del sink nell'attività Copy.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  Viene creata una **trasformazione** attività notebook e viene selezionato il servizio collegato creato nel passaggio precedente.
    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

     1. Selezionare la scheda **Impostazioni** . Per il *percorso del notebook*, il modello definisce un percorso per impostazione predefinita. Potrebbe essere necessario cercare e selezionare il percorso corretto del notebook caricato nel **prerequisito** 2. 

         ![17](media/solution-template-Databricks-notebook/databricks-tutorial-image17.png)
    
     1. Estrarre i *parametri di base* creati come illustrato nello screenshot. Devono essere passati al notebook di databricks da Data Factory. 

         ![Parametri di base](media/solution-template-Databricks-notebook/base-parameters.png)

1.  I **parametri della pipeline** sono definiti come indicato di seguito.

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1. Configurazione di set di impostazioni.
    1.  **SourceAvailabilityDataset** viene creato per verificare se i dati di origine sono disponibili.

        ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **SourceFilesDataset** : per la copia dei dati di origine.

        ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **DestinationFilesDataset** : per la copia nella posizione di sink/destinazione.

        1.  Servizio collegato- *sinkBlob_LS* creato nel passaggio precedente.

        2.  Percorso file- *sinkData/staged_sink*.

            ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)


1.  Selezionare **debug** per eseguire la pipeline. È possibile trovare il collegamento ai log di Databricks per log di Spark più dettagliati.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    È anche possibile verificare il file di dati usando lo strumento di esplorazione dell'archiviazione. Per la correlazione con le esecuzioni di pipeline di Data Factory, in questo esempio viene aggiunto l'ID di esecuzione della pipeline da Data Factory nella cartella di output. In questo modo è possibile risalire ai file generati da ogni esecuzione.

    ![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al servizio Azure Data Factory](introduction.md)
