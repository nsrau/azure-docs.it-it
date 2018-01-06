---
title: Come creare trigger finestra a cascata in Data Factory di Azure | Documenti Microsoft
description: Informazioni su come creare un trigger in Azure Data Factory in cui viene eseguita una pipeline in una finestra a cascata.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: shlo
ms.openlocfilehash: a3b056ae4bb4eda26fec58ca3b6bed7f0744e36e
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Come creare un trigger che viene eseguita una pipeline in una finestra a cascata
In questo articolo viene descritta la procedura per creare, avviare e monitorare un trigger di finestra a cascata. Per informazioni generali su trigger e i tipi sono supportati, vedere [della Pipeline di esecuzione e i trigger](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere l'[introduzione a Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

I trigger di finestra a cascata sono un tipo di trigger che viene attivato in un intervallo di tempo periodico da un'ora di inizio specificato, pur mantenendo lo stato. Finestre a cascata sono una serie di intervalli di tempo a dimensione fissa, non sovrapposte e contigue. Un trigger di finestra a cascata ha una relazione 1:1 con una pipeline e può fare riferimento solo una pipeline singolare.

## <a name="tumbling-window-trigger-type-properties"></a>A cascata finestra proprietà del tipo di Trigger
```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": "<<Minute/Hour>>",
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: "<<datetime – optional>>"",
            "delay": "<<timespan – optional>>",
            “maxConcurrency”: <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count":  <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
            }
        },
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "parameter1": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter2": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
                }
            }
      }    
}
```  

Nella tabella seguente fornisce una panoramica degli elementi principali relativi a ricorrenza e la pianificazione in un trigger di finestra a cascata.

| **Nome JSON** | **Descrizione** | **Valori consentiti** | **Obbligatorio** |
|:--- |:--- |:--- |:--- |
| **type** | Tipo di trigger. Questo problema viene risolto come "TumblingWindowTrigger". | string | Sì |
| **runtimeState** | <readOnly>I valori possibili: Avviato, arrestato, disabilitato | string | Sì, sola lettura |
| **frequency** |Il *frequenza* stringa che rappresenta l'unità della frequenza con cui ricorre il trigger. Valori supportati sono "minuto" e "ora". Se l'ora di inizio include parti di date più granulare rispetto alla frequenza, essi verranno eseguite in considerazione per calcolare i limiti della finestra. Per esempio: se la frequenza è ogni ora e l'ora di inizio è 2016-04-01T10:10:10Z, la prima finestra è (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z.)  | Stringa. Tipi supportati "minuto", "ora" | Sì |
| **interval** |Il *intervallo* un numero intero positivo e indica l'intervallo per il *frequenza* che determina la frequenza con cui verrà eseguito il trigger. Ad esempio, se *intervallo* è 3 e *frequenza* è "ora", il trigger ricorre ogni 3 ore. | Integer | Sì |
| **startTime**|*startTime* è in formato Data-Ora. *startTime* è la prima occorrenza e può essere nel passato. Primo intervallo di trigger sarà (startTime, startTime + intervallo). | Datetime | Sì |
| **endTime**|*endTime* è una data e ora. *endTime* è l'ultima occorrenza e può essere nel passato. | Datetime | Sì |
| **ritardo** | Specificare il ritardo prima dell'elaborazione di dati di avvio della finestra. La pipeline di esecuzione viene avviata dopo il tempo di esecuzione previsto + ritardo. Ritardo definisce come tempo di attesa scaduto il trigger tempo prima che venga attivato eseguire di nuovo. Non modifica l'ora di inizio finestra. | TimeSpan (esempio: 00:10:00, implica ritardo di 10 minuti) |  di serie Valore predefinito è "00: 00:00" |
| **concorrenza massima** | Numero di esecuzioni simultanee di trigger che vengono generati per windows che sono pronti. Esempio: se si sta tentando di recupero ogni ora per ieri, sarebbe 24 windows. Se la concorrenza = 10, gli eventi vengono attivati solo per 10 prima windows di trigger (00:00:01: 00: 09:00:10: 00). Al termine dell'esecuzione della pipeline attivate primi 10, esecuzione di trigger vengono attivati per i successivi 10 (10:00-11:00-20-19:00:00). Continuando con l'esempio di concorrenza = 10, se sono presenti 10 pronto, saranno presenti 10 esecuzioni di pipeline di windows. Se sono presenti solo 1 finestra pronto, solo esisterà 1 esecuzione della pipeline. | Integer | Sì. Valori possibili 1-50 |
| **retryPolicy: Count** | Il numero di tentativi prima dell'esecuzione della pipeline è contrassegnato come "Failed"  | Integer |  di serie Il valore predefinito è 0 tentativi |
| **retryPolicy: intervalInSeconds** | Il ritardo tra tentativi di ripetizione espresso in secondi | Integer |  di serie Il valore predefinito è 30 secondi |

### <a name="using-system-variables-windowstart-and-windowend"></a>Utilizzo di variabili di sistema: WindowStart e WindowEnd

Se si desidera utilizzare WindowStart e WindowEnd del trigger nella finestra a cascata le **pipeline** definizione (ad esempio per la parte di una query), è necessario passare le variabili come parametri per la pipeline di **trigger**definizione, come illustrato di seguito:
```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "MyWindowStart": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "MyWindowEnd": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    }
                }
            }
      }    
}
```  

Quindi, nella definizione di pipeline, per utilizzare i valori WindowStart e WindowEnd, utilizzare i parametri di conseguenza di "MyWindowStart" e "MyWindowEnd"

### <a name="notes-on-backfill"></a>Note sul recupero delle informazioni
Quando sono presenti più finestre per l'esecuzione (esp. in uno scenario di recupero delle informazioni), l'ordine di esecuzione di windows è deterministico e verrà eseguita da intervalli più vecchie fino alle più recenti. Non è possibile modificare questo comportamento al momento.

### <a name="updating-an-existing-triggerresource"></a>L'aggiornamento di un TriggerResource esistente
* Se la frequenza (o dimensioni della finestra) del trigger viene modificata, lo stato di windows è già elaborato verrà *non* essere reimpostato. Il trigger continua generazione per windows a partire dall'ultimo che eseguito utilizzando le nuove dimensioni della finestra.
* Se l'ora di fine del trigger modifiche (aggiunte o aggiornate), lo stato di windows già elaborato verrà *non* essere reimpostato. Il trigger rispetterà semplicemente la nuova ora di fine. Se l'ora di fine è prima di windows già eseguita, il trigger verrà interrotta. In caso contrario, interrompe quando viene rilevata la nuova ora di fine.

## <a name="sample-using-azure-powershell"></a>Esempio di utilizzo di PowerShell di Azure
In questa sezione viene illustrato come usare Azure PowerShell per creare, avviare e monitorare un trigger.

1. Creare un file JSON denominato MyTrigger.json nella cartella C:\ADFv2QuickStartPSH\ con il seguente contenuto:

   > [!IMPORTANT]
   > Impostare **startTime** all'ora UTC corrente e **endTime** a un'ora dopo l'ora UTC corrente ora prima di salvare il file JSON.

    ```json   
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```  
2. Creare un trigger utilizzando il **Set AzureRmDataFactoryV2Trigger** cmdlet.

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    
3. Confirm that the status of the trigger is **Stopped** by using the **Get-AzureRmDataFactoryV2Trigger** cmdlet.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
4. Avviare il trigger utilizzando il **inizio AzureRmDataFactoryV2Trigger** cmdlet:

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
5. Verificare che lo stato del trigger sia **Started** utilizzando il **Get AzureRmDataFactoryV2Trigger** cmdlet.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
6.  Ottenere trigger esegue con PowerShell tramite il **Get AzureRmDataFactoryV2TriggerRun** cmdlet. Per ottenere le informazioni sull'esecuzione di trigger, eseguire periodicamente il comando seguente: aggiornamento **TriggerRunStartedAfter** e **TriggerRunStartedBefore** valori corrispondono ai valori della definizione del trigger .

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
Per monitorare l'esecuzione di trigger/pipeline viene eseguito nel portale di Azure, vedere [esecuzione della pipeline di monitoraggio](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate sui trigger, vedere [della Pipeline di esecuzione e i trigger](concepts-pipeline-execution-triggers.md#triggers).
