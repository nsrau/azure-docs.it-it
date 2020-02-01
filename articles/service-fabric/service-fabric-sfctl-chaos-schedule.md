---
title: INTERFACCIA della riga di comando di Azure Service Fabric-pianificazione Chaos sfctl
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric. Include un elenco di comandi per la pianificazione Chaos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 29b365c48e405830e238945f1d94156f477c15b4
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906177"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Ottiene e imposta la pianificazione di chaos.

## <a name="commands"></a>Comandi:

|Comando|Description|
| --- | --- |
| get | Ottiene la pianificazione di Chaos che definisce quando e come eseguire Chaos. |
| set | Imposta la pianificazione usata da Chaos. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl chaos schedule get
Ottiene la pianificazione di Chaos che definisce quando e come eseguire Chaos.

Ottiene la versione della pianificazione di Chaos in uso e la pianificazione di Chaos che definisce quando e come eseguire Chaos.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos schedule set
Imposta la pianificazione usata da Chaos.

Chaos pianificherà automaticamente le esecuzioni in base alla pianificazione di Chaos. La pianificazione Chaos verrà aggiornata se la versione specificata corrisponde alla versione sul server. Quando si aggiorna la pianificazione Chaos, la versione sul server viene incrementata di 1. La versione sul server eseguirà il ritorno a 0 dopo il raggiungimento di un numero elevato. Se Chaos è in esecuzione quando viene effettuata la chiamata, la chiamata avrà esito negativo.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --chaos-parameters-dictionary | Elenco con codifica JSON che rappresenta un mapping di nomi di stringa agli elementi ChaosParameter che devono essere usati dai processi. |
| --expiry-date-utc | Data e ora in cui smettere di usare la pianificazione per pianificare Chaos.  Impostazione predefinita\: 9999-12-31T23\:59\:59.999Z. |
| --jobs | Elenco con codifica JSON di ChaosScheduleJobs indicante quando eseguire Chaos e con quali parametri eseguire Chaos. |
| --start-date-utc | Data e ora in cui iniziare a usare la pianificazione per pianificare Chaos.  Impostazione predefinita\: 1601-01-01T00\:00\:00.000Z. |
| --timeout -t | Impostazione predefinita\: 60. |
| --version | Numero di versione della pianificazione. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

### <a name="examples"></a>Esempi

Il comando seguente imposta una pianificazione (presumendo che la versione della pianificazione corrente sia 0) che inizia il giorno 2016-01-01 e scade il giorno 2038-01-01 per l'esecuzione di Chaos 24 ore al giorno, sette giorni alla settimana.
Chaos verrà pianificato nel cluster per tale periodo.
```
sfctl chaos schedule set --version 0 --start-date-utc "2016-01-01T00:00:00.000Z" --expiry-date-utc "2038-01-01T00:00:00.000Z"
    --chaos-parameters-dictionary
    [
    {
        "Key":"adhoc",
        "Value":{
            "MaxConcurrentFaults":3,
            "EnableMoveReplicaFaults":true,
            "ChaosTargetFilter":{
                "NodeTypeInclusionList":[
                "N0010Ref",
                "N0020Ref",
                "N0030Ref",
                "N0040Ref",
                "N0050Ref"
                ]
            },
            "MaxClusterStabilizationTimeoutInSeconds":60,
            "WaitTimeBetweenIterationsInSeconds":15,
            "WaitTimeBetweenFaultsInSeconds":30,
            "TimeToRunInSeconds":"600",
            "Context":{
                "Map":{
                "test":"value"
                }
            },
            "ClusterHealthPolicy":{
                "MaxPercentUnhealthyNodes":0,
                "ConsiderWarningAsError":true,
                "MaxPercentUnhealthyApplications":0
            }
        }
    }
    ]
    --jobs
    [
    {
        "ChaosParameters":"adhoc",
        "Days":{
            "Sunday":true,
            "Monday":true,
            "Tuesday":true,
            "Wednesday":true,
            "Thursday":true,
            "Friday":true,
            "Saturday":true
        },
        "Times":[
            {
                "StartTime":{
                "Hour":0,
                "Minute":0
                },
                "EndTime":{
                "Hour":23,
                "Minute":59
                }
            }
        ]
    }
    ]
```



## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).
