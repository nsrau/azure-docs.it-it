---
title: Creare la prima data factory di Azure con il Portale di Azure | Microsoft Docs
description: In questa esercitazione viene creata una pipeline di esempio di Azure Data Factory usando l'editor di Data Factory nel portale di Azure.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: 
editor: 
ms.assetid: d5b14e9e-e358-45be-943c-5297435d402d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: c4fe0e01936ebc131b10f011b98e9d0c1782179b
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="tutorial-build-your-first-data-factory-by-using-the-azure-portal"></a>Esercitazione: Creare la prima data factory con il portale di Azure
> [!div class="op_single_selector"]
> * [Panoramica e prerequisiti](data-factory-build-your-first-pipeline.md)
> * [Azure portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Modello di Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Questo articolo si applica alla versione 1 di Azure Data Factory, disponibile a livello generale. Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere la [guida introduttiva per la creazione di una data factory con Data Factory versione 2](../quickstart-create-data-factory-dot-net.md).

Questo articolo descrive come usare il [portale di Azure](https://portal.azure.com/) per creare la prima data factory. Per eseguire l'esercitazione usando altri strumenti/SDK, selezionare una delle opzioni dall'elenco a discesa. 

La pipeline in questa esercitazione ha una sola attività, l'attività Hive di Azure HDInsight, che esegue uno script Hive in un cluster HDInsight per trasformare i dati di input e generare i dati di output. L'esecuzione della pipeline è pianificata una volta al mese tra le ore di inizio e di fine specificate. 

> [!NOTE]
> La pipeline di dati in questa esercitazione trasforma i dati di input per produrre dati di output. Per un'esercitazione su come copiare dati usando Data Factory, vedere [Esercitazione: Copiare i dati da un archivio BLOB di Azure a un database SQL di Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Una pipeline può includere più attività ed è possibile concatenarne due, ovvero eseguire un'attività dopo l'altra, impostando il set di dati di output di un'attività come set di dati di input dell'altra. Per altre informazioni, vedere [Pianificazione ed esecuzione in Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

## <a name="prerequisites"></a>prerequisiti
Vedere la [panoramica dell'esercitazione](data-factory-build-your-first-pipeline.md) e seguire i passaggi descritti nella sezione "Prerequisiti".

Questo articolo non fornisce una panoramica concettuale del servizio Data factory. Per altre informazioni sul servizio, vedere [Introduzione ad Azure Data Factory](data-factory-introduction.md).  

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
Una data factory può comprendere una o più pipeline. Una pipeline può comprendere una o più attività. Un esempio è un'attività di copia per copiare dati da un'origine a un archivio dati di destinazione. Un altro esempio è un'attività Hive HDInsight per eseguire uno script Hive e trasformare i dati di input per generare dati di output. 

Per creare una data factory, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare **Nuovo** > **Dati e analisi** > **Data Factory**.

   ![Pannello Crea](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

3. Nel pannello **Nuova data factory** immettere **GetStartedDF** nel campo **Nome**.

   ![Pannello Nuova data factory](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

   > [!IMPORTANT]
   > Il nome della data factory deve essere univoco a livello globale. Se viene visualizzato l'errore "Il nome GetStartedDF per la data factory non è disponibile", modificare il nome della data factory. Usare ad esempio nomeutenteGetStartedDF e creare di nuovo la data factory. Per altre informazioni sulle regole di denominazione, vedere [Azure Data Factory - Regole di denominazione](data-factory-naming-rules.md).
   >
   > Il nome della data factory può essere registrato come nome DNS in futuro e divenire visibile pubblicamente.
   >
   >
4. In **Sottoscrizione** selezionare la sottoscrizione di Azure in cui creare la data factory.

5. Selezionare un gruppo di risorse esistente o crearne uno. Per l'esercitazione creare un gruppo di risorse denominato **ADFGetStartedRG**.

6. In **Località** selezionare una località per la data factory. Nell'elenco a discesa vengono visualizzate solo le aree supportate dal servizio Data Factory.

7. Selezionare la casella di controllo **Aggiungi al dashboard**.

8. Selezionare **Create**.

   > [!IMPORTANT]
   > Per creare istanze di data factory, l'utente deve essere membro del ruolo [Collaboratore Data factory](../../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) a livello di sottoscrizione/gruppo di risorse.
   >
   >
9. Nel dashboard viene visualizzato il riquadro seguente con lo stato **Deploying Data Factory** (Distribuzione della data factory):    

   ![Stato Deploying Data Factory (Distribuzione della data factory)](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)

10. Dopo la creazione della data factory, viene visualizzata la pagina **Data factory** con il relativo contenuto.     

    ![Pannello Data factory](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Prima di creare una pipeline nella data factory è necessario creare alcune entità di Data Factory. Creare prima i servizi collegati per collegare gli archivi dati/le risorse di calcolo al proprio archivio dati. Definire quindi i set di dati di input e di output per rappresentare i dati di input e di output negli archivi dati collegati. Creare infine la pipeline con un'attività che usa questi set di dati.

## <a name="create-linked-services"></a>Creare servizi collegati
In questo passaggio si collegano l'account di archiviazione di Azure e un cluster HDInsight su richiesta alla data factory. In questo esempio, l'account di archiviazione contiene i dati di input e di output per la pipeline. Il servizio HDInsight collegato viene usato per eseguire uno script Hive specificato nell'attività della pipeline in questo esempio. Identificare l'[archivio dati](data-factory-data-movement-activities.md)/[i servizi di calcolo](data-factory-compute-linked-services.md) usati nello scenario e quindi collegare tali servizi alla data factory creando servizi collegati.  

### <a name="create-a-storage-linked-service"></a>Creare un servizio collegato di archiviazione
In questo passaggio, l'account di archiviazione viene collegato alla data factory. In questa esercitazione viene usato lo stesso account di archiviazione per archiviare i dati di input/output e il file di script HQL.

1. Nel pannello **Data factory** relativo a **GetStartedDF** selezionare **Creare e distribuire**. Viene visualizzato l'editor di Data Factory.

   ![Riquadro Creare e distribuire](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)

2. Selezionare **Nuovo archivio dati** e scegliere **Archiviazione di Azure**.

   ![Pannello Nuovo archivio dati](./media/data-factory-build-your-first-pipeline-using-editor/new-data-store-azure-storage-menu.png)

3. Nell'editor verrà visualizzato lo script JSON per la creazione di un servizio collegato di archiviazione.

   ![Servizio collegato di archiviazione](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

4. Sostituire **account name** con il nome del proprio account di archiviazione. Sostituire **account key** con la chiave di accesso dell'account di archiviazione. Per informazioni su come ottenere la chiave di accesso alle risorse di archiviazione, vedere come visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione in [Gestire l'account di archiviazione](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

5. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.

    ![Pulsante Distribuisci](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   Al termine della distribuzione del servizio collegato, la finestra Bozza-1 viene nascosta e nella visualizzazione struttura ad albero a sinistra viene visualizzato **AzureStorageLinkedService**.

    ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)    

### <a name="create-an-hdinsight-linked-service"></a>Creare un servizio collegato HDInsight
In questo passaggio viene collegato un cluster HDInsight su richiesta alla data factory. Il cluster HDInsight viene creato automaticamente in fase di esecuzione e viene eliminato al termine dell'elaborazione e dopo che è rimasto inattivo per il periodo di tempo specificato.

1. Nell'editor di Data Factory selezionare **Altro** > **Nuovo calcolo** > **Cluster HDInsight su richiesta**.

    ![Nuovo calcolo](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)

2. Copiare e incollare il frammento di codice seguente nella finestra Bozza-1. Il frammento di codice JSON descrive le proprietà che vengono usate per creare il cluster HDInsight su richiesta.

    ```JSON
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    La tabella seguente fornisce le descrizioni delle proprietà JSON usate nel frammento di codice.

   | Proprietà | DESCRIZIONE |
   |:--- |:--- |
   | clusterSize |Specifica le dimensioni del cluster HDInsight. |
   | timeToLive | Specifica il tempo di inattività del cluster HDInsight prima che sia eliminato. |
   | linkedServiceName | Specifica l'account di archiviazione che viene usato per archiviare i log generati da HDInsight. |

    Tenere presente quanto segue:

     a. Data Factory crea automaticamente un cluster HDInsight basato su Linux con le proprietà JSON. Per altre informazioni, vedere [Servizio collegato HDInsight su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

     b. È possibile usare il proprio cluster HDInsight anziché un cluster HDInsight su richiesta. Per altre informazioni, vedere [Servizio collegato HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

     c. Il cluster HDInsight crea un contenitore predefinito nell'archivio BLOB specificato nella proprietà JSON (**linkedServiceName**). HDInsight non elimina il contenitore quando viene eliminato il cluster. Questo comportamento dipende dalla progettazione. Con il servizio collegato HDInsight su richiesta, viene creato un cluster HDInsight ogni volta che viene elaborata una sezione, a meno che non esista un cluster attivo (**timeToLive**). Il cluster viene eliminato automaticamente al termine dell'elaborazione.

     Man mano che vengono elaborate più sezioni, vengono visualizzati numerosi contenitori nell'archivio BLOB. Se non sono necessari per risolvere i problemi relativi ai processi, è possibile eliminarli per ridurre i costi di archiviazione. I nomi dei contenitori seguono questo schema: "adf**yourdatafactoryname**-**linkedservicename**-datetimestamp". Per eliminare i contenitori nell'archivio BLOB, usare strumenti come [Azure Storage Explorer](http://storageexplorer.com/).

     Per altre informazioni, vedere [Servizio collegato HDInsight su richiesta](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

3. Selezionare **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.

    ![Opzione di distribuzione](./media/data-factory-build-your-first-pipeline-using-editor/ondemand-hdinsight-deploy.png)

4. Verificare che nella visualizzazione albero a sinistra siano presenti sia **AzureStorageLinkedService** che **HDInsightOnDemandLinkedService**.

    ![Visualizzazione albero con servizi collegati](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## <a name="create-datasets"></a>Creare set di dati
In questo passaggio vengono creati set di dati per rappresentare i dati di input e di output per l'elaborazione Hive. I set di dati fanno riferimento all'oggetto AzureStorageLinkedService creato in precedenza in questa esercitazione. Il servizio collegato punta a un account di archiviazione. I set di dati specificano il contenitore, la cartella e il nome del file nella risorsa di archiviazione che contiene i dati di input e di output.   

### <a name="create-the-input-dataset"></a>Creare il set di dati di input
1. Nell'editor di Data Factory fare clic su **Altro** > **Nuovo set di dati** > **Archiviazione BLOB di Azure**.

    ![Nuovo set di dati](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)

2. Copiare e incollare il frammento di codice seguente nella finestra Bozza-1. Nel frammento di codice JSON viene creato un set di dati denominato **AzureBlobInput** che rappresenta i dati di input per un'attività nella pipeline. Si specifica anche che i dati di input si trovano nel contenitore BLOB denominato **adfgetstarted** e nella cartella denominata **inputdata**.

    ```JSON
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
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
    La tabella seguente fornisce le descrizioni delle proprietà JSON usate nel frammento di codice.

   | Proprietà | DESCRIZIONE |
   |:--- |:--- |
   | type |La proprietà type è impostata su **AzureBlob** perché i dati risiedono nell'archivio BLOB. |
   | linkedServiceName |Fa riferimento all'oggetto AzureStorageLinkedService creato in precedenza. |
   | folderPath | Specifica il contenitore BLOB e la cartella che contiene i BLOB di input. | 
   | fileName |Questa proprietà è facoltativa. Se si omette questa proprietà, vengono prelevati tutti i file da folderPath. In questo tutorial viene elaborato solo il file input.log. |
   | type |I file di log sono in formato testo, quindi si usa **TextFormat**. |
   | columnDelimiter |Le colonne nei file di log sono delimitate dalla virgola (`,`). |
   | frequenza/intervallo |La frequenza è impostata su **Month** e l'intervallo è **1**; ciò significa che le sezioni di input sono disponibili con cadenza mensile. |
   | external | Questa proprietà è impostata su **true** se i dati di input non vengono generati dalla pipeline. In questa esercitazione, il file input.log non viene generato dalla pipeline, quindi la proprietà viene impostata su **true**. |

    Per altre informazioni su queste proprietà JSON, vedere il [connettore BLOB di Azure](data-factory-azure-blob-connector.md#dataset-properties).

3. Selezionare **Distribuisci** sulla barra dei comandi per distribuire il set di dati appena creato. Il set di dati viene visualizzato nella visualizzazione struttura ad albero a sinistra.

### <a name="create-the-output-dataset"></a>Creare il set di dati di output
Viene ora creato il set di dati di output per rappresentare i dati di output archiviati nell'archivio BLOB.

1. Nell'editor di Data Factory fare clic su **Altro** > **Nuovo set di dati** > **Archiviazione BLOB di Azure**.

2. Copiare e incollare il frammento di codice seguente nella finestra Bozza-1. Nel frammento di codice JSON si crea un set di dati denominato **AzureBlobOutput** per specificare la struttura dei dati generati dallo script Hive. Si specifica anche che i risultati vengono archiviati nel contenitore BLOB denominato **adfgetstarted** e nella cartella denominata **partitioneddata**. La sezione **availability** specifica che il set di dati di output viene prodotto su base mensile.

    ```JSON
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
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
    Per le descrizioni di queste proprietà, vedere la sezione "Creare il set di dati di input". La proprietà esterna non viene impostata su un set di dati di output perché il set di dati viene generato dal servizio Data Factory.

3. Selezionare **Distribuisci** sulla barra dei comandi per distribuire il set di dati appena creato.

4. Verificare se il set di dati è stato creato correttamente.

    ![Visualizzazione albero con servizi collegati](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## <a name="create-a-pipeline"></a>Creare una pipeline
In questo passaggio viene creata la prima pipeline con un'attività Hive di HDInsight. La sezione di input è disponibile con cadenza mensile perché la frequenza è impostata su Month e l'intervallo è 1. La sezione di output viene prodotta con cadenza mensile. Anche la proprietà scheduler per un'attività viene impostata su Month. Le impostazioni per il set di dati di output e l'utilità di pianificazione dell'attività devono corrispondere. La pianificazione è al momento basata sul set di dati di output. È quindi necessario creare un set di dati di output anche se l'attività non genera alcun output. Se l'attività non richiede input, è possibile ignorare la creazione del set di dati di input. Le proprietà usate nel frammento di codice JSON seguente sono illustrate al termine di questa sezione.

1. Nell'editor di Data Factory fare clic su **Altro** > **Nuova pipeline**.

    ![Opzione Nuova pipeline](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)

2. Copiare e incollare il frammento di codice seguente nella finestra Bozza-1.

   > [!IMPORTANT]
   > Nel frammento di codice JSON sostituire **storageaccountname** con il nome dell'account di archiviazione.
   >
   >

    ```JSON
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService",
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
            "start": "2017-07-01T00:00:00Z",
            "end": "2017-07-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    Nel frammento di codice JSON viene creata una pipeline costituita da una singola attività che usa Hive per elaborare i dati in un cluster HDInsight.

    Il file di script Hive, **partitionweblogs.hql**, è archiviato nell'account di archiviazione, specificato da scriptLinkedService e denominato **AzureStorageLinkedService**. Il file è disponibile nella cartella **script** del contenitore **adfgetstarted**.

    La sezione **defines** viene usata per specificare le impostazioni di runtime che vengono passate allo script Hive come valori di configurazione Hive, ad esempio ${hiveconf:inputtable} e ${hiveconf:partitionedtable}.

    Le proprietà **start** ed **end** della pipeline ne specificano il periodo attivo.

    Nel codice JSON dell'attività si specifica che lo script Hive viene eseguito sulla risorsa di calcolo indicata da **linkedServiceName**: **HDInsightOnDemandLinkedService**.

   > [!NOTE]
   > Per altre informazioni sulle proprietà JSON usate nell'esempio, vedere la sezione "Pipeline JSON" (Codice JSON della pipeline) in [Pipelines and activities in Data Factory](data-factory-create-pipelines.md) (Pipeline e attività in Data Factory).
   >
   >
3. Verificare quanto segue:

   a. Il file **input.log** è presente nella cartella **inputdata** del contenitore **adfgetstarted** nell'archivio BLOB.

   b. Il file **partitionweblogs.hql** è presente nella cartella **script** del contenitore **adfgetstarted** nell'archivio BLOB. Se questi file non sono visibili, seguire la procedura descritta nella sezione "Prerequisiti" della [panoramica dell'esercitazione](data-factory-build-your-first-pipeline.md).

   c. **storageaccountname** è stato sostituito con il nome dell'account di archiviazione nel codice JSON della pipeline.

4. Selezionare **Distribuisci** sulla barra dei comandi per distribuire la pipeline. Dal momento che le date e ore di inizio (**start**) e fine (**end**) sono impostate nel passato e **isPaused** è impostato su **false**, la pipeline (l'attività nella pipeline) viene eseguita immediatamente dopo la distribuzione.

5. Verificare che la pipeline sia visibile nella visualizzazione albero.

    ![Visualizzazione albero con pipeline](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)



## <a name="monitor-a-pipeline"></a>Monitorare una pipeline
### <a name="monitor-a-pipeline-by-using-the-diagram-view"></a>Monitorare una pipeline usando la vista Diagramma
1. Nel pannello **Data Factory** selezionare **Diagramma**.

    ![Riquadro Diagramma](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)

2. La vista **Diagramma** visualizza una panoramica delle pipeline e dei set di dati usati in questa esercitazione.

    ![Vista diagramma](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png)

3. Per visualizzare tutte le attività nella pipeline, fare clic con il pulsante destro del mouse sulla pipeline nel diagramma e scegliere **Apri pipeline**.

    ![Menu Apri pipeline](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)

4. Assicurarsi che sia visualizzato **Attività Hive** nella pipeline.

    ![Visualizzazione Apri pipeline](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

    Per tornare alla visualizzazione precedente, selezionare **Data factory** nel menu nella parte superiore.

5. Nella vista **Diagramma** fare doppio clic sul set di dati **AzureBlobInput**. Verificare che lo stato della sezione sia **Pronto**. Potrebbero essere necessari alcuni minuti perché lo stato della sezione venga visualizzato come **Pronto**. Se dopo qualche minuto ciò non accade, verificare che il file di input **input.log** sia posizionato nel contenitore **adfgetstarted** e nella cartella **inputdata** corretti.

   ![Sezione di input nello stato Pronto](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)

6. Chiudere il pannello **AzureBlobInput**.

7. Nella vista **Diagramma** fare doppio clic sul set di dati **AzureBlobOutput**. Viene visualizzata la sezione in fase di elaborazione.

   ![Elaborazione del set di dati in corso](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)

8. Al termine dell'elaborazione, lo stato della sezione è **Pronto**.

   ![Set di dati nello stato Pronto](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)  

   > [!IMPORTANT]
   > La creazione di un cluster HDInsight su richiesta richiede generalmente circa 20 minuti. Prevedere quindi circa 30 minuti per l'elaborazione della sezione nella pipeline.
   >
   >

9. Quando lo stato della sezione è **Pronto**, cercare i dati di output nella cartella **partitioneddata** del contenitore **adfgetstarted** nell'archivio BLOB.  

   ![Dati di output](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)

10. Fare clic sulla sezione per visualizzare altre informazioni correlate nel pannello **Sezione dati**.

    ![Informazioni sulla sezione di dati](./media/data-factory-build-your-first-pipeline-using-editor/data-slice-details.png)

11. Nell'elenco **Esecuzioni attività** selezionare un'esecuzione di attività per visualizzare altre informazioni. In questo scenario si tratta dell'attività Hive. Le informazioni vengono visualizzate nel pannello **Dettagli esecuzione attività**.   

    ![Pannello Dettagli esecuzione attività](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-blade.png)    

   Nei file di log sono riportate la query Hive eseguita e le informazioni sullo stato. Tali file di log sono utili per risolvere eventuali problemi.
   Per altre informazioni, vedere [Monitorare e gestire le pipeline con i pannelli del portale di Azure](data-factory-monitor-manage-pipelines.md).

> [!IMPORTANT]
> Il file di input viene eliminato quando la sezione viene elaborata correttamente. Per eseguire di nuovo la sezione o ripetere l'esercitazione, caricare quindi il file di input (**input.log**) nella cartella **inputdata** del contenitore **adfgetstarted**.
>
>

### <a name="monitor-a-pipeline-by-using-the-monitor--manage-app"></a>Monitorare una pipeline usando l'app Monitoraggio e gestione
È anche possibile usare l'applicazione Monitoraggio e gestione per monitorare le pipeline. Per altre informazioni sull'uso dell'applicazione, vedere [Monitorare e gestire le pipeline di Data Factory con l'app Monitoraggio e gestione](data-factory-monitor-manage-app.md).

1. Selezionare il riquadro **Monitoraggio e gestione** nella home page della data factory.

    ![Riquadro Monitoraggio e gestione](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-tile.png)

2. Nell'applicazione Monitoraggio e gestione modificare **Ora di inizio** e **Ora di fine** in modo che corrispondano alle ore di inizio e di fine della pipeline. Selezionare **Applica**.

    ![App Monitoraggio e gestione](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-app.png)

3. Selezionare una finestra di attività nell'elenco **Activity Windows** (Finestre attività) per visualizzare le relative informazioni.

    ![Elenco Activity Windows (Finestre attività)](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-details.png)

## <a name="summary"></a>Summary
In questa esercitazione è stata creata una data factory per elaborare i dati eseguendo uno script Hive in un cluster Hadoop HDInsight. È stato usato l'editor di Data Factory nel portale di Azure per eseguire queste operazioni:  

* Creare una data factory.
* Creare due servizi collegati:
   * Un servizio collegato di archiviazione per collegare l'archivio BLOB che contiene i file di input/output alla data factory.
   * Un servizio collegato HDInsight su richiesta per collegare un cluster Hadoop HDInsight alla data factory. Data Factory crea un cluster Hadoop HDInsight JIT per elaborare i dati di input e generare i dati di output.
* Creare due set di dati che descrivono i dati di input e di output per un'attività Hive di HDInsight nella pipeline.
* Creare una pipeline con un'attività Hive di HDInsight.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stata creata una pipeline con un'attività di trasformazione (attività HDInsight) che esegue uno script Hive in un cluster HDInsight su richiesta. Per informazioni su come usare un'attività di copia per copiare i dati da un BLOB di Azure a un database SQL, vedere [Esercitazione: Copiare dati da un archivio BLOB al database SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Vedere anche 
| Argomento | DESCRIZIONE |
|:--- |:--- |
| [Pipeline](data-factory-create-pipelines.md) |Questo articolo fornisce informazioni sulle pipeline e sulle attività in Data Factory e su come usarle per costruire flussi di lavoro end-to-end basati sui dati per lo scenario o l'azienda. |
| [Set di dati](data-factory-create-datasets.md) |Questo articolo fornisce informazioni sui set di dati in Data Factory. |
| [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md) |Questo articolo descrive gli aspetti di pianificazione ed esecuzione del modello applicativo Data Factory. |
| [Monitorare e gestire le pipeline con l'app Monitoraggio e gestione](data-factory-monitor-manage-app.md) |Questo articolo descrive come monitorare, gestire ed eseguire il debug delle pipeline usando l'app Monitoraggio e gestione. |
