---
title: 'Esercitazione: Creare una definizione di processo Apache Spark in Synapse Studio'
description: 'Esercitazione: Usare Azure Synapse Analytics per creare definizioni di processi Spark e inviarle ad Apache Spark per un pool di Azure Synapse Analytics.'
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.openlocfilehash: ac3e163ffefcb7b164860b0c4fa42edc866227e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87065622"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>Esercitazione: Creare una definizione di processo Apache Spark in Synapse Studio

Questa esercitazione illustra come usare Azure Synapse Studio per creare definizioni di processi Apache Spark e quindi inviarle a un pool di Apache Spark.

Questa esercitazione illustra le attività seguenti:

* Creare una definizione di processo Apache Spark per PySpark (Python)
* Creare una definizione di processo Apache Spark per Spark (Scala)
* Creare una definizione di processo Apache Spark per .NET Spark(C#/F#)
* Inviare una definizione di processo Apache Spark come processo batch
* Aggiungere una definizione di processo Apache Spark a una pipeline

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare l'esercitazione, verificare che siano soddisfatti i requisiti seguenti:

* Area di lavoro di Azure Synapse Analytics. Per le istruzioni, vedere [Creare un'area di lavoro di Azure Synapse Analytics](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).
* Pool di Apache Spark.
* Account di archiviazione Azure Data Lake Storage Gen2. È necessario essere il proprietario dei dati del BLOB di archiviazione del file system di ADLS Gen2 che si vuole usare. Se non lo si è, è necessario aggiungere l'autorizzazione manualmente.

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>Creare una definizione di processo Apache Spark per PySpark (Python)

In questa sezione viene creata una definizione di processo Apache Spark per PySpark (Python).

1. Aprire [Azure Synapse Studio](https://web.azuresynapse.net/).

2. È possibile accedere ai [file di esempio per la creazione di definizioni di processi Apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python) per scaricare **wordcount.py** e **shakespear.txt**. Caricare quindi questi file in Archiviazione di Azure: Fare clic su **Dati**, selezionare **Account di archiviazione** e caricare i file correlati nel file system di ADLS Gen2. Se i file sono già in Archiviazione di Azure, saltare questo passaggio. 

     ![Caricamento del file Python](./media/apache-spark-job-definitions/upload-python-file.png)

3. Fare clic sull'hub **Sviluppo**, selezionare **Spark job definitions** (Definizioni di processo Spark) nel riquadro a sinistra, fare clic sui tre puntini (…) accanto a **Spark job definitions**, quindi scegliere **New Spark job definition** (Nuova definizione di processo Spark) dal menu di scelta rapida.

     ![Creazione di una nuova definizione per Python](./media/apache-spark-job-definitions/create-new-definition.png)

4. Selezionare **PySpark (Python)** nell'elenco a discesa del linguaggio nella finestra principale della definizione di processo Apache Spark.

5. Immettere le informazioni per la definizione di processo Apache Spark. È possibile copiare le informazioni di esempio.

     |  Proprietà   | Descrizione   |  
     | ----- | ----- |  
     |Nome definizione processo| Specificare un nome per la definizione di processo Apache Spark. Questo nome può essere aggiornato in qualsiasi momento fino a quando non viene pubblicato. Esempio: `job definition sample`|
     |File di definizione principale| File principale usato per il processo. Selezionare un file PY dalla risorsa di archiviazione. È possibile selezionare **Carica file** per caricare il file in un account di archiviazione. Esempio: `abfss://…/path/to/wordcount.py`|
     |Argomenti della riga di comando| Argomenti facoltativi per il processo. Esempio: `abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |File di riferimento| File aggiuntivi usati come riferimento nel file di definizione principale. È possibile selezionare **Carica file** per caricare il file in un account di archiviazione. |
     |Pool Spark| Il processo verrà inviato al pool di Apache Spark selezionato.|
     |Versione di Spark| Versione di Apache Spark in esecuzione nel pool di Apache Spark.|
     |Executors| Numero di executor da assegnare al pool di Apache Spark specificato per il processo.|
     |Dimensioni executor| Numero di core e memoria da usare per gli executor indicati nel pool di Apache Spark specificato per il processo.|  
     |Dimensioni driver| Numero di core e memoria da usare per il driver indicato nel pool di Apache Spark specificato per il processo.|

     ![Impostare il valore della definizione di processo Spark per Python](./media/apache-spark-job-definitions/create-py-definition.png)

6. Selezionare **Pubblica** per salvare la definizione di processo Apache Spark.

     ![Pubblicazione della definizione per Python](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>Creare una definizione di processo Apache Spark per Apache Spark (Scala)

In questa sezione viene creata una definizione di processo Apache Spark per Apache Spark (Scala).

 1. Aprire [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Nei [file di esempio per la creazione di definizioni di processi Apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala) scaricare **wordcount.jar** e **shakespear.txt**. Caricare quindi questi file in Archiviazione di Azure: Fare clic su **Dati**, selezionare **Account di archiviazione** e caricare i file correlati nel file system di ADLS Gen2. Se i file sono già in Archiviazione di Azure, saltare questo passaggio. 
 
     ![Preparazione della struttura di Scala](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. Fare clic sull'hub **Sviluppo**, selezionare **Spark job definitions** (Definizioni di processo Spark) nel riquadro a sinistra, fare clic sui tre puntini (…) accanto a **Spark job definitions**, quindi scegliere **New Spark job definition** (Nuova definizione di processo Spark) dal menu di scelta rapida.
     ![Creazione di una nuova definizione per Scala](./media/apache-spark-job-definitions/create-new-definition.png)

 4. Selezionare **Spark (Scala)** nell'elenco a discesa del linguaggio nella finestra principale della definizione di processo Apache Spark.

 5. Immettere le informazioni per la definizione di processo Apache Spark. È possibile copiare le informazioni di esempio.

     |  Proprietà   | Descrizione   |  
     | ----- | ----- |  
     |Nome definizione processo| Specificare un nome per la definizione di processo Apache Spark. Questo nome può essere aggiornato in qualsiasi momento fino a quando non viene pubblicato. Esempio: `job definition sample`|
     |File di definizione principale| File principale usato per il processo. Selezionare un file JAR dalla risorsa di archiviazione. È possibile selezionare **Carica file** per caricare il file in un account di archiviazione. Esempio: `abfss://…/path/to/wordcount.jar`|
     |Nome della classe principale| Identificatore completo o classe principale inclusa nel file di definizione principale. Esempio: `WordCount`|
     |Argomenti della riga di comando| Argomenti facoltativi per il processo. Esempio: `abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |File di riferimento| File aggiuntivi usati come riferimento nel file di definizione principale. È possibile selezionare **Carica file** per caricare il file in un account di archiviazione.|
     |Pool Spark| Il processo verrà inviato al pool di Apache Spark selezionato.|
     |Versione di Spark| Versione di Apache Spark in esecuzione nel pool di Apache Spark.|
     |Executors| Numero di executor da assegnare al pool di Apache Spark specificato per il processo.|  
     |Dimensioni executor| Numero di core e memoria da usare per gli executor indicati nel pool di Apache Spark specificato per il processo.|
     |Dimensioni driver| Numero di core e memoria da usare per il driver indicato nel pool di Apache Spark specificato per il processo.|

     ![Impostare il valore della definizione di processo Spark per Scala](./media/apache-spark-job-definitions/create-scala-definition.png)

 6. Selezionare **Pubblica** per salvare la definizione di processo Apache Spark.

     ![Pubblicazione della definizione per Scala](./media/apache-spark-job-definitions/publish-scala-definition.png)


## <a name="create-an-apache-spark-job-definition-for-net-sparkcf"></a>Creare una definizione di processo Apache Spark per .NET Spark(C#/F#)

In questa sezione viene creata una definizione di processo Apache Spark per .NET Spark(C#/F#).
 1. Aprire [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Nei [file di esempio per la creazione di definizioni di processi Apache Spark](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET) scaricare **wordcount.zip** e **shakespear.txt**. Caricare quindi questi file in Archiviazione di Azure: Fare clic su **Dati**, selezionare **Account di archiviazione** e caricare i file correlati nel file system di ADLS Gen2. Se i file sono già in Archiviazione di Azure, saltare questo passaggio. 

     ![Preparazione della struttura di DotNet](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. Fare clic sull'hub **Sviluppo**, selezionare **Spark job definitions** (Definizioni di processo Spark) nel riquadro a sinistra, fare clic sui tre puntini (…) accanto a **Spark job definitions**, quindi scegliere **New Spark job definition** (Nuova definizione di processo Spark) dal menu di scelta rapida.

     ![Creazione di una nuova definizione per DotNet](./media/apache-spark-job-definitions/create-new-definition.png)

 4. Selezionare **.NET Spark (C#/F#)** nell'elenco a discesa del linguaggio nella finestra principale della definizione di processo Apache Spark.

 5. Immettere le informazioni per la definizione di processo Apache Spark. È possibile copiare le informazioni di esempio.
    
     |  Proprietà   | Descrizione   |  
     | ----- | ----- |  
     |Nome definizione processo| Specificare un nome per la definizione di processo Apache Spark. Questo nome può essere aggiornato in qualsiasi momento fino a quando non viene pubblicato. Esempio: `job definition sample`|
     |File di definizione principale| File principale usato per il processo. Selezionare un file con estensione zip contenente l'applicazione .NET per Apache Spark (ovvero il file eseguibile principale, le DLL che includono le funzioni definite dall'utente e altri file necessari) dall'archivio. È possibile selezionare **Carica file** per caricare il file in un account di archiviazione. Esempio: `abfss://…/path/to/wordcount.zip`|
     |File eseguibile principale| File eseguibile principale nel file ZIP della definizione principale. Esempio: `WordCount`|
     |Argomenti della riga di comando| Argomenti facoltativi per il processo. Esempio: `abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |File di riferimento| Altri file necessari ai nodi di lavoro per l'esecuzione dell'applicazione .NET per Apache Spark non inclusi nel file con estensione zip della definizione principale (ovvero file con estensione jar dipendenti, DLL di funzioni definite dall'utente aggiuntive e altri file di configurazione). È possibile selezionare **Carica file** per caricare il file in un account di archiviazione.|
     |Pool Spark| Il processo verrà inviato al pool di Apache Spark selezionato.|
     |Versione di Spark| Versione di Apache Spark in esecuzione nel pool di Apache Spark.|
     |Executors| Numero di executor da assegnare al pool di Apache Spark specificato per il processo.|  
     |Dimensioni executor| Numero di core e memoria da usare per gli executor indicati nel pool di Apache Spark specificato per il processo.|
     |Dimensioni driver| Numero di core e memoria da usare per il driver indicato nel pool di Apache Spark specificato per il processo.|

     ![Impostare il valore della definizione di processo Spark per DotNet](./media/apache-spark-job-definitions/create-net-definition.png)

 6. Selezionare **Pubblica** per salvare la definizione di processo Apache Spark.

      ![Pubblicazione della definizione per DotNet](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>Inviare una definizione di processo Apache Spark come processo batch

Dopo aver creato una definizione di processo Apache Spark, è possibile inviarla a un pool di Apache Spark. Assicurarsi di essere il proprietario dei dati del BLOB di archiviazione del file system di ADLS Gen2 che si vuole usare. Se non lo si è, è necessario aggiungere l'autorizzazione manualmente.

### <a name="scenario-1-submit-apache-spark-job-definition"></a>Scenario 1: Inviare la definizione di processo Apache Spark
 1. Aprire una finestra di definizione di processo Apache Spark facendo clic su di essa.

      ![Aprire la definizione di processo Spark da inviare ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. Fare clic sull'icona **Invia** per inviare il progetto al pool di Apache Spark selezionato. È possibile fare clic sulla scheda **Spark monitoring URL** (URL di monitoraggio Spark) per visualizzare la query su log dell'applicazione Apache Spark.

    ![Fare clic sul pulsante Invia per inviare la definizione del processo Spark](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Finestra di dialogo Spark Submission (Invio Spark)](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>Scenario 2: Visualizzare lo stato di esecuzione del processo Apache Spark

 1. Fare clic su **Monitoraggio**, quindi selezionare l'opzione **Spark Applications** (Applicazioni Spark). Verrà visualizzata l'applicazione Apache Spark inviata.

     ![Visualizzare l'applicazione Spark](./media/apache-spark-job-definitions/view-spark-application.png)

 2. Fare quindi clic sull'applicazione Apache Spark per visualizzare la finestra **LogQuery** (Query su log). È possibile visualizzare lo stato di esecuzione del processo da **LogQuery** (Query su log).
     
     ![Visualizzare la finestra LogQuery (Query su log) per l'applicazione Spark](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Scenario 3: Verificare il file di output

 1. Fare clic su **Dati**, quindi selezionare **Account di archiviazione**. Una volta completata l'esecuzione, è possibile passare all'account di archiviazione ADLS Gen2 e controllare gli output generati.

     ![Visualizzare il file di output](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>Aggiungere una definizione di processo Apache Spark a una pipeline

In questa sezione viene aggiunta una definizione di processo Apache Spark a una pipeline.

 1. Aprire una definizione di processo Apache Spark esistente.

 2. Fare clic sull'icona nell'angolo in alto a destra della finestra della definizione di processo Apache Spark e selezionare **Existing Pipeline** (Pipeline esistente) o **New pipeline** (Nuova pipeline). Per altre informazioni, fare riferimento alla pagina Pipeline.

     ![Aggiunta alla pipeline](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![Aggiunta alla pipeline](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come usare Azure Synapse Studio per creare definizioni di processi Apache Spark e quindi inviarle a un pool di Apache Spark. Successivamente sarà possibile usare Azure Synapse Studio per creare set di dati di Power BI e gestire i dati di Power BI.

