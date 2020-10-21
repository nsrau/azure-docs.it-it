---
title: Criteri di rete di Azure Kubernetes | Microsoft Docs
description: Informazioni sui criteri di rete Kubernetes per proteggere il cluster Kubernetes.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 0ecf3e5f30ee38c0d60c77df3d6aae3ad90930e9
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92332286"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Panoramica di criteri di rete di Azure Kubernetes

I criteri di rete forniscono la microsegmentazione per i pod proprio come i gruppi di sicurezza di rete (gruppi) forniscono la micro segmentazione per le macchine virtuali. L'implementazione di gestione criteri di rete di Azure (nota anche come Azure NPM) supporta la specifica di criteri di rete Kubernetes standard. È possibile usare le etichette per selezionare un gruppo di Pod e definire un elenco di regole in ingresso e in uscita per filtrare il traffico da e verso questi pod. Altre informazioni sui criteri di rete di Kubernetes sono disponibili nella [documentazione di Kubernetes](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Panoramica dei criteri di rete di Kubernetes](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

L'implementazione di Azure NPM funziona insieme alla CNI di Azure che fornisce l'integrazione di VNet per i contenitori. NPM è attualmente supportato solo in Linux. L'implementazione impone il filtraggio del traffico configurando le regole IP Consenti e nega in IPTables Linux in base ai criteri definiti. Queste regole vengono raggruppate insieme usando IPSets Linux.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Pianificazione della sicurezza per il cluster Kubernetes
Quando si implementa la sicurezza per il cluster, usare i gruppi di sicurezza di rete (gruppi) per filtrare il traffico in ingresso e in uscita dalla subnet del cluster (traffico nord-sud). Usare NPM di Azure per il traffico tra i Pod nel cluster (traffico East-West).

## <a name="using-azure-npm"></a>Uso di Azure NPM
È possibile usare Azure NPM nei modi seguenti per fornire la micro-segmentazione per i pod.

### <a name="azure-kubernetes-service-aks"></a>Servizio Azure Kubernetes
NPM è disponibile in modalità nativa in AKS e può essere abilitato al momento della creazione del cluster. Per altre informazioni, vedere [proteggere il traffico tra i pod usando i criteri di rete in Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/use-network-policies).

### <a name="aks-engine"></a>AKS-motore
Azure Kubernetes Engine è uno strumento che genera un modello di Azure Resource Manager per la distribuzione di un cluster Kubernetes in Azure. La configurazione del cluster è specificata in un file JSON che viene passato allo strumento durante la generazione del modello. Per altre informazioni sull'intero elenco delle impostazioni di cluster supportate e le relative descrizioni, vedere Motore del servizio Microsoft Azure Container - Definizione del cluster.

Per abilitare i criteri nei cluster distribuiti tramite ACS-Engine, specificare il valore "azure" per l'impostazione networkPolicy nel file di definizione del cluster.

#### <a name="example-configuration"></a>Configurazione di esempio

La configurazione di esempio JSON seguente crea una nuova rete virtuale e una subnet, quindi distribuisce un cluster Kubernetes in tale rete con l'interfaccia di rete dei contenitori di Azure. È consigliabile usare il Blocco note per modificare il file JSON. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="do-it-yourself-diy-kubernetes-clusters-in-azure"></a>I cluster Kubernetes (DIY) in Azure
 Per i cluster DIY, installare innanzitutto il plug-in CNI e abilitarlo in ogni macchina virtuale in un cluster. Per istruzioni dettagliate, vedere [Distribuire il plug-in per un cluster Kubernetes](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Una volta distribuito il cluster, eseguire il `kubectl` comando seguente per scaricare e applicare il *daemon* di Azure NPM impostato al cluster.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
La soluzione è anche open source e il codice è disponibile nel [repository Azure Container Networking](https://github.com/Azure/azure-container-networking/tree/master/npm).

## <a name="monitor-and-visualize-network-configurations-with-azure-npm"></a>Monitorare e visualizzare le configurazioni di rete con NPM di Azure
Azure NPM include metriche Prometheus informative che consentono di monitorare e comprendere meglio le configurazioni. Fornisce visualizzazioni predefinite in portale di Azure o Grafana Labs. È possibile iniziare a raccogliere queste metriche usando monitoraggio di Azure o un server Prometeo.

### <a name="benefits-of-azure-npm-metrics"></a>Vantaggi delle metriche di Azure NPM
Gli utenti in precedenza erano in grado di apprendere solo la configurazione di rete con il comando `iptables -L` eseguito all'interno di un nodo del cluster, ottenendo un output dettagliato e difficili da comprendere. Le metriche NPM offrono i vantaggi seguenti relativi ai criteri di rete, alle regole di IPTables e a IPSets.
- Fornisce informazioni dettagliate sulla relazione tra le tre e una dimensione temporale per eseguire il debug di una configurazione.
- Numero di voci in tutte le IPSets e ogni IPSet.
- Tempo impiegato per applicare un criterio con la granularità a livello di IPTable/IPSet.
 
### <a name="supported-metrics"></a>Metriche supportate
Di seguito è riportato l'elenco delle metriche supportate:

|Nome misurazione |Descrizione  |Tipo di metrica Prometeo  |Etichette  |
|---------|---------|---------|---------|
|`npm_num_policies`     |numero di criteri di rete          |Misuratore         |-         |
|`npm_num_iptables_rules`     | numero di regole di IPTables     | Misuratore        |-         |         
|`npm_num_ipsets`     |numero di IPSets         |Misuratore            |-         |
|`npm_num_ipset_entries`     |numero di voci di indirizzi IP in tutte le IPSets         |Misuratore         |-         |
|`npm_add_policy_exec_time`     |Runtime per l'aggiunta di un criterio di rete         |Riepilogo         |quantile (0,5, 0,9 o 0,99)         |
|`npm_add_iptables_rule_exec_time`     |Runtime per l'aggiunta di una regola IPTables         |Riepilogo         |quantile (0,5, 0,9 o 0,99)         |
|`npm_add_ipset_exec_time`     |Runtime per l'aggiunta di un IPSet         |Riepilogo         |quantile (0,5, 0,9 o 0,99)         |
|`npm_ipset_counts` avanzate     |numero di voci all'interno di ogni singolo IPSet         |GaugeVec         |Imposta nome & hash         |

I diversi livelli di quantile nelle metriche "exec_time" consentono di distinguere tra scenari generali e casi peggiori.

Per ogni metrica di riepilogo "exec_time" sono inoltre disponibili una metrica "exec_time_count" e "exec_time_sum".

Le metriche possono essere ricavate tramite monitoraggio di Azure per i contenitori o tramite Prometeo.

### <a name="setup-for-azure-monitor"></a>Programma di installazione per monitoraggio di Azure
Il primo passaggio consiste nell'abilitare monitoraggio di Azure per i contenitori per il cluster Kubernetes. [Per informazioni generali, vedere Panoramica di monitoraggio di Azure per i contenitori](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview). Dopo aver abilitato monitoraggio di Azure per i contenitori, configurare il [monitoraggio di Azure per i contenitori ConfigMap](https://aka.ms/container-azm-ms-agentconfig) per abilitare l'integrazione NPM e la raccolta delle metriche di Prometeo NPM. Monitoraggio di Azure per i contenitori ConfigMap include una ```integrations``` sezione con le impostazioni per la raccolta delle metriche NPM. Queste impostazioni sono disabilitate per impostazione predefinita in ConfigMap. Con l'abilitazione dell'impostazione di base ```collect_basic_metrics = true``` , raccoglie le metriche NPM di base. L'abilitazione ```collect_advanced_metrics = true``` dell'impostazione avanzata raccoglie le metriche avanzate oltre alle metriche di base. 

Dopo aver modificato il ConfigMap, salvarlo localmente e applicare il ConfigMap al cluster come indicato di seguito.

```kubectl apply -f container-azm-ms-agentconfig.yaml``` Di seguito è riportato un frammento di [monitoraggio di Azure per i contenitori ConfigMap](https://aka.ms/container-azm-ms-agentconfig), che mostra l'integrazione NPM abilitata con la raccolta di metriche avanzate.
```
integrations: |-
    [integrations.azure_network_policy_manager]
        collect_basic_metrics = false
        collect_advanced_metrics = true
```
Le metriche avanzate sono facoltative e l'attivazione attiva automaticamente la raccolta di metriche di base. Le metriche avanzate attualmente includono solo `npm_ipset_counts`

Altre informazioni su [monitoraggio di Azure per le impostazioni di raccolta dei contenitori nella mappa di configurazione](https://aka.ms/azmon-containers-agent-collection-settings-doc)

### <a name="visualization-options-for-azure-monitor"></a>Opzioni di visualizzazione per monitoraggio di Azure
Quando è abilitata la raccolta di metriche NPM, è possibile visualizzare le metriche nel portale di Azure usando informazioni dettagliate sul contenitore o in Grafana.

#### <a name="viewing-in-azure-portal-under-insights-for-the-cluster"></a>Visualizzazione in portale di Azure in informazioni dettagliate per il cluster
Aprire il portale di Azure. Una volta nelle informazioni dettagliate del cluster, passare a "Workbooks" e aprire "Network Policy Manager (NPM) Configuration".

Oltre a visualizzare la cartella di lavoro (immagini seguenti), è anche possibile eseguire una query direttamente sulle metriche di Prometeo in "log" nella sezione Insights. Ad esempio, questa query restituirà tutte le metriche raccolte.
| dove TimeGenerated > ago (5h) | dove nome contiene "npm_"

È anche possibile eseguire una query Log Analytics direttamente per le metriche. Ulteriori informazioni su di esso con Introduzione con Log Analytics query] (https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-log-search) 

#### <a name="viewing-in-grafana-dashboard"></a>Visualizzazione nel dashboard di Grafana
Configurare il server Grafana e configurare un'origine dati Log Analytics come descritto [qui](https://grafana.com/grafana/plugins/grafana-azure-monitor-datasource). Importare quindi il [dashboard di Grafana con un back-end log Analytics](https://grafana.com/grafana/dashboards/10956) in Grafana Labs.

Il dashboard include oggetti visivi simili alla cartella di lavoro di Azure. È possibile aggiungere pannelli al grafico & visualizzare le metriche NPM dalla tabella InsightsMetrics.

### <a name="setup-for-prometheus-server"></a>Installazione del server Prometeo
Alcuni utenti possono scegliere di raccogliere le metriche con un server Prometeo invece di monitoraggio di Azure per i contenitori. È sufficiente aggiungere due processi alla configurazione di Scrape per raccogliere le metriche NPM.

Per installare un server Prometeo semplice, aggiungere il repository Helm nel cluster
```
helm repo add stable https://kubernetes-charts.storage.googleapis.com
helm repo update
```
Aggiungere quindi un server
```
helm install prometheus stable/prometheus -n monitoring \
--set pushgateway.enabled=false,alertmanager.enabled=false, \
--set-file extraScrapeConfigs=prometheus-server-scrape-config.yaml
```
dove `prometheus-server-scrape-config.yaml` è costituito da
```
- job_name: "azure-npm-node-metrics"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: node
  relabel_configs:
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
- job_name: "azure-npm-cluster-metrics"
  metrics_path: /cluster-metrics
  kubernetes_sd_configs:
  - role: service
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_service_name]
    regex: npm-metrics-cluster-service
    action: keep
# Comment from here to the end to collect advanced metrics: number of entries for each IPSet
  metric_relabel_configs:
  - source_labels: [__name__]
    regex: npm_ipset_counts
    action: drop
```


È anche possibile sostituire il `azure-npm-node-metrics` processo con il contenuto seguente o incorporarlo in un processo preesistente per i pod Kubernetes:
```
- job_name: "azure-npm-node-metrics-from-pod-config"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: pod
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_pod_annotationpresent_azure_npm_scrapeable]
    action: keep
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
```

### <a name="visualization-options-for-prometheus"></a>Opzioni di visualizzazione per Prometheus
Quando si usa un server Prometeo è supportato solo il dashboard Grafana. 

Se non è già stato fatto, configurare il server Grafana e configurare un'origine dati Prometheus. Importare quindi il [dashboard di Grafana con un back-end Prometheus](https://grafana.com/grafana/dashboards/13000) in Grafana Labs.

Gli oggetti visivi per questo dashboard sono identici a quelli del dashboard con un back-end Insights/Log Analytics.

### <a name="sample-dashboards"></a>Dashboard di esempio
Di seguito sono riportati alcuni esempi di dashboard per le metriche NPM in container Insights (CI) e Grafana

#### <a name="ci-summary-counts"></a>Conteggi Riepilogo CI
![Conteggi riepilogo cartella di lavoro di Azure](media/kubernetes-network-policies/workbook-summary-counts.png)

#### <a name="ci-counts-over-time"></a>Conteggi CI nel tempo
[![Conteggi delle cartelle di lavoro di Azure nel tempo](media/kubernetes-network-policies/workbook-counts-over-time.png)](media/kubernetes-network-policies/workbook-counts-over-time.png#lightbox)

#### <a name="ci-ipset-entries"></a>Voci IPSet CI
[![Voci di IPSet della cartella di lavoro di Azure](media/kubernetes-network-policies/workbook-ipset-entries.png)](media/kubernetes-network-policies/workbook-ipset-entries.png#lightbox)

#### <a name="ci-runtime-quantiles"></a>Quantili Runtime CI
![Quantili runtime della cartella di lavoro di Azure](media/kubernetes-network-policies/workbook-runtime-quantiles.png)

#### <a name="grafana-dashboard-summary-counts"></a>Conteggi Riepilogo del dashboard Grafana
![Conteggi Riepilogo del dashboard Grafana](media/kubernetes-network-policies/grafana-summary-counts.png)

#### <a name="grafana-dashboard-counts-over-time"></a>Conteggi del dashboard Grafana nel tempo
[![Conteggi del dashboard Grafana nel tempo](media/kubernetes-network-policies/grafana-counts-over-time.png)](media/kubernetes-network-policies/grafana-counts-over-time.png#lightbox)

#### <a name="grafana-dashboard-ipset-entries"></a>Voci del dashboard Grafana IPSet
[![Voci del dashboard Grafana IPSet](media/kubernetes-network-policies/grafana-ipset-entries.png)](media/kubernetes-network-policies/grafana-ipset-entries.png#lightbox)

#### <a name="grafana-dashboard-runtime-quantiles"></a>Quantili runtime del dashboard Grafana
[![Quantili runtime del dashboard Grafana](media/kubernetes-network-policies/grafana-runtime-quantiles.png)](media/kubernetes-network-policies/grafana-runtime-quantiles.png#lightbox)



## <a name="next-steps"></a>Passaggi successivi
- Informazioni sul [servizio Azure Kubernetes](../aks/intro-kubernetes.md).
-  Informazioni sulla [rete dei contenitori](container-networking-overview.md).
- [Distribuire il plug-in per i](deploy-container-networking.md) cluster Kubernetes o i contenitori docker.

    