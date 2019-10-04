---
title: Configurare monitoraggio di Azure per la raccolta dati degli agenti di contenitori | Microsoft Docs
description: Questo articolo descrive come configurare l'agente di monitoraggio di Azure per i contenitori per controllare la raccolta di log delle variabili di ambiente e stdout/stderr.
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
ms.date: 08/14/2019
ms.author: magoedte
ms.openlocfilehash: 7cd915c47fa0661a9da66d7ca3315480ce7d6b98
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709437"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Configurare la raccolta dei dati dell'agente per il monitoraggio di Azure per i contenitori

Monitoraggio di Azure per contenitori raccoglie le variabili stdout, stderr e Environment dai carichi di lavoro dei contenitori distribuiti nei cluster Kubernetes gestiti ospitati nel servizio Azure Kubernetes dall'agente in contenitori. Questo agente può anche raccogliere dati di serie temporali (detti anche metriche) da Prometheus usando l'agente in contenitori senza dover configurare e gestire un server e un database Prometheus. Per configurare le impostazioni di raccolta dati di Agent, è possibile creare un ConfigMaps Kubernetes personalizzato per controllare questa esperienza. 

Questo articolo illustra come creare ConfigMap e configurare la raccolta dei dati in base alle esigenze.

>[!NOTE]
>Il supporto per Prometheus è una funzionalità di anteprima pubblica al momento.
>

## <a name="configmap-file-settings-overview"></a>Panoramica delle impostazioni del file ConfigMap

Viene fornito un file ConfigMap modello che consente di modificarlo facilmente con le personalizzazioni senza doverlo creare da zero. Prima di iniziare, è necessario rivedere la documentazione di Kubernetes su [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) e acquisire familiarità con la creazione, la configurazione e la distribuzione di ConfigMaps. Ciò consentirà di filtrare stderr e stdout per spazio dei nomi o nell'intero cluster e le variabili di ambiente per tutti i contenitori in esecuzione in tutti i pod/nodi del cluster.

>[!IMPORTANT]
>La versione minima dell'agente supportata per raccogliere le variabili stdout, stderr e Environment dai carichi di lavoro del contenitore è ciprod06142019 o successiva. La versione minima dell'agente supportata per l'scraping delle metriche Prometeo è ciprod07092019 o successiva. Per verificare la versione dell'agente, dalla scheda **nodo** selezionare un nodo, quindi nel riquadro Proprietà prendere nota del valore della proprietà **tag immagine agente** .  

### <a name="data-collection-settings"></a>Impostazioni di raccolta dati

Di seguito sono riportate le impostazioni che possono essere configurate per controllare la raccolta dei dati.

|Chiave |Tipo di dati |Value |Descrizione |
|----|----------|------|------------|
|`schema-version` |Stringa (maiuscole/minuscole) |v1 |Si tratta della versione dello schema utilizzata dall'agente durante l'analisi di questo ConfigMap. La versione dello schema attualmente supportata è V1. La modifica di questo valore non è supportata e verrà rifiutata quando ConfigMap viene valutato.|
|`config-version` |Stringa | | Supporta la possibilità di tenere traccia della versione del file di configurazione nel sistema/repository del controllo del codice sorgente. I caratteri massimi consentiti sono 10 e tutti gli altri caratteri vengono troncati. |
|`[log_collection_settings.stdout] enabled =` |Boolean | true o false | Controlla se è abilitata la raccolta di log del contenitore stdout. Se è impostato `true` su e non viene escluso alcuno spazio dei nomi per`log_collection_settings.stdout.exclude_namespaces` la raccolta di log stdout (impostazione seguente), i log stdout verranno raccolti da tutti i contenitori in tutti i pod/nodi del cluster. Se non è specificato in ConfigMaps, il valore predefinito `enabled = true`è. |
|`[log_collection_settings.stdout] exclude_namespaces =`|Stringa | Matrice con valori delimitati da virgole |Matrice di spazi dei nomi Kubernetes per cui non verranno raccolti i log stdout. Questa impostazione è valida solo se `log_collection_settings.stdout.enabled` è impostato su `true`. Se non è specificato in ConfigMap, il valore predefinito `exclude_namespaces = ["kube-system"]`è.|
|`[log_collection_settings.stderr] enabled =` |Boolean | true o false |Questo controlla se è abilitata la raccolta di log del contenitore stderr. Se è impostato `true` su e non viene escluso alcuno spazio dei nomi per`log_collection_settings.stderr.exclude_namespaces` la raccolta di log di stdout (impostazione), i log stderr verranno raccolti da tutti i contenitori in tutti i pod/nodi del cluster. Se non è specificato in ConfigMaps, il valore predefinito `enabled = true`è. |
|`[log_collection_settings.stderr] exclude_namespaces =` |Stringa |Matrice con valori delimitati da virgole |Matrice di spazi dei nomi Kubernetes per cui non verranno raccolti i log stderr. Questa impostazione è valida solo se `log_collection_settings.stdout.enabled` è impostato su `true`. Se non è specificato in ConfigMap, il valore predefinito `exclude_namespaces = ["kube-system"]`è. |
| `[log_collection_settings.env_var] enabled =` |Boolean | true o false | Controlla se la raccolta di variabili di ambiente è abilitata. Quando è impostato `false`su, non viene raccolta alcuna variabile di ambiente per tutti i contenitori in esecuzione in tutti i pod/nodi del cluster. Se non è specificato in ConfigMap, il valore predefinito `enabled = true`è. |

### <a name="prometheus-scraping-settings"></a>Impostazioni di scraping Prometeo

![Architettura di monitoraggio dei contenitori per Prometheus](./media/container-insights-agent-config/monitoring-kubernetes-architecture.png)

Il monitoraggio di Azure per i contenitori offre un'esperienza uniforme per abilitare la raccolta di metriche Prometeo da parte di più frammenti di codice tramite i seguenti meccanismi, come illustrato nella tabella seguente. Le metriche vengono raccolte tramite un set di impostazioni specificato in un singolo file ConfigMap, che è lo stesso file usato per configurare la raccolta di stdout, stderr e le variabili di ambiente dai carichi di lavoro del contenitore. 

Il frammento attivo delle metriche da Prometheus viene eseguito da una delle due prospettive seguenti:

* URL HTTP a livello di cluster e individuare destinazioni dagli endpoint elencati di un servizio, servizi K8S come Kube-DNS e Kube-state-Metrics e annotazioni Pod specifiche di un'applicazione. Le metriche raccolte in questo contesto verranno definite nella sezione ConfigMap *[Prometheus data_collection_settings. cluster]* .
* URL HTTP a livello di nodo e individua destinazioni dagli endpoint elencati di un servizio. Le metriche raccolte in questo contesto verranno definite nella sezione ConfigMap *[Prometheus_data_collection_settings. Node]* .

| Endpoint | `Scope` | Esempio |
|----------|-------|---------|
| Annotazione Pod | A livello di cluster | annotazioni <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000" <br>prometheus.io/scheme: "http"` |
| Servizio Kubernetes | A livello di cluster | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| URL/endpoint | Per nodo e/o a livello di cluster | `http://myurl:9101/metrics` |

Quando si specifica un URL, monitoraggio di Azure per i contenitori esegue solo il frammento dell'endpoint. Quando si specifica il servizio Kubernetes, il nome del servizio viene risolto con il server DNS del cluster per ottenere l'indirizzo IP e quindi il servizio risolto viene frammentato.

|`Scope` | Chiave | Tipo di dati | Value | Descrizione |
|------|-----|-----------|-------|-------------|
| A livello di cluster | | | | Specificare uno dei tre metodi seguenti per rimuovere gli endpoint per le metriche. |
| | `urls` | Stringa | Matrice con valori delimitati da virgole | Endpoint HTTP (indirizzo IP o percorso URL valido specificato). Ad esempio: `urls=[$NODE_IP/metrics]`. ($NODE _IP è uno specifico parametro di monitoraggio di Azure per contenitori e può essere usato al posto dell'indirizzo IP del nodo. Deve essere tutti in maiuscolo.) |
| | `kubernetes_services` | Stringa | Matrice con valori delimitati da virgole | Una matrice di servizi Kubernetes per rimuovere le metriche da Kube-state-Metrics. Ad esempio,`kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Boolean | true o false | Quando è impostato `true` su nelle impostazioni a livello di cluster, monitoraggio di Azure per l'agente dei contenitori Kubernetes i pod nell'intero cluster per le annotazioni Prometeo seguenti:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Boolean | true o false | Consente di rimuovere il pod. `monitor_kubernetes_pods` deve essere impostato su `true`. |
| | `prometheus.io/scheme` | Stringa | http o https | Il valore predefinito è la rottamazione su HTTP. Se necessario, impostare su `https`. | 
| | `prometheus.io/path` | Stringa | Matrice con valori delimitati da virgole | Percorso della risorsa HTTP da cui recuperare le metriche. Se il percorso delle metriche non `/metrics`è, definirlo con questa annotazione. |
| | `prometheus.io/port` | Stringa | 9102 | Specificare una porta su cui restare in ascolto. Se la porta non è impostata, il valore predefinito è 9102. |
| A livello di nodo | `urls` | Stringa | Matrice con valori delimitati da virgole | Endpoint HTTP (indirizzo IP o percorso URL valido specificato). Ad esempio: `urls=[$NODE_IP/metrics]`. ($NODE _IP è uno specifico parametro di monitoraggio di Azure per contenitori e può essere usato al posto dell'indirizzo IP del nodo. Deve essere tutti in maiuscolo.) |
| A livello di nodo o a livello di cluster | `interval` | Stringa | 60 s | Il valore predefinito per l'intervallo di raccolta è di un minuto (60 secondi). È possibile modificare la raccolta per *[prometheus_data_collection_settings. Node]* e/o *[prometheus_data_collection_settings. cluster]* in unità di tempo, ad esempio NS, US (o Âμs), MS, s, m, h. |
| A livello di nodo o a livello di cluster | `fieldpass`<br> `fielddrop`| Stringa | Matrice con valori delimitati da virgole | È possibile specificare determinate metriche da raccogliere o meno dall'endpoint impostando l'elenco Consenti (`fieldpass`) e non consentire (`fielddrop`). È necessario impostare prima l'elenco Consenti. |

ConfigMap è un elenco globale e può essere applicato un solo ConfigMap all'agente. Non è possibile avere un altro ConfigMap che esegue la sovradecisione delle raccolte.

## <a name="configure-and-deploy-configmaps"></a>Configurare e distribuire ConfigMaps

Per configurare e distribuire il file di configurazione ConfigMap nel cluster, seguire questa procedura.

1. [Scaricare](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) il modello ConfigMap YAML file e salvarlo come container-AZM-MS-agentconfig. yaml.  
1. Modificare il file YAML di ConfigMap con le personalizzazioni.

    - Per escludere spazi dei nomi specifici per la raccolta di log di stdout, configurare la chiave o il valore `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`usando l'esempio seguente:.
    
    - Per disabilitare la raccolta delle variabili di ambiente per un contenitore specifico, impostare la `[log_collection_settings.env_var] enabled = true` chiave/valore per abilitare la raccolta di variabili a livello globale, quindi seguire i passaggi [qui](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) per completare la configurazione per il contenitore specifico.
    
    - Per disabilitare la raccolta di log stderr a livello di cluster, configurare la chiave o il valore usando l' `[log_collection_settings.stderr] enabled = false`esempio seguente:.
    
    - Gli esempi seguenti illustrano come configurare le metriche dei file ConfigMap da un URL a livello di cluster, dal DameonSet a livello di nodo di un agente e specificando un'annotazione Pod

        - Rimuovere le metriche Prometeo da un URL specifico nel cluster.

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings
         [prometheus_data_collection_settings.cluster] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h.
         fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
         fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
         urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

        - Rimuovere le metriche Prometeo da un DaemonSet di un agente in esecuzione in ogni nodo del cluster.

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings 
         [prometheus_data_collection_settings.node] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h. 
         # Node level scrape endpoint(s). These metrics will be scraped from agent's DaemonSet running in every node in the cluster 
         urls = ["http://$NODE_IP:9103/metrics"] 
         fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
         fielddrop = ["metric_to_drop"] 
        ```

        - Rimuovere le metriche Prometeo specificando un'annotazione pod.

        ```
         prometheus-data-collection-settings: |- 
         # Custom Prometheus metrics data collection settings
         [prometheus_data_collection_settings.cluster] 
         interval = "1m"  ## Valid time units are ns, us (or µs), ms, s, m, h
         monitor_kubernetes_pods = true #replicaset will scrape Kubernetes pods for the following prometheus annotations: 
          - prometheus.io/scrape:"true" #Enable scraping for this pod 
          - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
          - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
          - prometheus.io/port:"8000" #If port is not 9102 use this annotation
        ```

1. Creare ConfigMap eseguendo il comando kubectl seguente: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Esempio: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    La modifica della configurazione può richiedere alcuni minuti prima di essere applicata e tutti i pod omsagent del cluster verranno riavviati. Il riavvio è un riavvio in sequenza per tutti i pod omsagent, non tutti i riavvii nello stesso momento. Al termine del riavvio, viene visualizzato un messaggio simile al seguente e include il risultato: `configmap "container-azm-ms-agentconfig" created`.

Per verificare che la configurazione sia stata applicata correttamente, usare il comando seguente per esaminare i log di un pod `kubectl logs omsagent-fdf58 -n=kube-system`agente:. Se si verificano errori di configurazione dai pod omsagent, l'output visualizzerà errori simili ai seguenti:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Gli errori relativi all'applicazione delle modifiche di configurazione per Prometheus sono disponibili anche per la revisione.  Dai log da un pod di Agent usando lo stesso `kubectl logs` comando o da log attivi. I log attivi mostrano errori simili ai seguenti:

```
2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
```

Gli errori impediscono l'analisi del file da parte di omsagent, causando il riavvio e l'utilizzo della configurazione predefinita. Dopo aver corretto gli errori in ConfigMap, salvare il file YAML e applicare il ConfigMaps aggiornato eseguendo il comando: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="applying-updated-configmap"></a>Applicazione di ConfigMap aggiornati

Se è già stato distribuito un ConfigMap nel cluster e si vuole aggiornarlo con una configurazione più recente, è possibile modificare il file ConfigMap usato in precedenza e quindi applicarlo usando lo stesso comando precedente, `kubectl apply -f <configmap_yaml_file.yaml`.

La modifica della configurazione può richiedere alcuni minuti prima di essere applicata e tutti i pod omsagent del cluster verranno riavviati. Il riavvio è un riavvio in sequenza per tutti i pod omsagent, non tutti i riavvii nello stesso momento. Al termine del riavvio, viene visualizzato un messaggio simile al seguente e include il risultato: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Verifica della versione dello schema

Le versioni dello schema di configurazione supportate sono disponibili come annotazione pod (versioni dello schema) nel pod omsagent. È possibile visualizzarli con il comando kubectl seguente:`kubectl describe pod omsagent-fdf58 -n=kube-system`

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

## <a name="review-prometheus-data-usage"></a>Esaminare l'utilizzo dei dati di Prometeo

Per visualizzare le metriche Prometeo ricavate da monitoraggio di Azure, specificare "Prometeo" come spazio dei nomi. Ecco una query di esempio per visualizzare le metriche Prometheus dallo spazio dei nomi `default` kubernetes.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| extend tags=parse_json(Tags)
| where tostring(tags.namespace) == "default" 
```

I dati Prometheus possono anche essere sottoposti a query direttamente in base al nome.

```
InsightsMetrics 
| where Name contains "some_prometheus_metric"
```

Per identificare il volume di inserimento di ogni dimensione di metrica in GB al giorno per comprendere se è elevato, viene fornita la query seguente.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
L'output visualizzerà risultati simili ai seguenti:

![Registrare i risultati delle query del volume di inserimento dati](./media/container-insights-agent-config/log-query-example-usage-03.png)

Per stimare le dimensioni di ogni metrica in GB per un mese per comprendere se il volume di dati inseriti nell'area di lavoro è elevato, viene fornita la query seguente.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

L'output visualizzerà risultati simili ai seguenti:

![Registrare i risultati delle query del volume di inserimento dati](./media/container-insights-agent-config/log-query-example-usage-02.png)

Ulteriori informazioni su come monitorare l'utilizzo dei dati e analizzare i costi sono disponibili in [gestire l'utilizzo e i costi con i log di monitoraggio di Azure](../platform/manage-cost-storage.md).

## <a name="next-steps"></a>Passaggi successivi

Il monitoraggio di Azure per i contenitori non include un set predefinito di avvisi. Per informazioni su come creare avvisi consigliati per un utilizzo elevato della CPU e della memoria per supportare le procedure e i processi operativi, vedere la pagina [relativa alla creazione di avvisi di prestazioni con monitoraggio di Azure per i contenitori](container-insights-alerts.md) .

- Per altre informazioni su come usare Monitoraggio di Azure e monitorare altri aspetti del cluster del servizio Azure Kubernetes, vedere [Visualizzare l'integrità del servizio Kubernetes di Azure](container-insights-analyze.md).

- Visualizzare gli [esempi di query di log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query e esempi predefiniti per valutare o personalizzare gli avvisi, la visualizzazione o l'analisi dei cluster.
