---
title: Creare un processo di Analisi di flusso tramite Azure PowerShell
description: Questa guida introduttiva illustra in dettaglio l'uso del modulo Azure PowerShell per distribuire ed eseguire un processo di Analisi di flusso di Azure.
services: stream-analytics
author: SnehaGunda
ms.author: sngun
ms.date: 03/16/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 0be8cee9e6c7874282f4e8f43f75fa7f2490c14e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>Guida introduttiva: Creare un processo di Analisi di flusso tramite Azure PowerShell

Il modulo Azure PowerShell viene usato per creare e gestire le risorse di Azure tramite cmdlet o script di PowerShell. Questa guida introduttiva illustra in dettaglio l'uso del modulo Azure PowerShell per distribuire ed eseguire un processo di Analisi di flusso di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/).  

* Per questa guida introduttiva è richiesto il modulo Azure PowerShell versione 3.6 o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione installata nel computer locale. Se è necessario eseguire l'installazione o l'aggiornamento, vedere l'articolo [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Lo scenario illustrato in questo articolo descrive in che modo i dati vengono letti dall'archiviazione BLOB, vengono trasformati e quindi vengono scritti in un contenitore diverso nella stessa risorsa di archiviazione BLOB.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Connect-AzureRmAccount` e immettere le credenziali di Azure nella finestra del browser a comparsa. Dopo l'accesso, se si dispone di più sottoscrizioni, selezionare quella che si vuole usare per questa guida introduttiva eseguendo i cmdlet seguenti. Assicurarsi di sostituire <your subscription> con il nome della sottoscrizione:  

```powershell
# Log in to your Azure account
Connect-AzureRmAccount

# List all available subscriptions.
Get-AzureRmSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzureRmSubscription -SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

```powershell
$resourceGroup = "ASAPSRG"
$location = "WestUS2"
New-AzureRMResourceGroup `
  -Name $resourceGroup `
  -Location $location 
```

## <a name="prepare-the-input-data"></a>Preparare i dati di input

Prima di definire il processo di Analisi di flusso, è necessario preparare i dati configurati come input per il processo. Per preparare i dati di input richiesti dal processo, seguire questa procedura: 

1. Scaricare i [dati di esempio dei sensori](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) da GitHub.  

2. Creare un account di archiviazione standard per uso generico con replica dell'archiviazione con ridondanza locale usando il cmdlet [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount).  

3. Recuperare il contesto che definisce l'account di archiviazione da usare. Quando si usano gli account di archiviazione, si può fare riferimento al contesto anziché fornire ripetutamente le credenziali. Questo esempio crea un account di archiviazione denominato mystorageaccount con archiviazione con ridondanza locale e crittografia BLOB (abilitata per impostazione predefinita).  

4. Creare quindi un contenitore usando [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer), impostare le autorizzazioni su 'blob' per consentire l'accesso pubblico ai file e caricare i [dati di esempio dei sensori](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) scaricati in precedenza. 

Questi passaggi vengono eseguiti tramite l'esecuzione dello script PowerShell seguente:

```powershell
$storageAccountName = "mystorageaccount"
$storageAccount = New-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
$containerName = "myinputcontainer"

New-AzureStorageContainer `
  -Name $containerName `
  -Context $ctx `
  -Permission blob

Set-AzureStorageBlobContent `
  -File "C:\HelloWorldASA-InputStream.json" `
  -Container $containerName `
  -Context $ctx  

$storageAccountKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName).Value[0]
```

## <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.

Creare un processo di Analisi di flusso con il cmdlet [New-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0). Questo cmdlet accetta il nome del processo, il nome del gruppo di risorse e la definizione del processo come parametri. Il nome del processo può essere un nome descrittivo qualunque che identifichi il processo. Il nome del processo di Analisi di flusso può contenere solo caratteri alfanumerici, trattini e caratteri di sottolineatura e deve avere una lunghezza compresa tra 3 e 63 caratteri. La definizione del processo è un file JSON che contiene le proprietà necessarie per creare un processo. Nel computer locale creare un file denominato "JobDefinition.json" e aggiungere i dati JSON seguenti:

```json
{    
   "location":"Central US",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"drop",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
}
}
```

Eseguire quindi il cmdlet New-AzureRMStreamAnalyticsJob, assicurarsi di sostituire il valore della variabile jobDefinitionFile con il percorso in cui è memorizzato il file JSON di definizione del processo. 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  –File $jobDefinitionFile `
  –Name $jobName -Force 
```

## <a name="configure-input-to-the-job"></a>Configurare l'input per il processo

Aggiungere un input al processo tramite il cmdlet [New-AzureRMStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0). Questo cmdlet accetta il nome del processo, il nome dell'input del processo, il nome del gruppo di risorse e la definizione dell'input del processo come parametri. La definizione dell'input del processo è un file JSON che contiene le proprietà necessarie per configurare l'input del processo, in questo esempio si creerà un'archiviazione BLOB come input. Nel computer locale creare un file denominato "JobInputDefinition.json" e aggiungere i dati JSON seguenti, assicurarsi di sostituire il valore di **accountKey** con la chiave di accesso dell'account di archiviazione corrispondente al valore archiviato in $storageAccountKey. 

```json
{
    "properties": {
        "type": "Stream",
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                {
                   "accountName": "mystorageaccount",
                   "accountKey":"<Your storage account key>"
                }],
                "container": "myinputcontainer",
                "pathPattern": "",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8"
            }
        }
    },
    "name": "MyBlobInput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/inputs"
}
```

Eseguire quindi il cmdlet New-AzureRMStreamAnalyticsInput, assicurarsi di sostituire il valore della variabile jobDefinitionFile con il percorso in cui è memorizzato il file JSON di definizione dell'input del processo. 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRMStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>Configurare l'output per il processo

Aggiungere un output al processo tramite il cmdlet [New-AzureRMStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0). Questo cmdlet accetta il nome del processo, il nome dell'output del processo, il nome del gruppo di risorse e la definizione dell'output del processo come parametri. La definizione dell'output del processo è un file JSON che contiene le proprietà necessarie per configurare l'output del processo, in questo esempio si creerà un'archiviazione BLOB come output. Nel computer locale creare un file denominato "JobOutputDefinition.json" e aggiungere i dati JSON seguenti, assicurarsi di sostituire il valore di **accountKey** con la chiave di accesso dell'account di archiviazione corrispondente al valore archiviato in $storageAccountKey. 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<Your storage account key>"
                    }],
                "container": "myoutputcontainer",
                "pathPattern": "",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8",
                "format": "LineSeparated"
            }
        }
    },
    "name": "MyBlobOutput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/outputs"
}
```

Eseguire quindi il cmdlet New-AzureRMStreamAnalyticsOutput, assicurarsi di sostituire il valore della variabile jobOutputDefinitionFile con il percorso in cui è memorizzato il file JSON di definizione dell'output del processo. 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRMStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobOutputDefinitionFile `
  –Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>Definire la query di trasformazione

Aggiungere una trasformazione al processo tramite il cmdlet [New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0). Questo cmdlet accetta il nome del processo, il nome della trasformazione del processo, il nome del gruppo di risorse e la definizione della trasformazione del processo come parametri. Nel computer locale creare un file denominato "JobTransformationDefinition.json" e aggiungere i dati JSON seguenti. Il file JSON contiene un parametro di query che definisce la query di trasformazione:

```json
{     
   "name":"MyTransformation",  
   "type":"Microsoft.StreamAnalytics/streamingjobs/transformations",  
   "properties":{    
      "streamingUnits":1,  
      "script":null,  
      "query":" SELECT System.Timestamp AS OutputTime, dspl AS SensorName, Avg(temp) AS AvgTemperature INTO MyBlobOutput FROM MyBlobInput TIMESTAMP BY time GROUP BY TumblingWindow(second,30),dspl HAVING Avg(temp)>100"  
   }  
}
```

Eseguire quindi il cmdlet New-AzureRMStreamAnalyticsTransformation, assicurarsi di sostituire il valore della variabile jobTransformationDefinitionFile con il percorso in cui è memorizzato il file JSON di definizione della trasformazione del processo. 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRMStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobTransformationDefinitionFile `
  –Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Avviare il processo di Analisi di flusso e controllare l'output

Avviare il processo tramite il cmdlet [Start-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0). Questo cmdlet accetta il nome del processo, il nome del gruppo di risorse, la modalità di avvio dell'output e l'ora di inizio come parametri. OUtpputStartMode accetta due valori JobStartTime, CustomTime o LastOutputEventTime. Per informazioni su ciò a cui fa riferimento ognuno di questi valori, vedere la sezione [Parametri](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) nella documentazione di PowerShell. In questo esempio è possibile specificare la modalità come CustomTime e fornire un valore per OutputStartTime. 

Per il valore dell'ora, selezionare un giorno prima di quando è stato caricato il file nell'archiviazione BLOB, perché il momento in cui è stato caricato il file è precedente al momento attuale. Dopo aver eseguito il cmdlet seguente, verrà restituito "True" come output se il processo viene avviato. Nell'account di archiviazione viene creato un contenitore denominato "myoutputcontainer" con i dati trasformati. 

```powershell
Start-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-03-11T14:45:12Z 
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il processo di streaming e tutte le risorse correlate. Eliminando il processo si evita di pagare per le unità di streaming utilizzate dal processo. Se si prevede di usare il processo in futuro, è possibile arrestarlo e riavviarlo in un secondo momento, quando è necessario. Se non si intende continuare a usare il processo, eliminare tutte le risorse create tramite questa guida introduttiva eseguendo il cmdlet seguente:

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato distribuito un semplice processo di Analisi di flusso. Per informazioni sulla configurazione di altre origini di input e sull'esecuzione del rilevamento in tempo reale, continuare con l'articolo seguente:

> [!div class="nextstepaction"]
> [Rilevamento delle frodi in tempo reale tramite Analisi di flusso di Azure](stream-analytics-real-time-fraud-detection.md)
