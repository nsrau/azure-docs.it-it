---
title: 'Interfaccia della riga di comando Azure Service Fabric: sfctl chaos schedule | Microsoft Docs'
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl chaos schedule.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: f955ed63af221a08313042fcc8373b179ecbc120
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569383"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Ottiene e imposta la pianificazione di chaos.

## <a name="commands"></a>Comandi:

|Comando|DESCRIZIONE|
| --- | --- |
| get | Ottiene la pianificazione di Chaos che definisce quando e come eseguire Chaos. |
| set | Imposta la pianificazione usata da Chaos. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl chaos schedule get
Ottiene la pianificazione di Chaos che definisce quando e come eseguire Chaos.

Ottiene la versione della pianificazione di Chaos in uso e la pianificazione di Chaos che definisce quando e come eseguire Chaos.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos schedule set
Imposta la pianificazione usata da Chaos.

Chaos pianificherà automaticamente le esecuzioni in base alla pianificazione di Chaos. La versione nella pianificazione di input fornita deve corrispondere alla versione della pianificazione di Chaos nel server. Se la versione fornita non corrisponde alla versione nel server, la pianificazione di Chaos non viene aggiornata. Se la versione fornita corrisponde alla versione nel server, la pianificazione di Chaos viene aggiornata e la versione della pianificazione di Chaos nel server viene incrementata di uno ed esegue di nuovo il wrapping su 0 dopo 2.147.483.647. Se Chaos è in esecuzione quando viene effettuata la chiamata, la chiamata avrà esito negativo.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --chaos-parameters-dictionary | Elenco con codifica JSON che rappresenta un mapping di nomi di stringa agli elementi ChaosParameter che devono essere usati dai processi. |
| --expiry-date-utc | Data e ora in cui smettere di usare la pianificazione per pianificare Chaos.  Impostazione predefinita\: 9999-12-31T23\:59\:59.999Z. |
| --jobs | Elenco con codifica JSON di ChaosScheduleJobs indicante quando eseguire Chaos e con quali parametri eseguire Chaos. |
| --start-date-utc | Data e ora in cui iniziare a usare la pianificazione per pianificare Chaos.  Impostazione predefinita\: 1601-01-01T00\:00\:00.000Z. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |
| --version | Numero di versione della pianificazione. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

### <a name="examples"></a>Esempi

Il comando seguente imposta una pianificazione (presumendo che la versione della pianificazione corrente sia 0) che inizia il giorno 2016-01-01 e scade il giorno 2038-01-01 per l'esecuzione di Chaos 24 ore al giorno, sette giorni alla settimana. Chaos verrà pianificato nel cluster per tale periodo.

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


## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).
