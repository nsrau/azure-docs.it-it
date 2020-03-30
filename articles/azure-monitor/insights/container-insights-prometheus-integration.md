---
title: Configurare Monitoraggio di Azure per i contenitori Prometheus Integration Documenti Microsoft
description: Questo articolo descrive come configurare l'agente di Monitoraggio di Azure per i contenitori per eliminare le metriche da Prometheus con il cluster Kubernetes.This article describes how you can configure the Azure Monitor for containers agent to scrape metrics from Prometheus with your Kubernetes cluster.
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: b774bf042778ca9118a7bc9f051655b200d87659
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931430"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>Configurare il raschiamento delle metriche Prometheus con Monitoraggio di Azure per i contenitoriConfigure scraping of Prometheus metrics with Azure Monitor for containers

[Prometheus](https://prometheus.io/) è una soluzione di monitoraggio delle metriche open source popolare ed è una parte della [Cloud Native Compute Foundation](https://www.cncf.io/). Monitoraggio di Azure per i contenitori offre un'esperienza di onboarding uniforme per raccogliere le metriche di Prometheus.Azure Monitor for containers provides a seamless onboarding experience to collect Prometheus metrics. In genere, per utilizzare Prometheus, è necessario configurare e gestire un server Prometheus con un negozio. Tramite l'integrazione con Monitoraggio di Azure, non è necessario un server Prometheus.By integrating with Azure Monitor, a Prometheus server is not required. È sufficiente esporre l'endpoint delle metriche Prometheus tramite gli esportatori o i pod (applicazione) e l'agente con contenitori per Monitoraggio di Azure per i contenitori può eliminare le metriche per l'utente. 

![Architettura di monitoraggio del contenitore per Prometheus](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>La versione minima dell'agente supportata per la scraping Prometheus metrics cipro è ciprod07092019 o versione successiva e la versione dell'agente supportata per la `KubeMonAgentEvents` scrittura di errori di configurazione e agente nella tabella è ciprod10112019. Per ulteriori informazioni sulle versioni degli agenti e sugli elementi inclusi in ogni versione, vedere [le note sulla versione dell'agente.](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod) Per verificare la versione dell'agente, nella scheda **Nodo** selezionare un nodo e nel riquadro delle proprietà il valore nota della proprietà **Tag immagine agente.**

Il raschiatura delle metriche Prometheus è supportato con i cluster Kubernetes ospitati su:

- Servizio Azure Kubernetes
- Istanze di Azure Container
- Azure Stack o locale
- Azure Red Hat OpenShift

>[!NOTE]
>Per Azure Red Hat OpenShift, viene creato un file ConfigMap modello nello spazio dei nomi *openshift-azure-logging.* Non è configurato per raschiare attivamente le metriche o la raccolta dei dati dall'agente.
>

## <a name="azure-red-hat-openshift-prerequisites"></a>Azure Red Hat OpenShift Prerequisites

Prima di iniziare, verificare di essere membri del ruolo Di amministrazione cluster cliente del cluster Azure Red Hat OpenShift per configurare le impostazioni di scraping dell'agente contenitore e del prosito. Per verificare di essere membri del gruppo *osa-customer-admins,* eseguire il comando seguente:

``` bash
  oc get groups
```

L'output sarà simile al seguente:

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

Se si è membri del gruppo *osa-customer-admins,* `container-azm-ms-agentconfig` dovrebbe essere possibile elencare ConfigMap utilizzando il comando seguente:

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

L'output sarà simile al seguente:

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="prometheus-scraping-settings"></a>Impostazioni di raschiatura Prometheus

Il raschiamento attivo delle metriche di Prometheus viene eseguito da due prospettive:

* Cluster-wide: URL HTTP e destinazioni di individuazione dagli endpoint elencati di un servizio. Ad esempio, k8s servizi come kube-dns e kube-state-metrics e annotazioni di pod specifiche per un'applicazione. Le metriche raccolte in questo contesto verranno definite nella sezione ConfigMap *[Prometheus data_collection_settings.cluster]*.
* A livello di nodo: URL HTTP e individua destinazioni dagli endpoint elencati di un servizio. Le metriche raccolte in questo contesto verranno definite nella sezione ConfigMap *[Prometheus_data_collection_settings.node]*.

| Endpoint | Scope | Esempio |
|----------|-------|---------|
| Annotazione pod | A livello di cluster | Annotazioni: <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Servizio Kubernetes | A livello di cluster | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| URL/endpoint | Per nodo e/o a livello di cluster | `http://myurl:9101/metrics` |

Quando viene specificato un URL, Monitoraggio di Azure per i contenitori raschia solo l'endpoint. Quando viene specificato il servizio Kubernetes, il nome del servizio viene risolto con il server DNS del cluster per ottenere l'indirizzo IP e quindi il servizio risolto viene eliminato.

|Scope | Chiave | Tipo di dati | valore | Descrizione |
|------|-----|-----------|-------|-------------|
| A livello di cluster | | | | Specificare uno dei tre metodi seguenti per raschiare gli endpoint per le metriche. |
| | `urls` | string | Matrice delimitata da virgole | Endpoint HTTP (indirizzo IP o percorso URL valido specificato). Ad esempio `urls=[$NODE_IP/metrics]`. ($NODE_IP è un parametro specifico di Monitoraggio di Azure per i contenitori e può essere usato al posto dell'indirizzo IP del nodo. Deve essere tutto maiuscolo.) |
| | `kubernetes_services` | string | Matrice delimitata da virgole | Una serie di servizi Kubernetes per raschiare le metriche da kube-state-metrics. Ad esempio, `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Boolean | true o false | Se impostato `true` su nelle impostazioni a livello di cluster, l'agente di Monitoraggio di Azure per i contenitori eliminerà i pod Kubernetes nell'intero cluster per le annotazioni Prometheus seguenti:When set to in the cluster-wide settings, Azure Monitor for containers agent will scrape Kubernetes pods across the entire cluster for the following Prometheus annotations:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Boolean | true o false | Consente la raschiatura del baccello. `monitor_kubernetes_pods` deve essere impostato su `true`. |
| | `prometheus.io/scheme` | string | http o https | L'impostazione predefinita è la rottamazione su HTTP. Se necessario, `https`impostare su . | 
| | `prometheus.io/path` | string | Matrice delimitata da virgole | Percorso della risorsa HTTP da cui recuperare le metriche. Se il percorso `/metrics`delle metriche non è , definirlo con questa annotazione. |
| | `prometheus.io/port` | string | 9102 | Specificare una porta da cui raschiare. Se la porta non è impostata, il valore predefinito sarà 9102.If port is not set, it will default to 9102. |
| | `monitor_kubernetes_pods_namespaces` | string | Matrice delimitata da virgole | Un elenco consenti di spazi dei nomi per raschiare le metriche dai pod Kubernetes.<br> Ad esempio, usare `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| A livello di nodo | `urls` | string | Matrice delimitata da virgole | Endpoint HTTP (indirizzo IP o percorso URL valido specificato). Ad esempio `urls=[$NODE_IP/metrics]`. ($NODE_IP è un parametro specifico di Monitoraggio di Azure per i contenitori e può essere usato al posto dell'indirizzo IP del nodo. Deve essere tutto maiuscolo.) |
| A livello di nodo o a livello di clusterNode-wide or Cluster-wide | `interval` | string | 60s | Il valore predefinito dell'intervallo di raccolta è un minuto (60 secondi). È possibile modificare la raccolta per *[prometheus_data_collection_settings.node]* e/o *[prometheus_data_collection_settings.cluster]* in unità di tempo come s, m, h. |
| A livello di nodo o a livello di clusterNode-wide or Cluster-wide | `fieldpass`<br> `fielddrop`| string | Matrice delimitata da virgole | È possibile specificare determinate metriche da raccogliere o`fieldpass`meno dall'endpoint`fielddrop`impostando l'elenco allow ( ) e disallow ( ). È necessario impostare prima l'elenco Consenti. |

ConfigMaps è un elenco globale e all'agente può essere applicato un solo ConfigMap. Non è possibile avere un altro ConfigMaps overruling le raccolte.

## <a name="configure-and-deploy-configmaps"></a>Configurare e distribuire ConfigMaps

Eseguire la procedura seguente per configurare il file di configurazione di ConfigMap per i cluster Kubernetes.

1. [Scaricare](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) il modello ConfigMap yaml file e salvarlo come container-azm-ms-agentconfig.yaml.

   >[!NOTE]
   >Questo passaggio non è necessario quando si lavora con Azure Red Hat OpenShift poiché il modello ConfigMap esiste già nel cluster.

2. Modificare il file yaml di ConfigMap con le personalizzazioni per raschiare le metriche Prometheus. Se si sta modificando il file yaml di ConfigMap per `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` Azure Red Hat OpenShift, eseguire innanzitutto il comando per aprire il file in un editor di testo.

    >[!NOTE]
    >L'annotazione `openshift.io/reconcile-protect: "true"` seguente deve essere aggiunta sotto i metadati di *container-azm-ms-agentconfig* ConfigMap per impedire la riconciliazione. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Per raccogliere i servizi Kubernetes a livello di cluster, configurare il file ConfigMap utilizzando l'esempio seguente.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Per configurare la scraping delle metriche Prometheus da un URL specifico nel cluster, configurare il file ConfigMap utilizzando l'esempio seguente.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Per configurare la scraping delle metriche Prometheus dal DaemonSet di un agente per ogni singolo nodo del cluster, configurare quanto segue in ConfigMap:
    
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
        >$NODE_IP è un parametro specifico di Monitoraggio di Azure per i contenitori e può essere usato al posto dell'indirizzo IP del nodo. Deve essere tutto maiuscolo. 

    - Per configurare la raschiatura delle metriche Prometheus specificando un'annotazione del contenitore, effettuate le seguenti operazioni:

       1. In ConfigMap, specificare quanto segue:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Specificate la seguente configurazione per le annotazioni del baccello:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Se si desidera limitare il monitoraggio a spazi dei nomi specifici per i pod con annotazioni, `true` ad esempio includere solo `monitor_kubernetes_pods_namespaces` pod dedicati per i carichi di lavoro di produzione, impostare il `monitor_kubernetes_pod` filtro su in ConfigMap e aggiungere il filtro dello spazio dei nomi che specifica gli spazi dei nomi da scrape. Ad esempio, usare `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. Per i cluster diversi da Azure Red Hat OpenShift, `kubectl apply -f <configmap_yaml_file.yaml>`eseguire il comando kubectl seguente: .
    
    Esempio: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Per Azure Red Hat OpenShift, salvare le modifiche nell'editor.

Il completamento della modifica della configurazione può richiedere alcuni minuti prima di rendere effettiva tutti i pod omsagent del cluster verranno riavviati. Il riavvio è un riavvio in sequenza per tutti i pod omsagent, non per tutti contemporaneamente. Al termine dei riavvii, viene visualizzato un messaggio analogo al seguente `configmap "container-azm-ms-agentconfig" created`e include il risultato: .

È possibile visualizzare la ConfigMap aggiornata per Azure `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging`Red Hat OpenShift eseguendo il comando , . 

## <a name="applying-updated-configmap"></a>Applicazione di ConfigMap aggiornato

Se è già stato distribuito un ConfigMap nel cluster e si desidera aggiornarlo con una configurazione più recente, è possibile modificare il file ConfigMap utilizzato in precedenza e quindi applicare utilizzando gli stessi comandi di prima.

Per i cluster Kubernetes diversi da Azure Red `kubectl apply -f <configmap_yaml_file.yaml`Hat OpenShift, eseguire il comando . 

Per il cluster Azure Red Hat `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` OpenShift, eseguire il comando per aprire il file nell'editor predefinito per modificarlo e quindi salvarlo.

Il completamento della modifica della configurazione può richiedere alcuni minuti prima di rendere effettiva tutti i pod omsagent del cluster verranno riavviati. Il riavvio è un riavvio in sequenza per tutti i pod omsagent, non per tutti contemporaneamente. Al termine dei riavvii, viene visualizzato un messaggio analogo al seguente `configmap "container-azm-ms-agentconfig" updated`e include il risultato: .

## <a name="verify-configuration"></a>Verificare la configurazione

Per verificare che la configurazione sia stata applicata correttamente a un `kubectl logs omsagent-fdf58 -n=kube-system`cluster, utilizzare il comando seguente per esaminare i registri da un pod agente: . 

>[!NOTE]
>Questo comando non è applicabile al cluster Azure Red Hat OpenShift.This command is not applicable to Azure Red Hat OpenShift cluster.
> 

Se sono presenti errori di configurazione dai pod omsagent, l'output mostrerà errori simili ai seguenti:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Gli errori relativi all'applicazione delle modifiche di configurazione sono disponibili anche per la revisione. Per eseguire ulteriori operazioni di risoluzione dei problemi relativi alle modifiche alla configurazione e alla scraping delle metriche di Prometheus, sono disponibili le opzioni seguenti:

- Da un pod di `kubectl logs` dati dell'agente utilizzando lo stesso comando 
    >[!NOTE]
    >Questo comando non è applicabile al cluster Azure Red Hat OpenShift.This command is not applicable to Azure Red Hat OpenShift cluster.
    > 

- Da Live Data (anteprima). I registri Live Data (anteprima) mostrano errori simili ai seguenti:

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- Dalla tabella **KubeMonAgentEvents** nell'area di lavoro di Log Analytics. I dati vengono inviati ogni ora con gravità *di avviso* per gli errori di raschiamento e *Gravità errore* per gli errori di configurazione. Se non sono presenti errori, la voce nella tabella conterrà dati con informazioni di gravità *Info*, che non segnala errori. La proprietà **Tags** contiene ulteriori informazioni sull'ID contenitore e sul contenitore in cui si è verificato l'errore e anche la prima occorrenza, l'ultima occorrenza e il conteggio nell'ultima ora.

- Per Azure Red Hat OpenShift, controllare i log di omsagent eseguendo una ricerca nella tabella **ContainerLog** per verificare se la raccolta di log di openshift-azure-logging è abilitata.

Gli errori impediscono a omsagent di analizzare il file, causandone il riavvio e l'utilizzo della configurazione predefinita. Dopo aver corretto gli errori in ConfigMap in cluster diversi da Azure Red Hat OpenShift, salvare il file `kubectl apply -f <configmap_yaml_file.yaml`yaml e applicare le ConfigMap aggiornate eseguendo il comando: . 

Per Azure Red Hat OpenShift, modificare e salvare le `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`ConfigMap aggiornate eseguendo il comando: . .

## <a name="query-prometheus-metrics-data"></a>Dati delle metriche di Prometeo di queryQuery Prometheus metrics data

Per visualizzare le metriche prometheus eliminate da Monitoraggio azure ed eventuali errori di configurazione/eliminazione segnalati dall'agente, esaminare Dati delle [metriche di Query Prometheus](container-insights-log-search.md#query-prometheus-metrics-data) e [Query config o scraping errors.](container-insights-log-search.md#query-config-or-scraping-errors)

## <a name="view-prometheus-metrics-in-grafana"></a>Visualizza le metriche di Prometeo in Grafana

Monitoraggio di Azure per i contenitori supporta la visualizzazione delle metriche archiviate nell'area di lavoro di Log Analytics nei dashboard di Grafana.Azure Monitor for containers supports viewing metrics stored in your Log Analytics workspace in Grafana dashboards. È stato fornito un modello che è possibile scaricare dal [repository del dashboard](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) di Grafana per iniziare e fare riferimento per imparare a eseguire query su dati aggiuntivi dai cluster monitorati per visualizzarli nei dashboard Grafana personalizzati. 

## <a name="review-prometheus-data-usage"></a>Esaminare l'utilizzo dei dati PrometheusReview Prometheus data usage

Per identificare il volume di inserimento di ogni dimensione delle metriche in GB al giorno per capire se è elevato, viene fornita la query seguente.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
L'output mostrerà risultati simili ai seguenti:

![Registrare i risultati delle query del volume di inserimento datiLog query results of data ingestion volume](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

Per stimare le dimensioni di ogni metrica in GB per un mese per capire se il volume di dati ricevuti nell'area di lavoro è elevato, viene fornita la query seguente.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

L'output mostrerà risultati simili ai seguenti:

![Registrare i risultati delle query del volume di inserimento datiLog query results of data ingestion volume](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

Ulteriori informazioni su come monitorare l'utilizzo dei dati e analizzare i costi sono disponibili in [Gestire l'utilizzo e i costi con i](../platform/manage-cost-storage.md)log di Monitoraggio di Azure .

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulla configurazione delle impostazioni di raccolta degli agenti per le variabili stdout, stderr e environmental dai carichi di lavoro dei contenitori [sono disponibili qui](container-insights-agent-config.md). 
