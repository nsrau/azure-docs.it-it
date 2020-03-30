---
title: Configurare Monitoraggio di Azure per la raccolta dei dati dell'agente contenitori Documenti Microsoft
description: Questo articolo descrive come configurare l'agente di Monitoraggio di Azure per i contenitori per controllare la raccolta dei log di stdout/stderr e delle variabili di ambiente.
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 28b93190298ae61732ff7d2e297899af4ba0e5f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933014"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Configurare la raccolta dati dell'agente per Monitoraggio di Azure per i contenitoriConfigure agent data collection for Azure Monitor for containers

Monitoraggio di Azure per i contenitori raccoglie variabili stdout, stderr e ambientali dai carichi di lavoro dei contenitori distribuiti ai cluster Kubernetes gestiti dall'agente con contenitore. È possibile configurare le impostazioni di raccolta dati dell'agente creando una proprietà di configurazione Kubernetes personalizzata per controllare questa esperienza. 

In questo articolo viene illustrato come creare ConfigMap e configurare la raccolta dei dati in base alle proprie esigenze.

>[!NOTE]
>Per Azure Red Hat OpenShift, viene creato un file ConfigMap modello nello spazio dei nomi *openshift-azure-logging.* 
>

## <a name="configmap-file-settings-overview"></a>Panoramica delle impostazioni del file ConfigMap

Viene fornito un modello di file ConfigMap che consente di modificarlo facilmente con le personalizzazioni senza doverlo creare da zero. Prima di iniziare, è consigliabile esaminare la documentazione di Kubernetes su [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) e acquisire familiarità con la creazione, la configurazione e la distribuzione di ConfigMaps. In questo modo sarà possibile filtrare stderr e stdout per ogni spazio dei nomi o nell'intero cluster e le variabili di ambiente per qualsiasi contenitore in esecuzione in tutti i pod/nodi del cluster.

>[!IMPORTANT]
>La versione minima dell'agente supportata per raccogliere variabili stdout, stderr e environmental dai carichi di lavoro dei contenitori è ciprod06142019 o versione successiva. Per verificare la versione dell'agente, nella scheda **Nodo** selezionare un nodo e nel riquadro delle proprietà il valore nota della proprietà **Tag immagine agente.** Per ulteriori informazioni sulle versioni dell'agente e sugli elementi inclusi in ogni versione, vedere [le note sulla versione dell'agente.](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)

### <a name="data-collection-settings"></a>Impostazioni di raccolta dati

Di seguito sono riportate le impostazioni che possono essere configurate per controllare la raccolta dei dati.

|Chiave |Tipo di dati |valore |Descrizione |
|----|----------|------|------------|
|`schema-version` |Stringa (maiuscole/minuscole) |v1 |Questa è la versione dello schema utilizzata dall'agente durante l'analisi di questo ConfigMap. Lo schema-version attualmente supportato è v1. La modifica di questo valore non è supportata e verrà rifiutata quando si valuta ConfigMap.|
|`config-version` |string | | Supporta la possibilità di tenere traccia della versione di questo file di configurazione nel repository/repository o sistema di controllo del codice sorgente. Il numero massimo di caratteri consentiti è 10 e tutti gli altri caratteri vengono troncati. |
|`[log_collection_settings.stdout] enabled =` |Boolean | true o false | In questo modo viene seguito l'abilitazione della raccolta di log del contenitore stdout. Se impostato `true` su e non vengono esclusi spazi`log_collection_settings.stdout.exclude_namespaces` dei nomi per la raccolta di log stdout (impostazione seguente), i log di stdout verranno raccolti da tutti i contenitori in tutti i pod/nodi del cluster. Se non specificato in ConfigMaps, `enabled = true`il valore predefinito è . |
|`[log_collection_settings.stdout] exclude_namespaces =`|string | Matrice delimitata da virgole |Matrice di spazi dei nomi Kubernetes per i quali non verranno raccolti i log stdout. Questa impostazione è `log_collection_settings.stdout.enabled` efficace `true`solo se è impostata su . Se non specificato in ConfigMap, `exclude_namespaces = ["kube-system"]`il valore predefinito è .|
|`[log_collection_settings.stderr] enabled =` |Boolean | true o false |In questo modo viene verificato se la raccolta di log del contenitore stderr è abilitata. Se impostato `true` su e non vengono esclusi spazi`log_collection_settings.stderr.exclude_namespaces` dei nomi per la raccolta di log stdout (impostazione), i log stderr verranno raccolti da tutti i contenitori in tutti i pod/nodi del cluster. Se non specificato in ConfigMaps, `enabled = true`il valore predefinito è . |
|`[log_collection_settings.stderr] exclude_namespaces =` |string |Matrice delimitata da virgole |Matrice di spazi dei nomi Kubernetes per i quali non verranno raccolti i log stderr. Questa impostazione è `log_collection_settings.stdout.enabled` efficace `true`solo se è impostata su . Se non specificato in ConfigMap, `exclude_namespaces = ["kube-system"]`il valore predefinito è . |
| `[log_collection_settings.env_var] enabled =` |Boolean | true o false | Questa impostazione controlla la raccolta delle variabili di ambiente `enabled = true` in tutti i pod/nodi del cluster e il valore predefinito viene specificato quando non viene specificato in ConfigMaps. Se la raccolta di variabili di ambiente è abilitata a livello `AZMON_COLLECT_ENV` globale, è possibile disabilitarla per un contenitore specifico impostando la variabile di ambiente **su False** con un'impostazione Dockerfile o nel file di [configurazione per il Pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) nella sezione **env:.** Se la raccolta di variabili di ambiente è disabilitata a livello globale, non è possibile abilitare la raccolta per un contenitore specifico, ovvero l'unico override che può essere applicato a livello di contenitore consiste nel disabilitare la raccolta quando è già abilitata a livello globale.). |
| `[log_collection_settings.enrich_container_logs] enabled =` |Boolean | true o false | Questa impostazione controlla l'arricchimento dei log del contenitore per popolare i valori delle proprietà Name e Image per ogni record di log scritto nella tabella ContainerLog per tutti i log del contenitore nel cluster. Il valore `enabled = false` predefinito è quando non specificato in ConfigMap.It defaults to when not specified in ConfigMap. |

ConfigMaps è un elenco globale e all'agente può essere applicato un solo ConfigMap. Non è possibile avere un altro ConfigMaps overruling le raccolte.

## <a name="configure-and-deploy-configmaps"></a>Configurare e distribuire ConfigMaps

Eseguire la procedura seguente per configurare e distribuire il file di configurazione di ConfigMap nel cluster.

1. [Scaricare](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) il modello ConfigMap yaml file e salvarlo come container-azm-ms-agentconfig.yaml. 

   >[!NOTE]
   >Questo passaggio non è necessario quando si lavora con Azure Red Hat OpenShift poiché il modello ConfigMap esiste già nel cluster.

2. Modificare il file yaml configMap con le personalizzazioni per raccogliere variabili stdout, stderr e/o ambientali. Se si sta modificando il file yaml di ConfigMap per `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` Azure Red Hat OpenShift, eseguire innanzitutto il comando per aprire il file in un editor di testo.

    - Per escludere spazi dei nomi specifici per la raccolta di log stdout, configurare la chiave/valore utilizzando l'esempio seguente: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    
    - Per disabilitare la raccolta di variabili di `[log_collection_settings.env_var] enabled = true` ambiente per un contenitore specifico, impostare la chiave/valore per abilitare la raccolta di variabili a livello globale e quindi seguire i passaggi [qui](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) per completare la configurazione per il contenitore specifico.
    
    - Per disabilitare la raccolta dei registri stderr a livello di `[log_collection_settings.stderr] enabled = false`cluster, configurare la chiave/valore utilizzando l'esempio seguente: .

3. Per i cluster diversi da Azure Red Hat OpenShift, creare ConfigMap `kubectl apply -f <configmap_yaml_file.yaml>` eseguendo il comando kubectl seguente: in cluster diversi da Azure Red Hat OpenShift.For clusters other than Azure Red Hat OpenShift, create ConfigMap by running the following kubectl command: on clusters other than Azure Red Hat OpenShift. 
    
    Esempio: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Per Azure Red Hat OpenShift, salvare le modifiche nell'editor.

Il completamento della modifica della configurazione può richiedere alcuni minuti prima di rendere effettiva tutti i pod omsagent del cluster verranno riavviati. Il riavvio è un riavvio in sequenza per tutti i pod omsagent, non per tutti contemporaneamente. Al termine dei riavvii, viene visualizzato un messaggio analogo al seguente `configmap "container-azm-ms-agentconfig" created`e include il risultato: .

## <a name="verify-configuration"></a>Verificare la configurazione

Per verificare che la configurazione sia stata applicata correttamente a un cluster diverso da `kubectl logs omsagent-fdf58 -n=kube-system`Azure Red Hat OpenShift, utilizzare il comando seguente per esaminare i log da un pod agente: . Se sono presenti errori di configurazione dai pod omsagent, l'output mostrerà errori simili ai seguenti:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Gli errori relativi all'applicazione delle modifiche di configurazione sono disponibili anche per la revisione. Per eseguire ulteriori operazioni di risoluzione dei problemi relativi alle modifiche alla configurazione, sono disponibili le opzioni seguenti:

- Da un pod di `kubectl logs` dati dell'agente che utilizza lo stesso comando. 

    >[!NOTE]
    >Questo comando non è applicabile al cluster Azure Red Hat OpenShift.This command is not applicable to Azure Red Hat OpenShift cluster.
    > 

- Dai registri in diretta. I registri in tempo reale mostrano errori simili ai seguenti:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Dalla tabella **KubeMonAgentEvents** nell'area di lavoro di Log Analytics. I dati vengono inviati ogni ora con gravità *degli* errori di configurazione. Se non sono presenti errori, la voce nella tabella conterrà dati con informazioni di gravità *Info*, che non segnala errori. La proprietà **Tags** contiene ulteriori informazioni sull'ID contenitore e sul contenitore in cui si è verificato l'errore e anche la prima occorrenza, l'ultima occorrenza e il conteggio nell'ultima ora.

- Con Azure Red Hat OpenShift, controllare i log di omsagent eseguendo una ricerca nella tabella **ContainerLog** per verificare se la raccolta di log di openshift-azure-logging è abilitata.

Dopo aver corretto gli errori in ConfigMap in cluster diversi da Azure Red Hat OpenShift, salvare il file `kubectl apply -f <configmap_yaml_file.yaml`yaml e applicare le ConfigMap aggiornate eseguendo il comando: . Per Azure Red Hat OpenShift, modificare e salvare le ConfigMap aggiornate eseguendo il comando:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>Applicazione di ConfigMap aggiornato

Se è già stato distribuito un ConfigMap in cluster diversi da Azure Red Hat OpenShift e si desidera aggiornarlo con una configurazione più recente, `kubectl apply -f <configmap_yaml_file.yaml`è possibile modificare il file ConfigMap usato in precedenza e quindi applicare usando lo stesso comando usato in precedenza, . Per Azure Red Hat OpenShift, modificare e salvare le ConfigMap aggiornate eseguendo il comando:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Il completamento della modifica della configurazione può richiedere alcuni minuti prima di rendere effettiva tutti i pod omsagent del cluster verranno riavviati. Il riavvio è un riavvio in sequenza per tutti i pod omsagent, non per tutti contemporaneamente. Al termine dei riavvii, viene visualizzato un messaggio analogo al seguente `configmap "container-azm-ms-agentconfig" updated`e include il risultato: .

## <a name="verifying-schema-version"></a>Verifica della versione dello schema

Le versioni dello schema di configurazione supportate sono disponibili come annotazione del pod (schema-versions) nel contenitore omsagent. Si possono vedere con il seguente comando kubectl:`kubectl describe pod omsagent-fdf58 -n=kube-system`

L'output sarà simile al seguente con le versioni dello schema di annotazione:The output will show similar to the following with the annotation schema-versions:

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

- Monitoraggio di Azure per i contenitori non include un set predefinito di avvisi. Esaminare la sezione Creare avvisi di prestazioni con Monitoraggio di Azure per i [contenitori per](container-insights-alerts.md) informazioni su come creare avvisi consigliati per un utilizzo elevato della CPU e della memoria per supportare le procedure e i processi devOps o operativi.

- Con il monitoraggio abilitato per raccogliere l'integrità e l'utilizzo delle risorse dell'AKS o del cluster ibrido e dei carichi di lavoro in esecuzione su di essi, vedere [come usare](container-insights-analyze.md) Monitoraggio di Azure per i contenitori.

- Visualizzare esempi di query di [log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query predefinite ed esempi per valutare o personalizzare gli avvisi, la visualizzazione o l'analisi dei cluster.
