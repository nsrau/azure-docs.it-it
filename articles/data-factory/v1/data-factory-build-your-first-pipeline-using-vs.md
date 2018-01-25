---
title: Creare la prima data factory (Visual Studio) | Documentazione Microsoft
description: In questa esercitazione viene creata una pipeline di esempio di Azure Data Factory usando Visual Studio.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 7398c0c9-7a03-4628-94b3-f2aaef4a72c5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 49fb249b6ff1169527829c77a6539926ec99b912
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="tutorial-create-a-data-factory-by-using-visual-studio"></a>Esercitazione: Creare una data factory con Visual Studio
> [!div class="op_single_selector" title="Tools/SDKs"]
> * [Panoramica e prerequisiti](data-factory-build-your-first-pipeline.md)
> * [Azure portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Modello di Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere la [guida introduttiva per la creazione di una data factory con Azure Data Factory versione 2](../quickstart-create-data-factory-dot-net.md).

Questa esercitazione illustra come creare una data factory di Azure con Visual Studio. Si crea un progetto di Visual Studio usando il modello di progetto DataFactory, si definiscono le entità della data factory (servizi collegati, set di dati e pipeline) in formato JSON e quindi si pubblicano/distribuiscono queste entità nel cloud. 

La pipeline in questa esercitazione include un'attività, l'**attività Hive di HDInsight**, che esegue uno script Hive in un cluster Azure HDInsight per trasformare i dati di input e generare i dati di output. L'esecuzione della pipeline è pianificata una volta al mese tra le ore di inizio e di fine specificate. 

> [!NOTE]
> Questa esercitazione non illustra come copiare dati con Azure Data Factory. Per un'esercitazione su come copiare dati usando Azure Data Factory, vedere [Copiare dati da un archivio BLOB al database SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Una pipeline può includere più attività ed è possibile concatenarne due, ovvero eseguire un'attività dopo l'altra, impostando il set di dati di output di un'attività come set di dati di input dell'altra. Per altre informazioni, vedere [pianificazione ed esecuzione in Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).


## <a name="walkthrough-create-and-publish-data-factory-entities"></a>Procedura dettagliata: Creare e pubblicare le entità della data factory
Di seguito sono elencati i passaggi da eseguire nell'ambito di questa procedura dettagliata:

1. Creare due servizi collegati, **AzureStorageLinkedService1** e **HDInsightOnDemandLinkedService1**. 
   
    In questa esercitazione, i dati sia di input che di output per l'attività Hive si trovano nello stesso archivio BLOB di Azure. Per elaborare i dati di input esistenti e generare i dati di output si usa un cluster HDInsight su richiesta, che viene creato automaticamente da Azure Data Factory in fase di esecuzione quando i dati di input sono pronti per l'elaborazione. È necessario collegare i calcoli e gli archivi dati alla data factory affinché il servizio Data Factory possa connettervisi in fase di esecuzione. Di conseguenza, si collega l'account di archiviazione di Azure alla data factory usando AzureStorageLinkedService1 e si collega un cluster HDInsight su richiesta usando HDInsightOnDemandLinkedService1. Durante la pubblicazione, si specifica il nome della data factory da creare o di una data factory esistente.  
2. Creare due set di dati, **InputDataset** e **OutputDataset**, che rappresentano i dati di input e di output archiviati nell'archivio BLOB di Azure. 
   
    Le definizioni dei set di dati fanno riferimento al servizio collegato Archiviazione di Azure creato nel passaggio precedente. Per InputDataset, si specificano il contenitore BLOB (adfgetstarted) e la cartella (inputdata) che contiene un BLOB con i dati di input. Per OutputDataset, si specificano il contenitore BLOB (adfgetstarted) e la cartella (partitioneddata) che contiene i dati di output. Specificare anche altre proprietà come struttura, disponibilità e criteri.
3. Creare una pipeline denominata **MyFirstPipeline**. 
  
    In questa procedura dettagliata, la pipeline include una sola attività, l'**attività Hive di HDInsight**, che trasforma i dati di input per generare i dati di output eseguendo uno script Hive in un cluster HDInsight su richiesta. Per altre informazioni in merito, vedere l'articolo relativo all'[attività Hive](data-factory-hive-activity.md). 
4. Creare una data factory denominata **DataFactoryUsingVS**. Distribuire la data factory e tutte le entità di Data Factory, ovvero i servizi collegati, le tabelle e la pipeline.
5. Dopo la pubblicazione, si useranno i pannelli del portale e l'app di monitoraggio e gestione di Azure per monitorare la pipeline. 
  
### <a name="prerequisites"></a>prerequisiti
1. Vedere la [panoramica dell'esercitazione](data-factory-build-your-first-pipeline.md) ed eseguire i passaggi relativi ai **prerequisiti** . È anche possibile selezionare l'opzione **Panoramica e prerequisiti** nell'elenco a discesa in alto per passare a tale articolo. Dopo aver completato i prerequisiti, tornare a questo articolo selezionando l'opzione **Visual Studio** nell'elenco a discesa.
2. Per creare istanze di data factory, è necessario essere membri del ruolo [Collaboratore Data factory](../../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) a livello di sottoscrizione/gruppo di risorse.  
3. È necessario disporre dei seguenti prodotti installati nel computer in uso:
   * Visual Studio 2013 o Visual Studio 2015
   * Download di Azure SDK per Visual Studio 2013 o Visual Studio 2015. Passare alla [pagina di download di Azure](https://azure.microsoft.com/downloads/) e fare clic su **VS 2013** o **VS 2015** nella sezione **.NET**.
   * Scaricare il plug-in Azure Data Factory più recente per Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) o [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). È anche possibile aggiornare il plug-in nel modo seguente: dal menu scegliere **Strumenti** -> **Estensioni e aggiornamenti** -> **Online** -> **Visual Studio Gallery** -> **Microsoft Azure Data Factory Tools for Visual Studio** -> **Aggiorna**.

Verrà ora usato Visual Studio per creare una data factory di Azure.

### <a name="create-visual-studio-project"></a>Creare un progetto di Visual Studio
1. Avviare **Visual Studio 2013** o **Visual Studio 2015**. Fare clic su **File**, scegliere **Nuovo** e quindi fare clic su **Progetto**. Si dovrebbe vedere la finestra di dialogo **Nuovo progetto** .  
2. Nella finestra di dialogo **Nuovo progetto** selezionare il modello **DataFactory** e fare clic su **Progetto data factory vuoto**.   

    ![Finestra di dialogo Nuovo progetto](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)
3. Immettere un **nome** per il progetto, un **percorso** e un nome per la **soluzione** e fare clic su **OK**.

    ![Controllers\HomeController.cs](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### <a name="create-linked-services"></a>Creare servizi collegati
In questo passaggio si creano due servizi collegati: **Archiviazione di Azure** e **HDInsight su richiesta**. 

Il servizio collegato Archiviazione di Azure collega l'account di archiviazione di Azure alla data factory fornendo le informazioni di connessione. Il servizio Data Factory usa la stringa di connessione dell'impostazione del servizio collegato per connettersi alla risorsa di archiviazione di Azure in fase di esecuzione. Tale risorsa di archiviazione contiene i dati di input e di output per la pipeline e il file di script Hive usato dall'attività Hive. 

Con il servizio collegato HDInsight su richiesta, il cluster HDInsight viene creato automaticamente in fase di esecuzione quando i dati di input sono pronti per l'elaborazione. Il cluster viene eliminato al termine dell'elaborazione e dopo che è rimasto inattivo per il periodo di tempo specificato. 

> [!NOTE]
> Per creare una data factory, specificarne il nome e le impostazioni al momento della pubblicazione della soluzione Data Factory.

#### <a name="create-azure-storage-linked-service"></a>Creare il servizio collegato Archiviazione di Azure
1. Fare clic con il pulsante destro del mouse su **Servizi collegati** in Esplora soluzioni, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.      
2. Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **Servizio collegato Archiviazione di Azure** nell'elenco e fare clic su **Aggiungi**.
    ![Servizio collegato Archiviazione di Azure](./media/data-factory-build-your-first-pipeline-using-vs/new-azure-storage-linked-service.png)
3. Sostituire `<accountname>` e `<accountkey>` con il nome e la chiave dell'account di archiviazione di Azure. Per informazioni su come ottenere la chiave di accesso alle risorse di archiviazione, vedere le informazioni su come visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione in [Gestire l'account di archiviazione](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).
    ![Servizio collegato Archiviazione di Azure](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)
4. Salvare il file **AzureStorageLinkedService1.json** .

#### <a name="create-azure-hdinsight-linked-service"></a>Creare un servizio collegato Azure HDInsight
1. Fare clic con il pulsante destro del mouse su **Servizi collegati** in **Esplora soluzioni**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.
2. Selezionare **HDInsight On Demand Linked Service** (Servizio collegato HDInsight su richiesta) e fare clic su **Aggiungi**.
3. Sostituire il codice **JSON** con quello seguente:

     ```json
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
        "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService1"
            }
        }
    }
    ```

    La tabella seguente fornisce le descrizioni delle proprietà JSON usate nel frammento di codice:

    Proprietà | DESCRIZIONE
    -------- | ----------- 
    ClusterSize | Specifica le dimensioni del cluster Hadoop HDInsight.
    TimeToLive | Specifica il tempo di inattività del cluster HDInsight, prima che sia eliminato.
    linkedServiceName | Specifica l'account di archiviazione usato per archiviare i log generati dal cluster Hadoop HDInsight. 

    > [!IMPORTANT]
    > Il cluster HDInsight crea un **contenitore predefinito** nell'archivio BLOB specificato nel file JSON (linkedServiceName). HDInsight non elimina il contenitore quando viene eliminato il cluster. Questo comportamento dipende dalla progettazione. Con il servizio collegato HDInsight su richiesta, viene creato un cluster HDInsight ogni volta che viene elaborata una sezione, a meno che non esista un cluster attivo (timeToLive). Il cluster viene eliminato al termine dell'elaborazione.
    > 
    > Man mano che vengono elaborate più sezioni, vengono visualizzati numerosi contenitori nell'archivio BLOB di Azure. Se non sono necessari per risolvere i problemi relativi ai processi, è possibile eliminarli per ridurre i costi di archiviazione. I nomi dei contenitori seguono il modello `adf<yourdatafactoryname>-<linkedservicename>-datetimestamp`. Per eliminare i contenitori nell'archivio BLOB di Azure, usare strumenti come [Microsoft Azure Storage Explorer](http://storageexplorer.com/) .

    Per altre informazioni sulle proprietà JSON, vedere l'articolo relativo ai [servizi collegati di calcolo](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). 
4. Salvare il file **HDInsightOnDemandLinkedService1.json** .

### <a name="create-datasets"></a>Creare set di dati
In questo passaggio vengono creati set di dati per rappresentare i dati di input e di output per l'elaborazione Hive. I set di dati fanno riferimento all'oggetto **AzureStorageLinkedService1** creato in precedenza in questa esercitazione. Il servizio collegato punta a un account di archiviazione di Azure e i set di dati specificano il contenitore, la cartella e il nome del file nella risorsa di archiviazione che contiene i dati di input e di output.   

#### <a name="create-input-dataset"></a>Creare set di dati di input
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Tabelle**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.
2. Selezionare **BLOB di Azure** dall'elenco, cambiare il nome del file in **InputDataSet.json** e quindi fare clic su **Aggiungi**.
3. Sostituire il codice **JSON** nell'editor con il frammento di codice JSON seguente:

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    Questo frammento JSON definisce un set di dati denominato **AzureBlobInput** che rappresenta i dati di input per l'attività Hive nella pipeline. Si specifica che i dati di input si trovano nel contenitore BLOB denominato `adfgetstarted` e nella cartella denominata `inputdata`.

    La tabella seguente fornisce le descrizioni delle proprietà JSON usate nel frammento di codice:

    Proprietà | DESCRIZIONE |
    -------- | ----------- |
    type |La proprietà type è impostata su **AzureBlob** perché i dati risiedono nell'archivio BLOB di Azure.
    linkedServiceName | Fa riferimento all'oggetto AzureStorageLinkedService1 creato in precedenza.
    fileName |Questa proprietà è facoltativa. Se si omette questa proprietà, vengono prelevati tutti i file da folderPath. In tal caso viene elaborato solo il file input.log.
    type | I file di log sono in formato testo, quindi viene usato TextFormat. |
    columnDelimiter | Le colonne nei file di log sono delimitate dalla virgola (`,`).
    frequenza/intervallo | La frequenza è impostata su Month e l'intervallo è 1, ciò significa che le sezioni di input sono disponibili con cadenza mensile.
    external | Questa proprietà è impostata su true se i dati di input per l'attività non vengono generati dalla pipeline. Viene specificata solo per i set di dati di input. Per il set di dati di input della prima attività, impostare sempre questa proprietà su true.
4. Salvare il file **InputDataset.json** .

#### <a name="create-output-dataset"></a>Creare il set di dati di output
Viene ora creato il set di dati di output per rappresentare i dati di output archiviati nell'archivio BLOB di Azure.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Tabelle**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.
2. Selezionare **Blob di Azure** dall'elenco, cambiare il nome del file in **OutputDataset.json** e quindi fare clic su **Aggiungi**.
3. Sostituire il codice **JSON** nell'editor con il codice JSON seguente:
    
    ```json
    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
                "folderPath": "adfgetstarted/partitioneddata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            }
        }
    }
    ```
    Questo frammento JSON definisce un set di dati denominato **AzureBlobOutput** che rappresenta i dati di output generati dall'attività Hive nella pipeline. Si specifica che i dati di output generati dall'attività Hive verranno inseriti nel contenitore BLOB denominato `adfgetstarted` e nella cartella denominata `partitioneddata`. 
    
    La sezione **availability** specifica che il set di dati di output viene generato su base mensile. Il set di dati di output determina la pianificazione della pipeline. La pipeline viene eseguita con cadenza mensile tra le relative ore di inizio e di fine. 

    Per le descrizioni di queste proprietà, vedere la sezione **Creare il set di dati di input** . La proprietà external non viene impostata per un set di dati di output perché il set di dati viene generato dalla pipeline.
4. Salvare il file **OutputDataset.json** .

### <a name="create-pipeline"></a>Creare una pipeline
Finora sono stati creati il servizio collegato Archiviazione di Azure e i set di dati di input e di output. Ora si creerà una pipeline con un'attività **HDInsightHive**. L'**input** per l'attività Hive viene impostato su **AzureBlobInput** e l'**output** viene impostato su **AzureBlobOutput**. Una sezione del set di dati di input è disponibile con cadenza mensile (frequency: Month, interval: 1) e anche la sezione di output viene generata ogni mese. 

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Pipeline**, scegliere **Aggiungi** e fare clic su **Nuovo elemento.**
2. Selezionare **Hive Transformation Pipeline** (Pipeline di trasformazione Hive) dall'elenco e fare clic su **Aggiungi**.
3. Sostituire il codice **JSON** con il frammento seguente:

    > [!IMPORTANT]
    > Sostituire `<storageaccountname>` con il nome del proprio account di archiviazione.

    ```json
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService1",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2016-04-01T00:00:00Z",
            "end": "2016-04-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    > [!IMPORTANT]
    > Sostituire `<storageaccountname>` con il nome del proprio account di archiviazione.

    Il frammento JSON definisce una pipeline costituita da una singola attività Hive, che esegue uno script Hive per elaborare i dati di input in un cluster HDInsight su richiesta e generare i dati di output. Nella matrice nella sezione activities del codice JSON della pipeline è presente una sola attività il cui tipo è impostato su **HDInsightHive**. 

    Nelle proprietà del tipo specifiche dell'attività Hive di HDInsight si specificano il servizio collegato Archiviazione di Azure in cui si trova il file di script Hive, il percorso del file di script e i parametri per tale file. 

    Il file di script Hive, **partitionweblogs.hql**, è archiviato nell'account di archiviazione di Azure (specificato da scriptLinkedService) e nella cartella `script` nel contenitore `adfgetstarted`.

    La sezione `defines` viene usata per specificare le impostazioni di runtime che vengono passate allo script Hive come valori di configurazione Hive, ad esempio `${hiveconf:inputtable}` e `${hiveconf:partitionedtable})`.

    Le proprietà **start** ed **end** della pipeline ne specificano il periodo attivo. Poiché il set di dati è stato configurato per essere generato con cadenza mensile, la pipeline genera una sola sezione perché le date di inizio e di fine sono incluse nello stesso mese.

    Nel codice JSON dell'attività si specifica che lo script Hive viene eseguito sulla risorsa di calcolo specificata da **linkedServiceName** - **HDInsightOnDemandLinkedService**.
4. Salvare il file **HiveActivity1.json** .

### <a name="add-partitionweblogshql-and-inputlog-as-a-dependency"></a>Aggiungere partitionweblogs.hql e input.log come dipendenza
1. Fare clic con il pulsante destro del mouse su **Dipendenze** nella finestra **Esplora soluzioni**, scegliere **Aggiungi** e quindi fare clic su **Elemento esistente**.  
2. Passare a **C:\ADFGettingStarted**, selezionare i file **partitionweblogs.hql** e **input.log** e quindi fare clic su **Aggiungi**. Questi due file sono stati creati come prerequisiti in [Panoramica dell'esercitazione](data-factory-build-your-first-pipeline.md).

Quando si pubblica la soluzione nel passaggio successivo, il file **partitionweblogs.hql** viene caricato nella cartella **script** del contenitore BLOB `adfgetstarted`.   

### <a name="publishdeploy-data-factory-entities"></a>Pubblicare/Distribuire le entità della data factory
In questo passaggio si pubblicano le entità della data factory (servizi collegati, set di dati e pipeline) del progetto nel servizio Azure Data Factory. Nel corso del processo di pubblicazione si specifica il nome della data factory. 

1. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Pubblica**.
2. Se viene visualizzato **Sign in to your Microsoft account** (Accedere all'account Microsoft), nella finestra di dialogo immettere le credenziali per l'account associato alla sottoscrizione di Azure e fare clic su **Accedi**.
3. Verrà visualizzata la finestra di dialogo seguente:

   ![Finestra di dialogo Pubblica](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
4. Nella pagina **Configure data factory** (Configura data factory), procedere come segue:

    ![Pubblicazione: impostazioni per una nuova data factory](media/data-factory-build-your-first-pipeline-using-vs/publish-new-data-factory.png)

   1. Selezionare l’opzione **Crea nuova data factory** .
   2. Immettere un **nome** univoco per la data factory, ad esempio **DataFactoryUsingVS09152016**. Il nome deve essere univoco a livello globale.
   3. Selezionare la sottoscrizione adatta per il campo **Sottoscrizione** . 
        > [!IMPORTANT]
        > Se non viene visualizzata alcuna sottoscrizione, verificare di aver eseguito l'accesso con un account amministratore o coamministratore della sottoscrizione.
   4. Selezionare il **gruppo di risorse** per la data factory da creare.
   5. Selezionare l' **area** per la data factory.
   6. Fare clic su **Avanti** per passare alla pagina **Pubblica elementi**. Premere **TAB** per uscire dal campo Nome se il pulsante **Avanti** è disabilitato.

    > [!IMPORTANT]
    > Se al momento della pubblicazione viene visualizzato l'errore **Il nome "DataFactoryUsingVS" per la data factory non è disponibile**, modificare il nome (ad esempio in nomeutenteDataFactoryUsingVS). Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere l'argomento relativo alle [regole di denominazione di Data factory](data-factory-naming-rules.md) .   
1. Nella pagina **Pubblica elementi** assicurarsi che tutte le data factory siano selezionate e fare clic su **Avanti** per passare alla pagina **Riepilogo**.

    ![Pagina Publish Items (Pubblica elementi)](media/data-factory-build-your-first-pipeline-using-vs/publish-items-page.png)     
2. Esaminare il riepilogo e fare clic su **Avanti** per avviare il processo di distribuzione e visualizzare lo **Stato della distribuzione**.

    ![Pagina Riepilogo](media/data-factory-build-your-first-pipeline-using-vs/summary-page.png)
3. Nella pagina **Stato della distribuzione** , è possibile visualizzare lo stato del processo di distribuzione. Fare clic su Finish (Fine) dopo il termine della distribuzione.

Elementi importanti da considerare:

- Se viene visualizzato l'errore **La sottoscrizione non è registrata per l'uso dello spazio dei nomi Microsoft.DataFactory**, eseguire una di queste operazioni e provare a ripetere la pubblicazione:
    - In Azure PowerShell eseguire questo comando per registrare il provider di Data Factory.
        ```PowerShell   
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
        ```
        È possibile eseguire questo comando per verificare che il provider di Data Factory sia registrato.

        ```PowerShell
        Get-AzureRmResourceProvider
        ```
    - Accedere usando la sottoscrizione di Azure nel [portale di Azure](https://portal.azure.com) e passare al pannello Data Factory oppure creare un'istanza di Data Factory nel portale di Azure. Questa azione registra automaticamente il provider.
- Il nome di Data Factory può essere registrato come un nome DNS in futuro e pertanto divenire visibile pubblicamente.
- Per creare istanze di data factory, è necessario essere un amministratore o un coamministratore della sottoscrizione di Azure.

### <a name="monitor-pipeline"></a>Monitorare la pipeline
In questo passaggio si monitora la pipeline usando la vista diagramma della data factory. 

#### <a name="monitor-pipeline-using-diagram-view"></a>Monitorare la pipeline con la vista diagramma
1. Accedere al [portale di Azure](https://portal.azure.com/) e seguire questa procedura:
   1. Fare clic su **Altri servizi** e quindi su **Data factory**.
       
        ![Esplora data factory](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png)
   2. Selezionare il nome della data factory (ad esempio, **DataFactoryUsingVS09152016**) nell'elenco di data factory.
   
       ![Selezionare la data factory](./media/data-factory-build-your-first-pipeline-using-vs/select-first-data-factory.png)
2. Nella home page della data factory fare clic su **Diagramma**.

    ![Riquadro Diagramma](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
3. In Vista diagramma saranno visualizzati una panoramica delle pipeline e i set di dati usati in questa esercitazione.

    ![Vista Diagramma](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png)
4. Per visualizzare tutte le attività nella pipeline, fare clic con il pulsante destro del mouse sulla pipeline nel diagramma e scegliere Apri pipeline.

    ![Menu Apri pipeline](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
5. Assicurarsi che l'attività HDInsightHive sia visualizzata nella pipeline.

    ![Visualizzazione Apri pipeline](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

    Per tornare alla visualizzazione precedente, fare clic su **Data Factory** nel menu di navigazione nella parte superiore.
6. In **Vista diagramma** fare doppio clic sul set di dati **AzureBlobInput**. Verificare che lo stato della sezione sia **Pronto** . Potrebbero essere necessari alcuni minuti perché lo stato della sezione venga visualizzato come Pronto. Se dopo qualche minuto ciò non accade, verificare che il file di input (input.log) sia posizionato nel contenitore (`adfgetstarted`) e nella cartella (`inputdata`) corretti. Verificare anche che la proprietà **external** per il set di dati di input sia impostata su **true**. 

   ![Sezione di input nello stato Pronto](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
7. Fare clic su **X** per chiudere il pannello **AzureBlobInput**.
8. In **Vista diagramma** fare doppio clic sul set di dati **AzureBlobOutput**. Viene visualizzata la sezione in fase di elaborazione.

   ![Set di dati](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. Al termine dell'elaborazione lo stato della sezione è **Pronta** .

   > [!IMPORTANT]
   > La creazione di un cluster HDInsight su richiesta di solito richiede tempo (circa 20 minuti). Di conseguenza, prevedere **circa 30 minuti** per l'elaborazione della sezione nella pipeline.  
   
    ![Set di dati](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png)    
10. Quando lo stato sella sezione è **Pronto**, cercare i dati di output nella cartella `partitioneddata` del contenitore `adfgetstarted` nell'archivio BLOB.  

    ![Dati di output](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)
11. Fare clic sulla sezione per visualizzare i relativi dettagli in un pannello **Sezione dati** .

    ![Dettagli sezione dati](./media/data-factory-build-your-first-pipeline-using-vs/data-slice-details.png)  
12. Fare clic su un'esecuzione di attività nell'elenco **Esecuzioni attività** (in questo scenario, un'attività Hive) per visualizzare i relativi dettagli nella finestra **Dettagli esecuzione attività**. 
  
    ![Dettagli esecuzione attività](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-blade.png)    

    Nei file di log sono riportate la query Hive eseguita e le informazioni sullo stato. Tali file di log sono utili per risolvere eventuali problemi.  

Per istruzioni su come usare il portale di Azure per monitorare la pipeline e i set di dati creati in questa esercitazione, vedere [Monitorare e gestire le pipeline di Azure Data Factory](data-factory-monitor-manage-pipelines.md) .

#### <a name="monitor-pipeline-using-monitor--manage-app"></a>Monitorare la pipeline con l'app Monitoraggio e gestione
È anche possibile usare l'applicazione Monitoraggio e gestione per monitorare le pipeline. Per informazioni dettagliate sull'uso di questa applicazione, vedere [Monitorare e gestire le pipeline di Azure Data Factory con la nuova app di monitoraggio e gestione](data-factory-monitor-manage-app.md).

1. Fare clic sul riquadro Monitoraggio e gestione.

    ![Riquadro Monitoraggio e gestione](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-tile.png)
2. Verrà visualizzata l'applicazione Monitoraggio e gestione. Modificare **Ora di inizio** e **Ora di fine** in modo che corrispondano alle ore di inizio (04-01-2016 12:00 AM) e di fine (04-02-2016 12:00 AM) della pipeline e quindi fare clic su **Applica**.

    ![App Monitoraggio e gestione](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-app.png)
3. Per visualizzare i dettagli di una finestra attività, selezionarla nell'**elenco delle finestre attività**.
    ![Dettagli finestra attività](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-details.png)

> [!IMPORTANT]
> Il file di input viene eliminato quando la sezione viene elaborata correttamente. Per eseguire di nuovo la sezione o ripetere l'esercitazione, caricare quindi il file di input (input.log) nella cartella `inputdata` del contenitore `adfgetstarted`.

### <a name="additional-notes"></a>Note aggiuntive
- Una data factory può comprendere una o più pipeline. Una pipeline può comprendere una o più attività. Ad esempio, un'attività di copia per copiare dati da un archivio dati di origine a uno di destinazione e un'attività Hive HDInsight per eseguire uno script Hive e trasformare i dati di input. Vedere gli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats) per tutte le origini e i sink supportati dall'attività di copia. Per l'elenco di servizi di calcolo supportati da Data Factory, vedere [Servizi collegati di calcolo](data-factory-compute-linked-services.md) .
- I servizi collegati collegano archivi dati o servizi di calcolo a una data factory di Azure. Vedere gli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats) per tutte le origini e i sink supportati dall'attività di copia. Vedere l'articolo relativo ai [servizi collegati di calcolo](data-factory-compute-linked-services.md) per un elenco dei servizi di calcolo supportati da Data Factory e le [attività di trasformazione](data-factory-data-transformation-activities.md) eseguibili in tali servizi.
- Per informazioni dettagliate sulle proprietà JSON usate nella definizione del servizio collegato Archiviazione di Azure, vedere l'articolo relativo allo [spostamento di dati da e verso BLOB di Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service).
- È possibile usare il proprio cluster HDInsight anziché un cluster HDInsight su richiesta. Per informazioni dettagliate, vedere [Servizi collegati di calcolo](data-factory-compute-linked-services.md) .
-  Data Factory crea automaticamente un cluster HDInsight **basato su Linux** con il codice JSON precedente. Per i dettagli, vedere [Servizio collegato Azure HDInsight su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) .
- Il cluster HDInsight crea un **contenitore predefinito** nell'archivio BLOB specificato nel file JSON (linkedServiceName). HDInsight non elimina il contenitore quando viene eliminato il cluster. Questo comportamento dipende dalla progettazione. Con il servizio collegato HDInsight su richiesta, viene creato un cluster HDInsight ogni volta che viene elaborata una sezione, a meno che non esista un cluster attivo (timeToLive). Il cluster viene eliminato al termine dell'elaborazione.
    
    Man mano che vengono elaborate più sezioni, vengono visualizzati numerosi contenitori nell'archivio BLOB di Azure. Se non sono necessari per risolvere i problemi relativi ai processi, è possibile eliminarli per ridurre i costi di archiviazione. I nomi dei contenitori seguono il modello `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Per eliminare i contenitori nell'archivio BLOB di Azure, usare strumenti come [Microsoft Azure Storage Explorer](http://storageexplorer.com/) .
- In questo momento la pianificazione è basata sul set di dati di output, quindi è necessario creare un set di dati di output anche se l'attività non genera alcun output. Se l'attività non richiede input, è possibile ignorare la creazione del set di dati di input. 
- Questa esercitazione non illustra come copiare dati con Azure Data Factory. Per un'esercitazione su come copiare dati usando Azure Data Factory, vedere [Copiare dati da un archivio BLOB al database SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="use-server-explorer-to-view-data-factories"></a>Usare Esplora Server per visualizzare le data factory
1. In **Visual Studio** fare clic su **Visualizza** nel menu e fare clic su **Esplora server**.
2. Nella finestra Esplora server espandere **Azure** e **Data factory**. Se viene visualizzata la finestra **Accedi a Visual Studio**, immettere l'**account** associato alla sottoscrizione di Azure e fare clic su **Continua**. Immettere la **password** e fare clic su **Accedi**. Visual Studio cerca di ottenere le informazioni su tutte le data factory di Azure nella sottoscrizione. Lo stato di questa operazione viene visualizzato nella finestra **Data Factory Task List** (Elenco attività Data Factory).

    ![Esplora server](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. È possibile fare clic con il pulsante destro del mouse su una data factory e scegliere l'opzione **Export Data Factory to New Project** (Esporta la data factory in un nuovo progetto) per creare un progetto di Visual Studio basato su una data factory esistente.

    ![Data factory di Azure](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## <a name="update-data-factory-tools-for-visual-studio"></a>Aggiornare gli strumenti di Data factory di Azure per Visual Studio
Per aggiornare gli strumenti di Azure Data Factory per Visual Studio, eseguire queste operazioni:

1. Fare clic su **Strumenti**nel menu e selezionare **Estensioni e aggiornamenti**.
2. Selezionare **Aggiornamenti** nel riquadro sinistro e quindi selezionare **Visual Studio Gallery**.
3. Selezionare **Azure Data Factory tools for Visual Studio** e fare clic su **Aggiorna**. Se questa voce non è visibile, si dispone già della versione più recente dello strumento.

## <a name="use-configuration-files"></a>Usare i file di configurazione
È possibile usare i file di configurazione in Visual Studio per configurare le proprietà di pipeline/tabelle/servizi collegati in modo diverso a seconda dell'ambiente.

Considerare la definizione JSON seguente per un servizio collegato Archiviazione di Azure. Per specificare **connectionString** con valori diversi per accountname e accountkey in base all'ambiente di sviluppo, di test o di produzione, in cui vengono distribuite le entità di Data Factory, usare un file di configurazione separato per ogni ambiente.

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="add-a-configuration-file"></a>Aggiungere un file di configurazione
Per aggiungere un file di configurazione per ogni ambiente, seguire questa procedura:   

1. Fare clic con il pulsante destro del mouse sul progetto Data Factory nella soluzione di Visual Studio, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.
2. Selezionare **Config** nell'elenco di modelli installati a sinistra, selezionare **File di configurazione**, immettere un **nome** per il file di configurazione e fare clic su **Aggiungi**.

    ![Aggiungere un file di configurazione](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Aggiungere i parametri di configurazione e i relativi valori nel formato seguente:

    ```json
    {
        "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
        "AzureStorageLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        ],
        "AzureSqlLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value":  "Server=tcp:<Azure sql server name>.database.windows.net,1433;Database=<Azure Sql database>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        ]
    }
    ```

    Questo esempio configura la proprietà connectionString di un servizio collegato Archiviazione di Azure e di un servizio collegato Azure SQL. Si noti che la sintassi per specificare il nome è [JsonPath](http://goessner.net/articles/JsonPath/).   

    Se JSON ha una proprietà con una matrice di valori come indicato nel codice seguente:  

    ```json
    "structure": [
          {
              "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
        }
    ],
    ```

    Configurare le proprietà come illustrato nel file di configurazione seguente, ovvero usare l'indicizzazione in base zero:

    ```json
    {
        "name": "$.properties.structure[0].name",
        "value": "FirstName"
    }
    {
        "name": "$.properties.structure[0].type",
        "value": "String"
    }
    {
        "name": "$.properties.structure[1].name",
        "value": "LastName"
    }
    {
        "name": "$.properties.structure[1].type",
        "value": "String"
    }
    ```

### <a name="property-names-with-spaces"></a>Nomi delle proprietà con spazi
Se il nome di una proprietà contiene spazi, usare le parentesi quadre come illustrato nell'esempio seguente per il nome del server di database:

```json
 {
     "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
     "value": "MyAsqlServer.database.windows.net"
 }
```

### <a name="deploy-solution-using-a-configuration"></a>Distribuire la soluzione usando una configurazione
Quando si pubblicano entità di Data factory di Azure in VS, è possibile specificare la configurazione che si vuole usare per tale operazione di pubblicazione.

Per pubblicare entità in un progetto di Data factory di Azure usando il file di configurazione:   

1. Fare clic con il pulsante destro del mouse sul progetto Data Factory e scegliere **Pubblica** per visualizzare la finestra di dialogo **Publish Items** (Pubblica elementi).
2. Selezionare una data factory esistente o specificare i valori per crearne una nella pagina **Configure data factory** (Configura data factory) e fare clic su **Avanti**.   
3. Nella pagina **Publish Items** (Pubblica elementi) è presente un elenco a discesa con le configurazioni disponibili per il campo **Select Deployment Config** (Seleziona configurazione di distribuzione).

    ![Selezionare un file di configurazione](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)
4. Selezionare il **file di configurazione** che si vuole usare e fare clic su **Avanti**.
5. Verificare che il nome del file JSON sia presente nella pagina **Riepilogo** e fare clic su **Avanti**.
6. Fare clic su **Fine** al termine dell'operazione di distribuzione.

Al momento della distribuzione, i valori del file di configurazione vengono usati per impostare i valori per le proprietà nei file JSON prima che le entità vengano distribuite nel servizio Azure Data Factory.   

## <a name="use-azure-key-vault"></a>Usare l'Insieme di credenziali delle chiavi di Azure
Non è consigliabile ed è spesso contrario ai criteri di sicurezza eseguire il commit di dati sensibili come le stringhe di connessione nel repository del codice. Per altre informazioni sull'archiviazione di informazioni riservate in Azure Key Vault e sul relativo uso durante la pubblicazione di entità di Data Factory, vedere l'esempio di [ADF Secure Publish](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFSecurePublish) in GitHub. L'estensione Secure Publish per Visual Studio permette di archiviare i segreti in Key Vault, mentre in servizi collegati o configurazioni di distribuzione vengono specificati solo riferimenti a tali segreti. I riferimenti vengono risolti quando si pubblicano entità di Data Factory di Azure. È possibile eseguire il commit di questi file al repository di origine senza esporre i segreti.

## <a name="summary"></a>Summary
In questa esercitazione è stata creata un'istanza di Azure Data Factory per elaborare i dati eseguendo lo script Hive in un cluster Hadoop di HDInsight. È stato usato l'editor di Data Factory nel portale di Azure per eseguire questa procedura:  

1. Creare un'istanza di Azure **Data Factory**.
2. Creare due **servizi collegati**:
   1. **Archiviazione di Azure** per collegare l'archivio BLOB di Azure che contiene i file di input/output alla data factory.
   2. **Azure HDInsight** per collegare un cluster Hadoop di HDInsight alla data factory. Azure Data Factory crea un cluster Hadoop di HDInsight JIT per elaborare i dati di input e generare i dati di output.
3. Creare due **set di dati**che descrivono i dati di input e di output per l'attività Hive di HDInsight nella pipeline.
4. Creare una **pipeline** con un'attività **Hive di HDInsight**.  

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stata creata una pipeline con un'attività di trasformazione (attività HDInsight) che esegue uno script Hive in un cluster HDInsight su richiesta. Per informazioni su come usare un'attività di copia per copiare i dati da un BLOB di Azure ad Azure SQL, vedere [Esercitazione: Copiare i dati di un BLOB di Azure in Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

È possibile concatenare due attività, ovvero eseguire un'attività dopo l'altra, impostando il set di dati di output di un'attività come set di dati di input di altre attività. Per informazioni dettagliate, vedere [Pianificazione ed esecuzione con Data Factory](data-factory-scheduling-and-execution.md). 


## <a name="see-also"></a>Vedere anche
| Argomento | DESCRIZIONE |
|:--- |:--- |
| [Pipeline](data-factory-create-pipelines.md) |Questo articolo contiene informazioni sulle pipeline e sulle attività in Azure Data Factory e su come usarle per costruire flussi di lavoro basati sui dati per lo scenario o l'azienda. |
| [Set di dati](data-factory-create-datasets.md) |Questo articolo fornisce informazioni sui set di dati in Azure Data Factory. |
| [Attività di trasformazione dei dati](data-factory-data-transformation-activities.md) |Questo articolo fornisce un elenco di attività di trasformazione dei dati (ad esempio, la trasformazione Hive di HDInsight usata in questa esercitazione) supportate da Azure Data Factory. |
| [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md) |Questo articolo descrive gli aspetti di pianificazione ed esecuzione del modello applicativo di Data factory di Azure. |
| [Monitorare e gestire le pipeline con l'app di monitoraggio](data-factory-monitor-manage-app.md) |Questo articolo descrive come monitorare, gestire ed eseguire il debug delle pipeline usando l'app di monitoraggio e gestione. |
