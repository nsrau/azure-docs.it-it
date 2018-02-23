---
title: "Usare attività personalizzate in una pipeline di Azure Data Factory"
description: "Informazioni su come creare attività personalizzate e usarle in una pipeline di Azure Data Factory."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shengc
ms.openlocfilehash: ad829fc771bf67953315f3f42abd66eaa2628c13
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Usare attività personalizzate in una pipeline di Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-use-custom-activities.md)
> * [Versione 2 - Anteprima](transform-data-using-dotnet-custom-activity.md)

In una pipeline di Azure Data Factory è possibile usare due tipi di attività.

- [Attività di spostamento dei dati](copy-activity-overview.md) per spostare dati tra [archivi dati supportati di origine e sink](copy-activity-overview.md#supported-data-stores-and-formats).
- [Attività di trasformazione dei dati](transform-data.md) per trasformare i dati usando servizi di calcolo come Azure HDInsight, Azure Batch e Azure Machine Learning. 

Per spostare dati da o verso un archivio dati non supportato da Data Factory oppure per trasformare o elaborare dati in un modo non supportato da Data Factory, è possibile creare un'**attività personalizzata** contenente la logica di spostamento o trasformazione dei dati necessaria e usare tale attività in una pipeline. L'attività personalizzata esegue la logica del codice personalizzata in un pool di **Azure Batch** di macchine virtuali.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività DotNet (personalizzata) in Data Factory versione 1](v1/data-factory-use-custom-activities.md).
 

Vedere gli articoli seguenti se non si ha familiarità con il servizio Azure Batch:

* [Nozioni di base di Azure Batch](../batch/batch-technical-overview.md) per una panoramica del servizio Azure Batch.
* Cmdlet [New-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) per creare un account di Azure Batch oppure [Portale di Azure](../batch/batch-account-create-portal.md) per creare l'account di Azure Batch usando il portale di Azure. Per istruzioni dettagliate sull'uso del cmdlet, vedere l'articolo [Uso di Azure PowerShell per gestire l'account di Azure Batch](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
* [New AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) per creare un pool di Batch di Azure.

## <a name="azure-batch-linked-service"></a>Servizio collegato Azure Batch 
Il codice JSON seguente definisce un servizio collegato Azure Batch di esempio. Per i dettagli, vedere [Ambienti di calcolo supportati da Azure Data Factory](compute-linked-services.md)

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

 Per altre informazioni sul servizio collegato Azure Batch, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md). 

## <a name="custom-activity"></a>Attività personalizzata

Il frammento di codice JSON seguente definisce una pipeline con una semplice attività personalizzata. La definizione dell'attività contiene un riferimento al servizio collegato Azure Batch. 

```json
{
    "name": "MyCustomActivityPipeline",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "helloworld.exe",
          "folderPath": "customactv2/helloworld",
          "resourceLinkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }]
    }
  }
```

In questo esempio, helloworld.exe è un'applicazione personalizzata salvata nella cartella customactv2/helloworld dell'account di Archiviazione di Azure usato in resourceLinkedService. L'attività personalizzata invia l'applicazione personalizzata per l'esecuzione in Azure Batch. Nel comando è possibile sostituire l'applicazione con qualsiasi altra applicazione che possa essere eseguita nel sistema operativo di destinazione dei nodi del pool di Azure Batch. 

Nella tabella seguente vengono descritti i nomi e le descrizioni delle proprietà specifiche per questa attività. 

| Proprietà              | DESCRIZIONE                              | Obbligatoria |
| :-------------------- | :--------------------------------------- | :------- |
| name                  | Nome dell'attività nella pipeline     | Sì      |
| description           | Testo che descrive l'attività.  | No        |
| type                  | Per l'attività personalizzata, il tipo corrisponde a **Custom**. | Sì      |
| linkedServiceName     | Servizio collegato ad Azure Batch. Per informazioni su questo servizio collegato, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md).  | Sì      |
| command               | Comando dell'applicazione personalizzata da eseguire. Se l'applicazione è già disponibile nel nodo del pool di Azure Batch, è possibile ignorare resourceLinkedService e folderPath. È ad esempio possibile specificare come comando `cmd /c dir`, supportato in modo nativo dal nodo del pool di batch di Windows. | Sì      |
| resourceLinkedService | Servizio di Archiviazione di Azure collegato all'account di archiviazione in cui è archiviata l'applicazione personalizzata | No        |
| folderPath            | Percorso della cartella dell'applicazione personalizzata e di tutte le relative dipendenze | No        |
| referenceObjects      | Matrice di servizi collegati e set di dati esistenti. I servizi collegati e i set di dati a cui si fa riferimento vengono passati all'applicazione personalizzata in formato JSON. Il codice personalizzato può quindi fare riferimento a risorse di Data Factory | No        |
| extendedProperties    | Proprietà definite dall'utente che possono essere passate all'applicazione personalizzata in formato JSON. Il codice personalizzato può quindi fare riferimento a proprietà aggiuntive | No        |

## <a name="executing-commands"></a>Esecuzione di comandi

È possibile eseguire direttamente un comando tramite l'attività personalizzata. L'esempio seguente esegue un comando "echo hello world" nei nodi del pool di destinazione di Azure Batch e stampa l'output in stdout. 

  ```json
  {
    "name": "MyCustomActivity",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "cmd /c echo hello world"
        }
      }]
    }
  } 
  ```

## <a name="passing-objects-and-properties"></a>Passaggio di oggetti e proprietà

Questo esempio illustra come usare le proprietà referenceObjects ed extendedProperties per passare oggetti e proprietà definite dall'utente di Data Factory all'applicazione personalizzata. 


```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "aSampleSecureString"
            },
            "PropertyBagPropertyName1": "PropertyBagValue1",
            "propertyBagPropertyName2": "PropertyBagValue2",
            "dateTime1": "2015-04-12T12:13:14Z"              
        }
      }
    }]
  }
}
```

Quando l'attività viene eseguita, le proprietà referenceObjects ed extendedProperties vengono archiviate nei file descritti di seguito, che vengono distribuiti nella stessa cartella di esecuzione di SampleApp.exe: 

- activity.json

  Contiene extendedProperties e le proprietà dell'attività personalizzata. 

- linkedServices.json

  Contiene una matrice di servizi collegati definiti nella proprietà referenceObjects. 

- datasets.json

  Contiene una matrice di set di dati definiti nella proprietà referenceObjects. 

Il codice di esempio seguente illustra come SampleApp.exe è in grado di accedere alle informazioni necessarie dai file JSON: 

```csharp
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.connectionString.value);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>Recuperare gli output di esecuzione

  Per avviare una pipeline, eseguire il comando di PowerShell seguente: 

  ```.powershell
  $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
  ```
  Quando la pipeline è in esecuzione, è possibile controllare l'output dell'esecuzione usando i comandi seguenti: 

  ```.powershell
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

  **stdout** e **stderr** dell'applicazione personalizzata vengono salvati nel contenitore **adfjobs** nel servizio collegato di Archiviazione di Azure definito durante la creazione del servizio collegato Azure Batch con un GUID dell'attività. È possibile ottenere il percorso dettagliato dall'output di Esecuzione attività, come illustrato nel frammento di codice seguente: 

  ```shell
  Pipeline ' MyCustomActivity' run finished. Result:

  ResourceGroupName : resourcegroupname
  DataFactoryName   : datafactoryname
  ActivityName      : MyCustomActivity
  PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
  PipelineName      : MyCustomActivity
  Input             : {command}
  Output            : {exitcode, outputs, effectiveIntegrationRuntime}
  LinkedServiceName : 
  ActivityRunStart  : 10/5/2017 3:33:06 PM
  ActivityRunEnd    : 10/5/2017 3:33:28 PM
  DurationInMs      : 21203
  Status            : Succeeded
  Error             : {errorCode, message, failureType, target}

  Activity Output section:
  "exitcode": 0
  "outputs": [
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
  ]
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
  Activity Error section:
  "errorCode": ""
  "message": ""
  "failureType": ""
  "target": "MyCustomActivity"
  ```
Se si desidera usare il contenuto di stdout.txt nelle attività downstream, è possibile ottenere il percorso del file stdout.txt nell'espressione @activity('MyCustomActivity').output.outputs[0]". 

  > [!IMPORTANT]
  > - Activity.json, linkedServices.json e datasets.json vengono archiviati nella cartella di runtime dell'attività Batch. Per questo esempio, activity.json, linkedServices.json e datasets.json vengono archiviati nel percorso "https://adfv2storage.blob.core.windows.net/adfjobs/<GUID>/runtime/". Se necessario, la pulizia di questi file deve essere eseguita separatamente. 
  > - Poiché i servizi collegati usano Runtime di integrazione (self-hosted), le informazioni riservate, ad esempio le chiavi o le password, vengono crittografate da Runtime di integrazione (self-hosted) per verificare che le credenziali rimangano nell'ambiente di rete privata definito dal cliente. Alcuni campi riservati potrebbero risultare mancanti se il codice dell'applicazione personalizzata fa riferimento a tali campi in questo modo. Se necessario, usare SecureString in extendedProperties anziché un riferimento a servizi collegati. 

## <a name="difference-between-custom-activity-in-azure-data-factory-version-2-and-custom-dotnet-activity-in-azure-data-factory-version-1"></a>Differenza tra l'attività personalizzata in Azure Data Factory versione 2 e attività DotNet (personalizzata) in Azure Data Factory versione 1

  In Azure Data Factory versione 1 il codice dell'attività DotNet (personalizzata) viene implementato mediante la creazione di un progetto della libreria di classi .Net con una classe che implementa il metodo Execute dell'interfaccia IDotNetActivity. I servizi collegati, i set di dati e le proprietà estese nel payload JSON dell'attività DotNet (personalizzata) vengono passati al metodo Execution come oggetti tipizzati sicuri. Per informazioni dettagliate, vedere [DotNet (personalizzata) nella versione 1](v1/data-factory-use-custom-activities.md). Per questo motivo, il codice personalizzato deve essere scritto in .Net Framework 4.5.2 ed essere eseguito sui nodi pool di Azure Batch basati su Windows. 

  Nell'attività personalizzata di Azure Data Factory versione 2 non è necessario implementare un'interfaccia .Net. È ora possibile eseguire direttamente i comandi, gli script e il proprio codice personalizzato compilato come eseguibile. A tale fine specificare la proprietà Command e la proprietà folderPath. L'attività personalizzata consente di caricare il file eseguibile e le dipendenze in folderpath ed esegue il comando per l'utente. 

  I servizi collegati, i set di dati definiti in referenceObjects e le proprietà Extended definite nel payload JSON dell'attività personalizzata sono accessibili dal file eseguibile come file JSON. È possibile accedere alle proprietà necessarie tramite un serializzatore JSON come illustrato nell'esempio di codice precedente SampleApp.exe. 

  Con le modifiche introdotte nell'attività personalizzata di Azure Data Factory versione 2, è possibile scrivere la logica del codice personalizzato nel linguaggio preferito ed eseguirlo nei sistemi operativi di Windows e Linux supportati da Azure Batch. 

  Nella tabella seguente vengono descritte le differenze tra l'attività personalizzata di Data Factory V2 e l'attività DotNet (personalizzata) di Data Factory V1: 


|Differenze      |Attività personalizzata versione 2      | Attività DotNet (personalizzata) versione 1      |
| ---- | ---- | ---- |
|Modalità di definizione della logica personalizzata      |Tramite l'esecuzione di qualsiasi file eseguibile (esistente o mediante l'implementazione del file eseguibile personalizzato)      |Mediante l'implementazione di un file DLL .Net      |
|Ambiente di esecuzione della logica personalizzata      |Windows o Linux      |Windows (.Net Framework 4.5.2)      |
|Esecuzione di script      |Supporto dell'esecuzione diretta di script, ad esempio "cmd /c echo hello world" sulla macchina virtuale di Windows      |Richiede l'implementazione nel file DLL .Net      |
|Set di dati obbligatorio      |Facoltativo      |Richiesta per concatenare le attività e passare le informazioni      |
|Passare le informazioni dall'attività alla logica personalizzata      |Tramite ReferenceObjects (LinkedServices e Datasets) ed ExtendedProperties (proprietà personalizzate) e      |Tramite ExtendedProperties (proprietà personalizzate), set di dati di input e output      |
|Recuperare le informazioni nella logica personalizzata      |Analizzare i file activity.json, linkedServices.json e datasets.json archiviati nella stessa cartella del file eseguibile      |Tramite .Net SDK (.Net Frame 4.5.2)      |
|Registrazione      |Scrive direttamente in STDOUT      |Implementazione del logger nel file DLL .Net      |


  Se si dispone di un codice .Net esistente scritto per la versione 1 dell'attività DotNet (personalizzata), è necessario modificarlo per consentirne il funzionamento con l'attività personalizzata versione 2 con le linee guida di alto livello seguenti:  

   - Modificare il progetto da una libreria di classi .NET a un'app console. 
   - Avviare l'applicazione con il metodo Main, il metodo Execute dell'interfaccia IDotNetActivity non è più necessario. 
   - Leggere e analizzare i servizi collegati, i set di dati e le attività con il serializzatore JSON invece come oggetti tipizzati sicuri e passare i valori delle proprietà necessarie alla logica principale del codice personalizzato. Fare riferimento all'esempio di codice precedente SampleApp.exe. 
   - L'oggetto logger non è più supportato, gli output dei file eseguibili possono essere stampati nella console e salvati in stdout.txt. 
   - Il pacchetto NuGet Microsoft.Azure.Management.DataFactories non è più necessario. 
   - Compilare il codice, caricare il file eseguibile e le dipendenze in Archiviazione di Azure e definire il percorso nella proprietà folderPath. 

Per un esempio completo di come è possibile riscrivere l'esempio end-to-end DLL e pipeline descritto nel documento di Data Factory versione 1 [Usare attività personalizzate in una pipeline di Azure Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) nel formato dell'attività personalizzata di Data Factory versione 2, vedere l'[esempio di attività personalizzata di Data Factory versione 2](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFv2CustomActivitySample). 

## <a name="auto-scaling-of-azure-batch"></a>Scalabilità automatica di Azure Batch
È anche possibile creare un pool di Azure Batch con la funzionalità **Scalabilità automatica** . Ad esempio, è possibile creare un pool di Azure Batch con 0 VM dedicate e una formula di scalabilità basata sul numero di attività in sospeso. 

La formula di esempio seguente consente di ottenere il comportamento seguente: quando il pool viene creato inizialmente, inizia con 1 macchina virtuale. La metrica $PendingTasks definisce il numero di attività in esecuzione e quelle in coda.  La formula trova il numero medio di attività in sospeso negli ultimi 180 secondi e imposta TargetDedicated di conseguenza. Assicura che TargetDedicated non vada mai oltre 25 macchine virtuali. Pertanto, quando vengono inviate nuove attività, il pool si espande automaticamente e al completamento delle attività le macchine virtuali diventano disponibili una alla volta e la scalabilità automatica le riduce. È possibile regolare startingNumberOfVMs e maxNumberofVMs in base alle esigenze.

Formula di scalabilità automatica:

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Per i dettagli, vedere [Ridimensionare automaticamente i nodi di calcolo in un pool di Azure Batch](../batch/batch-automatic-scaling.md) .

Se il pool usa il valore predefinito [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), possono essere necessari 15-30 minuti perché il servizio Batch prepari la VM prima di eseguire l'attività personalizzata.  Se il pool usa un valore autoScaleEvaluationInterval diverso, il servizio Batch può richiedere un valore autoScaleEvaluationInterval + 10 minuti.


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti, che illustrano altre modalità di trasformazione dei dati: 

* [Attività U-SQL](transform-data-using-data-lake-analytics.md)
* [Attività Hive](transform-data-using-hadoop-hive.md)
* [Attività Pig](transform-data-using-hadoop-pig.md)
* [Attività MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Attività di streaming di Hadoop](transform-data-using-hadoop-streaming.md)
* [Attività Spark](transform-data-using-spark.md)
* [Machine Learning Bach Execution Activity](transform-data-using-machine-learning.md) (Attività di esecuzione batch di Machine Learning)
* [Attività stored procedure](transform-data-using-stored-procedure.md)
