---
title: Configurare monitoraggio di Azure per i contenitori integrazione Prometheus | Microsoft Docs
description: Questo articolo descrive come configurare il monitoraggio di Azure per l'agente dei contenitori per eliminare le metriche da Prometheus con il cluster di servizi Kubernetes di Azure.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 51bdf0cfedb30fbd95f9a44e8f4a0efe4e857104
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514342"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>Configurare la frammentazione delle metriche Prometeo con monitoraggio di Azure per i contenitori

[Prometheus](https://prometheus.io/) è una famosa soluzione open source per il monitoraggio di metriche che fa parte di [Cloud Native Compute Foundation](https://www.cncf.io/). Il monitoraggio di Azure per i contenitori offre un'esperienza di onboarding trasparente per raccogliere le metriche Prometeo. In genere, per usare Prometeo, è necessario configurare e gestire un server Prometeo con un archivio. Grazie all'integrazione con monitoraggio di Azure, non è necessario un server Prometeo. È sufficiente esporre l'endpoint della metrica Prometheus tramite gli esportatori o i pod (applicazione) e l'agente in contenitori per monitoraggio di Azure per i contenitori può ricavare le metriche per l'utente. 

![Architettura di monitoraggio dei contenitori per Prometheus](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>La versione minima dell'agente supportata per l'scraping delle metriche Prometheus è ciprod07092019 o successiva e la versione dell'agente supportata per la scrittura degli errori di configurazione e di Agent nella tabella `KubeMonAgentEvents` è ciprod10112019. Per ulteriori informazioni sulle versioni degli agenti e sulle funzionalità incluse in ogni versione, vedere le [Note sulla versione degli agenti](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod). Per verificare la versione dell'agente, dalla scheda **nodo** selezionare un nodo, quindi nel riquadro Proprietà prendere nota del valore della proprietà **tag immagine agente** .

### <a name="prometheus-scraping-settings"></a>Impostazioni di scraping Prometeo

Il frammento attivo delle metriche da Prometheus viene eseguito da una delle due prospettive seguenti:

* URL HTTP a livello di cluster e individuare destinazioni dagli endpoint elencati di un servizio. Ad esempio, i servizi K8S come Kube-DNS e Kube-state-Metrics e le annotazioni Pod specifiche di un'applicazione. Le metriche raccolte in questo contesto verranno definite nella sezione ConfigMap *[Prometheus data_collection_settings. cluster]* .
* URL HTTP a livello di nodo e individua destinazioni dagli endpoint elencati di un servizio. Le metriche raccolte in questo contesto verranno definite nella sezione ConfigMap *[Prometheus_data_collection_settings. Node]* .

| Endpoint | Scope | Esempio |
|----------|-------|---------|
| Annotazione Pod | A livello di cluster | annotazioni <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Servizio Kubernetes | A livello di cluster | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| URL/endpoint | Per nodo e/o a livello di cluster | `http://myurl:9101/metrics` |

Quando si specifica un URL, monitoraggio di Azure per i contenitori esegue solo il frammento dell'endpoint. Quando si specifica il servizio Kubernetes, il nome del servizio viene risolto con il server DNS del cluster per ottenere l'indirizzo IP e quindi il servizio risolto viene frammentato.

|Scope | Chiave | Tipo di dati | Valore | Descrizione |
|------|-----|-----------|-------|-------------|
| A livello di cluster | | | | Specificare uno dei tre metodi seguenti per rimuovere gli endpoint per le metriche. |
| | `urls` | String | Matrice con valori delimitati da virgole | Endpoint HTTP (indirizzo IP o percorso URL valido specificato). Ad esempio: `urls=[$NODE_IP/metrics]`. ($NODE _IP è uno specifico parametro di monitoraggio di Azure per contenitori e può essere usato al posto dell'indirizzo IP del nodo. Deve essere tutti in maiuscolo.) |
| | `kubernetes_services` | String | Matrice con valori delimitati da virgole | Una matrice di servizi Kubernetes per rimuovere le metriche da Kube-state-Metrics. Ad esempio, `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Booleano | true o false | Se è impostato su `true` nelle impostazioni a livello di cluster, monitoraggio di Azure per l'agente contenitori eliminerà i pod Kubernetes nell'intero cluster per le annotazioni Prometheus seguenti:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Booleano | true o false | Consente di rimuovere il pod. `monitor_kubernetes_pods` deve essere impostato su `true`. |
| | `prometheus.io/scheme` | String | http o https | Il valore predefinito è la rottamazione su HTTP. Se necessario, impostare su `https`. | 
| | `prometheus.io/path` | String | Matrice con valori delimitati da virgole | Percorso della risorsa HTTP da cui recuperare le metriche. Se il percorso delle metriche non è `/metrics`, definirlo con questa annotazione. |
| | `prometheus.io/port` | String | 9102 | Specificare una porta da cui deframmentare. Se la porta non è impostata, il valore predefinito è 9102. |
| | `monitor_kubernetes_pods_namespaces` | String | Matrice con valori delimitati da virgole | Un elenco di spazi dei nomi consentiti per rimuovere le metriche dai pod Kubernetes.<br> Ad esempio, `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| A livello di nodo | `urls` | String | Matrice con valori delimitati da virgole | Endpoint HTTP (indirizzo IP o percorso URL valido specificato). Ad esempio: `urls=[$NODE_IP/metrics]`. ($NODE _IP è uno specifico parametro di monitoraggio di Azure per contenitori e può essere usato al posto dell'indirizzo IP del nodo. Deve essere tutti in maiuscolo.) |
| A livello di nodo o a livello di cluster | `interval` | String | 60 s | Il valore predefinito per l'intervallo di raccolta è di un minuto (60 secondi). È possibile modificare la raccolta per *[prometheus_data_collection_settings. Node]* e/o *[prometheus_data_collection_settings. cluster]* in unità di tempo quali s, m, h. |
| A livello di nodo o a livello di cluster | `fieldpass`<br> `fielddrop`| String | Matrice con valori delimitati da virgole | È possibile specificare determinate metriche da raccogliere o meno dall'endpoint impostando l'elenco Consenti (`fieldpass`) e non consentito (`fielddrop`). È necessario impostare prima l'elenco Consenti. |

ConfigMaps è un elenco globale e può essere applicato un solo ConfigMap all'agente. Non è possibile avere un altro ConfigMaps che esegue la sovradecisione delle raccolte.

## <a name="configure-and-deploy-configmaps"></a>Configurare e distribuire ConfigMaps

Per configurare e distribuire il file di configurazione ConfigMap nel cluster, seguire questa procedura.

1. [Scaricare](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) il modello ConfigMap YAML file e salvarlo come container-AZM-MS-agentconfig. yaml.

2. Modificare il file YAML di ConfigMap con le personalizzazioni per rimuovere le metriche Prometeo.

    - Per raccogliere i servizi Kubernetes a livello di cluster, configurare il file ConfigMap usando l'esempio seguente.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Per configurare il frammento di metriche Prometheus da un URL specifico nel cluster, configurare il file ConfigMap usando l'esempio seguente.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Per configurare il frammento di metriche Prometheus da un DaemonSet dell'agente per ogni singolo nodo del cluster, configurare quanto segue in ConfigMap:
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE _IP è uno specifico parametro di monitoraggio di Azure per contenitori e può essere usato al posto dell'indirizzo IP del nodo. Deve essere tutti in maiuscolo. 

    - Per configurare la frammentazione delle metriche Prometeo specificando un'annotazione Pod, seguire questa procedura:

       1. In ConfigMap specificare quanto segue:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Specificare la configurazione seguente per le annotazioni pod:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Se si vuole limitare il monitoraggio a spazi dei nomi specifici per i pod con annotazioni, ad esempio includere solo i pod dedicati per i carichi di lavoro di produzione, impostare il `monitor_kubernetes_pod` su `true` in ConfigMap e aggiungere il filtro dello spazio dei nomi `monitor_kubernetes_pods_namespaces` specificando spazi dei nomi da cui rimuovere. Ad esempio, `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. Creare ConfigMap eseguendo il comando kubectl seguente: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Esempio: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    La modifica della configurazione può richiedere alcuni minuti prima di essere applicata e tutti i pod omsagent del cluster verranno riavviati. Il riavvio è un riavvio in sequenza per tutti i pod omsagent, non tutti i riavvii nello stesso momento. Al termine del riavvio, viene visualizzato un messaggio simile al seguente e include il risultato: `configmap "container-azm-ms-agentconfig" created`.

## <a name="applying-updated-configmap"></a>Applicazione di ConfigMap aggiornati

Se è già stato distribuito un ConfigMap nel cluster e si vuole aggiornarlo con una configurazione più recente, è possibile modificare il file ConfigMap usato in precedenza e quindi applicarlo usando lo stesso comando precedente, `kubectl apply -f <configmap_yaml_file.yaml`.

La modifica della configurazione può richiedere alcuni minuti prima di essere applicata e tutti i pod omsagent del cluster verranno riavviati. Il riavvio è un riavvio in sequenza per tutti i pod omsagent, non tutti i riavvii nello stesso momento. Al termine del riavvio, viene visualizzato un messaggio simile al seguente e include il risultato: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verify-configuration"></a>Verificare la configurazione 

Per verificare che la configurazione sia stata applicata correttamente, usare il comando seguente per esaminare i log da un pod agente: `kubectl logs omsagent-fdf58 -n=kube-system`. Se si verificano errori di configurazione dai pod omsagent, l'output visualizzerà errori simili ai seguenti:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Gli errori correlati all'applicazione delle modifiche di configurazione sono disponibili anche per la revisione. Sono disponibili le opzioni seguenti per eseguire ulteriori operazioni di risoluzione dei problemi relativi alle modifiche alla configurazione e al frammento di metriche Prometeo:

- Da un Log pod di Agent usando lo stesso comando `kubectl logs`. 

- Da log attivi. I log attivi mostrano errori simili ai seguenti:

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- Dalla tabella **KubeMonAgentEvents** nell'area di lavoro log Analytics. I dati vengono inviati ogni ora con gravità di *avviso* per errori di scarto e gravità dell' *errore* per gli errori di configurazione. Se non sono presenti errori, la voce nella tabella avrà dati con *informazioni*di gravità, che non segnalano errori. La proprietà **Tags** contiene altre informazioni sul Pod e sull'ID contenitore in cui si è verificato l'errore, nonché la prima occorrenza, l'ultima occorrenza e il conteggio nell'ultima ora.

Gli errori impediscono l'analisi del file da parte di omsagent, causando il riavvio e l'utilizzo della configurazione predefinita. Dopo aver corretto gli errori in ConfigMap, salvare il file YAML e applicare il ConfigMaps aggiornato eseguendo il comando: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="query-prometheus-metrics-data"></a>Eseguire query sui dati di metrica Prometeo

Per visualizzare le metriche di Prometeo ricavate da monitoraggio di Azure e gli eventuali errori di configurazione/scraping segnalati dall'agente, esaminare i [dati delle metriche di Prometeo](container-insights-log-search.md#query-prometheus-metrics-data) e la configurazione delle [query o gli errori di scraping](container-insights-log-search.md#query-config-or-scraping-errors).

## <a name="view-prometheus-metrics-in-grafana"></a>Visualizzare le metriche Prometheus in Grafana

Il monitoraggio di Azure per i contenitori supporta la visualizzazione delle metriche archiviate nell'area di lavoro Log Analytics nei dashboard di Grafana. È disponibile un modello che è possibile scaricare dal [repository dashboard](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) di Grafana per iniziare e fare riferimento a per informazioni su come eseguire query sui dati aggiuntivi dai cluster monitorati per visualizzare i dashboard Grafana personalizzati. 

## <a name="review-prometheus-data-usage"></a>Esaminare l'utilizzo dei dati di Prometeo

Per identificare il volume di inserimento di ogni dimensione di metrica in GB al giorno per comprendere se è elevato, viene fornita la query seguente.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
L'output visualizzerà risultati simili ai seguenti:

![Registrare i risultati delle query del volume di inserimento dati](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

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

![Registrare i risultati delle query del volume di inserimento dati](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

Ulteriori informazioni su come monitorare l'utilizzo dei dati e analizzare i costi sono disponibili in [gestire l'utilizzo e i costi con i log di monitoraggio di Azure](../platform/manage-cost-storage.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla configurazione delle impostazioni di raccolta agenti per stdout, stderr e le variabili di ambiente da carichi di lavoro del contenitore, vedere [qui](container-insights-agent-config.md). 