---
title: Trasformare i dati con Spark in Azure Data Factory | Microsoft Docs
description: "Questa esercitazione offre istruzioni dettagliate per la trasformazione dei dati tramite l'attività Spark in Azure Data Factory."
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
ms.date: 10/06/2017
ms.author: shengc
ms.openlocfilehash: 74bff33edd738642ea88ca9b3430aac0ff32bc6f
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2017
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>Trasformare dati nel cloud usando l'attività Spark in Azure Data Factory
In questa esercitazione si usa Azure PowerShell per creare una pipeline di Data Factory che trasforma i dati con un'attività Spark e un servizio collegato HDInsight su richiesta. In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare una data factory. 
> * Creare e distribuire servizi collegati.
> * Creare e distribuire una pipeline. 
> * Avviare un'esecuzione della pipeline.
> * Monitorare l'esecuzione della pipeline.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
* **Account di archiviazione di Azure**. Creare uno script Python e un file di input, quindi caricarli nell'archivio di Azure. L'output del programma Spark viene archiviato in questo account di archiviazione. Il cluster Spark su richiesta usa lo stesso account di archiviazione come risorsa di archiviazione primaria.  
* **Azure PowerShell**. Seguire le istruzioni in [How to install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Come installare e configurare Azure PowerShell).


### <a name="upload-python-script-to-your-blob-storage-account"></a>Caricare lo script Python nell'account di archiviazione BLOB
1. Creare un file Python denominato **WordCount_Spark.py** con il contenuto seguente: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Sostituire **&lt;storageAccountName&gt;** con il nome del proprio account di archiviazione di Azure. Salvare quindi il file. 
3. Nell'Archivio BLOB di Azure creare un contenitore denominato **adftutorial**, se non esiste. 
4. Creare una cartella denominata **spark**.
5. Creare una sottocartella denominata **script** nella cartella **spark**. 
6. Caricare il file **WordCount_Spark.py** nella sottocartella **script**. 


### <a name="upload-the-input-file"></a>Caricare il file di input
1. Creare un file denominato **minecraftstory.txt** con del testo. Il programma Spark conta il numero di parole in questo testo. 
2. Creare una sottocartella denominata `inputfiles` nella cartella `spark`. 
3. Caricare il file `minecraftstory.txt` nella sottocartella `inputfiles`. 

## <a name="author-linked-services"></a>Creare servizi collegati
In questa sezione vengono creati due servizi collegati: 
    
- Un servizio collegato di Archiviazione di Azure che collega l'account di archiviazione di Azure alla data factory. Questo archivio viene usato dal cluster HDInsight su richiesta. Contiene anche lo script Spark da eseguire. 
- Un servizio collegato HDInsight su richiesta. Azure Data Factory crea automaticamente un cluster HDInsight, esegue il programma Spark ed elimina il cluster HDInsight dopo che è rimasto inattivo per un periodo di tempo preconfigurato. 

### <a name="azure-storage-linked-service"></a>Servizio collegato Archiviazione di Azure
Creare un file JSON usando l'editor preferito, copiare la definizione JSON seguente di un servizio collegato di Archiviazione di Azure e quindi salvare il file con il nome **MyStorageLinkedService.json**.  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
          "type": "SecureString"
        }
      }
    }
}
```
Sostituire &lt;storageAccountName&gt; e &lt;storageAccountKey&gt; con il nome e la chiave dell'account di archiviazione di Azure. 


### <a name="on-demand-hdinsight-linked-service"></a>Servizio collegato HDInsight su richiesta
Creare un file JSON usando l'editor preferito, copiare la definizione JSON seguente di un servizio collegato di Azure HDInsight e quindi salvare il file con il nome **MyOnDemandSparkLinkedService.json**.  

```json
{
    "name": "MyOnDemandSparkLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
        "clusterSize": 2,
        "clusterType": "spark",
        "timeToLive": "00:15:00",
        "hostSubscriptionId": "<subscriptionID> ",
        "servicePrincipalId": "<servicePrincipalID>",
        "servicePrincipalKey": {
          "value": "<servicePrincipalKey>",
          "type": "SecureString"
        },
        "tenant": "<tenant ID>",
        "clusterResourceGroup": "<resourceGroupofHDICluster>",
        "version": "3.6",
        "osType": "Linux",
        "clusterNamePrefix":"ADFSparkSample",
        "linkedServiceName": {
          "referenceName": "MyStorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }
}
```
Aggiornare i valori per le proprietà seguenti nella definizione del servizio collegato: 

- **hostSubscriptionId**. Sostituire &lt;subscriptionID&gt; con l'ID della sottoscrizione di Azure. Il cluster HDInsight su richiesta verrà creato in questa sottoscrizione. 
- **tenant**. Sostituire &lt;tenantID&gt; con l'ID del tenant di Azure. 
- **servicePrincipalId**, **servicePrincipalKey**. Sostituire &lt;servicePrincipalID&gt; e &lt;servicePrincipalKey&gt; con l'ID e la chiave dell'entità servizio in Azure Active Directory. Questa entità servizio deve essere un membro del ruolo Collaboratore della sottoscrizione o del gruppo di risorse in cui viene creato il cluster. Vedere come [creare un'applicazione e un'entità servizio di Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md) per dettagli. 
- **clusterResourceGroup**. Sostituire &lt;resourceGroupOfHDICluster&gt; con il nome del gruppo di risorse in cui deve essere creato il cluster HDInsight. 

> [!NOTE]
> Azure HDInsight applica un limite al numero totale di core che è possibile usare in ogni area di Azure supportata. Per il servizio collegato HDInsight su richiesta, il cluster HDInsight verrà creato nello stesso percorso dell'archivio di Azure usato come risorsa di archiviazione primaria. Assicurarsi che siano disponibili sufficienti quote di core per la creazione del cluster. Per altre informazioni, vedere [Configurare i cluster di HDInsight con Hadoop, Spark, Kafka e altro ancora](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 


## <a name="author-a-pipeline"></a>Creare una pipeline 
In questo passaggio si crea una nuova pipeline con un'attività Spark. L'attività usa l'esempio del **conteggio parole**. Se non è già stato fatto, scaricare il contenuto da questo percorso.

Creare un file JSON usando l'editor preferito, copiare la definizione JSON seguente di una pipeline e quindi salvare il file con il nome **MySparkOnDemandPipeline.json**. 

```json
{
  "name": "MySparkOnDemandPipeline",
  "properties": {
    "activities": [
      {
        "name": "MySparkActivity",
        "type": "HDInsightSpark",
        "linkedServiceName": {
            "referenceName": "MyOnDemandSparkLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "rootPath": "adftutorial/spark",
          "entryFilePath": "script/WordCount_Spark.py",
          "getDebugInfo": "Failure",
          "sparkJobLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ]
  }
}
```

Tenere presente quanto segue: 

- rootPath punta alla cartella spark del contenitore adftutorial. 
- entryFilePath punta al file WordCount_Spark.py nella sottocartella script della cartella spark. 


## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory 
Sono state create definizioni di servizio collegato e pipeline nei file JSON. Verrà ora creata una data factory e verranno distribuiti i file JSON del servizio collegato e della pipeline usando i cmdlet di PowerShell. Eseguire questi comandi di PowerShell uno alla volta: 

1. Impostare le variabili una alla volta.

    **Nome gruppo di risorse**
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup" 
    ```

    **Nome data factory. Deve essere univoco a livello globale** 
    ```powershell
    $dataFactoryName = "MyDataFactory09102017"
    ```
    
    **Nome pipeline**
    ```powershell
    $pipelineName = "MySparkOnDemandPipeline" # Name of the pipeline
    ```
2. Avviare **PowerShell**. Tenere aperto Azure PowerShell fino al termine di questa guida introduttiva. Se si chiude e si riapre, sarà necessario eseguire di nuovo questi comandi. Data Factory V2 consente attualmente di creare data factory solo nelle aree Stati Uniti orientali, Stati Uniti orientali 2 ed Europa occidentale. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.

    Eseguire questo comando e immettere il nome utente e la password usati per accedere al portale di Azure:
        
    ```powershell
    Login-AzureRmAccount
    ```        
    Eseguire questo comando per visualizzare tutte le sottoscrizioni per l'account:

    ```powershell
    Get-AzureRmSubscription
    ```
    Eseguire il comando seguente per selezionare la sottoscrizione da usare. Sostituire **SubscriptionId** con l'ID della sottoscrizione di Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"    
    ```  
3. Creare il gruppo di risorse ADFTutorialResourceGroup. 

    ```powershell
    New-AzureRmResourceGroup -Name $resourceGroupName -Location "East Us" 
    ```
4. Creare la data factory. 

    ```powershell
     $df = Set-AzureRmDataFactoryV2 -Location EastUS -Name $dataFactoryName -ResourceGroupName $resourceGroupName
    ```

    Eseguire questo comando per visualizzare l'output: 

    ```powershell
    $df
    ```
5. Passare alla cartella in cui sono stati creati i file JSON ed eseguire questo comando per distribuire un servizio collegato di Archiviazione di Azure: 
       
    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"
    ```
6. Eseguire questo comando per distribuire un servizio collegato Spark su richiesta: 
       
    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyOnDemandSparkLinkedService" -File "MyOnDemandSparkLinkedService.json"
    ```
7. Eseguire questo comando per distribuire una pipeline: 
       
    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MySparkOnDemandPipeline.json"
    ```
    
## <a name="start-and-monitor-a-pipeline-run"></a>Avviare e monitorare un'esecuzione della pipeline  

1. Avviare un'esecuzione della pipeline. Viene anche acquisito l'ID di esecuzione della pipeline per il monitoraggio futuro.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName  
    ```
2. Eseguire questo script per verificare costantemente lo stato di esecuzione della pipeline fino al termine.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    
        if(!$result) {
            Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
        }
        elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        }
        else {
            Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
        ($result | Format-List | Out-String)
        Start-Sleep -Seconds 15
    }

    Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"

    Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n" 
    ```  
3. Ecco l'output dell'esecuzione di esempio: 

    ```
    Pipeline run status: In Progress
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : 
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : 
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 
    DurationInMs      : 
    Status            : InProgress
    Error             :
    …
    
    Pipeline ' MySparkOnDemandPipeline' run finished. Result:
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : MyDataFactory09102017
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : {clusterInUse, jobId, ExecutionProgress, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 9/20/2017 6:46:30 AM
    DurationInMs      : 763466
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "clusterInUse": "https://ADFSparkSamplexxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azurehdinsight.net/"
    "jobId": "0"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MySparkActivity"
    ```
4. Verificare che sia stata creata una cartella denominata `outputfiles` nella cartella `spark` del contenitore adftutorial con l'output del programma Spark. 


## <a name="next-steps"></a>Passaggi successivi
La pipeline in questo esempio copia i dati da una posizione a un'altra in un archivio BLOB di Azure. Si è appreso come: 

> [!div class="checklist"]
> * Creare una data factory. 
> * Creare e distribuire servizi collegati.
> * Creare e distribuire una pipeline. 
> * Avviare un'esecuzione della pipeline.
> * Monitorare l'esecuzione della pipeline.

Passare alla prossima esercitazione per informazioni su come trasformare i dati tramite l'esecuzione di uno script Hive in un cluster Azure HDInsight che si trova in una rete virtuale. 

> [!div class="nextstepaction"]
> [Esercitazione: Trasformare dati usando Hive nella rete virtuale di Azure](tutorial-transform-data-hive-virtual-network.md).





