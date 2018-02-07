---
title: Trasformare dati usando Hive nella rete virtuale di Azure | Microsoft Docs
description: "Questa esercitazione offre istruzioni dettagliate per la trasformazione dei dati tramite l'attività Hive in Azure Data Factory."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2018
ms.author: shengc
ms.openlocfilehash: c1dfa969f8665fc06d365bdb91d57382ee04c315
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory"></a>Trasformare dati nella rete virtuale di Azure usando l'attività Hive in Azure Data Factory
In questa esercitazione si usa il portale di Azure per creare una pipeline di Data Factory che trasforma i dati con un'attività Hive in un cluster HDInsight che si trova in una rete virtuale di Azure. In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare una data factory. 
> * Creare un runtime di integrazione self-hosted
> * Creare servizi collegati Archiviazione di Azure e Azure HDInsight.
> * Creare una pipeline con un'attività Hive.
> * Attivare un'esecuzione della pipeline.
> * Monitorare l'esecuzione della pipeline 
> * Verificare l'output

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti
- **Account di archiviazione di Azure**. Creare uno script Hive e caricarlo nell'archivio di Azure. L'output dello script Hive viene archiviato in questo account di archiviazione. In questo esempio, il cluster HDInsight usa questo account di archiviazione di Azure come risorsa di archiviazione primaria. 
- **Rete virtuale di Azure.** Se non è disponibile una rete virtuale di Azure, crearla seguendo [queste istruzioni](../virtual-network/quick-create-portal.md). In questo esempio, il cluster HDInsight si trova in una rete virtuale di Azure. Ecco una configurazione di esempio della rete virtuale di Azure. 

    ![Creare una rete virtuale](media/tutorial-transform-data-using-hive-in-vnet-portal/create-virtual-network.png)
- **Cluster HDInsight.** Creare un cluster HDInsight e aggiungerlo alla rete virtuale creata nel passaggio precedente seguendo questo articolo: [Estendere Azure HDInsight usando Rete virtuale di Azure](../hdinsight/hdinsight-extend-hadoop-virtual-network.md). Ecco una configurazione di esempio di HDInsight in una rete virtuale. 

    ![HDInsight in una rete virtuale](media/tutorial-transform-data-using-hive-in-vnet-portal/hdinsight-virtual-network-settings.png)
- **Azure PowerShell**. Seguire le istruzioni in [How to install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Come installare e configurare Azure PowerShell).
- **Una macchina virtuale**. Creare una macchina virtuale (VM) di Azure e aggiungerla alla stessa rete virtuale che contiene il cluster HDInsight. Per informazioni dettagliate, vedere [come creare macchine virtuali](../virtual-network/quick-create-portal.md#create-virtual-machines). 

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Caricare lo script Hive nell'account di archiviazione BLOB

1. Creare un file Hive SQL denominato **hivescript.hql** con il contenuto seguente:

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. Nell'Archivio BLOB di Azure creare un contenitore denominato **adftutorial**, se non esiste.
3. Creare una cartella denominata **hivescripts**.
4. Caricare il file **hivescript.hql** nella sottocartella **hivescripts**.

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory
1. Accedere al [Portale di Azure](https://portal.azure.com/).    
2. Scegliere **Nuovo** dal menu a sinistra, fare clic su **Dati e analisi** e quindi fare clic su **Data factory**. 
   
   ![Nuovo->DataFactory](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-data-factory-menu.png)
3. Nella pagina **Nuova data factory** immettere **ADFTutorialHiveFactory** per **Nome**. 
      
     ![Pagina Nuova data factory](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-azure-data-factory.png)
 
   Il nome della data factory di Azure deve essere **univoco a livello globale**. Se viene visualizzato l'errore seguente, modificare il nome della data factory, ad esempio nomeutenteMyAzureSsisDataFactory, e riprovare. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Selezionare la **sottoscrizione** di Azure in cui creare la data factory. 
4. Per il **gruppo di risorse**, eseguire una di queste operazioni:
     
      - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa. 
      - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.   
         
      Per informazioni sui gruppi di risorse, vedere l'articolo relativo all' [uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selezionare **V2 (anteprima)** per **Versione**.
5. Selezionare la **località** per la data factory. Nell'elenco vengono mostrate solo le località supportate per la creazione di data factory.
6. Selezionare **Aggiungi al dashboard**.     
7. Fare clic su **Crea**.
8. Nel dashboard viene visualizzato il riquadro seguente con lo stato: **Deploying data factory** (Distribuzione della data factory). 

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media/tutorial-transform-data-using-hive-in-vnet-portal/deploying-data-factory.png)
9. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine.
   
   ![Home page di Data factory](./media/tutorial-transform-data-using-hive-in-vnet-portal/data-factory-home-page.png)
10. Fare clic su **Crea e monitora** per avviare l'interfaccia utente di Data Factory in una scheda separata.
11. Nella pagina **Attività iniziali** passare alla scheda **Modifica** nel pannello a sinistra, come mostrato nell'immagine seguente: 

   ![Scheda Modifica](./media/tutorial-transform-data-using-hive-in-vnet-portal/get-started-page.png)

## <a name="create-a-self-hosted-integration-runtime"></a>Creare un runtime di integrazione self-hosted
Dato che il cluster Hadoop si trova all'interno di una rete virtuale, è necessario installare un runtime di integrazione self-hosted nella stessa rete virtuale. In questa sezione si crea una nuova VM, si aggiunge la VM alla stessa rete virtuale e vi si installa un runtime di integrazione self-hosted. Il runtime di integrazione self-hosted consente al servizio Data Factory di inviare richieste di elaborazione a un servizio di calcolo come HDInsight all'interno di una rete virtuale, nonché di spostare dati tra archivi dati in una rete virtuale e Azure. Un runtime di integrazione self-hosted viene usato anche quando l'archivio dati o il servizio di calcolo si trova in un ambiente locale. 

1. Nell'interfaccia utente di Azure Data Factory fare clic su **Connessioni** nella parte inferiore della finestra, passare alla scheda **Integration Runtimes** (Runtime di integrazione) e fare clic sul pulsante **+ Nuovo** sulla barra degli strumenti. 

   ![Menu per nuovo runtime di integrazione](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-integration-runtime-menu.png)
2. Nella finestra **Integration Runtime Setup** (Configurazione runtime di integrazione) selezionare l'opzione **Perform data movement and dispatch activities to external computes** (Eseguire attività di invio e spostamento dati in servizi di calcolo esterni) e fare clic su **Avanti**. 

   ![Selezionare l'opzione per l'esecuzione di attività di invio e spostamento dati](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-perform-data-movement-compute-option.png)
3. Selezionare **Public Network** (Rete pubblica) e fare clic su **Avanti**.
    
   ![Selezionare una rete privata](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-private-network.png)
4. Immettere **MySelfHostedIR** per **Nome**, e fare clic su **Avanti**. 

   ![Specificare il nome del runtime di integrazione](./media/tutorial-transform-data-using-hive-in-vnet-portal/integration-runtime-name.png) 
5. Copiare la **chiave di autenticazione** per il runtime di integrazione facendo clic sul pulsante di copia e salvarla. Tenere aperta la finestra. Questa chiave verrà usata per registrare il runtime di integrazione installato in una macchina virtuale. 

   ![Copiare la chiave di autenticazione](./media/tutorial-transform-data-using-hive-in-vnet-portal/copy-key.png)

### <a name="install-ir-on-a-virtual-machine"></a>Installare il runtime di integrazione in una macchina virtuale

1. Nella macchina virtuale di Azure, scaricare il [runtime di integrazione self-hosted](https://www.microsoft.com/download/details.aspx?id=39717). Usare la **chiave di autenticazione** ottenuta nel passaggio precedente per registrare manualmente il runtime di integrazione self-hosted. 

    ![Registrare il runtime di integrazione](media/tutorial-transform-data-using-hive-in-vnet-portal/register-integration-runtime.png)

2. Al termine della registrazione del runtime di integrazione self-hosted verrà visualizzato il messaggio seguente. 
   
    ![Registrazione completata](media/tutorial-transform-data-using-hive-in-vnet-portal/registered-successfully.png)
3. Fare clic su **Avvia Configuration Manager**. Quando il nodo viene connesso al servizio cloud, viene visualizzata la pagina seguente: 
   
    ![Connessione del nodo](media/tutorial-transform-data-using-hive-in-vnet-portal/node-is-connected.png)

### <a name="self-hosted-ir-in-the-azure-data-factory-ui"></a>Runtime di integrazione self-hosted nell'interfaccia utente di Azure Data Factory

1. Nell'**interfaccia utente di Azure Data Factory** verranno visualizzati il nome e lo stato della VM self-hosted.

   ![Nodi self-hosted esistenti](./media/tutorial-transform-data-using-hive-in-vnet-portal/existing-self-hosted-nodes.png)
2. Fare clic su **Fine** per chiudere la finestra **Integration Runtime Setup** (Configurazione runtime di integrazione). Il runtime di integrazione self-hosted verrà visualizzato nell'elenco dei runtime di integrazione.

   ![Runtime di integrazione self-hosted nell'elenco](./media/tutorial-transform-data-using-hive-in-vnet-portal/self-hosted-ir-in-list.png)


## <a name="create-linked-services"></a>Creare servizi collegati

In questa sezione vengono creati e distribuiti due servizi collegati:
- Un **servizio collegato Archiviazione di Azure** che collega un account di archiviazione di Azure alla data factory. Questa risorsa di archiviazione è l'archiviazione primaria usata dal cluster HDInsight. In questo caso, l'account di archiviazione di Azure viene usato per archiviare lo script Hive e l'output dello script.
- Un **servizio collegato HDInsight**. Azure Data Factory invia lo script Hive a questo cluster HDInsight per l'esecuzione.

### <a name="create-azure-storage-linked-service"></a>Creare il servizio collegato Archiviazione di Azure

1. Passare alla scheda **Servizi collegati** e fare clic su **Nuovo**.

   ![Pulsante Nuovo per il servizio collegato](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. Nella finestra **New Linked Service** (Nuovo servizio collegato) selezionare **Archiviazione BLOB di Azure** e fare clic su **Continua**. 

   ![Selezionare Archiviazione BLOB di Azure](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-azure-storage.png)
3. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura:

    1. Immettere **AzureStorageLinkedService** per **Nome**.
    2. Selezionare **MySelfHostedIR** per **Connect via integration runtime** (Connetti tramite runtime di integrazione).
    3. Selezionare il proprio account di archiviazione di Azure per **Nome account di archiviazione**. 
    4. Per testare la connessione all'account di archiviazione, fare clic su **Test connessione**.
    5. Fare clic su **Save**.
   
        ![Specificare un account di archiviazione BLOB di Azure](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-storage-account.png)

### <a name="create-hdinsight-linked-service"></a>Creare un servizio collegato HDInsight

1. Fare di nuovo clic su **Nuovo** per creare un altro servizio collegato. 
    
   ![Pulsante Nuovo per il servizio collegato](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. Passare alla scheda **Calcolo**, selezionare **Azure HDInsight** e fare clic su **Continua**.

    ![Selezionare Azure HDInsight](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight.png)
3. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura:

    1. Immettere **AzureHDInsightLinkedService** per **Nome**.
    2. Selezionare **Bring your own HDInsight** (Bring Your Own HDInsight). 
    3. Selezionare il proprio cluster HDInsight per **Hdi cluster** (Cluster HDInsight). 
    4. Immettere il **nome utente** per il cluster HDInsight.
    5. Immettere la **password** dell'utente. 
    
        ![Impostazioni Azure HDInsight](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-hdinsight.png)

Questo articolo presuppone che sia disponibile l'accesso al cluster tramite Internet, ad esempio che sia possibile connettersi al cluster all'indirizzo `https://clustername.azurehdinsight.net`. Questo indirizzo usa il gateway pubblico, che non è disponibile se sono stati usati gruppi di sicurezza di rete o route definite dall'utente per limitare l'accesso da Internet. Per consentire a Data Factory di inviare processi al cluster HDInsight nella rete virtuale di Azure, è necessario configurare la rete virtuale di Azure in modo che l'URL possa essere risolto nell'indirizzo IP privato del gateway usato da HDInsight.

1. Nel portale di Azure aprire la rete virtuale in cui si trova il cluster HDInsight. Aprire l'interfaccia di rete avente il nome che inizia con `nic-gateway-0`. Annotarne l'indirizzo IP privato, ad esempio 10.6.0.15. 
2. Se la rete virtuale di Azure ha un server DNS, aggiornare il record DNS in modo che l'URL del cluster HDInsight `https://<clustername>.azurehdinsight.net` possa essere risolto in `10.6.0.15`. Se non è disponibile un server DNS nella rete virtuale di Azure, il problema può essere temporaneamente risolto modificando il file hosts (C:\Windows\System32\drivers\etc) di tutte le VM registrate come nodi del runtime di integrazione self-hosted aggiungendo una voce simile alla seguente: 

    `10.6.0.15 myHDIClusterName.azurehdinsight.net`

## <a name="create-a-pipeline"></a>Creare una pipeline 
In questo passaggio si crea una nuova pipeline con un'attività Hive. L'attività esegue uno script Hive per restituire i dati da una tabella di esempio e salvarli in un percorso definito dall'utente.

Tenere presente quanto segue:

- **scriptPath** punta al percorso dello script Hive nell'account di archiviazione di Azure usato per MyStorageLinkedService. Il percorso fa distinzione tra maiuscole e minuscole.
- **Output** è un argomento usato nello script Hive. Usare il formato `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` in modo che l'output punti a una cartella esistente nell'archivio di Azure. Il percorso fa distinzione tra maiuscole e minuscole. 

1. Nell'interfaccia utente di Data Factory fare clic su **+ (segno più)** nel riquadro a sinistra e quindi su **Pipeline**. 

    ![Menu per nuova pipeline](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-pipeline-menu.png)
2. Nella casella degli strumenti **Attività** espandere **HDInsight** e trascinare l'attività **Hive** nell'area di progettazione della pipeline. 

    ![Trascinare l'attività Hive](./media/tutorial-transform-data-using-hive-in-vnet-portal/drag-drop-hive-activity.png)
3. Nella finestra delle proprietà passare alla scheda **HDI Cluster** (Cluster HDInsight) e selezionare **AzureHDInsightLinkedService** per **HDInsight Linked Service** (Servizio collegato HDInsight).

    ![Selezionare il servizio collegato HDInsight](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight-linked-service.png)
4. Passare alla scheda **Script** e seguire questa procedura: 

    1. Selezionare **AzureStorageLinkedService** per **Servizio script collegato**. 
    2. Per **Percorso file** fare clic su **Sfoglia risorsa di archiviazione**. 
 
        ![Sfoglia risorsa di archiviazione](./media/tutorial-transform-data-using-hive-in-vnet-portal/browse-storage-hive-script.png)
    3. Nella finestra **Choose a file or folder** (Scegliere un file o una cartella) passare alla cartella **hivescripts** del contenitore **adftutorial**, selezionare **hivescript.hql** e fare clic su **Fine**.  
        
        ![Scegliere un file o una cartella](./media/tutorial-transform-data-using-hive-in-vnet-portal/choose-file-folder.png) 
    4. Verificare che in **Percorso file** venga visualizzato **adftutorial/hivescripts/hivescript.hql**.

        ![Impostazioni script](./media/tutorial-transform-data-using-hive-in-vnet-portal/confirm-hive-script-settings.png)
    5. Nella scheda **Script** espandere la sezione **Avanzate**. 
    6. Fare clic su **Auto-fill from script** (Compila automaticamente da script) per **Parametri**. 
    7. Immettere il valore per il parametro **Output** nel formato seguente: `wasb://<Blob Container>@<StorageAccount>.blob.core.windows.net/outputfolder/`. Ad esempio: `wasb://adftutorial@mystorageaccount.blob.core.windows.net/outputfolder/`.
 
        ![Argomenti script](./media/tutorial-transform-data-using-hive-in-vnet-portal/script-arguments.png)
1. Per pubblicare gli elementi in Data Factory, fare clic su **Pubblica**.

    ![Pubblica](./media/tutorial-transform-data-using-hive-in-vnet-portal/publish.png)

## <a name="trigger-a-pipeline-run"></a>Attivare un'esecuzione della pipeline

1. Per prima cosa, convalidare la pipeline facendo clic sul pulsante **Convalida** sulla barra degli strumenti. Chiudere la finestra **Pipeline Validation Output** (Output di convalida della pipeline) facendo clic sulla **freccia destra (>>)**. 

    ![Convalidare la pipeline](./media/tutorial-transform-data-using-hive-in-vnet-portal/validate-pipeline.png) 
2. Per attivare un'esecuzione della pipeline, fare clic su Trigger sulla barra degli strumenti e quindi su Trigger Now (Attiva adesso). 

    ![Trigger now (Attiva adesso)](./media/tutorial-transform-data-using-hive-in-vnet-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

1. Passare alla scheda **Monitoraggio** a sinistra. Nell'elenco **Pipeline Runs** (Esecuzioni di pipeline) verrà visualizzata un'esecuzione della pipeline. 

    ![Monitorare le esecuzioni di pipeline](./media/tutorial-transform-data-using-hive-in-vnet-portal/monitor-pipeline-runs.png)
2. Per aggiornare l'elenco, fare clic su **Aggiorna**.
4. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, fare clic su **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Gli altri collegamenti ad azioni riguardano l'arresto e la riesecuzione della pipeline. 

    ![Visualizzare le esecuzioni di attività](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs-link.png)
5. Verrà visualizzata una sola esecuzione di attività perché la pipeline contiene una sola attività, di tipo **HDInsightHive**. Per tornare alla visualizzazione precedente, fare clic sul collegamento **Pipeline** in alto.

    ![Esecuzioni attività](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs.png)
6. Verificare che venga visualizzato un file di output in **outputfolder** nel contenitore **adftutorial**. 

    ![File di output](./media/tutorial-transform-data-using-hive-in-vnet-portal/output-file.png)

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono stati eseguiti i passaggi seguenti: 

> [!div class="checklist"]
> * Creare una data factory. 
> * Creare un runtime di integrazione self-hosted
> * Creare servizi collegati Archiviazione di Azure e Azure HDInsight.
> * Creare una pipeline con un'attività Hive.
> * Attivare un'esecuzione della pipeline.
> * Monitorare l'esecuzione della pipeline 
> * Verificare l'output

Passare all'esercitazione successiva per informazioni sulla trasformazione dei dati usando un cluster Spark in Azure:

> [!div class="nextstepaction"]
>[Diramazione e concatenamento del flusso di controllo di Data factory](tutorial-control-flow-portal.md)



