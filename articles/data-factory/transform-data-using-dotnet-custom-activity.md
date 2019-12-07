---
title: Usare attività personalizzate in una pipeline
description: Informazioni su come creare attività personalizzate e usarle in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/26/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: d922100d2747822d5683c0f65b169327c2866318
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893876"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Usare attività personalizzate in una pipeline di Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-use-custom-activities.md)
> * [Versione corrente](transform-data-using-dotnet-custom-activity.md)

In una pipeline di Azure Data Factory è possibile usare due tipi di attività.

- [Attività di spostamento dei dati](copy-activity-overview.md) per spostare dati tra [archivi dati supportati di origine e sink](copy-activity-overview.md#supported-data-stores-and-formats).
- [Attività di trasformazione dei dati](transform-data.md) per trasformare i dati usando servizi di calcolo come Azure HDInsight, Azure Batch e Azure Machine Learning.

Per spostare dati da o verso un archivio dati non supportato da Data Factory oppure per trasformare o elaborare dati in un modo non supportato da Data Factory, è possibile creare un'**attività personalizzata** contenente la logica di spostamento o trasformazione dei dati necessaria e usare tale attività in una pipeline. L'attività personalizzata esegue la logica del codice personalizzata in un pool di **Azure Batch** di macchine virtuali.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vedere gli articoli seguenti se non si ha familiarità con il servizio Azure Batch:

* [Nozioni di base di Azure Batch](../batch/batch-technical-overview.md) per una panoramica del servizio Azure Batch.
* Cmdlet [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) per creare un account di Azure batch (o) [portale di Azure](../batch/batch-account-create-portal.md) per creare l'account Azure batch usando portale di Azure. Per istruzioni dettagliate sull'uso del cmdlet, vedere l'articolo [Uso di Azure PowerShell per gestire l'account di Azure Batch](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
* Cmdlet [New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) per creare un pool di Azure batch.

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

| Proprietà              | Description                              | Obbligatoria |
| :-------------------- | :--------------------------------------- | :------- |
| name                  | Nome dell'attività nella pipeline     | SÌ      |
| description           | Testo che descrive l'attività.  | No       |
| type                  | Per l'attività personalizzata, il tipo corrisponde a **Custom**. | SÌ      |
| linkedServiceName     | Servizio collegato ad Azure Batch. Per informazioni su questo servizio collegato, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md).  | SÌ      |
| command               | Comando dell'applicazione personalizzata da eseguire. Se l'applicazione è già disponibile nel nodo del pool di Azure Batch, è possibile ignorare resourceLinkedService e folderPath. È ad esempio possibile specificare come comando `cmd /c dir`, supportato in modo nativo dal nodo del pool di batch di Windows. | SÌ      |
| resourceLinkedService | Servizio di Archiviazione di Azure collegato all'account di archiviazione in cui è archiviata l'applicazione personalizzata | No &#42;       |
| folderPath            | Percorso della cartella dell'applicazione personalizzata e di tutte le relative dipendenze<br/><br/>Se sono presenti dipendenze archiviate nelle sottocartelle, vale a dire, in una struttura di cartelle gerarchiche in *folderPath*, la struttura di cartelle è attualmente di tipo flat quando i file vengono copiati in Azure Batch. Vale a dire, tutti i file vengono copiati in un'unica cartella senza sottocartelle. Per risolvere questo comportamento, è possibile comprimere i file, copiare il file compresso e quindi decomprimerlo con codice personalizzato nel percorso desiderato. | No &#42;       |
| referenceObjects      | Matrice di servizi collegati e set di dati esistenti. I servizi collegati e i set di dati a cui si fa riferimento vengono passati all'applicazione personalizzata in formato JSON. Il codice personalizzato può quindi fare riferimento a risorse di Data Factory | No       |
| extendedProperties    | Proprietà definite dall'utente che possono essere passate all'applicazione personalizzata in formato JSON. Il codice personalizzato può quindi fare riferimento a proprietà aggiuntive | No       |
| retentionTimeInDays | Tempo di conservazione per i file inviati per l'attività personalizzata. Il valore predefinito è 30 giorni. | No |

&#42; Le proprietà `resourceLinkedService` e `folderPath` devono essere specificate oppure omesse entrambe.

> [!NOTE]
> Se si passano servizi collegati come referenceObjects nell'attività personalizzata, è consigliabile passare un servizio collegato Azure Key Vault abilitato (poiché non contiene stringhe sicure) e recuperare le credenziali usando il nome del segreto direttamente dalla chiave Insieme di credenziali dal codice. È possibile trovare [un esempio che](https://github.com/nabhishek/customactivity_sample/tree/linkedservice) fa riferimento al servizio collegato abilitato per AKV, recupera le credenziali da Key Vault e quindi accede all'archiviazione nel codice.

## <a name="custom-activity-permissions"></a>Autorizzazioni per le attività personalizzate

L'attività personalizzata imposta l'account utente automatico di Azure Batch sull'*accesso senza privilegi di amministratore con ambito di attività* (specifica di utente automatico predefinito). Non è possibile modificare il livello di autorizzazione dell'account utente automatico. Per altre informazioni, vedere [Eseguire attività con account utente in Batch | Account utente automatici](../batch/batch-user-accounts.md#auto-user-accounts).

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

- `activity.json`

  Contiene extendedProperties e le proprietà dell'attività personalizzata.

- `linkedServices.json`

  Contiene una matrice di servizi collegati definiti nella proprietà referenceObjects.

- `datasets.json`

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
            Console.WriteLine(linkedServices[0].properties.typeProperties.accountName);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>Recuperare gli output di esecuzione

Per avviare una pipeline, eseguire il comando di PowerShell seguente:

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
```

Quando la pipeline è in esecuzione, è possibile controllare l'output dell'esecuzione usando i comandi seguenti:

```powershell
while ($True) {
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

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

```
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
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
]
"effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
Activity Error section:
"errorCode": ""
"message": ""
"failureType": ""
"target": "MyCustomActivity"
```

Se si desidera usare il contenuto di stdout.txt nelle attività downstream, è possibile ottenere il percorso del file stdout.txt nell'espressione "\@activity('MyCustomActivity').output.outputs[0]".

> [!IMPORTANT]
> - Activity.json, linkedServices.json e datasets.json vengono archiviati nella cartella di runtime dell'attività Batch. Per questo esempio, i file Activity. JSON, linkedServices. JSON e DataSets. JSON vengono archiviati nel percorso `"https://adfv2storage.blob.core.windows.net/adfjobs/\<GUID>/runtime/"`. Se necessario, la pulizia di questi file deve essere eseguita separatamente.
> - Per i servizi collegati che usano il runtime di integrazione self-hosted, le informazioni riservate, come chiavi o password, vengono crittografate dal runtime di integrazione self-hosted per verificare che le credenziali rimangano nell'ambiente di rete privata definito dal cliente. Alcuni campi riservati potrebbero risultare mancanti se il codice dell'applicazione personalizzata fa riferimento a tali campi in questo modo. Se necessario, usare SecureString in extendedProperties anziché un riferimento a servizi collegati.

## <a name="pass-outputs-to-another-activity"></a>Passare gli output a un'altra attività

È possibile inviare valori personalizzati dal codice di un'attività personalizzata ad Azure Data Factory. È possibile farlo scrivendoli in `outputs.json` dall'applicazione. Data Factory copia il contenuto di `outputs.json` e lo accoda all'output dell'attività come valore della proprietà `customOutput`. Il limite delle dimensioni è 2MB. Se si desidera utilizzare il contenuto di `outputs.json` nelle attività downstream, è possibile ottenere il valore utilizzando l'espressione `@activity('<MyCustomActivity>').output.customOutput`.

## <a name="retrieve-securestring-outputs"></a>Recuperare gli output SecureString

I valori delle proprietà sensibili designati come tipo *SecureString*, come illustrato in alcuni degli esempi in questo articolo, vengono mascherati nella scheda Monitoraggio nell'interfaccia utente di Data Factory.  Nell'esecuzione effettiva della pipeline, tuttavia, una proprietà *SecureString* viene serializzata come JSON all'interno del `activity.json` file come testo normale. ad esempio:

```json
"extendedProperties": {
  "connectionString": {
    "type": "SecureString",
    "value": "aSampleSecureString"
  }
}
```

Questa serializzazione non è propriamente sicura e non è progettata per esserlo. La finalità è quella di suggerire ad Azure Data Factory di mascherare il valore nella scheda Monitoraggio.

Per accedere a proprietà di tipo *SecureString* da un'attività personalizzata, leggere il file `activity.json` che viene inserito nella stessa cartella di .EXE, deserializzare JSON, quindi accedere alla proprietà JSON (extendedProperties => [propertyName] => value).

## <a name="compare-v2-v1"></a> Confrontare l'attività personalizzata della versione 2 e l'attività DotNet (personalizzata) della versione 1

In Azure Data Factory versione 1 si implementa un'attività DotNet (personalizzata) creando un progetto libreria di classi .NET con una classe che implementa il metodo `Execute` dell'interfaccia `IDotNetActivity`. I servizi collegati, i set di dati e le proprietà estese nel payload JSON di un'attività DotNet (personalizzata) vengono passati al metodo di esecuzione come oggetti fortemente tipizzati. Per informazioni dettagliate sul comportamento della versione 1, vedere [Attività DotNet (personalizzate) nella versione 1](v1/data-factory-use-custom-activities.md). A causa di questa implementazione, il codice dell'attività DotNet della versione 1 deve avere come destinazione .NET Framework 4.5.2. L'attività DotNet della versione 1 deve inoltre essere eseguita sui nodi di pool di Azure Batch basati su Windows.

Nell'attività personalizzata Azure Data Factory V2 non è necessario implementare un'interfaccia .NET. È ora possibile eseguire direttamente comandi, script e codice personalizzato, compilato come eseguibile. Per configurare questa implementazione, specificare la proprietà `Command` con la proprietà `folderPath`. L'attività personalizzata carica il file eseguibile e le relative dipendenze in `folderpath` ed esegue il comando automaticamente.

I servizi collegati, i set di dati definiti in referenceObjects e le proprietà estese definite nel payload JSON di un'attività personalizzata di Data Factory versione 2 sono accessibili dal file eseguibile come file JSON. È possibile accedere alle proprietà obbligatorie tramite un serializzatore JSON, come illustrato nell'esempio di codice precedente SampleApp.exe.

Con le modifiche introdotte nell'attività personalizzata di Data Factory versione 2, è possibile scrivere la logica del codice personalizzata nel linguaggio preferito ed eseguirla nei sistemi operativi Windows e Linux supportati da Azure Batch.

La tabella seguente illustra le differenze tra l'attività personalizzata di Data Factory versione 2 e l'attività DotNet (personalizzata) di Data Factory versione 1:

|Differenze      | Attività personalizzata      | Attività DotNet (personalizzata) versione 1      |
| ---- | ---- | ---- |
|Modalità di definizione della logica personalizzata      |Mediante l'uso di un file eseguibile      |Implementando una DLL .NET      |
|Ambiente di esecuzione della logica personalizzata      |Windows o Linux      |Windows (.NET Framework 4.5.2)      |
|Esecuzione di script      |Supporta l'esecuzione diretta di script, ad esempio "cmd /c echo hello world" su macchine virtuali Windows      |Richiede l'implementazione nella DLL .NET      |
|Set di dati obbligatorio      |Facoltativo      |Richiesta per concatenare le attività e passare le informazioni      |
|Passare le informazioni dall'attività alla logica personalizzata      |Tramite ReferenceObjects (LinkedServices e Datasets) ed ExtendedProperties (proprietà personalizzate)      |Tramite ExtendedProperties (proprietà personalizzate), set di dati di input e output      |
|Recuperare le informazioni nella logica personalizzata      |Analizza i file activity.json, linkedServices.json e datasets.json archiviati nella stessa cartella del file eseguibile      |Tramite .NET SDK (.NET frame 4.5.2)      |
|Registrazione      |Scrive direttamente in STDOUT      |Implementazione del logger nella DLL .NET      |

Se si dispone di codice .NET esistente scritto per un'attività DotNet della versione 1 (personalizzata), è necessario modificare il codice affinché funzioni con la versione corrente dell'attività personalizzata. Aggiornare il codice seguendo queste linee guida generali:

  - Modificare il progetto da una libreria di classi .NET a un'app console.
  - Avviare l'applicazione con il metodo `Main`. Il metodo `Execute` dell'interfaccia `IDotNetActivity` non è più necessario.
  - Leggere e analizzare i servizi collegati, i set di dati e l'attività con un serializzatore JSON e non come oggetti fortemente tipizzati. Passare i valori delle proprietà obbligatorie alla logica del codice personalizzata principale. Fare riferimento all'esempio di codice precedente SampleApp.exe.
  - L'oggetto Logger non è più supportato. L'output del file eseguibile può essere stampato nella console e viene salvato in stdout.txt.
  - Il pacchetto NuGet Microsoft.Azure.Management.DataFactories non è più necessario.
  - Compilare il codice, caricare il file eseguibile e le relative dipendenze in Archiviazione di Azure e definire il percorso nella proprietà `folderPath`.

Per un'illustrazione completa di come l'esempio end-to-end di DLL e pipeline, descritto nell'articolo relativo a Data Factory versione 1 [Usare attività personalizzate in una pipeline di Azure Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities), può essere riscritto come attività personalizzata di Data Factory, vedere l'[esempio di attività personalizzata di Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFv2CustomActivitySample).

## <a name="auto-scaling-of-azure-batch"></a>Scalabilità automatica di Azure Batch

È anche possibile creare un pool di Azure Batch con la funzionalità **Scalabilità automatica** . Ad esempio, è possibile creare un pool di Azure Batch con 0 VM dedicate e una formula di scalabilità basata sul numero di attività in sospeso.

Di seguito la formula di esempio consente di ottenere il comportamento seguente: quando il pool viene creato inizialmente, inizia con 1 macchina virtuale. La metrica $PendingTasks definisce il numero di attività in esecuzione e quelle in coda. La formula trova il numero medio di attività in sospeso negli ultimi 180 secondi e imposta TargetDedicated di conseguenza. Assicura che TargetDedicated non vada mai oltre 25 macchine virtuali. Pertanto, quando vengono inviate nuove attività, il pool si espande automaticamente e al completamento delle attività le macchine virtuali diventano disponibili una alla volta e la scalabilità automatica le riduce. È possibile regolare startingNumberOfVMs e maxNumberofVMs in base alle esigenze.

Formula di scalabilità automatica:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Per i dettagli, vedere [Ridimensionare automaticamente i nodi di calcolo in un pool di Azure Batch](../batch/batch-automatic-scaling.md) .

Se il pool usa il valore predefinito [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), possono essere necessari 15-30 minuti perché il servizio Batch prepari la VM prima di eseguire l'attività personalizzata. Se il pool usa un valore autoScaleEvaluationInterval diverso, il servizio Batch può richiedere un valore autoScaleEvaluationInterval + 10 minuti.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti che illustrano come trasformare i dati in altri modi:

* [Attività U-SQL](transform-data-using-data-lake-analytics.md)
* [Attività Hive](transform-data-using-hadoop-hive.md)
* [Attività Pig](transform-data-using-hadoop-pig.md)
* [Attività MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Attività di streaming di Hadoop](transform-data-using-hadoop-streaming.md)
* [Attività Spark](transform-data-using-spark.md)
* [Machine Learning Bach Execution Activity](transform-data-using-machine-learning.md) (Attività di esecuzione batch di Machine Learning)
* [Attività stored procedure](transform-data-using-stored-procedure.md)
