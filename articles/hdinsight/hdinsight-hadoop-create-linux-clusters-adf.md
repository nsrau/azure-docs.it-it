---
title: Creare cluster Hadoop on demand con Data Factory - Azure HDInsight | Microsoft Docs
description: Informazioni su come creare cluster Hadoop on demand in HDInsight con Azure Data Factory.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: spelluru
manager: jhubbard
editor: cgronlun
ms.assetid: 1f3b3a78-4d16-4d99-ba6e-06f7bb185d6a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: spelluru
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: 657c436cc45bb3818f89b922d74e03cae894778e
ms.contentlocale: it-it
ms.lasthandoff: 06/13/2017

---
# <a name="create-on-demand-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Creare cluster Hadoop on demand in HDInsight con Azure Data Factory
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

[Azure Data Factory](../data-factory/data-factory-introduction.md) è un servizio di integrazione delle informazioni basato sul cloud che consente di automatizzare lo spostamento e la trasformazione dei dati. È possibile creare un cluster Hadoop di HDInsight JIT per elaborare una sezione dati di input ed eliminare il cluster al termine dell'elaborazione. Ecco alcuni dei vantaggi offerti dall'uso di un cluster Hadoop di HDInsight su richiesta:

- Si paga solo per il tempo in cui il processo è in esecuzione nel cluster Hadoop di HDInsight, più un breve tempo di inattività configurabile. La fatturazione dei cluster HDInsight viene calcolata al minuto, indipendentemente dal fatto che siano in uso o meno. Quando si usa un servizio collegato HDInsight su richiesta in Data Factory, i cluster vengono creati su richiesta. I cluster vengono eliminati automaticamente quando i processi vengono completati. Di conseguenza, si paga solo per il tempo di esecuzione del processo e il breve tempo di inattività (impostazione timeToLive).
- È possibile creare un flusso di lavoro usando una pipeline di Data Factory. Ad esempio, è possibile usare la pipeline per copiare dati da un'istanza locale di SQL Server a un archivio BLOB di Azure, elaborare i dati eseguendo uno script Hive e uno script Pig in un cluster Hadoop di HDInsight su richiesta e quindi copiare i dati dei risultati in Azure SQL Data Warehouse perché vengano utilizzati da applicazioni di business intelligence.
- È possibile pianificare l'esecuzione periodica (oraria, giornaliera, settimanale, mensile e così via) del flusso di lavoro.

In Azure Data Factory, una data factory può includere una o più pipeline di dati. Una pipeline di dati include una o più attività. Esistono due tipi di attività: [attività di spostamento dei dati](../data-factory/data-factory-data-movement-activities.md) e [attività di trasformazione dei dati](../data-factory/data-factory-data-transformation-activities.md). Le attività di spostamento dei dati (che attualmente includono solo l'attività di copia) vengono usate per spostare dati da un archivio dati di origine a un archivio dati di destinazione. Le attività di trasformazione dei dati vengono usate per trasformare/elaborare i dati. L'attività Hive di HDInsight è una delle attività di trasformazione supportate da Data Factory. L'attività di trasformazione Hive verrà usata in questa esercitazione.

È possibile configurare un'attività Hive in modo da usare il cluster Hadoop di HDInsight dell'utente oppure un cluster Hadoop di HDInsight su richiesta. In questa esercitazione, l'attività Hive nella pipeline della data factory viene configurata per usare un cluster HDInsight su richiesta. Ecco quindi cosa accade quando l'attività viene eseguita per elaborare una sezione dati:

1. Viene creato automaticamente un cluster Hadoop di HDInsight JIT per elaborare la sezione.  
2. I dati di input vengono elaborati eseguendo uno script HiveQL nel cluster.
3. Il cluster Hadoop di HDInsight viene eliminato al termine dell'elaborazione ed è inattivo per l'intervallo di tempo configurato (impostazione timeToLive). Se la sezione dati successiva è disponibile per l'elaborazione entro il tempo di inattività di timeToLive, per l'elaborazione della sezione viene usato lo stesso cluster.  

In questa esercitazione, lo script HiveQL associato all'attività Hive esegue queste azioni:

1. Crea una tabella esterna che fa riferimento ai dati di blog non elaborati contenuti in un archivio BLOB di Azure.
2. Partiziona i dati non elaborati per anno e per mese.
3. Archivia i dati partizionati nell'archivio BLOB di Azure.

In questa esercitazione, lo script HiveQL associato all'attività Hive crea una tabella esterna che fa riferimento ai dati di blog non elaborati contenuti nell'archivio BLOB di Azure. Ecco le righe di esempio per ogni mese nel file di input.

```
2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Lo script HiveQL partiziona i dati non elaborati per anno e per mese e crea tre cartelle di output in base all'input precedente. Ogni cartella contiene un file con le voci di ogni mese.

```
adfgetstarted/partitioneddata/year=2014/month=1/000000_0
adfgetstarted/partitioneddata/year=2014/month=2/000000_0
adfgetstarted/partitioneddata/year=2014/month=3/000000_0
```

Per vedere un elenco delle attività di trasformazione dei dati di Data Factory oltre alle attività Hive, vedere [Trasformazione e analisi tramite Data Factory di Azure](../data-factory/data-factory-data-transformation-activities.md).

> [!NOTE]
> Attualmente, da Azure Data Factory è possibile creare solo cluster HDInsight versione 3.2.

## <a name="prerequisites"></a>Prerequisiti
Per poter eseguire le istruzioni descritte nell'articolo è necessario disporre dei seguenti elementi:

* [Sottoscrizione di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell.

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="prepare-storage-account"></a>Preparare un account di archiviazione
In questo scenario, è possibile usare fino a tre account di archiviazione:

- account di archiviazione predefinito per il cluster HDInsight
- account di archiviazione per i dati di input
- account di archiviazione per i dati di output

Per semplificare l'esercitazione, si usa un solo account di archiviazione per tutti e tre gli scopi. Lo script di esempio di Azure PowerShell di questa sezione consente di eseguire queste operazioni:

1. Accedere ad Azure.
2. Creare un gruppo di risorse di Azure.
3. Creare un account di archiviazione di Azure.
4. Creare un contenitore BLOB nell'account di archiviazione.
5. Copiare i due file seguenti nel contenitore BLOB:

   * File di dati di input: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
   * Script HiveQL: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

     Entrambi i file vengono archiviati in un contenitore BLOB pubblico.


**Per preparare l'archiviazione e copiare i file con Azure PowerShell:**
> [!IMPORTANT]
> Specificare i nomi del gruppo di risorse di Azure e dell'account di archiviazione di Azure che verranno creati dallo script.
> Prendere nota del **nome del gruppo di risorse**, del **nome dell'account di archiviazione** e della **chiave dell'account di archiviazione** restituiti dallo script. Saranno necessari nella sezione successiva.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfhiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Login-AzureRmAccount}
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

Per altre informazioni sullo script di PowerShell, vedere [Uso di Azure PowerShell con Archiviazione di Azure](../storage/storage-powershell-guide-full.md). Se invece si vuole usare l'interfaccia della riga di comando di Azure, vedere lo script corrispondente nella sezione [Appendice](#appendix).

**Per esaminare l'account di archiviazione e il suo contenuto**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Gruppi di risorse** nel pannello di sinistra.
3. Fare doppio clic sul nome del gruppo di risorse creato con lo script di PowerShell. Se sono presenti troppi gruppi di risorse elencati, usare il filtro.
4. Nel riquadro **Risorse** dovrebbe essere elencata una sola risorsa, a meno che il gruppo di risorse non sia condiviso con altri progetti. Tale risorsa è l'account di archiviazione con il nome specificato in precedenza. Fare clic sul nome dell'account di archiviazione.
5. Fare clic sui riquadri **BLOB** .
6. Fare clic sul contenitore **adfgetstarted** . Vengono visualizzate due cartelle: **inputdata** e **script**.
7. Aprire le cartelle e controllare i file al loro interno. La cartella inputdata contiene il file input.log con i dati di input, mentre la cartella script contiene il file di script HiveQL.

## <a name="create-a-data-factory-using-resource-manager-template"></a>Creare una data factory usando un modello di Resource Manager
Con l'account di archiviazione, i dati di input e lo script HiveQL preparato, si è pronti per creare un'istanza di Azure Data Factory. Esistono diversi metodi per creare un'istanza di Data Factory. In questa esercitazione si crea una data factory distribuendo un modello di Azure Resource Manager con il portale di Azure. È possibile distribuire un modello di Resource Manager anche con l'[interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) e [Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md#deploy-local-template). Per altri metodi di creazione di un'istanza di Data Factory, vedere [l'esercitazione per creare la prima istanza di Data Factory](../data-factory/data-factory-build-your-first-pipeline.md).

1. Fare clic sull'immagine seguente per accedere ad Azure e aprire il modello di Resource Manager nel portale di Azure. Il modello si trova all'indirizzo https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json. Per informazioni dettagliate sulle entità definite nel modello, vedere la sezione [Entità di Data Factory nel modello](#data-factory-entities-in-the-template). 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-adf/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Selezionare l'opzione **Usa esistente** per l'impostazione **Gruppo di risorse** e quindi selezionare il nome del gruppo di risorse creato nel passaggio precedente (con lo script di PowerShell).
3. Immettere un nome per la data factory in **Nome data factory**. Il nome deve essere univoco a livello globale.
4. Immettere il **nome dell'account di archiviazione** e la **chiave dell'account di archiviazione** di cui si è preso nota nel passaggio precedente.
5. Dopo aver letto le **condizioni**, selezionare **Accetto le condizioni riportate sopra**.
6. Selezionare l'opzione **Aggiungi al dashboard**.
6. Fare clic su **Acquista/Crea**. Nel Dashboard è presente il riquadro **Distribuzione modello**. Attendere che venga visualizzato il pannello **Gruppo di risorse** per il gruppo di risorse. Per aprire il pannello del gruppo di risorse è anche possibile fare clic sul riquadro con il nome del gruppo di risorse come titolo.
6. Se il pannello del gruppo di risorse non è visualizzato, fare clic sul riquadro per aprire il gruppo di risorse. Ora sarà visibile un'altra risorsa Data Factory oltre alla risorsa dell'account di archiviazione.
7. Fare clic sul nome della data factory, ossia sul valore specificato per il parametro **Nome data factory**.
8. Nel pannello Data Factory fare clic sul riquadro **Diagramma**. Il diagramma mostra un'attività con un set di dati di input e un set di dati di output:

    ![Diagramma della pipeline attività Hive di HDInsight on demand in Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)

    I nomi sono definiti nel modello di Resource Manager.
9. Fare doppio clic su **AzureBlobOutput**.
10. Tra le **sezioni aggiornate di recente**verrà visualizzata una sezione. Se lo stato è **In corso**, attendere finché non diventa **Pronto**. Per creare un cluster HDInsight sono in genere necessari circa **20 minuti**.

### <a name="check-the-data-factory-output"></a>Controllare l'output della data factory

1. Per controllare il contenuto dei contenitori adfgetstarted, usare la stessa procedura dell'ultima sessione. Oltre a **adfgetsarted**sono disponibili due nuovi contenitori:

   * Un contenitore il cui nome segue il modello `adf<yourdatafactoryname>-linkedservicename-datetimestamp` e che è il contenitore predefinito per il cluster HDInsight.
   * adfjobs, il contenitore per i log dei processi di Azure Data Factory.

     L'output della data factory viene archiviato in **adfgetstarted**, in base alla configurazione definita nel modello di Resource Manager.
2. Fare clic su **adfgetstarted**.
3. Fare doppio clic su **partitioneddata**. Verrà visualizzata una cartella **year=2014** perché tutti i blog sono datati 2014.

    ![Output della pipeline attività Hive di HDInsight on demand in Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    Eseguendo il drill-down dell'elenco si vedranno tre cartelle relative a gennaio, febbraio e marzo. È presente un log per ogni mese.

    ![Output della pipeline attività Hive di HDInsight on demand in Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

## <a name="data-factory-entities-in-the-template"></a>Entità di Data Factory nel modello
Il modello di Resource Manager di livello principale per una data factory si presenta come segue:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```

### <a name="define-data-factory"></a>Definire una data factory
È possibile definire una data factory nel modello di Resource Manager come illustrato nell'esempio seguente:  

```json
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
}
```
dataFactoryName è il nome della data factory specificato quando si distribuisce il modello. Il servizio Data Factory è attualmente supportato solo negli Stati Uniti occidentali, negli Stati Uniti orientali e in Europa settentrionale.

### <a name="defining-entities-within-the-data-factory"></a>Definizione delle entità nella data factory
Le entità di Data Factory seguenti vengono definite nel modello JSON:

* [Servizio collegato Archiviazione di Azure](#azure-storage-linked-service)
* [Servizio collegato su richiesta HDInsight](#hdinsight-on-demand-linked-service)
* [Set di dati di input del BLOB di Azure](#azure-blob-input-dataset)
* [Set di dati di output del BLOB di Azure](#azure-blob-output-dataset)
* [Pipeline di dati con un'attività di copia](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Servizio collegato Archiviazione di Azure
Il servizio collegato Archiviazione di Azure collega l'account di archiviazione di Azure alla data factory. In questa esercitazione lo stesso account di archiviazione viene usato come account di Archiviazione HDInsight predefinito, come archivio dati di input e come archivio dati di output. Di conseguenza, si definisce un solo servizio collegato Archiviazione di Azure. Nella definizione del servizio collegato si specificano il nome e la chiave dell'account di archiviazione di Azure. Per informazioni dettagliate sulle proprietà JSON usate per definire un servizio collegato di Archiviazione di Azure, vedere [Servizio collegato Archiviazione di Azure](../data-factory/data-factory-azure-blob-connector.md#azure-storage-linked-service).

```json
{
    "name": "[variables('storageLinkedServiceName')]",
    "type": "linkedservices",
    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
**connectionString** usa i parametri storageAccountName e storageAccountKey. I valori per questi parametri vengono specificati durante la distribuzione del modello.  

#### <a name="hdinsight-on-demand-linked-service"></a>Servizio collegato su richiesta HDInsight
Nella definizione del servizio collegato HDInsight su richiesta si specificano i valori per i parametri di configurazione usati dal servizio Data Factory per creare un cluster Hadoop di HDInsight in fase di esecuzione. Per informazioni dettagliate sulle proprietà JSON usate per definire un servizio collegato su richiesta HDInsight, vedere [Servizi collegati di calcolo](../data-factory/data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).  

```json

{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "sshUserName": "myuser",                            
            "sshPassword": "MyPassword!",
            "linkedServiceName": "[variables('storageLinkedServiceName')]"
        }
    }
}
```
Tenere presente quanto segue:

* Data Factory crea automaticamente un cluster HDInsight **basato su Linux**.
* Il cluster Hadoop di HDInsight viene creato nella stessa area dell'account di archiviazione.
* Si noti l'impostazione di *timeToLive* . Data Factory elimina automaticamente il cluster dopo un periodo di inattività di 30 minuti.
* Il cluster HDInsight crea un **contenitore predefinito** nell'archivio BLOB specificato nel file JSON (**linkedServiceName**). HDInsight non elimina il contenitore quando viene eliminato il cluster. Questo comportamento dipende dalla progettazione. Con il servizio collegato HDInsight su richiesta, viene creato un cluster HDInsight ogni volta che è necessario elaborare una sezione, a meno che non esista un cluster attivo (**timeToLive**) che viene eliminato al termine dell'elaborazione.

Per i dettagli, vedere [Servizio collegato Azure HDInsight su richiesta](../data-factory/data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) .

> [!IMPORTANT]
> Man mano che vengono elaborate più sezioni, vengono visualizzati numerosi contenitori nell'archivio BLOB di Azure. Se non sono necessari per risolvere i problemi relativi ai processi, è possibile eliminarli per ridurre i costi di archiviazione. I nomi dei contenitori seguono questo schema: "adf**yourdatafactoryname**-**linkedservicename**-datetimestamp". Per eliminare i contenitori nell'archivio BLOB di Azure, usare strumenti come [Microsoft Azure Storage Explorer](http://storageexplorer.com/) .

#### <a name="azure-blob-input-dataset"></a>Set di dati di input del BLOB di Azure
Nella definizione del set di dati di input si specificano i nomi del contenitore BLOB, della cartella e del file contenente i dati di input. Per informazioni dettagliate sulle proprietà JSON usate per definire un set di dati del BLOB di Azure, vedere [Proprietà del set di dati del BLOB di Azure](../data-factory/data-factory-azure-blob-connector.md#dataset-properties).

```json

{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
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

Nella definizione JSON, si notino le impostazioni specifiche seguenti:

```json
"fileName": "input.log",
"folderPath": "adfgetstarted/inputdata",
```

#### <a name="azure-blob-output-dataset"></a>Set di dati di output del BLOB di Azure
Nella definizione del set di dati di output si specificano i nomi del contenitore BLOB e della cartella contenente i dati di output. Per informazioni dettagliate sulle proprietà JSON usate per definire un set di dati del BLOB di Azure, vedere [Proprietà del set di dati del BLOB di Azure](../data-factory/data-factory-azure-blob-connector.md#dataset-properties).  

```json

{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "adfgetstarted/partitioneddata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        }
    }
}
```

folderPath specifica il percorso della cartella contenente i dati di output:

```json
"folderPath": "adfgetstarted/partitioneddata",
```

L'impostazione [disponibilità dei set di dati](../data-factory/data-factory-create-datasets.md#dataset-availability) è la seguente:

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "style": "EndOfInterval"
},
```

In Azure Data Factory, la disponibilità dei set di dati di output attiva la pipeline. In questo esempio, la sezione viene prodotta mensilmente l'ultimo giorno del mese (EndOfInterval). Per altre informazioni, vedere [Pianificazione ed esecuzione con Data factory](../data-factory/data-factory-scheduling-and-execution.md).

#### <a name="data-pipeline"></a>Data Pipeline
Viene definita una pipeline che trasforma i dati eseguendo lo script Hive in un cluster HDInsight di Azure su richiesta. Per le descrizioni degli elementi JSON usati per definire una pipeline in questo esempio, vedere [Pipeline JSON](../data-factory/data-factory-create-pipelines.md#pipeline-json).

```json
{
    "type": "datapipelines",
    "name": "[parameters('dataFactoryName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobInputDatasetName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobOutputDatasetName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
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
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-01-01T00:00:00Z",
        "end": "2016-01-31T00:00:00Z",
        "isPaused": false
    }
}
```

La pipeline contiene un'attività, HDInsightHive. Poiché sia la data di inizio che la data di fine sono nel mese di gennaio 2016, vengono elaboratori i dati per un solo mese (una sezione). Le date di *inizio* e di *fine* dell'attività sono entrambe già passate, quindi Data Factory elabora i dati del mese immediatamente. Se la data finale è futura, Data Factory crea un'altra sezione a tempo debito. Per altre informazioni, vedere [Pianificazione ed esecuzione con Data factory](../data-factory/data-factory-scheduling-and-execution.md).

## <a name="clean-up-the-tutorial"></a>Eseguire la pulizia dell'esercitazione

### <a name="delete-the-blob-containers-created-by-on-demand-hdinsight-cluster"></a>Eliminare i contenitori BLOB creati dal cluster HDInsight su richiesta
Con il servizio collegato HDInsight on demand viene creato un cluster HDInsight ogni volta che è necessario elaborare una sezione, a meno che non esista un cluster attivo, timeToLive, che viene eliminato al termine dell'elaborazione. Per ogni cluster, Azure Data Factory crea un contenitore BLOB nell'archivio BLOB di Azure usato come account di archiviazione predefinito per il cluster. Anche se il cluster HDInsight viene eliminato, il contenitore di archiviazione BLOB predefinito e l'account di archiviazione associato non vengono eliminati. Questo comportamento dipende dalla progettazione. Man mano che vengono elaborate più sezioni, vengono visualizzati numerosi contenitori nell'archivio BLOB di Azure. Se non sono necessari per risolvere i problemi relativi ai processi, è possibile eliminarli per ridurre i costi di archiviazione. I nomi dei contenitori seguono il modello `adfyourdatafactoryname-linkedservicename-datetimestamp`.

Eliminare le cartelle **adfjobs** e **adfyourdatafactoryname-linkedservicename-datetimestamp**. Il contenitore adfjobs contiene i log dei processi di Azure Data Factory.

### <a name="delete-the-resource-group"></a>Eliminare il gruppo di risorse.
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) viene usato per distribuire, gestire e monitorare la soluzione come gruppo.  Eliminando un gruppo di risorse vengono eliminati tutti i componenti all'interno del gruppo.  

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Gruppi di risorse** nel pannello di sinistra.
3. Fare clic sul nome del gruppo di risorse creato con lo script di PowerShell. Se sono presenti troppi gruppi di risorse elencati, usare il filtro. Viene aperto il gruppo di risorse in un nuovo pannello.
4. Nel riquadro **Risorse** dovrebbe essere indicato l'account di archiviazione predefinito e l'istanza Data Factory, a meno che il gruppo di risorse non sia condiviso con altri progetti.
5. Fare clic su **Elimina** nella parte superiore del pannello. In questo modo si eliminano l'account di archiviazione e i dati in esso archiviati.
6. Immettere il nome del gruppo di risorse per confermare l'eliminazione e quindi fare clic su **Elimina**.

Se non si vuole eliminare l'account di archiviazione quando si elimina il gruppo di risorse, prendere in considerazione l'architettura seguente in modo da separare i dati aziendali dall'account di archiviazione predefinito. In questo caso, si ha un gruppo di risorse per l'account di archiviazione con i dati aziendali e un secondo gruppo di risorse per l'account di archiviazione predefinito per il servizio collegato HDInsight e la data factory. L'eliminazione del secondo gruppo di risorse non influisce sull'account di archiviazione dei dati aziendali. A tale scopo, procedere come segue:

* Aggiungere quanto segue al gruppo di risorse di livello principale insieme alla risorsa Microsoft.DataFactory/datafactories nel modello di Resource Manager. Viene creato un account di archiviazione:

    ```json
    {
        "name": "[parameters('defaultStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": {

        },
        "properties": {
            "accountType": "Standard_LRS"
        }
    },
    ```
* Aggiungere un nuovo punto di servizio collegato al nuovo account di archiviazione:

    ```json
    {
        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
        "type": "linkedservices",
        "name": "[variables('defaultStorageLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('defaultStorageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccountName')), variables('defaultApiVersion')).key1)]"
            }
        }
    },
    ```
* Configurare il servizio collegato HDInsight on demand con un dependsOn e un additionalLinkedServiceNames aggiuntivi:

    ```json
    {
        "dependsOn": [
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('defaultStorageLinkedServiceName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"

        ],
        "type": "linkedservices",
        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "sshUserName": "myuser",                            
                "sshPassword": "MyPassword!",
                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
            }
        }
    },            
    ```
## <a name="next-steps"></a>Passaggi successivi
Questo articolo ha descritto come usare Azure Data Factory per creare il cluster HDInsight on demand per l'elaborazione dei processi Hive. Altre informazioni:

* [Esercitazione di Hadoop: Introduzione all'uso di Hadoop basato su Linux in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Creare cluster Hadoop basati su Linux in HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Documentazione relativa a HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
* [Documentazione di Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

## <a name="appendix"></a>Appendice

### <a name="azure-cli-script"></a>Script dell'interfaccia della riga di comando di Azure
Per eseguire l'esercitazione è possibile usare l'interfaccia della riga di comando di Azure anziché Azure PowerShell. Per usare l'interfaccia della riga di comando di Azure, per prima cosa installarla seguendo queste istruzioni:

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

#### <a name="use-azure-cli-to-prepare-the-storage-and-copy-the-files"></a>Usare l'interfaccia della riga di comando di Azure per preparare l'archiviazione e copiare i file

```
azure login

azure config mode arm

azure group create --name "<Azure Resource Group Name>" --location "East US 2"

azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
```

Il nome del contenitore è *adfgetstarted*. Mantenerlo invariato, altrimenti sarebbe necessario aggiornare il modello di modello di Resource Manager. Se occorre assistenza per questo script dell'interfaccia della riga di comando, vedere [Utilizzo dell'interfaccia della riga di comando di Azure con archiviazione di Azure](../storage/storage-azure-cli.md).

