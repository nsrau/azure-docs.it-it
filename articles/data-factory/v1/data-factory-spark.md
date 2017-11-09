---
title: Chiamare i programmi Spark da Azure Data Factory | Microsoft Docs
description: "È possibile chiamare i programmi Spark da una data factory di Azure usando l'attività MapReduce."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 0eff48ec65a01a2fc3fa9f7652dd8e1a0fc8dd2a
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Chiamare i programmi Spark dalle pipeline Azure Data Factory

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Attività Hive](data-factory-hive-activity.md)
> * [Attività di Pig](data-factory-pig-activity.md)
> * [Attività MapReduce](data-factory-map-reduce.md)
> * [Attività di Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> * [Attività Spark](data-factory-spark.md)
> * [Attività di esecuzione batch di Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Attività della risorsa di aggiornamento di Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Attività stored procedure](data-factory-stored-proc-activity.md)
> * [Attività U-SQL di Data Lake Analytics](data-factory-usql-activity.md)
> * [Attività personalizzata di .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 di Azure Data Factory, disponibile a livello generale. Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere [Transform data using spark activity in Data Factory version 2](../transform-data-using-spark.md) (Trasformare dati tramite l'attività Spark in Data Factory versione 2).

## <a name="introduction"></a>Introduzione
L'attività Spark è una delle [attività di trasformazione dei dati](data-factory-data-transformation-activities.md) supportate da Azure Data Factory. Questa attività esegue il programma Spark specificato nel cluster Apache Spark in Azure HDInsight.    

> [!IMPORTANT]
> - L'attività Spark non supporta i cluster Spark HDInsight che usano Azure Data Lake Store come risorsa di archiviazione primaria.
> - L'attività Spark supporta solo cluster Spark HDInsight esistenti (personalizzati). Non supporta un servizio collegato HDInsight su richiesta.

## <a name="walkthrough-create-a-pipeline-with-spark-activity"></a>Procedura dettagliata: creare una pipeline con attività Spark
Di seguito viene illustrata la procedura usata in generale per creare una pipeline di Data Factory con un'attività Spark.  

1. Creare una data factory.
2. Creare un servizio collegato Archiviazione di Azure per collegare l'archiviazione di Azure associata al cluster Spark HDInsight alla data factory.     
2. Creare un servizio collegato HDInsight di Azure per collegare il cluster Apache Spark in HDInsight di Azure alla data factory.
3. Creare un set di dati che faccia riferimento al servizio collegato di Archiviazione di Azure. Attualmente, è necessario specificare un set di dati di output per un'attività anche se non viene prodotto alcun output.  
4. Creare una pipeline con l'attività Spark che faccia riferimento al servizio collegato HDInsight creato al passaggio 2. L'attività è configurata con il set di dati creato nel passaggio precedente come un set di dati di output. Il set di dati di output è ciò su cui si basa la pianificazione (oraria, giornaliera e così via). Pertanto, è necessario specificare il set di dati di output anche se l'attività non ha prodotto alcun output.

### <a name="prerequisites"></a>Prerequisiti
1. Creare un **account di Archiviazione di Azure generico** seguendo le istruzioni della procedura dettagliata: [Creare un account di archiviazione](../../storage/common/storage-create-storage-account.md#create-a-storage-account).  
2. Creare un **cluster Apache Spark in HDInsight di Azure** seguendo le istruzioni riportate nell'esercitazione: [Creare il cluster Apache Spark in HDInsight di Azure](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Associare l'account di archiviazione di Azure creato al passaggio 1 con questo cluster.  
3. Scaricare e leggere il file di script python **test.py**, disponibile all'indirizzo: [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).  
3.  Caricare **test.py** nella cartella **pyFiles** nel contenitore **adfspark** nell'archiviazione BLOB di Azure. Creare la cartella e il contenitore, se non esistono.

### <a name="create-data-factory"></a>Creare un'istanza di Data Factory
In questo passaggio iniziale viene creata la data factory.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Fare clic su **NUOVO** nel menu a sinistra e quindi su **Dati e analisi** e **Data factory**.
3. Nel pannello **Nuova data factory** immettere **SparkDF** come nome.

   > [!IMPORTANT]
   > Il nome della data factory di Azure deve essere **univoco a livello globale**. Se viene visualizzato l'errore **Il nome "SparkDF" per la data factory non è disponibile**, cambiare il nome della data factory, ad esempio nomeutenteSparkDFdate, e provare di nuovo a crearla. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'argomento [Azure Data Factory - Regole di denominazione](data-factory-naming-rules.md) .   
4. Selezionare la **sottoscrizione di Azure** in cui creare la data factory.
5. Selezionare un **gruppo di risorse** di Azure esistente o crearne uno nuovo.
6. Selezionare l'opzione **Aggiungi al dashboard**.  
6. Fare clic su **Crea** nel pannello **Nuova data factory**.

   > [!IMPORTANT]
   > Per creare istanze di data factory, è necessario essere membri del ruolo [Collaboratore Data factory](../../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) a livello di sottoscrizione/gruppo di risorse.
7. Nel **dashboard** del portale di Azure verrà visualizzata la data factory in fase di creazione:   
8. Dopo la creazione della data factory, viene visualizzata la pagina corrispondente con elencato il contenuto della data factory. Se non è possibile visualizzare la pagina della data factory, fare clic sul rispettivo riquadro nel dashboard.

    ![Pannello Data factory](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Creazione di servizi collegati
In questo passaggio si creano due servizi collegati, uno per collegare il cluster Spark alla data factory e l'altro per collegare l'archiviazione di Azure alla data factory.  

#### <a name="create-azure-storage-linked-service"></a>Creare il servizio collegato Archiviazione di Azure
In questo passaggio l'account di archiviazione di Azure viene collegato alla data factory. Un set di dati creato in un passaggio successivo di questa procedura dettagliata si riferisce a questo servizio collegato. Anche il servizio collegato HDInsight definito nel passaggio successivo fa riferimento a questo servizio collegato.  

1. Fare clic su **Creare e distribuire** nel pannello **Data Factory** per la data factory interessata. Verrà visualizzato l'editor di Data Factory.
2. Fare clic su **Nuovo archivio dati** e scegliere **Archiviazione di Azure**.

   ![Nuovo archivio dati - Archiviazione di Azure - Menu](./media/data-factory-spark/new-data-store-azure-storage-menu.png)
3. Nell'editor verrà visualizzato lo **script JSON** per la creazione di un servizio collegato Archiviazione di Azure.

   ![Servizio collegato Archiviazione di Azure](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)
4. Sostituire **account name** e **account key** con il nome e la chiave di accesso dell'account di archiviazione di Azure. Per informazioni su come ottenere la chiave di accesso alle risorse di archiviazione, vedere le informazioni su come visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione in [Gestire l'account di archiviazione](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).
5. Per distribuire il servizio collegato, fare clic su **Distribuisci** sulla barra dei comandi. Al termine della distribuzione del servizio collegato, la finestra **Bozza-1** verrà nascosta e nella visualizzazione albero a sinistra verrà visualizzato **AzureStorageLinkedService**.

#### <a name="create-hdinsight-linked-service"></a>Creare un servizio collegato HDInsight
In questo passaggio si crea un servizio collegato HDInsight di Azure per collegare il cluster Spark HDInsight alla data factory. In questo esempio il cluster HDInsight viene usato per eseguire il programma Spark specificato nell'attività Spark della pipeline.  

1. Fare clic su **... Altro** sulla barra degli strumenti, scegliere **Nuovo calcolo** e quindi fare clic su **Cluster HDInsight**.

    ![Creare un servizio collegato HDInsight](media/data-factory-spark/new-hdinsight-linked-service.png)
2. Copiare e incollare il frammento di codice seguente nella finestra **Bozza-1** . Nell'editor JSON editor seguire questa procedura:
    1. Specificare l'**URI** per il cluster Spark HDInsight. Ad esempio: `https://<sparkclustername>.azurehdinsight.net/`.
    2. Specificare il nome dell'**utente** che ha accesso al cluster Spark.
    3. Specificare la **password** per l'utente.
    4. Specificare il **servizio collegato Archiviazione di Azure** associato al cluster Spark HDInsight. Nell'esempio è: **AzureStorageLinkedService**.

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - L'attività Spark non supporta i cluster Spark HDInsight che usano Azure Data Lake Store come risorsa di archiviazione primaria.
    > - L'attività Spark supporta solo cluster Spark HDInsight esistenti (personalizzati). Non supporta un servizio collegato HDInsight su richiesta.

    Per informazioni dettagliate sul servizio collegato HDInsight, vedere [Servizio collegato HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).
3.  Per distribuire il servizio collegato, fare clic su **Distribuisci** sulla barra dei comandi.  

### <a name="create-output-dataset"></a>Creare il set di dati di output
Il set di dati di output è ciò su cui si basa la pianificazione (oraria, giornaliera e così via). Pertanto è necessario specificare il set di dati di output per l'attività Spark nella pipeline, anche se l'attività non produce alcun output. La specifica di un set di dati di input per l'attività è facoltativa.

1. Nell'**editor di Data Factory** fare clic su **... Altro** sulla barra dei comandi e quindi fare clic su **Nuovo set di dati** e selezionare **Archivio BLOB di Azure**.  
2. Copiare e incollare il frammento di codice seguente nella finestra Bozza-1. Il frammento JSON definisce un set di dati denominato **OutputDataset**. Specificare anche che i risultati devono essere archiviati nel contenitore BLOB denominato **adfspark** e nella cartella denominata **pyFiles/output**. Come accennato in precedenza, questo set di dati è un set di dati fittizio. Il programma Spark in questo esempio non produce alcun output. La sezione **availability** specifica che il set di dati di output viene prodotto su base giornaliera.  

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
3. Per distribuire il set di dati, fare clic su **Distribuisci** sulla barra dei comandi.


### <a name="create-pipeline"></a>Creare una pipeline
In questo passaggio viene creata una pipeline con un'**attività HDInsightSpark**. In questo momento la pianificazione è basata sul set di dati di output, quindi è necessario creare un set di dati di output anche se l'attività non genera alcun output. Se l'attività non richiede input, è possibile ignorare la creazione del set di dati di input. Pertanto in questo esempio non viene specificato alcun set di dati di input.

1. Nell'**editor di Data Factory** fare clic su **... Altro** sulla barra dei comandi e quindi fare clic su **Nuova pipeline**.
2. Sostituire lo script nella finestra Bozza-1 con lo script seguente:

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```
    Tenere presente quanto segue:
    - La proprietà **type** è impostata su **HDInsightSpark**.
    - Il **rootPath** è impostato su **adfspark\\pyFiles**, dove adfspark è il contenitore BLOB di Azure e pyFiles è la cartella di file nel contenitore. In questo esempio, l'archivio BLOB di Azure è quello associato al cluster Spark. È possibile caricare il file in un archivio di Azure diverso. In tal caso, creare un servizio collegato Archiviazione di Azure per collegare l'account di archiviazione alla data factory. Quindi, specificare il nome del servizio collegato come valore per la proprietà **sparkJobLinkedService**. Vedere [Proprietà dell'attività Spark](#spark-activity-properties) per informazioni dettagliate su questa e altre proprietà supportate dall'attività Spark.  
    - La proprietà **entryFilePath** è impostata su **test.py**, ovvero il file python.
    - La proprietà **getDebugInfo** è impostata su **Sempre**, a indicare che i file di log vengono sempre generati (con esito positivo o negativo).

        > [!IMPORTANT]
        > Non è consigliabile impostare questa proprietà su `Always` in un ambiente di produzione, a meno che non si stia tentando di risolvere un problema.
    - La sezione **outputs** presenta un set di dati di output. Anche se il programma Spark non genera alcun output, è necessario specificare un set di dati di output. Il set di dati di output è ciò su cui si basa la pianificazione della pipeline (oraria, giornaliera e così via).  

        Per informazioni dettagliate sulle proprietà supportate dall'attività Spark, vedere la sezione [Proprietà dell'attività Spark](#spark-activity-properties).
3. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire la pipeline.

### <a name="monitor-pipeline"></a>Monitorare la pipeline
1. Fare clic su **X** per chiudere i pannelli dell'editor di Data Factory e tornare al pannello Data Factory. Fare clic su **Monitoraggio e gestione** per avviare l'applicazione di monitoraggio in un'altra scheda.

    ![Riquadro Monitoraggio e gestione](media/data-factory-spark/monitor-and-manage-tile.png)
2. Modificare il filtro **Ora di inizio** in alto su **2/1/2017** e fare clic su **Applica**.
3. Verrà visualizzata una sola finestra di attività, in quanto tra l'ora di inizio (2017-02-01) e l'ora di fine (2017-02-02) della pipeline c'è un solo giorno. Verificare che lo stato della sezione dati sia **Pronto**.

    ![Monitorare la pipeline](media/data-factory-spark/monitor-and-manage-app.png)    
4. Selezionare la **finestra attività** per visualizzare i dettagli sull'esecuzione dell'attività. Se si verifica un errore, i dettagli sono visualizzati nel riquadro di destra.

### <a name="verify-the-results"></a>Verificare i risultati

1. Avviare **Jupyter Notebook** per il cluster Spark HDInsight accedendo a: https://CLUSTERNAME.azurehdinsight.net/jupyter. È possibile anche avviare il dashboard del cluster per il cluster Spark HDInsight e quindi avviare **Jupyter Notebook**.
2. Fare clic su **Nuovo** -> **PySpark** per avviare un nuovo notebook.

    ![Nuovo notebook Jupyter](media/data-factory-spark/jupyter-new-book.png)
3. Eseguire il comando seguente copiando e incollando il testo e premendo **MAIUSC + INVIO** alla fine della seconda istruzione.  

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
4. Verificare che i dati della tabella hvac siano visibili:  

    ![Risultati della query Jupyter](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Per informazioni dettagliate, vedere la sezione [Eseguire una query SQL Spark](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). 

### <a name="troubleshooting"></a>Risoluzione dei problemi
Poiché **getDebugInfo** è impostato su **Sempre**, è possibile vedere una sottocartella **log** nella cartella **pyFiles** nel contenitore BLOB di Azure. Il file di log nella cartella di registro offre dettagli aggiuntivi. Tale file è particolarmente utile quando si verifica un errore. In un ambiente di produzione può risultare utile impostarlo su **Errore**.

Per la risoluzione del problema, eseguire i passaggi seguenti:


1. Accedere a `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![Applicazione interfaccia utente di Yarn](media/data-factory-spark/yarnui-application.png)  
2. Fare clic su **Log** per uno dei tentativi di esecuzione.

    ![Pagina Applicazione](media/data-factory-spark/yarn-applications.png)
3. Nella pagina del log dovrebbero essere visualizzate informazioni aggiuntive sull'errore.

    ![Errore log](media/data-factory-spark/yarnui-application-error.png)

Le sezioni seguenti contengono informazioni sulle entità di Data Factory e sull'uso del cluster Apache Spark e dell'attività di Spark nella data factory.

## <a name="spark-activity-properties"></a>Proprietà dell'attività Spark
Di seguito è riportata la definizione JSON di esempio di una pipeline con attività Spark:    

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    }
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

La tabella seguente fornisce le descrizioni delle proprietà JSON usate nella definizione JSON:

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| name | Nome dell'attività nella pipeline. | Sì |
| Descrizione | Testo che descrive l'attività. | No |
| type | Questa proprietà deve essere impostata su HDInsightSpark. | Sì |
| linkedServiceName | Riferimento a un servizio collegato HDInsight in cui viene eseguito il programma Spark. | Sì |
| rootPath | Contenitore BLOB di Azure e cartella che contiene il file Spark. Il nome del file distingue tra maiuscole e minuscole. | Sì |
| entryFilePath | Percorso relativo alla cartella radice del pacchetto/codice Spark. | Sì |
| className | Classe principale Java/Spark dell'applicazione | No |
| arguments | Elenco di argomenti della riga di comando del programma Spark. | No |
| proxyUser | Account utente da rappresentare per eseguire il programma Spark | No |
| sparkConfig | Specificare i valori delle proprietà di configurazione di Spark elencati nell'argomento: [Configurazione di SparK: proprietà dell'applicazione](https://spark.apache.org/docs/latest/configuration.html#available-properties). | No |
| getDebugInfo | Specifica quando i file di log di Spark vengono copiati nell'archiviazione di Azure usata dal cluster HDInsight (o) specificata da sparkJobLinkedService. Valori consentiti: None, Always o Failure. Valore predefinito: None. | No |
| sparkJobLinkedService | Il servizio collegato di archiviazione di Azure che contiene il file di processo, le dipendenze e i log di Spark.  Se non si specifica un valore per questa proprietà, viene usato lo spazio di archiviazione associato al cluster HDInsight. | No |

## <a name="folder-structure"></a>Struttura di cartelle
L'attività Spark non supporta uno script inline come invece fanno le attività Pig e Hive. I processi Spark sono anche più estendibili dei processi Pig/Hive. Per i processi Spark è possibile offrire più dipendenze, ad esempio pacchetti jar (posizionati in CLASSPATH di java), file python (posizionati in PYTHONPATH) e qualsiasi altro file.

Creare la struttura seguente di cartelle nell'archivio BLOB di Azure a cui fa riferimento il servizio collegato HDInsight. Caricare i file dipendenti nelle sottocartelle appropriate all'interno della cartella radice rappresentata da **entryFilePath**. Ad esempio, caricare i file python nella sottocartella pyFiles e i file jar nella sottocartella jars della cartella radice. In fase di esecuzione, il servizio Data Factory prevede la struttura di cartelle seguente nell'archivio BLOB di Azure:     

| Path | Descrizione | Obbligatorio | Tipo |
| ---- | ----------- | -------- | ---- |
| . | Percorso radice del processo Spark nel servizio collegato di archiviazione  | Sì | Cartella |
| &lt;definito dall'utente &gt; | Percorso che punta al file di ingresso del processo Spark | Sì | File |
| ./jars | Tutti i file in questa cartella vengono caricati e inseriti nel classpath java del cluster | No | Cartella |
| ./pyFiles | Tutti i file in questa cartella vengono caricati e inseriti nel PYTHONPATH del cluster | No | Cartella |
| ./files | Tutti i file in questa cartella vengono caricati e inseriti nella directory di lavoro executor | No | Cartella |
| ./archives | Tutti i file in questa cartella sono decompressi | No | Cartella |
| ./logs | Cartella in cui sono archiviati i log del cluster Spark.| No | Cartella |

Di seguito è riportato un esempio per una risorsa di archiviazione che contiene due file di processo Spark nell'archivio BLOB di Azure a cui fa riferimento il servizio collegato HDInsight.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
