---
title: Configurare monitoraggio di Azure per la raccolta di dati dell'agente di contenitori | Microsoft Docs
description: Questo articolo descrive come è possibile configurare il monitoraggio di Azure per controllare stdout/stderr dell'agente di contenitori e le variabili di ambiente di raccolta log.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2019
ms.author: magoedte
ms.openlocfilehash: 1e7506e311c38d87371dd1b65440b6fb41a7ce78
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341654"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Configurare la raccolta di dati dell'agente di monitoraggio di Azure per contenitori

Monitoraggio di Azure per contenitori raccoglie stdout, stderr e variabili di ambiente da carichi di lavoro contenitore distribuiti al cluster Kubernetes ospitato in Azure Kubernetes Service (AKS) dall'agente nei contenitori gestiti. È possibile configurare le impostazioni di raccolta dati dell'agente tramite la creazione di un oggetto personalizzato Kubernetes ConfigMaps per controllare questa esperienza. Questo articolo illustra come creare ConfigMap e configurare la raccolta di dati in base alle esigenze.

## <a name="configure-your-cluster-with-custom-data-collection-settings"></a>Configurare il cluster con le impostazioni di raccolta dati personalizzati

Un file di modello di oggetto ConfigMap viene fornito nella quale è possibile modificarlo facilmente con le personalizzazioni senza che sia necessario crearlo da zero. Prima di iniziare, è consigliabile consultare la documentazione di Kubernetes sui [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) e acquisire familiarità con le procedure per creare, configurare e distribuire ConfigMaps. In questo modo sarà possibile filtrare stderr e stdout per ogni spazio dei nomi o l'intero cluster e le variabili di ambiente per qualsiasi contenitore in esecuzione in tutti i POD/nodi del cluster.

>[!IMPORTANT]
>La versione minima dell'agente supportata per questa funzionalità è microsoft / oms:ciprod06142019 o versione successiva. 

### <a name="overview-of-configurable-data-collection-settings"></a>Panoramica delle impostazioni di raccolta dati può essere configurato

Di seguito sono le impostazioni che possono essere configurate per controllare la raccolta dei dati.

|Chiave |Tipo di dati |Value |Descrizione |
|----|----------|------|------------|
|`schema-version` |Stringa (maiuscole / minuscole) |v1 |Si tratta della versione dello schema utilizzata dall'agente durante l'analisi di questo oggetto ConfigMap. Schema-versione attualmente supportata è la versione 1. Modifica questo valore non è supportata e verrà rifiutata quando ConfigMap viene valutata.|
|`config-version` |String | | Supporta la possibilità di tenere traccia di versione del file config nel repository del codice sorgente controllo sistema /. Numero massimo di caratteri consentito è 10 e tutti gli altri caratteri vengono troncati. |
|`[log_collection_settings.stdout] enabled =` |Boolean | true o false | Consente di controllare se è abilitata la raccolta di log stdout contenitore. Se impostato su `true` e nessuno spazio dei nomi viene escluse per la raccolta di log stdout (`log_collection_settings.stdout.exclude_namespaces` impostazione sotto), verranno raccolti i log stdout da tutti i contenitori in tutti i POD/nodi del cluster. Se non specificato in ConfigMaps, il valore predefinito è `enabled = true`. |
|`[log_collection_settings.stdout] exclude_namespaces =`|String | Matrice delimitato da virgole |Matrice di spazi dei nomi Kubernetes per cui stdout non verranno raccolti i log. Questa impostazione ha effetto solo se `log_collection_settings.stdout.enabled` è impostata su `true`. Se non specificato nell'oggetto ConfigMap, il valore predefinito è `exclude_namespaces = ["kube-system"]`.|
|`[log_collection_settings.stderr] enabled =` |Boolean | true o false |Consente di controllare se è abilitata la raccolta di log stderr contenitore. Se impostato su `true` e nessuno spazio dei nomi viene escluse per la raccolta di log stdout (`log_collection_settings.stderr.exclude_namespaces` impostazione), verranno raccolti i log stderr da tutti i contenitori in tutti i POD/nodi del cluster. Se non specificato in ConfigMaps, il valore predefinito è `enabled = true`. |
|`[log_collection_settings.stderr] exclude_namespaces =` |string |Matrice delimitato da virgole |Matrice di spazi dei nomi Kubernetes per cui stderr non verranno raccolti i log. Questa impostazione ha effetto solo se `log_collection_settings.stdout.enabled` è impostata su `true`. Se non specificato nell'oggetto ConfigMap, il valore predefinito è `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` |Boolean | true o false | Consente di controllare se la raccolta di variabili di ambiente è abilitata. Se impostato su `false`, nessuna variabile di ambiente vengono raccolti per qualsiasi contenitore in esecuzione in tutti i POD/nodi del cluster. Se non specificato nell'oggetto ConfigMap, il valore predefinito è `enabled = true`. |

### <a name="configure-and-deploy-configmaps"></a>Configurare e distribuire ConfigMaps

Eseguire la procedura seguente per configurare e distribuire il file di configurazione ConfigMap nel cluster.

1. [Scaricare](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) il codice yaml ConfigMap modello e salvarlo come contenitore-azm-ms-agentconfig.yaml.  
1. Modificare il file yaml ConfigMap con le personalizzazioni. 

    - Per escludere gli spazi dei nomi specifici per la raccolta di log stdout, si configura il chiave/valore usando l'esempio seguente: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    - Per disabilitare la raccolta di variabili di ambiente per un contenitore specifico, impostare il chiave/valore `[log_collection_settings.env_var] enabled = true` per abilitare la raccolta della variabile a livello globale e quindi seguire i passaggi [qui](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) per completare la configurazione per il contenitore specifico.
    - Per disabilitare la raccolta dei log stderr a livello di cluster, si configura il chiave/valore usando l'esempio seguente: `[log_collection_settings.stderr] enabled = false`.

1. Creare l'oggetto ConfigMap eseguendo il seguente comando di kubectl: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Esempio: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    La modifica della configurazione può richiedere alcuni minuti prima che diventino effettive e verranno riavviati tutti i POD omsagent nel cluster. Il riavvio è un riavvio in sequenza per tutti i POD omsagent, non tutte riavviate nello stesso momento. Dopo il riavvio del completamento, viene visualizzato un messaggio che è simile al seguente che include il risultato: `configmap "container-azm-ms-agentconfig" created`.

Per verificare la configurazione è stata applicata, usare il comando seguente per esaminare i log dal pod agente: `kubectl logs omsagent-fdf58 -n=kube-system`. Se sono presenti errori di configurazione dai POD osmagent, l'output visualizzerà gli errori simili al seguente:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Errori impediscono l'analisi del file, in modo da riavviare, utilizzare la configurazione predefinita di omsagent. Dopo aver corretto gli errori nell'oggetto ConfigMap, salvare il file yaml e applicare il ConfigMaps aggiornato eseguendo il comando: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="applying-updated-configmap"></a>Applicazione aggiornato ConfigMap

Se hai già distribuito un oggetto ConfigMap per il cluster e si desidera aggiornare con una configurazione più recente, è sufficiente modificare il file oggetto ConfigMap è utilizzato in precedenza e quindi applicare utilizzando lo stesso comando, come in precedenza, `kubectl apply -f <configmap_yaml_file.yaml`.

La modifica della configurazione può richiedere alcuni minuti prima che diventino effettive e verranno riavviati tutti i POD omsagent nel cluster. Il riavvio è un riavvio in sequenza per tutti i POD omsagent, non tutte riavviate nello stesso momento. Dopo il riavvio del completamento, viene visualizzato un messaggio che è simile al seguente che include il risultato: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Verifica della versione dello schema

Versioni dello schema di configurazione supportati sono disponibili come annotazione di pod (versioni dello schema) nel pod omsagent. È possibile visualizzarli con il comando kubectl seguente: `kubectl describe pod omsagent-fdf58 -n=kube-system`

L'output visualizzerà simile al seguente con le versioni dello schema di annotazione:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su come usare Monitoraggio di Azure e monitorare altri aspetti del cluster del servizio Azure Kubernetes, vedere [Visualizzare l'integrità del servizio Kubernetes di Azure](container-insights-analyze.md).
- Vista [esempi di query di log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query predefinito ed esempi per valutare o personalizzare per gli avvisi, la visualizzazione o l'analisi dei cluster.