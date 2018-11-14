---
title: 'Esercitazione: Creare cluster Apache Hadoop on demand in Azure HDInsight con Data Factory '
description: Informazioni su come creare cluster Apache Hadoop on demand in HDInsight con Azure Data Factory.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: hrasheed
ms.openlocfilehash: 76651874951255d9b01efdc6e91892f6852d948d
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036377"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Esercitazione: Creare cluster Apache Hadoop on demand in HDInsight con Azure Data Factory
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

In questo articolo si apprenderà come creare un cluster Apache Hadoop on demand in Azure HDInsight con Azure Data Factory. Si useranno quindi le pipeline di dati in Azure Data Factory per eseguire i processi Hive ed eliminare il cluster. Al termine di questa esercitazione, si apprenderà come rendere operativa l'esecuzione di un processo Big Data in cui la creazione del cluster, l'esecuzione del processo e l'eliminazione del cluster vengono eseguite in base a una pianificazione.

Questa esercitazione illustra le attività seguenti: 

> [!div class="checklist"]
> * Creare un account di archiviazione di Azure
> * Comprendere l'attività di Azure Data Factory
> * Creare una data factory con il portale di Azure
> * Creare servizi collegati
> * Creare una pipeline
> * Attivare una pipeline
> * Monitorare una pipeline
> * Verificare l'output

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

- Un'entità servizio di Azure Active Directory. Dopo aver creato l'entità servizio, assicurarsi di recuperare l'**ID dell'applicazione** e la **chiave di autenticazione** seguendo le istruzioni nell'articolo collegato. Più avanti in questa esercitazione saranno necessari questi valori. Assicurarsi anche che l'entità servizio sia un membro del ruolo *Collaboratore* della sottoscrizione o del gruppo di risorse in cui viene creato il cluster. Per istruzioni su come recuperare i valori richiesti e assegnare i ruoli appropriati, vedere la pagina relativa alla [creazione di un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-an-azure-storage-account"></a>Creare un account di archiviazione di Azure

In questa sezione, creare un account di archiviazione che verrà usato come risorsa di archiviazione predefinita per il cluster HDInsight creato on demand. Questo account di archiviazione contiene anche lo script HiveQL di esempio (**hivescript.hql**) che consente di simulare un processo Hive di esempio eseguito nel cluster.

In questa sezione viene usato uno script Azure PowerShell per creare l'account di archiviazione e copiare i file necessari al suo interno. Lo script di esempio di Azure PowerShell di questa sezione consente di eseguire queste operazioni:

1. Accedere ad Azure.
1. Crea un gruppo di risorse di Azure.
1. Creare un account di Archiviazione di Azure.
1. Creare un contenitore BLOB nell'account di archiviazione
1. Copiare lo script HiveQL di esempio (**hivescript.hql**) nel contenitore BLOB. Lo script è disponibile all'indirizzo [https://hditutorialdata.blob.core.windows.net/adfv2hiveactivity/hivescripts/hivescript.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql). Lo script di esempio è già disponibile in un altro contenitore BLOB pubblico. Questo script di PowerShell esegue una copia di questi file nell'account di archiviazione di Azure creato.


**Per creare un account di archiviazione e copiare i file con Azure PowerShell:**
> [!IMPORTANT]
> Specificare i nomi del gruppo di risorse di Azure e dell'account di archiviazione di Azure che verranno creati dallo script.
> Prendere nota del **nome del gruppo di risorse**, del **nome dell'account di archiviazione** e della **chiave dell'account di archiviazione** restituiti dallo script. Saranno necessari nella sezione successiva.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
Login-AzureRmAccount
#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -type Standard_LRS `
    -Location $location

$destStorageAccountKey = (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzureStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous
$destContext = New-AzureStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzureStorageContainer -Name $destContainerName -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzureStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName

$blobs|Start-AzureStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzureStorageBlob -Context $destContext -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

**Per verificare la creazione dell'account di archiviazione**

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Gruppi di risorse** nel riquadro di sinistra.
1. Fare doppio clic sul nome del gruppo di risorse creato con lo script di PowerShell. Se sono presenti troppi gruppi di risorse elencati, usare il filtro.
1. Nel riquadro **Risorse** è elencata una sola risorsa, a meno che il gruppo di risorse non sia condiviso con altri progetti. Tale risorsa è l'account di archiviazione con il nome specificato in precedenza. Selezionare il nome dell'account di archiviazione.
1. Fare clic sui riquadri **BLOB**.
1. Fare clic sul contenitore **adfgetstarted**. Viene visualizzata una cartella denominata **hivescripts**.
1. Aprire la cartella e assicurarsi che contenga il file di script di esempio **hivescript.hql**.

## <a name="understand-the-azure-data-factory-activity"></a>Comprendere l'attività di Azure Data Factory

[Azure Data Factory](../data-factory/introduction.md) orchestra e automatizza lo spostamento e la trasformazione dei dati. Azure Data Factory può creare un cluster Hadoop di HDInsight JIT per elaborare una sezione dati di input ed eliminare il cluster al termine dell'elaborazione. 

In Azure Data Factory, una data factory può includere una o più pipeline di dati. Una pipeline di dati include una o più attività. Sono disponibili due tipi di attività:

- [Attività di spostamento dei dati](../data-factory/copy-activity-overview.md) che vengono usate per spostare dati da un archivio dati di origine a un archivio dati di destinazione.
- [Attività di trasformazione dei dati](../data-factory/transform-data.md). Le attività di trasformazione dei dati vengono usate per trasformare/elaborare i dati. L'attività Hive di HDInsight è una delle attività di trasformazione supportate da Data Factory. L'attività di trasformazione Hive verrà usata in questa esercitazione.

In questo articolo viene configurata l'attività Hive per la creazione di un cluster Hadoop di HDInsight on demand. Ecco cosa accade quando l'attività viene eseguita per elaborare i dati:

1. Viene creato automaticamente un cluster Hadoop di HDInsight JIT per elaborare la sezione. 

1. I dati di input vengono elaborati eseguendo uno script HiveQL nel cluster. In questa esercitazione, lo script HiveQL associato all'attività Hive esegue queste azioni:
    
    - Usa la tabella esistente (*hivesampletable*) per creare un'altra tabella **HiveSampleOut**.
    - Popola la tabella **HiveSampleOut** solo con colonne specifiche dalla tabella *hivesampletable* originale.
    
1. Il cluster Hadoop di HDInsight viene eliminato al termine dell'elaborazione ed è inattivo per l'intervallo di tempo configurato (impostazione timeToLive). Se la sezione dati successiva è disponibile per l'elaborazione entro il tempo di inattività di timeToLive, per l'elaborazione della sezione viene usato lo stesso cluster.  

## <a name="create-a-data-factory"></a>Creare una data factory

1. Accedere al [Portale di Azure](https://portal.azure.com/).

1. Nel portale di Azure selezionare **Create a resource** > **Data + Analytics** > **Data Factory** (Crea una risorsa>Dati e analisi>Data Factory).

    ![Azure Data Factory sul portale](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Azure Data Factory sul portale")

1. Immettere o selezionare i valori, come illustrato nello screenshot seguente:

    ![Creare Azure Data Factory tramite il portale di Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/create-data-factory-portal.png "Creare Azure Data Factory tramite il portale di Azure")

    Immettere o selezionare i valori seguenti:
    
    |Proprietà  |DESCRIZIONE  |
    |---------|---------|
    |**Nome** |  Immettere un nome per la data factory. Il nome deve essere univoco a livello globale.|
    |**Sottoscrizione**     |  Selezionare la sottoscrizione di Azure. |
    |**Gruppo di risorse**     | Selezionare **Use existing** (Usa esistente) e quindi selezionare il gruppo di risorse creato con lo script di PowerShell. |
    |**Versione**     | Selezionare **V2 (anteprima)** |
    |**Posizione**     | Il percorso viene automaticamente impostato sulla posizione specificata durante la creazione del gruppo di risorse precedente. Per questa esercitazione, il percorso viene impostato su **Stati Uniti orientali 2**. |
    

1. Selezionare **Aggiungi al dashboard** e quindi selezionare **Crea**. Verrà visualizzato un nuovo riquadro denominato **Invio della distribuzione** nel dashboard del portale. La creazione di una data factory potrebbe richiedere tra 2 e 4 minuti.

    ![Stato di avanzamento della Distribuzione modelli](./media/hdinsight-hadoop-create-linux-clusters-adf/deployment-progress-tile.png "Stato di avanzamento della distribuzione modelli") 
 
1. Dopo la creazione, nel portale viene visualizzata la panoramica della data factory.

    ![Panoramica di Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Panoramica di Azure Data Factory")

1. Selezionare **Crea e monitora** per avviare il portale di creazione e monitoraggio di Azure Data Factory.

## <a name="create-linked-services"></a>Creare servizi collegati

In questa sezione si creano due servizi collegati nella data factory.

- Un **servizio collegato Archiviazione di Azure** che collega un account di archiviazione di Azure alla data factory. Questo archivio viene usato dal cluster HDInsight su richiesta. Include anche lo script Hive che è in esecuzione nel cluster.
- Un **servizio collegato HDInsight su richiesta**. Azure Data Factory crea automaticamente un cluster HDInsight ed esegue lo script Hive. Elimina quindi il cluster HDInsight dopo un tempo di inattività preconfigurato.

###  <a name="create-an-azure-storage-linked-service"></a>Creare un servizio collegato Archiviazione di Azure

1. Nel riquadro di sinistra della pagina **Attività iniziali** selezionare l'icona **Modifica**.

    ![Creare un servizio collegato Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "Creare un servizio collegato Azure Data Factory")

1. Selezionare **Connessioni** nell'angolo inferiore sinistro della finestra e quindi **+ Nuovo**.

    ![Creare connessioni in Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "Creare connessioni in Azure Data Factory")

1. Nella finestra di dialogo **New Linked Service** (Nuovo servizio collegato) selezionare **Archiviazione BLOB di Azure** e quindi **Continua**.

    ![Creare un servizio di archiviazione di Azure collegato per la data factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "Creare un servizio di archiviazione di Azure collegato per la data factory")

1. Specificare un nome per il servizio di archiviazione collegato, selezionare l'account di archiviazione di Azure creato come parte dello script PowerShell e quindi selezionare **Fine**.

    ![Specificare un nome per il servizio di archiviazione di Azure collegato](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "Specificare un nome per il servizio di archiviazione di Azure collegato")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Creare un servizio collegato HDInsight su richiesta

1. Selezionare il pulsante **+ Nuovo** per creare un altro servizio collegato.

1. Nella finestra **New Linked Service** (Nuovo servizio collegato) selezionare **Calcolo** > **Azure HDInsight** e quindi selezionare **Continua**.

    ![Creare un servizio collegato HDInsight per Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "Creare un servizio collegato HDInsight per Azure Data Factory")

1. Nella finestra **New Linked Service** (Nuovo servizio collegato) specificare i valori richiesti.

    ![Fornire i valori per il servizio collegato HDInsight](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "Fornire i valori per il servizio collegato HDInsight")

    Immettere i valori seguenti e lasciare le altre impostazioni sui valori predefiniti.

    | Proprietà | DESCRIZIONE |
    | --- | --- |
    | NOME | Immettere un nome per il servizio collegato HDInsight |
    | type | Selezionare **HDInsight on demand** |
    | Servizio collegato Archiviazione di Azure | Selezionare il servizio collegato di archiviazione creato in precedenza. |
    | Tipo di cluster | Selezionare **hadoop** |
    | Durata (TTL) | Specificare il periodo per cui si desidera che il cluster HDInsight sia disponibile prima di essere eliminato automaticamente.|
    | ID entità servizio | Fornire l'ID applicazione dell'entità servizio di Azure Active Directory che è stata creata come parte dei prerequisiti |
    | Chiave entità servizio | Specificare la chiave di autenticazione per l'entità servizio di Azure Active Directory |
    | Prefisso nome cluster | Fornire un valore che precederà tutti i tipi di cluster creati dalla data factory |
    | Gruppo di risorse | Selezionare il gruppo di risorse creato come parte dello script PowerShell usato in precedenza| 
    | Nome utente cluster SSH | Immettere un nome utente SSH |
    | Password cluster SSH | Fornire una password per l'utente SSH |

    Selezionare **Fine**.

## <a name="create-a-pipeline"></a>Creare una pipeline

1. Selezionare il pulsante **+** (segno più) e quindi selezionare **Pipeline**.

    ![Creare una pipeline in Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "Creare una pipeline in Azure Data Factory")

1. Nella casella degli strumenti **Attività** espandere **HDInsight** e trascinare l'attività **Hive** nell'area di progettazione della pipeline. Nella scheda **Generale** specificare un nome per l'attività.

    ![Aggiungere attività alla pipeline di Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "Aggiungere attività alla pipeline di Data Factory")

1. Assicurarsi di avere selezionato l'attività Hive, selezionare la scheda **Cluster HDI** e dall'elenco a discesa **HDInsight Linked Service** (Servizio collegato HDInsight) selezionare il servizio collegato creato in precedenza per HDInsight.

    ![Fornire i dettagli del cluster HDInsight per la pipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "Fornire i dettagli del cluster HDInsight per la pipeline")

1. Selezionare la scheda **Script** e completare questa procedura:
    
    1. Selezionare **HDIStorageLinkedService** per **Servizio script collegato**. Questo è il valore del servizio di archiviazione collegato creato in precedenza.
    
    1. Per **Percorso file**, selezionare **Sfoglia risorsa di archiviazione** e passare alla posizione in cui si trova lo script Hive di esempio. Se in precedenza è stato eseguito lo script di PowerShell, il percorso dovrebbe essere `adfgetstarted/hivescripts/hivescript.hql`.
    
        ![Fornire dettagli dello script Hive per la pipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "Fornire dettagli dello script Hive per la pipeline")
    
    1. In **Avanzate** > **Parametri**, selezionare **Auto-fill from script** (Compila automaticamente da script). Questa opzione ricerca tutti i parametri nello script Hive che richiedono valori in fase di esecuzione. Lo script usato (**hivescript.hql**) ha un parametro di **output**. Fornire il valore nel formato `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` in modo da puntare a una cartella esistente nell'Archiviazione di Azure. Il percorso fa distinzione tra maiuscole e minuscole. Questo è il percorso in cui verrà archiviato l'output dello script.
    
        ![Fornire i parametri per lo script Hive](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "Fornire i parametri per lo script Hive")

1. Per convalidare la pipeline, selezionare **Convalida**. Selezionare il pulsante **>>** (freccia destra) per chiudere la finestra di convalida.

    ![Convalidare la pipeline di Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "Convalidare la pipeline di Azure Data Factory")

1. Infine, selezionare **Publish All** (Pubblica tutti) per pubblicare gli artefatti in Azure Data Factory.

    ![Pubblicare la pipeline di Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "Pubblicare la pipeline di Azure Data Factory")

## <a name="trigger-a-pipeline"></a>Attivare una pipeline

1. Nella barra degli strumenti della finestra di progettazione selezionare **Trigger** > **Trigger now** (Attiva>Attiva ora).

    ![Attivare la pipeline di Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "Attivare la pipeline di Azure Data Factory")

1. Selezionare **Fine** nella barra laterale popup.

## <a name="monitor-a-pipeline"></a>Monitorare una pipeline

1. Passare alla scheda **Monitoraggio** a sinistra. Nell'elenco **Pipeline Runs** (Esecuzioni di pipeline) verrà visualizzata un'esecuzione della pipeline. Si noti lo stato di esecuzione nella colonna **Stato**.

    ![Monitorare la pipeline di Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "Monitorare la pipeline di Azure Data Factory")

1. Selezionare **Aggiorna** per aggiornare lo stato.

1. È anche possibile selezionare l'icona **View Activity Runs** (Visualizza le esecuzioni di attività) per visualizzare le esecuzioni di attività associate alla pipeline. Nello screenshot qui di seguito viene visualizzata una sola esecuzione di attività perché la pipeline creata contiene una sola attività. Per tornare alla visualizzazione precedente, selezionare **Pipeline** nella parte superiore della pagina.

    ![Monitorare l'attività della pipeline di Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "Monitorare l'attività della pipeline di Azure Data Factory")


## <a name="verify-the-output"></a>Verificare l'output

1. Per verificare l'output, nel portale di Azure passare all'account di archiviazione usato per questa esercitazione. Verranno visualizzate le cartelle o i contenitori seguenti:

    - Viene visualizzato **adfgerstarted/outputfolder** che contiene l'output dello script Hive che è stato eseguito come parte della pipeline.

    - Viene visualizzato un contenitore **adfhdidatafactory-\<linked-service-name>-\<timestamp>**. Questo contenitore è il percorso di archiviazione predefinito del cluster HDInsight che è stato creato come parte dell'esecuzione della pipeline.

    - Viene visualizzato un contenitore **adfjobs** che include i log del processo di Azure Data Factory.  

        ![Verificare la pipeline di Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "Verificare la pipeline di Azure Data Factory")


## <a name="clean-up-the-tutorial"></a>Eseguire la pulizia dell'esercitazione

Con la creazione del cluster HDInsight on-demand, non è necessario eliminare in modo esplicito il cluster HDInsight. Il cluster verrà eliminato in base alla configurazione fornita durante la creazione della pipeline. Tuttavia, anche dopo l'eliminazione del cluster, gli account di archiviazione a esso associati continuano a esistere. Questo comportamento è previsto da progettazione per mantenere intatti i dati. Tuttavia, se non si intende rendere persistenti i dati, è possibile eliminare l'account di archiviazione creato.

In alternativa, è possibile eliminare l'intero gruppo di risorse creato per questa esercitazione. Ciò consente di eliminare l'account di archiviazione e Azure Data Factory creati.

### <a name="delete-the-resource-group"></a>Eliminare il gruppo di risorse.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Gruppi di risorse** nel riquadro di sinistra.
1. Selezionare il nome del gruppo di risorse creato con lo script di PowerShell. Se sono presenti troppi gruppi di risorse elencati, usare il filtro. Si apre il pannello del gruppo di risorse.
1. Nel riquadro **Risorse** dovrebbe essere indicato l'account di archiviazione predefinito e l'istanza Data Factory, a meno che il gruppo di risorse non sia condiviso con altri progetti.
1. Selezionare **Elimina gruppo di risorse**. In questo modo si eliminano l'account di archiviazione e i dati in esso archiviati.

    ![Eliminare il gruppo di risorse](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "Eliminare il gruppo di risorse")

1. Immettere il nome del gruppo di risorse per confermare l'eliminazione e quindi fare clic su **Elimina**.


## <a name="next-steps"></a>Passaggi successivi
Questo articolo descrive come usare Azure Data Factory per creare il cluster HDInsight on demand per l'elaborazione dei processi Hive. Passare all'articolo successivo per apprendere come creare i cluster HDInsight con la configurazione personalizzata.

> [!div class="nextstepaction"]
>[Creare cluster HDInsight di Azure con la configurazione personalizzata](hdinsight-hadoop-provision-linux-clusters.md)


