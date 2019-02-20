---
title: Creare dipendenze del trigger di finestra a cascata in Azure Data Factory | Microsoft Docs
description: Informazioni su come creare dipendenze del trigger di finestra a cascata in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: douglasl
ms.openlocfilehash: c51c1056869cbd7741fae2ed1a554a7c794d1a39
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966589"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Creare una dipendenza del trigger di finestra a cascata

Questo articolo descrive la procedura per creare una dipendenza del trigger di finestra a cascata. Per informazioni più specifiche ed esempi sui trigger di finestra a cascata, vedere [Procedura per creare un trigger di finestra a cascata](how-to-create-tumbling-window-trigger.md).

Per creare una catena di dipendenze e accertarsi che un trigger venga eseguito solo dopo la corretta esecuzione di un altro trigger nella data factory, usare questa funzionalità avanzata per creare una dipendenza in una finestra a cascata.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Creare una dipendenza nell'interfaccia utente di Data Factory

Per creare dipendenze di un trigger, selezionare **Trigger > Avanzate > Nuovo**, quindi scegliere il trigger da cui dipendere con le dimensioni e gli scostamenti appropriati. Selezionare **Fine** e pubblicare le modifiche alla data factory per attivare le dipendenze.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png)

## <a name="tumbling-window-dependency-properties"></a>Proprietà di una dipendenza in una finestra a cascata

Una dipendenza di un trigger in una finestra a cascata dispone delle proprietà seguenti:

```json
{
    "name": "DemoTrigger",
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
            "startTime": "2018-10-04T00:00:00.000Z",
            "delay": "00:00:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": "-02:00:00",
                    "offset": "-01:00:00",
                    "referenceTrigger": {
                        "referenceName": "DemoDependency1",
                        "type": "TriggerReference"
                    }
                }
            ]
        }
    }
}
```

La tabella seguente fornisce l'elenco degli attributi necessari per definire una dipendenza in una finestra a cascata.

| **Nome proprietà** | **Descrizione**  | **Tipo** | **Obbligatorio** |
|---|---|---|---|
| Trigger  | Tutti i trigger presenti in una finestra a cascata vengono visualizzati nell'elenco a tendina. Scegliere il trigger da cui dipendere.  | TumblingWindowTrigger | Sì |
| Offset | Scostamento del trigger delle dipendenze. Specificare un valore nel formato intervallo di tempo. Sono consentiti gli scostamenti positivi e negativi. Questo parametro è obbligatorio se il trigger dipende da se stesso. Se così non fosse, è facoltativo. L'auto-dipendenza deve avere sempre uno scostamento negativo. | TimeSpan | Auto-dipendenza: Sì altri: No  |
| Dimensioni della finestra | Dimensione della finestra a cascata di dipendenze. Specificare un valore nel formato di intervallo di tempo. Questo parametro è facoltativo e, | TimeSpan | No   |
|||||

## <a name="tumbling-window-self-dependency-properties"></a>Proprietà di auto-dipendenza in una finestra a cascata

Creare una auto-dipendenza nei casi in cui il trigger non deve proseguire alla finestra successiva fino al corretto completamento della finestra precedente. I trigger con auto-dipendenza presenteranno le proprietà seguenti:

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

Di seguito sono illustrati gli scenari e l'utilizzo delle proprietà di dipendenze in una finestra a cascata.

## <a name="dependency-offset"></a>Scostamento della dipendenza

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png)

## <a name="dependency-size"></a>Dimensione della dipendenza

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png)

## <a name="self-dependency"></a>Auto-dipendenza

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png)

## <a name="usage-scenarios"></a>Scenari di utilizzo

### <a name="dependency-on-another-tumbling-window-trigger"></a>Dipendenza di un altro trigger in una finestra a cascata

Ad esempio, un processo quotidiano di elaborazione dati di telemetria dipendente da un altro processo giornaliero, che aggrega gli output degli ultimi sette giorni e genera flussi di finestre in una sequenza di sette giorni:

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png)

### <a name="dependency-on-itself"></a>Dipendenza su se stesso

Un processo giornaliero senza interruzioni nei flussi di output del processo:

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png)

## <a name="monitor-dependencies"></a>Monitorare le dipendenze

È possibile monitorare la catena delle dipendenze e le finestre corrispondenti dalla pagina di monitoraggio dell'esecuzione del trigger. Navigare alla voce **Monitoraggio > Esecuzioni di trigger**.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png)

Fare clic sullo specchio per visualizzare tutte le esecuzioni di trigger dipendenti delle finestre selezionate.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere [Procedura per creare un trigger di finestra a cascata](how-to-create-tumbling-window-trigger.md).