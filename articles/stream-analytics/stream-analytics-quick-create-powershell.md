---
title: Creare un processo di Analisi di flusso tramite Azure PowerShell
description: Questa guida introduttiva illustra in dettaglio l'uso del modulo Azure PowerShell per distribuire ed eseguire un processo di Analisi di flusso di Azure.
services: stream-analytics
author: SnehaGunda
ms.author: sngun
ms.date: 05/14/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 2b5d8bfd6dbe36637a0c6873e941118e7ee71b80
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212433"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>Guida introduttiva: Creare un processo di Analisi di flusso tramite Azure PowerShell

Il modulo Azure PowerShell viene usato per creare e gestire le risorse di Azure tramite cmdlet o script di PowerShell. Questa guida introduttiva illustra in dettaglio l'uso del modulo Azure PowerShell per distribuire ed eseguire un processo di Analisi di flusso di Azure. 

Il processo di esempio legge i dati in streaming da un BLOB nell'archivio BLOB di Azure. Il file di dati di input usati in questa guida introduttiva contiene i dati statici solo a scopo illustrativo. In uno scenario reale si usa il flusso di dati di input per un processo di Analisi di flusso. Il processo trasforma quindi i dati usando il linguaggio di query di analisi di flusso per calcolare la temperatura media quando è superiore a 100 °. Scrive infine gli eventi di output risultanti in un altro file. 

## <a name="before-you-begin"></a>Prima di iniziare

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/).  

* Per questa guida introduttiva è richiesto il modulo Azure PowerShell versione 3.6 o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione installata nel computer locale. Se è necessario eseguire l'installazione o l'aggiornamento, vedere l'articolo [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). 


## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando `Connect-AzureRmAccount` e immettere le credenziali di Azure nella finestra del browser a comparsa:

```powershell
# Log in to your Azure account
Connect-AzureRmAccount
```

Dopo l'accesso, se si dispone di più sottoscrizioni, selezionare quella che si vuole usare per questa guida introduttiva eseguendo i cmdlet seguenti. Assicurarsi di sostituire <your subscription name> con il nome della sottoscrizione:  

```powershell
# List all available subscriptions.
Get-AzureRmSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzureRmSubscription -SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzureRmResourceGroup `
   -Name $resourceGroup `
   -Location $location 
```

## <a name="prepare-the-input-data"></a>Preparare i dati di input

Prima di definire il processo di Analisi di flusso, preparare i dati configurati come input per il processo.

1. Scaricare i [dati di esempio dei sensori](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) da GitHub. Fare clic con il pulsante destro del mouse sul collegamento e scegliere **Save Link As...** (Salva collegamento con nome) o **Save target as** (Salva destinazione con nome).

2. Il blocco di codice di PowerShell seguente esegue vari comandi per preparare i dati di input richiesti dal processo. Esaminare le sezioni per comprendere il codice. 

   1. Creare un account di archiviazione standard per uso generico usando il cmdlet [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount).  Questo esempio crea un account di archiviazione denominato mystorageaccount con archiviazione con ridondanza locale e crittografia BLOB (abilitata per impostazione predefinita).  

   2. Recuperare il contesto `$storageAccount.Context` che definisce l'account di archiviazione da usare. Quando si usano gli account di archiviazione, si può fare riferimento al contesto anziché fornire ripetutamente le credenziali. 

   3. Creare un contenitore di archiviazione usando il cmdlet [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) e caricare i [dati di esempio del sensore](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) scaricati in precedenza. 

   4. Copiare la chiave di archiviazione restituita dal codice e incollarla chiave nei file JSON per la creazione di input e output del processo di streaming in un secondo momento.

   ```powershell
   $storageAccountName = "mystorageaccount"
   $storageAccount = New-AzureRmStorageAccount `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName `
     -Location $location `
     -SkuName Standard_LRS `
     -Kind Storage
   
   $ctx = $storageAccount.Context
   $containerName = "streamanalytics"
   
   New-AzureStorageContainer `
     -Name $containerName `
     -Context $ctx
   
   Set-AzureStorageBlobContent `
     -File "c:\HelloWorldASA-InputStream.json" `
     -Blob "input/HelloWorldASA-InputStream.json" `
     -Container $containerName `
     -Context $ctx  
   
   $storageAccountKey = (Get-AzureRmStorageAccountKey `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName).Value[0]
   
   Write-Host "The <storage account key> placeholder needs to be replaced in your input and output json files with this key value:" 
   Write-Host $storageAccountKey -ForegroundColor Cyan
   ```

## <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.

Creare un processo di Analisi di flusso con il cmdlet [New-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0). Questo cmdlet accetta il nome del processo, il nome del gruppo di risorse e la definizione del processo come parametri. Il nome del processo può essere un qualsiasi nome descrittivo che identifica il processo. Può contenere solo caratteri alfanumerici, trattini e caratteri di sottolineatura e deve avere una lunghezza compresa tra 3 e 63 caratteri. La definizione del processo è un file JSON che contiene le proprietà necessarie per creare un processo. Nel computer locale creare un file denominato `JobDefinition.json` e aggiungere i dati JSON seguenti:

```json
{    
   "location":"WestUS2",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"adjust",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
   }
}
```

Eseguire quindi il cmdlet `New-AzureRmStreamAnalyticsJob`. Assicurarsi di sostituire il valore della variabile `jobDefinitionFile` con il percorso in cui è stato archiviato il file JSON della definizione del processo. 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -File $jobDefinitionFile `
  -Name $jobName `
  -Force 
```

## <a name="configure-input-to-the-job"></a>Configurare l'input per il processo

Aggiungere un input al processo tramite il cmdlet [New-AzureRmStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0). Questo cmdlet accetta il nome del processo, il nome dell'input del processo, il nome del gruppo di risorse e la definizione dell'input del processo come parametri. La definizione dell'input del processo è un file JSON che contiene le proprietà necessarie per configurare l'input del processo. In questo esempio come input verrà creato un archivio BLOB. 

Nel computer locale creare un file denominato `JobInputDefinition.json` e aggiungere i dati JSON seguenti. Assicurarsi di sostituire il valore di `accountKey` con la chiave di accesso dell'account di archiviazione corrispondente al valore archiviato in $storageAccountKey. 

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
                   "accountKey":"<storage account key>"
                }],
                "container": "streamanalytics",
                "pathPattern": "input/",
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

Eseguire quindi il cmdlet `New-AzureRmStreamAnalyticsInput` e assicurarsi di sostituire il valore della variabile `jobDefinitionFile` con il percorso in cui è stato archiviato il file JSON della definizione dell'input del processo. 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRmStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>Configurare l'output per il processo

Aggiungere un output al processo tramite il cmdlet [New-AzureRMStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0). Questo cmdlet accetta il nome del processo, il nome dell'output del processo, il nome del gruppo di risorse e la definizione dell'output del processo come parametri. La definizione dell'output del processo è un file JSON che contiene le proprietà necessarie per configurare l'output del processo. In questo esempio come output viene usato l'archivio BLOB. 

Nel computer locale creare un file denominato `JobOutputDefinition.json` e aggiungere i dati JSON seguenti. Assicurarsi di sostituire il valore di `accountKey` con la chiave di accesso dell'account di archiviazione corrispondente al valore archiviato in $storageAccountKey. 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<storage account key>"
                    }],
                "container": "streamanalytics",
                "pathPattern": "output/",
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

Eseguire quindi il cmdlet `New-AzureRmStreamAnalyticsOutput`. Assicurarsi di sostituire il valore della variabile `jobOutputDefinitionFile` con il percorso in cui è stato archiviato il file JSON della definizione dell'output del processo. 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRmStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobOutputDefinitionFile `
  -Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>Definire la query di trasformazione

Aggiungere una trasformazione al processo tramite il cmdlet [New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0). Questo cmdlet accetta il nome del processo, il nome della trasformazione del processo, il nome del gruppo di risorse e la definizione della trasformazione del processo come parametri. Nel computer locale creare un file denominato `JobTransformationDefinition.json` e aggiungere i dati JSON seguenti. Il file JSON contiene un parametro di query che definisce la query di trasformazione:

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

Eseguire quindi il cmdlet `New-AzureRmStreamAnalyticsTransformation`. Assicurarsi di sostituire il valore della variabile `jobTransformationDefinitionFile` con il percorso in cui è stato archiviato il file JSON della definizione della trasformazione del processo. 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRmStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Avviare il processo di Analisi di flusso e controllare l'output

Avviare il processo tramite il cmdlet [Start-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0). Questo cmdlet accetta il nome del processo, il nome del gruppo di risorse, la modalità di avvio dell'output e l'ora di inizio come parametri. `OutputStartMode` accetta i valori `JobStartTime`, `CustomTime` o `LastOutputEventTime`. Per altre informazioni sugli elementi a cui fanno riferimento ognuno di questi valori, vedere la sezione sui [parametri](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) nella documentazione di PowerShell. In questo esempio specificare `CustomTime` come valore di mode e fornire un valore per `OutputStartTime`. 

Come valore di time selezionare `2018-01-01`. La data di inizio viene selezionata in quanto precede il timestamp dell'evento dai dati di esempio. Dopo aver eseguito il cmdlet seguente, verrà restituito `True` come output se il processo viene avviato. Nel contenitore di archiviazione viene creata una cartella di output con i dati trasformati. 

```powershell
Start-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-01-01T00:00:00Z 
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il processo di streaming e tutte le risorse correlate. Eliminando il processo si evita di pagare per le unità di streaming usate dal processo. Se si prevede di usare il processo in futuro, è possibile non eliminarlo e arrestarlo per il momento. Se non si intende continuare a usare il processo, eliminare tutte le risorse create tramite questa guida introduttiva eseguendo il cmdlet seguente:

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato distribuito un semplice processo di Analisi di flusso. Per informazioni sulla configurazione di altre origini di input e sull'esecuzione del rilevamento in tempo reale, continuare con l'articolo seguente:

> [!div class="nextstepaction"]
> [Rilevamento delle frodi in tempo reale tramite Analisi di flusso di Azure](stream-analytics-real-time-fraud-detection.md)
