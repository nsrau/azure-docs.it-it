---
title: Creare dipendenze del trigger di finestra a cascata in Azure Data Factory | Microsoft Docs
description: Informazioni su come creare dipendenze del trigger di finestra a cascata in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: daperlov
ms.openlocfilehash: 6e5e293e9759f091b6537d5efab9884e0a20fabc
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725500"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Creare una dipendenza del trigger di finestra a cascata

Questo articolo descrive la procedura per creare una dipendenza del trigger di finestra a cascata. Per informazioni più specifiche ed esempi sui trigger di finestra a cascata, vedere [Procedura per creare un trigger di finestra a cascata](how-to-create-tumbling-window-trigger.md).

Per creare una catena di dipendenze e accertarsi che un trigger venga eseguito solo dopo la corretta esecuzione di un altro trigger nella data factory, usare questa funzionalità avanzata per creare una dipendenza in una finestra a cascata.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Creare una dipendenza nell'interfaccia utente di Data Factory

Per creare dipendenze di un trigger, selezionare **Trigger > Avanzate > Nuovo**, quindi scegliere il trigger da cui dipendere con le dimensioni e gli scostamenti appropriati. Selezionare **Fine** e pubblicare le modifiche alla data factory per attivare le dipendenze.

![Creazione] di dipendenze (media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "Creazione") di dipendenze

## <a name="tumbling-window-dependency-properties"></a>Proprietà di una dipendenza in una finestra a cascata

Un trigger di finestra a cascata con una dipendenza presenta le proprietà seguenti:

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
| offset | Scostamento del trigger delle dipendenze. Fornire un valore nel formato di intervallo di tempo ed entrambi gli offset negativi e positivi sono consentiti. Questa proprietà è obbligatoria se il trigger dipende da se stesso e in tutti gli altri casi è facoltativo. L'auto-dipendenza deve avere sempre uno scostamento negativo. Se non viene specificato alcun valore, la finestra è uguale a quella del trigger. | Timespan<br/>(hh:mm:ss) | Auto-dipendenza: Sì<br/>Altro: No |
| size | Dimensione della finestra a cascata di dipendenze. Fornire un valore TimeSpan positivo. Questa proprietà è facoltativa. | Timespan<br/>(hh:mm:ss) | No  |

> [!NOTE]
> Un trigger di finestra a cascata può dipendere da un massimo di altri due trigger.

## <a name="tumbling-window-self-dependency-properties"></a>Proprietà di auto-dipendenza in una finestra a cascata

Negli scenari in cui il trigger non deve passare alla finestra successiva finché la finestra precedente non viene completata correttamente, compilare una dipendenza autonoma. Un trigger di autodipendenze che dipende dal successo delle esecuzioni precedenti di se stesso entro la data/ora precedente avrà le proprietà seguenti:

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

Di seguito sono illustrate le illustrazioni degli scenari e l'utilizzo delle proprietà di dipendenza della finestra a cascata.

### <a name="dependency-offset"></a>Scostamento della dipendenza

![Esempio di offset](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Esempio di offset")

### <a name="dependency-size"></a>Dimensione della dipendenza

![Esempio di dimensioni](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "Esempio di dimensioni")

### <a name="self-dependency"></a>Auto-dipendenza

![Dipendenza autonoma](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "Dipendenza autonoma")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Dipendenza di un altro trigger in una finestra a cascata

Un processo di elaborazione dei dati di telemetria giornaliero a seconda di un altro processo giornaliero che esegue l'aggregazione degli ultimi sette giorni e genera flussi di finestra in sequenza per sette giorni:

![Esempio di dipendenza](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "Esempio di dipendenza")

### <a name="dependency-on-itself"></a>Dipendenza su se stesso

Un processo giornaliero senza interruzioni nei flussi di output del processo:

![Esempio di dipendenza autonoma](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "Esempio di dipendenza autonoma")

## <a name="monitor-dependencies"></a>Monitorare le dipendenze

È possibile monitorare la catena delle dipendenze e le finestre corrispondenti dalla pagina di monitoraggio dell'esecuzione del trigger. Navigare alla voce **Monitoraggio > Esecuzioni di trigger**.

![Monitorare] le esecuzioni di trigger (media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "Monitorare") le esecuzioni di trigger

Fare clic sull'icona azione per visualizzare tutte le esecuzioni di trigger dipendenti della finestra selezionata.

![Monitorare] le dipendenze (media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "Monitorare") le dipendenze

Nell'esempio precedente, un trigger giornaliero dipende da un trigger orario senza finestra definita e da un offset di 3 ore. Di conseguenza, il trigger viene eseguito dopo 24 esecuzioni riuscite della dipendenza.

## <a name="next-steps"></a>Passaggi successivi

* Vedere [come creare un trigger di finestra a cascata](how-to-create-tumbling-window-trigger.md)
