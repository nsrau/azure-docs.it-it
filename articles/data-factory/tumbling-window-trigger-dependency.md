---
title: Creare dipendenze trigger di finestra a cascataCreate tumbling window trigger dependencies
description: Informazioni su come creare dipendenze del trigger di finestra a cascata in Azure Data Factory.
services: data-factory
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/29/2019
ms.openlocfilehash: 0557c9b9eb65654c4a11c1389ace4776ab60a61d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532571"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Creare una dipendenza del trigger di finestra a cascata

Questo articolo descrive la procedura per creare una dipendenza del trigger di finestra a cascata. Per informazioni più specifiche ed esempi sui trigger di finestra a cascata, vedere [Procedura per creare un trigger di finestra a cascata](how-to-create-tumbling-window-trigger.md).

Per creare una catena di dipendenze e accertarsi che un trigger venga eseguito solo dopo la corretta esecuzione di un altro trigger nella data factory, usare questa funzionalità avanzata per creare una dipendenza in una finestra a cascata.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Creare una dipendenza nell'interfaccia utente di Data Factory

Per creare dipendenze di un trigger, selezionare **Trigger > Avanzate > Nuovo**, quindi scegliere il trigger da cui dipendere con le dimensioni e gli scostamenti appropriati. Selezionare **Fine** e pubblicare le modifiche alla data factory per attivare le dipendenze.

![Creazione di dipendenzeDependency Creation](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "Creazione di dipendenzeDependency Creation")

## <a name="tumbling-window-dependency-properties"></a>Proprietà di una dipendenza in una finestra a cascata

Un trigger di finestra a cascata con una dipendenza ha le proprietà seguenti:A tumbling window trigger with a dependency has the following properties:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

La tabella seguente fornisce l'elenco degli attributi necessari per definire una dipendenza in una finestra a cascata.

| **Nome proprietà** | **Descrizione**  | **Tipo** | **Obbligatorio** |
|---|---|---|---|
| type  | Tutti i trigger presenti in una finestra a cascata vengono visualizzati nell'elenco a tendina. Scegliere il trigger da cui dipendere.  | TumblingWindowTriggerDependencyReference o SelfDependencyTumblingWindowTriggerReference | Sì |
| offset | Scostamento del trigger delle dipendenze. Fornire un valore in formato intervallo di tempo e sono consentiti offset negativi e positivi. Questa proprietà è obbligatoria se il trigger dipende da se stesso e in tutti gli altri casi è facoltativo. L'auto-dipendenza deve avere sempre uno scostamento negativo. Se non viene specificato alcun valore, la finestra corrisponde al trigger stesso. | TimeSpan<br/>(hh:mm:ss) | Autodipendenza: Sì<br/>Altro: No |
| size | Dimensione della finestra a cascata di dipendenze. Fornire un valore di intervallo di tempo positivo. Questa proprietà è facoltativa. | TimeSpan<br/>(hh:mm:ss) | No  |

> [!NOTE]
> Un trigger di finestra a cascata può dipendere da un massimo di altri due trigger.

## <a name="tumbling-window-self-dependency-properties"></a>Proprietà di auto-dipendenza in una finestra a cascata

Negli scenari in cui il trigger non deve passare alla finestra successiva fino al completamento della finestra precedente, creare un'autodipendenza. Un trigger di auto-dipendenza che dipende dal successo delle esecuzioni precedenti di se stesso all'interno dell'ora precedente avrà le seguenti proprietà:

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>Scenari ed esempi di utilizzo

Di seguito sono riportate le illustrazioni degli scenari e l'utilizzo delle proprietà di dipendenza delle finestre a cascata.

### <a name="dependency-offset"></a>Scostamento della dipendenza

![Esempio offset](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Esempio offset")

### <a name="dependency-size"></a>Dimensione della dipendenza

![Esempio di dimensione](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "Esempio di dimensione")

### <a name="self-dependency"></a>Auto-dipendenza

![Autodipendenza](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "Auto-dipendenza")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Dipendenza di un altro trigger in una finestra a cascata

Un processo di elaborazione di telemetria giornaliero che dipende da un altro processo giornaliero che aggrega l'output degli ultimi sette giorni e genera flussi di finestra in sequenza di sette giorni:A daily telemetry processing job depending on another daily job aggregating the last seven days output and generates seven day rolling window streams:

![Esempio di dipendenza](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "Esempio di dipendenza")

### <a name="dependency-on-itself"></a>Dipendenza su se stesso

Un processo giornaliero senza interruzioni nei flussi di output del processo:

![Esempio di auto-dipendenza](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "Esempio di auto-dipendenza")

Per una dimostrazione su come creare pipeline dipendenti in Azure Data Factory usando il trigger di finestra a cascata, guardare il video seguente:For a demonstration on how to create dependent pipelines in your Azure Data Factory using tumbling window trigger, watch the following video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="monitor-dependencies"></a>Monitorare le dipendenze

È possibile monitorare la catena di dipendenze e le finestre corrispondenti dalla pagina di monitoraggio dell'esecuzione del trigger. Navigare alla voce **Monitoraggio > Esecuzioni di trigger**. Nella colonna Azioni è possibile eseguire nuovamente il trigger o visualizzarne le dipendenze.

![Monitorare le esecuzioni del trigger](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "Monitorare le esecuzioni del trigger")

Se si fa clic su 'Visualizza dipendenze trigger', è possibile visualizzare lo stato delle dipendenze. Se uno dei trigger di dipendenza ha esito negativo, è necessario rieseguirlo correttamente per poter eseguire il trigger dipendente. Un trigger di finestra a cascata attenderà le dipendenze per sette giorni prima del timeout.

![Monitorare le dipendenze](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "Monitorare le dipendenze")

Per un maggiore oggetto visivo per visualizzare la pianificazione delle dipendenze trigger, selezionare la visualizzazione Gantt.

![Monitorare le dipendenze](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "Monitorare le dipendenze")

## <a name="next-steps"></a>Passaggi successivi

* Recensione [Come creare un trigger di finestra a cascata](how-to-create-tumbling-window-trigger.md)
