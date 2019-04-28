---
title: Interfaccia della riga di comando Azure Service Fabric - sfctl sa-cluster | Microsoft Docs
description: Descrive i comandi dell'interfaccia della riga di comando di Service Fabric sfctl standalone cluster.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: a652439729e538b3ce2545ab3b09284e6645ce9d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60556390"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
Gestisce i cluster di Service Fabric autonomi.

## <a name="commands"></a>Comandi:

|Comando|DESCRIZIONE|
| --- | --- |
| config | Ottiene la configurazione del cluster autonomo di Service Fabric. |
| config-upgrade | Avvia l'aggiornamento della configurazione di un cluster autonomo di Service Fabric. |
| upgrade-status | Ottiene lo stato dell'aggiornamento della configurazione del cluster di un cluster autonomo di Service Fabric. |

## <a name="sfctl-sa-cluster-config"></a>sfctl sa-cluster config
Ottiene la configurazione del cluster autonomo di Service Fabric.

La configurazione del cluster contiene le proprietà del cluster che includono i diversi tipi di nodi nel cluster, le configurazioni di sicurezza, gli errori e le topologie del dominio di aggiornamento e così via.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --configuration-api-version [Obbligatorio] | Versione API della configurazione json del cluster autonomo. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-cluster config-upgrade
Avvia l'aggiornamento della configurazione di un cluster autonomo di Service Fabric.

Convalida i parametri di aggiornamento della configurazione forniti e avvia l'aggiornamento della configurazione di un cluster se i parametri sono validi.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --cluster-config [Obbligatorio] | Configurazione del cluster. |
| --application-health-policies | Dizionario con codifica JSON delle coppie di nomi del tipo di applicazione e percentuale massima di stato non integro prima che venga generato l'errore. |
| --delta-unhealthy-nodes | Percentuale massima consentita di riduzione delle prestazioni nell'integrità delta durante l'aggiornamento. I valori consentiti sono valori interi compresi tra 0 e 100. |
| --health-check-retry | Intervallo di tempo tra i tentativi di esecuzione dei controlli integrità se l'applicazione o il cluster non è integro.  Impostazione predefinita\: PT0H0M0S. |
| --health-check-stable | Tempo di attesa per cui l'applicazione o il cluster devono rimanere integri prima di passare al dominio di aggiornamento successivo.  Impostazione predefinita\: PT0H0M0S. <br><br> Viene prima interpretato come stringa che rappresenta una durata ISO 8601. Se l'esito è negativo, viene interpretato come numero che rappresenta il numero totale di millisecondi. |
| --health-check-wait | Intervallo di tempo di attesa dopo il completamento di un dominio di aggiornamento prima di avviare il processo dei controlli integrità.  Impostazione predefinita\: PT0H0M0S. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |
| --unhealthy-applications | Percentuale massima consentita di applicazioni non integre durante l'aggiornamento. I valori consentiti sono valori interi compresi tra 0 e 100. |
| --unhealthy-nodes | Percentuale massima consentita di nodi non integri durante l'aggiornamento. I valori consentiti sono valori interi compresi tra 0 e 100. |
| --upgrade-domain-delta-unhealthy-nodes | Percentuale massima consentita di riduzione delle prestazioni nell'integrità delta del dominio di aggiornamento durante l'aggiornamento. I valori consentiti sono valori interi compresi tra 0 e 100. |
| --upgrade-domain-timeout | Tempo necessario al completamento di ogni dominio di aggiornamento prima dell'esecuzione di FailureAction.  Impostazione predefinita\: PT0H0M0S. <br><br> Viene prima interpretato come stringa che rappresenta una durata ISO 8601. Se l'esito è negativo, viene interpretato come numero che rappresenta il numero totale di millisecondi. |
| --upgrade-timeout | Tempo necessario al completamento dell'aggiornamento prima dell'esecuzione di FailureAction.  Impostazione predefinita\: PT0H0M0S. <br><br> Viene prima interpretato come stringa che rappresenta una durata ISO 8601. Se l'esito è negativo, viene interpretato come numero che rappresenta il numero totale di millisecondi. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

### <a name="examples"></a>Esempi

Avviare un aggiornamento della configurazione cluster

```
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-cluster upgrade-status
Ottiene lo stato dell'aggiornamento della configurazione del cluster di un cluster autonomo di Service Fabric.

Ottiene i dettagli dello stato dell'aggiornamento della configurazione del cluster di un cluster autonomo di Service Fabric.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |


## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).