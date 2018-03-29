---
title: Creare trigger di finestra a cascata in Azure Data Factory | Microsoft Docs
description: Informazioni su come creare un trigger in Azure Data Factory per l'esecuzione di una pipeline in una finestra a cascata.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: shlo
ms.openlocfilehash: 312072a5de21ff1c6b602fed93b77c564b15a9f1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Creare un trigger per l'esecuzione di una pipeline in una finestra a cascata
Questo articolo descrive la procedura per creare, avviare e monitorare un trigger di finestra a cascata. Per informazioni generali sui trigger e i tipi supportati, vedere [Esecuzione e trigger di pipeline](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Questo articolo è applicabile ad Azure Data Factory versione 2, attualmente in versione di anteprima. Se si usa Azure Data Factory versione 1, disponibile a livello generale, vedere [Introduzione ad Azure Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

I trigger di finestra a cascata vengono attivati in base a un intervallo di tempo periodico a partire da un'ora di inizio specificata, mantenendo al tempo stesso lo stato. Le finestre a cascata sono costituite da una serie di intervalli temporali di dimensioni fisse, contigui e non sovrapposti. Un trigger di finestra a cascata ha una relazione uno a uno con una pipeline e può fare riferimento solo a una singola pipeline.

## <a name="tumbling-window-trigger-type-properties"></a>Proprietà del tipo di trigger di finestra a cascata
Una finestra a cascata ha le proprietà del tipo di trigger seguenti:

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

La tabella seguente fornisce una panoramica generale degli elementi JSON principali correlati alla ricorrenza e alla pianificazione di un trigger di finestra a cascata:

| Elemento JSON | DESCRIZIONE | type | Valori consentiti | Obbligatoria |
|:--- |:--- |:--- |:--- |:--- |
| **type** | Tipo di trigger. Il tipo è il valore fisso "TumblingWindowTrigger". | string | "TumblingWindowTrigger" | Sì |
| **runtimeState** | Stato attuale del runtime del trigger.<br/>**Nota**: questo elemento è \<readOnly>. | string | "Started", "Stopped", "Disabled" | Sì |
| **frequency** | Stringa che rappresenta l'unità di frequenza (minuti o ore) con cui si ripete il trigger. Se i valori di data di **startTime** sono più granulari del valore di **frequency**, le date di **startTime** sono considerate quando vengono calcolati i limiti della finestra. Ad esempio, se il valore di **frequency** è ogni ora e il valore di **startTime** è 2016-04-01T10:10:10Z, la prima finestra è (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z). | string | "minute", "hour"  | Sì |
| **interval** | Numero intero positivo indicante l'intervallo per il valore **frequency**, che determina la frequenza con cui viene eseguito il trigger. Se, ad esempio, **interval** è 3 e **frequency** è "hour", il trigger si ripete ogni 3 ore. | Integer | Numero intero positivo. | Sì |
| **startTime**| Prima occorrenza, che può essere nel passato. Il primo intervallo di trigger è (**startTime**, **startTime** + **interval**). | Datetime | Valore DateTime. | Sì |
| **endTime**| Ultima occorrenza, che può essere nel passato. | Datetime | Valore DateTime. | Sì |
| **delay** | Periodo di tempo in base a cui ritardare l'avvio dell'elaborazione dei dati per la finestra. L'esecuzione della pipeline viene avviata dopo il tempo di esecuzione previsto più il periodo di **ritardo**. Il valore **delay** definisce per quanto tempo il trigger rimane in attesa dopo la scadenza prima di attivare una nuova esecuzione. Il valore **delay** non modifica il valore **startTime** della finestra. Ad esempio, un valore di **delay** pari a 00:10:00 implica un ritardo di 10 minuti. | TimeSpan  | Valore di ora in cui il valore predefinito è 00:00:00. | No  |
| **maxConcurrency** | Il numero di esecuzioni di trigger simultanee che vengono generate per le finestre che sono pronte. Ad esempio, per recuperare le informazioni relative alle esecuzioni ogni ora per i risultati del giorno prima in 24 finestre. Se **maxConcurrency** = 10, gli eventi di attivazione vengono generati solo per le prime 10 finestre (00:00-01:00 - 09:00-10:00). Al termine delle prime 10 esecuzioni di pipeline attivate, le esecuzioni di trigger vengono generate per le 10 finestre successive (10:00-11:00 - 19:00-20:00). Continuando con questo esempio di **maxConcurrency** = 10, se sono pronte 10 finestre, ci sono 10 esecuzioni di pipeline totali. Se c'è solo una finestra pronta, è disponibile solo un'esecuzione di pipeline. | Integer | Numero intero compreso tra 1 e 50. | Sì |
| **retryPolicy: Count** | Numero di tentativi prima che l'esecuzione della pipeline venga contrassegnata come "Non riuscita".  | Integer | Numero intero, in cui il valore predefinito è 0 (nessun tentativo). | No  |
| **retryPolicy: intervalInSeconds** | Ritardo tra i tentativi di ripetizione specificato in secondi. | Integer | Numero di secondi, in cui il valore predefinito è 30. | No  |

### <a name="windowstart-and-windowend-system-variables"></a>Variabili di sistema WindowStart e WindowEnd

È possibile usare le variabili di sistema **WindowStart** e **WindowEnd** del trigger di finestra a cascata nella definizione della **pipeline** (ovvero per una parte di una query). Passare le variabili di sistema come parametri alla pipeline nella definizione del **trigger**. L'esempio seguente mostra come passare tali variabili come parametri:

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

Per usare i valori delle variabili di sistema **WindowStart** e **WindowEnd** nella definizione della pipeline, usare i parametri "MyWindowStart" e "MyWindowEnd" di conseguenza.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Ordine di esecuzione delle finestre in uno scenario di recupero delle informazioni
Quando sono presenti più finestre pronte per l'esecuzione (soprattutto in uno scenario di recupero delle informazioni), l'ordine di esecuzione per le finestre è deterministico, dagli intervalli meno recenti ai più recenti. Attualmente non è possibile modificare questo comportamento.

### <a name="existing-triggerresource-elements"></a>Elementi TriggerResource esistenti
I punti elencati di seguito si applicano agli elementi **TriggerResource** esistenti:

* Se il valore per l'elemento **frequency** (o le dimensioni della finestra) del trigger viene modificato, lo stato delle finestre che sono già elaborate *non* viene reimpostato. Il trigger continua ad attivarsi per le finestre dall'ultima finestra eseguita usando le nuove dimensioni della finestra.
* Se il valore per l'elemento **endTime** del trigger viene modificato (aggiunto o aggiornato), lo stato delle finestre che sono già elaborate *non* viene reimpostato. Il trigger rispetta il nuovo valore **endTime**. Se il nuovo valore **endTime** è precedente alle finestre già eseguite, il trigger si interrompe. In caso contrario, il trigger si interrompe quando viene rilevato il nuovo valore **endTime**.

## <a name="sample-for-azure-powershell"></a>Esempio per Azure PowerShell
Questa sezione illustra come usare Azure PowerShell per creare, avviare e monitorare un trigger.

1. Creare un file JSON denominato **MyTrigger.json** nella cartella C:\ADFv2QuickStartPSH con il contenuto seguente:

   > [!IMPORTANT]
   > Prima di salvare il file JSON, impostare il valore dell'elemento **startTime** sull'ora UTC corrente. Impostare il valore dell'elemento **endTime** su un'ora dopo l'ora UTC corrente.

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

2. Creare un trigger usando il cmdlet **Start-AzureRmDataFactoryV2Trigger**:

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Confermare che lo stato del trigger viene **interrotto** usando il cmdlet **Get-AzureRmDataFactoryV2Trigger**:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Avviare il trigger usando il cmdlet **Start-AzureRmDataFactoryV2Trigger**:

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Confermare che lo stato del trigger viene **avviato** usando il cmdlet **Get-AzureRmDataFactoryV2Trigger**:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Ottenere le esecuzioni di trigger in Azure PowerShell usando il cmdlet **Get AzureRmDataFactoryV2TriggerRun**. Per ottenere informazioni sulle esecuzioni di trigger, eseguire periodicamente il comando seguente. Aggiornare i valori di **TriggerRunStartedAfter** e **TriggerRunStartedBefore** in modo che corrispondano a quelli nella definizione del trigger:

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Per monitorare le esecuzioni di trigger e pipeline nel portale di Azure, vedere [Monitorare le esecuzioni di pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate sui trigger, vedere [Esecuzione e trigger di pipeline](concepts-pipeline-execution-triggers.md#triggers).
