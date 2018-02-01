---
title: Chiamare i programmi Spark da Azure Data Factory | Microsoft Docs
description: "Informazioni su come chiamare programmi Spark da una data factory di Azure usando l'attività MapReduce."
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
ms.date: 01/10/2018
ms.author: spelluru
robots: noindex
ms.openlocfilehash: f03c3b6e275c0bc97df9e687a20acf45956664d2
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Chiamare i programmi Spark dalle pipeline Azure Data Factory

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Attività Hive](data-factory-hive-activity.md)
> * [Attività Pig](data-factory-pig-activity.md)
> * [Attività MapReduce](data-factory-map-reduce.md)
> * [Attività di streaming di Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Attività Spark](data-factory-spark.md)
> * [Machine Learning Bach Execution Activity](data-factory-azure-ml-batch-execution-activity.md) (Attività di esecuzione batch di Machine Learning)
> * [Attività Aggiorna risorsa di Machine Learning ](data-factory-azure-ml-update-resource-activity.md)
> * [Attività stored procedure](data-factory-stored-proc-activity.md)
> * [Attività U-SQL di Data Lake Analytics](data-factory-usql-activity.md)
> * [Attività personalizzata .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 di Azure Data Factory, disponibile a livello generale. Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere [Trasformare dati tramite l'attività Spark in Data Factory versione 2](../transform-data-using-spark.md).

## <a name="introduction"></a>Introduzione
L'attività Spark è una delle [attività di trasformazione dei dati](data-factory-data-transformation-activities.md) supportate da Data Factory. Questa attività esegue il programma Spark specificato nel cluster Spark in Azure HDInsight. 

> [!IMPORTANT]
> - L'attività Spark non supporta i cluster Spark HDInsight che usano Azure Data Lake Store come risorsa di archiviazione primaria.
> - L'attività Spark supporta solo cluster Spark HDInsight esistenti (personalizzati). Non supporta un servizio collegato HDInsight su richiesta.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Procedura dettagliata: Creare una pipeline con un'attività Spark
Di seguito viene illustrata la procedura usata in genere per creare una pipeline di data factory con un'attività Spark: 

* Creare una data factory.
* Creare un servizio collegato Archiviazione di Azure per collegare alla data factory la risorsa di archiviazione associata al cluster Spark HDInsight.
* Creare un servizio collegato HDInsight per collegare il cluster Spark in HDInsight alla data factory.
* Creare un set di dati che faccia riferimento al servizio collegato di archiviazione. Attualmente, è necessario specificare un set di dati di output per un'attività anche se non viene prodotto alcun output. 
* Creare una pipeline con attività Spark che faccia riferimento al servizio collegato HDInsight creato. L'attività è configurata con il set di dati creato nel passaggio precedente come un set di dati di output. Il set di dati di output è ciò su cui si basa la pianificazione (oraria, giornaliera). È pertanto necessario specificare il set di dati di output anche se l'attività non genera in realtà alcun output.

### <a name="prerequisites"></a>prerequisiti
1. Creare un account di archiviazione generico seguendo le istruzioni in [Creare un account di archiviazione](../../storage/common/storage-create-storage-account.md#create-a-storage-account).

2. Creare un cluster Spark in HDInsight seguendo le istruzioni riportate nell'esercitazione [Creare un cluster Spark in HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Associare l'account di archiviazione creato al passaggio 1 a questo cluster.

3. Scaricare e leggere il file di script Python **test.py** disponibile nel sito Web all'indirizzo [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).

4. Caricare **test.py** nella cartella **pyFiles** nel contenitore **adfspark** nell'archiviazione BLOB. Creare la cartella e il contenitore, se non esistono.

### <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
Per creare una data factory, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare **Nuovo** > **Dati e analisi** > **Data Factory**.

3. Nel pannello **Nuova data factory** immettere **SparkDF** nel campo **Nome**.

   > [!IMPORTANT]
   > È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato l'errore "Il nome SparkDF per la data factory non è disponibile", modificare il nome della data factory. Usare ad esempio nomeutenteSparkDFdata e creare di nuovo la data factory. Per altre informazioni sulle regole di denominazione, vedere [Data Factory - Regole di denominazione](data-factory-naming-rules.md).

4. In **Sottoscrizione** selezionare la sottoscrizione di Azure in cui creare la data factory.

5. Selezionare un gruppo di risorse esistente o creare un gruppo di risorse di Azure.

6. Selezionare la casella di controllo **Aggiungi al dashboard**.

7. Selezionare **Create**.

   > [!IMPORTANT]
   > Per creare istanze di data factory, l'utente deve essere membro del ruolo [Collaboratore Data factory](../../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) a livello di sottoscrizione/gruppo di risorse.

8. Nel dashboard del portale di Azure viene visualizzata la data factory creata.

9. Dopo la creazione della data factory, viene visualizzata la pagina **Data factory** con il relativo contenuto. Se la pagina **Data factory** non è visualizzata, selezionare il riquadro corrispondente nel dashboard.

    ![Pannello Data factory](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Creare servizi collegati
In questo passaggio vengono creati due servizi collegati. Un servizio collega il cluster Spark e l'altro collega la risorsa di archiviazione alla data factory. 

#### <a name="create-a-storage-linked-service"></a>Creare un servizio collegato di archiviazione
In questo passaggio, l'account di archiviazione viene collegato alla data factory. Un set di dati creato in un passaggio successivo di questa procedura dettagliata si riferisce a questo servizio collegato. Anche il servizio collegato HDInsight definito nel passaggio successivo fa riferimento a questo servizio collegato. 

1. Nel pannello **Data factory** selezionare **Creare e distribuire**. Viene visualizzato l'editor di Data Factory.

2. Selezionare **Nuovo archivio dati** e scegliere **Archiviazione di Azure**.

   ![Nuovo archivio dati](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

3. Lo script JSON usato per creare un servizio collegato di archiviazione viene visualizzato nell'editor.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

4. Sostituire **nome account** e **chiave account** con il nome e la chiave di accesso dell'account di archiviazione. Per informazioni su come ottenere la chiave di accesso alle risorse di archiviazione, vedere come visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione in [Gestire l'account di archiviazione](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

5. Per distribuire il servizio collegato, selezionare **Distribuisci** sulla barra dei comandi. Al termine della distribuzione del servizio collegato, la finestra Bozza-1 viene nascosta e nella visualizzazione struttura ad albero a sinistra viene visualizzato **AzureStorageLinkedService**.

#### <a name="create-an-hdinsight-linked-service"></a>Creare un servizio collegato HDInsight
In questo passaggio viene creato un servizio collegato HDInsight per collegare il cluster Spark HDInsight alla data factory. In questo esempio il cluster HDInsight viene usato per eseguire il programma Spark specificato nell'attività Spark della pipeline. 

1. Nell'editor di Data Factory selezionare **Altro** > **Nuovo calcolo** > **Cluster HDInsight**.

    ![Creare un servizio collegato HDInsight](media/data-factory-spark/new-hdinsight-linked-service.png)

2. Copiare e incollare il frammento di codice seguente nella finestra Bozza-1. Nell'editor JSON eseguire la procedura seguente:

    a. Specificare l'URI per il cluster Spark HDInsight. Ad esempio: `https://<sparkclustername>.azurehdinsight.net/`.

    b. Specificare il nome dell'utente che ha accesso al cluster Spark.

    c. Specificare la password dell'utente.

    d. Specificare il servizio collegato di archiviazione associato al cluster Spark HDInsight. In questo esempio è AzureStorageLinkedService.

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

    Per altre informazioni sul servizio collegato HDInsight, vedere [Servizio collegato HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

3. Per distribuire il servizio collegato, selezionare **Distribuisci** sulla barra dei comandi. 

### <a name="create-the-output-dataset"></a>Creare il set di dati di output
Il set di dati di output è ciò su cui si basa la pianificazione (oraria, giornaliera). È quindi necessario specificare nella pipeline un set di dati di output per l'attività Spark, anche se l'attività non genera alcun output. La specifica di un set di dati di input per l'attività è facoltativa.

1. Nell'editor di Data Factory fare clic su **Altro** > **Nuovo set di dati** > **Archiviazione BLOB di Azure**.

2. Copiare e incollare il frammento di codice seguente nella finestra Bozza-1. Il frammento JSON definisce un set di dati denominato **OutputDataset**. Specificare anche che i risultati devono essere archiviati nel contenitore BLOB denominato **adfspark** e nella cartella denominata **pyFiles/output**. Come accennato in precedenza, questo set di dati è fittizio. Il programma Spark in questo esempio non genera alcun output. La sezione **availability** specifica che il set di dati di output viene prodotto su base giornaliera. 

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
3. Per distribuire il set di dati, selezionare **Distribuisci** sulla barra dei comandi.


### <a name="create-a-pipeline"></a>Creare una pipeline
In questo passaggio viene creata una pipeline con un'attività HDInsightSpark. La pianificazione è al momento basata sul set di dati di output. È quindi necessario creare un set di dati di output anche se l'attività non genera alcun output. Se l'attività non richiede input, è possibile ignorare la creazione del set di dati di input. Pertanto in questo esempio non viene specificato alcun set di dati di input.

1. Nell'editor di Data Factory selezionare **Altro** > **Nuova pipeline**.

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

    a. La proprietà **type** è impostata su **HDInsightSpark**.

    b. La proprietà **rootPath** è impostata su **adfspark\\pyFiles**, dove adfspark è il contenitore BLOB e pyFiles è la cartella di file nel contenitore. In questo esempio, l'archivio BLOB è quello associato al cluster Spark. È possibile caricare il file in un account di archiviazione diverso. In tal caso, creare un servizio collegato di archiviazione per collegare l'account di archiviazione alla data factory. Quindi, specificare il nome del servizio collegato come valore per la proprietà **sparkJobLinkedService**. Per altre informazioni su questa e altre proprietà supportate dall'attività Spark, vedere [Proprietà dell'attività Spark](#spark-activity-properties).

    c. La proprietà **entryFilePath** è impostata su **test.py**, ovvero sul file Python.

    d. La proprietà **getDebugInfo** è impostata su **Sempre**, a indicare che i file di log vengono sempre generati (con esito positivo o negativo).

    > [!IMPORTANT]
    > Non è consigliabile impostare questa proprietà su `Always` in un ambiente di produzione, a meno che non si stia tentando di risolvere un problema.

    e. La sezione **outputs** presenta un set di dati di output. Anche se il programma Spark non genera alcun output, è necessario specificare un set di dati di output. Il set di dati di output è ciò su cui si basa la pianificazione della pipeline (oraria, giornaliera). 

    Per altre informazioni sulle proprietà supportate dall'attività Spark, vedere la sezione [Proprietà dell'attività Spark](#spark-activity-properties).

3. Per distribuire la pipeline, selezionare **Distribuisci** sulla barra dei comandi.

### <a name="monitor-a-pipeline"></a>Monitorare una pipeline
1. Nel pannello **Data factory** selezionare **Monitoraggio e gestione** per avviare l'applicazione di monitoraggio in un'altra scheda.

    ![Riquadro Monitoraggio e gestione](media/data-factory-spark/monitor-and-manage-tile.png)

2. Modificare il filtro **Ora di inizio** in alto su **2/1/2017** e selezionare **Applica**.

3. Viene visualizzata una sola finestra di attività, perché tra l'ora di inizio (2017-02-01) e l'ora di fine (2017-02-02) della pipeline c'è un solo giorno. Verificare che lo stato della sezione dati sia **Pronto**.

    ![Monitorare la pipeline](media/data-factory-spark/monitor-and-manage-app.png)

4. Nell'elenco **Activity Windows** (Finestre attività) selezionare un'esecuzione attività per visualizzarne i dettagli. Se si verifica un errore, i dettagli sono visualizzati nel riquadro di destra.

### <a name="verify-the-results"></a>Verificare i risultati

1. Avviare il notebook di Jupyter per il cluster HDInsight Spark passando a [questo sito Web](https://CLUSTERNAME.azurehdinsight.net/jupyter). È anche possibile aprire il dashboard del cluster Spark HDInsight e quindi avviare il notebook di Jupyter.

2. Per avviare un nuovo notebook selezionare **Nuovo** > **PySpark**.

    ![Nuovo notebook Jupyter](media/data-factory-spark/jupyter-new-book.png)

3. Eseguire il comando seguente copiando e incollando il testo e premendo Maiusc + Invio alla fine della seconda istruzione:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
4. Verificare che i dati della tabella hvac siano visibili. 

    ![Risultati della query Jupyter](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Per istruzioni dettagliate, vedere la sezione [Eseguire una query SQL Spark](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). 

### <a name="troubleshooting"></a>risoluzione dei problemi
Poiché getDebugInfo è impostato su **Always** (Sempre), è possibile vedere una sottocartella log nella cartella pyFiles nel contenitore BLOB di Azure. Il file di log nella cartella log offre informazioni aggiuntive. Tale file è particolarmente utile quando si verifica un errore. In un ambiente di produzione è consigliabile impostarlo su **Failure** (Operazione non riuscita).

Per altre azioni di risoluzione del problema, eseguire la procedura seguente:


1. Passare a `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![Applicazione interfaccia utente di Yarn](media/data-factory-spark/yarnui-application.png)

2. Selezionare **Log** per uno dei tentativi di esecuzione.

    ![Pagina Applicazione](media/data-factory-spark/yarn-applications.png)

3. Nella pagina del log sono visualizzate le informazioni aggiuntive sull'errore seguenti:

    ![Errore log](media/data-factory-spark/yarnui-application-error.png)

Le sezioni seguenti contengono informazioni sulle entità della data factory per l'uso del cluster Spark e dell'attività Spark nella data factory.

## <a name="spark-activity-properties"></a>Proprietà dell'attività Spark
Di seguito è riportata la definizione JSON di esempio di una pipeline con un'attività Spark: 

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

La tabella seguente descrive le proprietà JSON usate nella definizione JSON.

| Proprietà | DESCRIZIONE | Obbligatoria |
| -------- | ----------- | -------- |
| name | Nome dell'attività nella pipeline. | Sì |
| description | Testo che descrive la funzione dell'attività. | No  |
| type | Questa proprietà deve essere impostata su HDInsightSpark. | Sì |
| linkedServiceName | Riferimento a un servizio collegato HDInsight in cui viene eseguito il programma Spark. | Sì |
| rootPath | Contenitore BLOB e cartella che contiene il file Spark. Il nome del file fa distinzione tra maiuscole e minuscole. | Sì |
| entryFilePath | Percorso relativo alla cartella radice del pacchetto/codice Spark. | Sì |
| className | Classe principale Java/Spark dell'applicazione. | No  |
| arguments | Elenco di argomenti della riga di comando del programma Spark. | No  |
| proxyUser | Account utente da rappresentare per eseguire il programma Spark. | No  |
| sparkConfig | Specificare i valori delle proprietà di configurazione di Spark elencati in [Spark configuration: Application properties](https://spark.apache.org/docs/latest/configuration.html#available-properties) (Configurazione di Spark: proprietà dell'applicazione). | No  |
| getDebugInfo | Specifica quando i file di log di Spark vengono copiati nella risorsa di archiviazione usata dal cluster HDInsight (o) specificata da sparkJobLinkedService. Valori consentiti: None (Nessuna), Always (Sempre) o Failure (Operazione non riuscita). Il valore predefinito è None (Nessuna). | No  |
| sparkJobLinkedService | Il servizio collegato di archiviazione che contiene il file di processo, le dipendenze e i log di Spark. Se non si specifica un valore per questa proprietà, viene usata la risorsa di archiviazione associata al cluster HDInsight. | No  |

## <a name="folder-structure"></a>Struttura di cartelle
L'attività Spark non supporta uno script inline come invece fanno le attività Pig e Hive. I processi Spark sono anche più estendibili dei processi Pig/Hive. Per i processi Spark è possibile offrire più dipendenze, ad esempio pacchetti jar (disponibili in CLASSPATH di Java), file Python (disponibili in PYTHONPATH) e qualsiasi altro file.

Creare la struttura di cartelle seguente nell'archivio BLOB a cui fa riferimento il servizio collegato HDInsight. Caricare quindi i file dipendenti nelle sottocartelle appropriate all'interno della cartella radice rappresentata da **entryFilePath**. Caricare, ad esempio, i file Python nella sottocartella pyFiles e i file jar nella sottocartella jars della cartella radice. In runtime, il servizio Data Factory prevede la struttura di cartelle seguente nell'archivio BLOB: 

| path | DESCRIZIONE | Obbligatoria | type |
| ---- | ----------- | -------- | ---- |
| . | Percorso radice del processo Spark nel servizio collegato di archiviazione. | Sì | Cartella |
| &lt;definito dall'utente &gt; | Percorso che punta al file di ingresso del processo Spark. | Sì | File |
| ./jars | Tutti i file in questa cartella vengono caricati e inseriti in classpath Java del cluster. | No  | Cartella |
| ./pyFiles | Tutti i file in questa cartella vengono caricati e inseriti in PYTHONPATH del cluster. | No  | Cartella |
| ./files | Tutti i file in questa cartella vengono caricati e inseriti nella directory di lavoro executor. | No  | Cartella |
| ./archives | Tutti i file in questa cartella sono decompressi. | No  | Cartella |
| ./logs | Cartella in cui sono archiviati i log del cluster Spark.| No  | Cartella |

Di seguito è riportato un esempio di risorsa di archiviazione che contiene due file di processo Spark nell'archivio BLOB a cui fa riferimento il servizio collegato HDInsight:

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
