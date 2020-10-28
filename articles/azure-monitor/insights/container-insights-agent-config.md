---
title: Configurare monitoraggio di Azure per la raccolta dati degli agenti di contenitori | Microsoft Docs
description: Questo articolo descrive come configurare l'agente di monitoraggio di Azure per i contenitori per controllare la raccolta di log delle variabili di ambiente e stdout/stderr.
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: f21b841bc129012b684d2a1c59eb72989fe9e0e0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890497"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Configurare la raccolta dei dati dell'agente per Monitoraggio di Azure per i contenitori

Monitoraggio di Azure per contenitori raccoglie le variabili stdout, stderr e Environment dai carichi di lavoro dei contenitori distribuiti nei cluster Kubernetes gestiti dall'agente in contenitori. Per configurare le impostazioni di raccolta dati di Agent, è possibile creare un ConfigMaps Kubernetes personalizzato per controllare questa esperienza. 

Questo articolo illustra come creare ConfigMap e configurare la raccolta dei dati in base alle esigenze.

>[!NOTE]
>Per Azure Red Hat OpenShift, viene creato un file ConfigMap modello nello spazio dei nomi *OpenShift-Azure-Logging* . 
>

## <a name="configmap-file-settings-overview"></a>Panoramica delle impostazioni del file ConfigMap

Viene fornito un file ConfigMap modello che consente di modificarlo facilmente con le personalizzazioni senza doverlo creare da zero. Prima di iniziare, è necessario rivedere la documentazione di Kubernetes su [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) e acquisire familiarità con la creazione, la configurazione e la distribuzione di ConfigMaps. Ciò consentirà di filtrare stderr e stdout per spazio dei nomi o nell'intero cluster e le variabili di ambiente per tutti i contenitori in esecuzione in tutti i pod/nodi del cluster.

>[!IMPORTANT]
>La versione minima dell'agente supportata per raccogliere le variabili stdout, stderr e Environment dai carichi di lavoro del contenitore è ciprod06142019 o successiva. Per verificare la versione dell'agente, dalla scheda **nodo** selezionare un nodo, quindi nel riquadro Proprietà prendere nota del valore della proprietà **tag immagine agente** . Per ulteriori informazioni sulle versioni degli agenti e sulle funzionalità incluse in ogni versione, vedere le [Note sulla versione degli agenti](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod).

### <a name="data-collection-settings"></a>Impostazioni di raccolta dati

Nella tabella seguente vengono descritte le impostazioni che è possibile configurare per controllare la raccolta dei dati:

| Chiave | Tipo di dati | valore | Description |
|--|--|--|--|
| `schema-version` | Stringa (maiuscole/minuscole) | v1 | Si tratta della versione dello schema utilizzata dall'agente<br> durante l'analisi di questo ConfigMap.<br> La versione dello schema attualmente supportata è V1.<br> La modifica di questo valore non è supportata e sarà<br> rifiutato quando viene valutato ConfigMap. |
| `config-version` | string |  | Supporta la possibilità di tenere traccia della versione del file di configurazione nel sistema/repository del controllo del codice sorgente.<br> I caratteri massimi consentiti sono 10 e tutti gli altri caratteri vengono troncati. |
| `[log_collection_settings.stdout] enabled =` | Boolean | true o false | Controlla se è abilitata la raccolta di log del contenitore stdout. Quando è impostato su `true` e non viene escluso alcuno spazio dei nomi per la raccolta di log stdout<br> ( `log_collection_settings.stdout.exclude_namespaces` impostazione seguente), i log stdout verranno raccolti da tutti i contenitori in tutti i pod/nodi del cluster. Se non è specificato in ConfigMaps,<br> il valore predefinito è `enabled = true` . |
| `[log_collection_settings.stdout] exclude_namespaces =` | string | Matrice con valori delimitati da virgole | Matrice di spazi dei nomi Kubernetes per cui non verranno raccolti i log stdout. Questa impostazione è efficace solo se<br> `log_collection_settings.stdout.enabled`<br> è impostato su `true`.<br> Se non è specificato in ConfigMap, il valore predefinito è<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.stderr] enabled =` | Boolean | true o false | Questo controlla se è abilitata la raccolta di log del contenitore stderr.<br> Quando è impostato su `true` e non viene escluso alcuno spazio dei nomi per la raccolta di log stdout<br> ( `log_collection_settings.stderr.exclude_namespaces` impostazione), i log stderr verranno raccolti da tutti i contenitori in tutti i pod/nodi del cluster.<br> Se non è specificato in ConfigMaps, il valore predefinito è<br> `enabled = true`. |
| `[log_collection_settings.stderr] exclude_namespaces =` | string | Matrice con valori delimitati da virgole | Matrice di spazi dei nomi Kubernetes per cui non verranno raccolti i log stderr.<br> Questa impostazione è efficace solo se<br> `log_collection_settings.stdout.enabled` è impostato su `true`.<br> Se non è specificato in ConfigMap, il valore predefinito è<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` | Boolean | true o false | Questa impostazione controlla la raccolta delle variabili di ambiente<br> in tutti i pod/nodi del cluster<br> e il valore predefinito è `enabled = true` quando non è specificato<br> in ConfigMaps.<br> Se la raccolta di variabili di ambiente è abilitata a livello globale, è possibile disabilitarla per un contenitore specifico<br> impostando la variabile di ambiente<br> `AZMON_COLLECT_ENV` per **false** con un'impostazione Dockerfile o nel file di [configurazione per il Pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) nella sezione **env:** .<br> Se la raccolta di variabili di ambiente è disabilitata a livello globale, non è possibile abilitare la raccolta per un contenitore specifico, ovvero l'unico override che può essere applicato a livello di contenitore è disabilitare la raccolta quando è già abilitata a livello globale. |
| `[log_collection_settings.enrich_container_logs] enabled =` | Boolean | true o false | Questa impostazione controlla l'arricchimento dei log del contenitore per popolare i valori delle proprietà Name e image<br> per ogni record di log scritto nella tabella ContainerLog per tutti i log del contenitore nel cluster.<br> Il valore predefinito è `enabled = false` quando non è specificato in ConfigMap. |
| `[log_collection_settings.collect_all_kube_events]` | Boolean | true o false | Questa impostazione consente la raccolta di eventi Kube di tutti i tipi.<br> Per impostazione predefinita, gli eventi KUBE con tipo *Normal* non vengono raccolti. Quando questa impostazione è impostata su `true` , gli eventi *normali* non vengono più filtrati e vengono raccolti tutti gli eventi.<br> Per impostazione predefinita, il parametro è impostato su `false`. |

### <a name="metric-collection-settings"></a>Impostazioni della raccolta metrica

La tabella seguente descrive le impostazioni che è possibile configurare per controllare la raccolta delle metriche:

| Chiave | Tipo di dati | valore | Description |
|--|--|--|--|
| `[metric_collection_settings.collect_kube_system_pv_metrics] enabled =` | Boolean | true o false | Questa impostazione consente di raccogliere le metriche di utilizzo del volume permanente (PV) nello spazio dei nomi Kube-System. Per impostazione predefinita, le metriche di utilizzo per i volumi permanenti con attestazioni del volume permanenti nello spazio dei nomi Kube-System non vengono raccolte. Quando questa impostazione è impostata su `true` , vengono raccolte le metriche di utilizzo PV per tutti gli spazi dei nomi. Per impostazione predefinita, il parametro è impostato su `false`. |

ConfigMaps è un elenco globale e può essere applicato un solo ConfigMap all'agente. Non è possibile avere un altro ConfigMaps che esegue la sovradecisione delle raccolte.

## <a name="configure-and-deploy-configmaps"></a>Configurare e distribuire ConfigMaps

Per configurare e distribuire il file di configurazione ConfigMap nel cluster, seguire questa procedura.

1. Scaricare il [modello CONFIGMAP YAML file](https://aka.ms/container-azm-ms-agentconfig) e salvarlo come container-AZM-MS-agentconfig. yaml. 

   > [!NOTE]
   > Questo passaggio non è necessario quando si lavora con Azure Red Hat OpenShift perché il modello ConfigMap esiste già nel cluster.

2. Modificare il file YAML ConfigMap con le personalizzazioni per raccogliere le variabili di stdout, stderr e/o di ambiente. Se si sta modificando il file YAML di ConfigMap per Azure Red Hat OpenShift, eseguire prima il comando `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` per aprire il file in un editor di testo.

    - Per escludere spazi dei nomi specifici per la raccolta di log di stdout, configurare la chiave o il valore usando l'esempio seguente: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]` .
    
    - Per disabilitare la raccolta delle variabili di ambiente per un contenitore specifico, impostare la chiave/valore `[log_collection_settings.env_var] enabled = true` per abilitare la raccolta di variabili a livello globale, quindi seguire i passaggi [qui](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) per completare la configurazione per il contenitore specifico.
    
    - Per disabilitare la raccolta di log stderr a livello di cluster, configurare la chiave o il valore usando l'esempio seguente: `[log_collection_settings.stderr] enabled = false` .

3. Per i cluster diversi da Azure Red Hat OpenShift, creare ConfigMap eseguendo il comando kubectl seguente: `kubectl apply -f <configmap_yaml_file.yaml>` in cluster diversi da Azure Red Hat OpenShift. 
    
    Esempio: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Per Azure Red Hat OpenShift, salvare le modifiche nell'editor.

La modifica della configurazione può richiedere alcuni minuti prima di essere applicata e tutti i pod omsagent del cluster verranno riavviati. Il riavvio è un riavvio in sequenza per tutti i pod omsagent, non tutti i riavvii nello stesso momento. Al termine del riavvio, viene visualizzato un messaggio simile al seguente e include il risultato: `configmap "container-azm-ms-agentconfig" created` .

## <a name="verify-configuration"></a>Verificare la configurazione

Per verificare che la configurazione sia stata applicata correttamente a un cluster diverso da Azure Red Hat OpenShift, usare il comando seguente per esaminare i log da un pod agente: `kubectl logs omsagent-fdf58 -n kube-system` . Se si verificano errori di configurazione dai pod omsagent, l'output visualizzerà errori simili ai seguenti:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Gli errori correlati all'applicazione delle modifiche di configurazione sono disponibili anche per la revisione. Per eseguire ulteriori operazioni di risoluzione dei problemi relativi alle modifiche di configurazione, sono disponibili le opzioni seguenti:

- Da un pod di Agent log con lo stesso `kubectl logs` comando. 

    >[!NOTE]
    >Questo comando non è applicabile al cluster Azure Red Hat OpenShift.
    > 

- Da log attivi. I log attivi mostrano errori simili ai seguenti:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Dalla tabella **KubeMonAgentEvents** nell'area di lavoro log Analytics. I dati vengono inviati ogni ora con gravità dell' *errore* per gli errori di configurazione. Se non sono presenti errori, la voce nella tabella avrà dati con *informazioni* di gravità, che non segnalano errori. La proprietà **Tags** contiene altre informazioni sul Pod e sull'ID contenitore in cui si è verificato l'errore, nonché la prima occorrenza, l'ultima occorrenza e il conteggio nell'ultima ora.

- Con Azure Red Hat OpenShift, controllare i log omsagent cercando la tabella **ContainerLog** per verificare se è abilitata la raccolta dei log di OpenShift-Azure-Logging.

Dopo aver corretto gli errori in ConfigMap in cluster diversi da Azure Red Hat OpenShift, salvare il file YAML e applicare il ConfigMaps aggiornato eseguendo il comando: `kubectl apply -f <configmap_yaml_file.yaml` . Per Azure Red Hat OpenShift, modificare e salvare il ConfigMaps aggiornato eseguendo il comando:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>Applicazione di ConfigMap aggiornati

Se è già stato distribuito un ConfigMap in cluster diversi da Azure Red Hat OpenShift e si vuole aggiornarlo con una configurazione più recente, è possibile modificare il file ConfigMap usato in precedenza e quindi applicarlo usando lo stesso comando precedente, `kubectl apply -f <configmap_yaml_file.yaml` . Per Azure Red Hat OpenShift, modificare e salvare il ConfigMaps aggiornato eseguendo il comando:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

La modifica della configurazione può richiedere alcuni minuti prima di essere applicata e tutti i pod omsagent del cluster verranno riavviati. Il riavvio è un riavvio in sequenza per tutti i pod omsagent, non tutti i riavvii nello stesso momento. Al termine del riavvio, viene visualizzato un messaggio simile al seguente e include il risultato: `configmap "container-azm-ms-agentconfig" updated` .

## <a name="verifying-schema-version"></a>Verifica della versione dello schema

Le versioni dello schema di configurazione supportate sono disponibili come annotazione pod (versioni dello schema) nel pod omsagent. È possibile visualizzarli con il comando kubectl seguente: `kubectl describe pod omsagent-fdf58 -n=kube-system`

L'output sarà simile al seguente con le versioni dello schema di annotazione:

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

- Il monitoraggio di Azure per i contenitori non include un set predefinito di avvisi. Per informazioni su come creare avvisi consigliati per un utilizzo elevato della CPU e della memoria per supportare le procedure e i processi operativi, vedere la pagina [relativa alla creazione di avvisi di prestazioni con monitoraggio di Azure per i contenitori](./container-insights-log-alerts.md) .

- Con il monitoraggio abilitato per raccogliere lo stato e l'utilizzo delle risorse del servizio contenitore di Azure o del cluster ibrido e dei carichi di lavoro in esecuzione su di essi, informazioni su [come usare](container-insights-analyze.md) monitoraggio di Azure per i contenitori.

- Visualizzare gli [esempi di query di log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query e esempi predefiniti per valutare o personalizzare gli avvisi, la visualizzazione o l'analisi dei cluster.