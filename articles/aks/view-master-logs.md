---
title: Visualizzare i log del controller del servizio Azure Kubernetes
description: Informazioni su come abilitare e visualizzare i log per il nodo master di Kubernetes nel servizio Azure Kubernetes
services: container-service
ms.topic: article
ms.date: 10/14/2020
ms.openlocfilehash: 59e7259ae352491bddebe054f2c34bdc810ea48a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183227"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Abilitare e controllare i log del nodo master di Kubernetes nel servizio Azure Kubernetes

Con il servizio Azure Kubernetes, i componenti master, ad esempio *kube-apiserver* e *kube-controller-manager* vengono forniti come servizio gestito. Si creano e si gestiscono i nodi che eseguono *kubelet* e il runtime del contenitore e si distribuiscono le applicazioni attraverso il server API Kubernetes gestito. Per facilitare la risoluzione dei problemi dell'applicazione e dei servizi, potrebbe essere necessario visualizzare i log generati da tali componenti master. Questo articolo illustra come usare i log di Monitoraggio di Azure per abilitare i log ed eseguire query dai componenti master di Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede un cluster del servizio Azure Kubernetes esistente in esecuzione nel proprio account di Azure. Se non si dispone ancora di un cluster del servizio Azure Kubernetes, crearne uno usando l'[interfaccia della riga di comando di Azure][cli-quickstart] oppure il [portale di Azure][portal-quickstart]. Log di monitoraggio di Azure funziona con i cluster Kubernetes RBAC, Azure RBAC e non RBAC abilitati.

## <a name="enable-resource-logs"></a>Abilitare i log risorse

Per raccogliere e rivedere i dati da più origini, i log di Monitoraggio di Azure forniscono un linguaggio di query e un motore di analisi che offrono informazioni dettagliate per l'ambiente in uso. Viene usata un'area di lavoro per collazionare e analizzare i dati che possa integrarsi con altri servizi di Azure, ad esempio Application Insights e Centro sicurezza. Per usare una piattaforma diversa per analizzare i log, è invece possibile scegliere di inviare i log delle risorse a un account di archiviazione di Azure o a un hub eventi. Per altre informazioni, vedere [Analizzare i dati di log in Monitoraggio di Azure][log-analytics-overview].

I log di monitoraggio di Azure sono abilitati e gestiti nella portale di Azure. Per abilitare la raccolta dei log per i componenti master di Kubernetes nel cluster del servizio Azure Kubernetes, aprire il portale di Azure in un Web browser e completare i passaggi seguenti:

1. Selezionare il gruppo di risorse per il cluster servizio Azure Kubernetes, ad esempio *myResourceGroup*. Non selezionare il gruppo di risorse che contiene le singole risorse del cluster servizio Azure Kubernetes, ad esempio *MC_myResourceGroup_myservizio Azure KubernetesCluster_eastus*.
1. Sul lato sinistro, scegliere **Impostazioni di diagnostica**.
1. Selezionare il cluster AKS, ad esempio *myAKSCluster*, quindi scegliere di **aggiungere l'impostazione di diagnostica**.
1. Immettere un nome, ad esempio *myAKSClusterLogs*, quindi selezionare l'opzione **Invia a Log Analytics**.
1. Selezionare un'area di lavoro esistente o crearne una nuova. Se si crea un'area di lavoro, specificare un nome per l'area di lavoro, un gruppo di risorse e un percorso.
1. Nell'elenco dei log disponibili selezionare i log che si desidera abilitare. Per questo esempio, abilitare i log *Kube-audit* e *Kube-audit-admin* . I log comuni includono *Kube-apiserver*, *Kube-Controller-Manager* e *Kube-Scheduler*. È possibile restituire e modificare i log raccolti dopo l'abilitazione delle aree di lavoro di Log Analytics.
1. Quando si è pronti, selezionare **Salva** per abilitare la raccolta dei log selezionati.

## <a name="log-categories"></a>Categorie di log

Oltre alle voci scritte da Kubernetes, i log di controllo del progetto hanno anche voci da AKS.

I log di controllo vengono registrati in tre categorie: *Kube-audit*, *Kube-audit-admin* e *Guard*.

- La categoria *Kube-audit* contiene tutti i dati del log di controllo per ogni evento di controllo, inclusi *Get*, *List*, *create*, *Update*, *Delete*, *patch* e *post*.
- La categoria *Kube-audit-admin* è un subset della categoria *Kube-audit* log. *Kube-audit-admin* riduce significativamente il numero di log escludendo gli eventi di controllo *Get* ed *List* dal log.
- La categoria *Guard* è gestita Azure ad e controlli RBAC di Azure. Per Azure AD gestiti: token in, informazioni sull'utente. Per il controllo degli accessi in base al ruolo di Azure: verifiche di accesso.

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Pianificare un pod di test nel cluster servizio Azure Kubernetes

Per generare alcuni log, creare un nuovo pod nel cluster servizio Azure Kubernetes. Il seguente esempio di manifesto YAML può essere usato per creare un'istanza NGINX di base. Creare un file denominato `nginx.yaml` in un editor a scelta e incollare il contenuto seguente:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

Creare il pod con il comando [kubectl create][kubectl-create] e specificare il file YAML, come illustrato nell'esempio seguente:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Visualizzare i log raccolti

Potrebbero essere necessari fino a 10 minuti per abilitare e visualizzare i log di diagnostica.

> [!NOTE]
> Se sono necessari tutti i dati dei log di controllo per la conformità o altri scopi, raccoglierli e archiviarli in una risorsa di archiviazione economica, ad esempio l'archiviazione BLOB. Usare la categoria di log *Kube-audit-admin* per raccogliere e salvare un set significativo di dati dei log di controllo a scopo di monitoraggio e avviso.

Nel portale di Azure passare al cluster AKS e selezionare **logs (registri** ) sul lato sinistro. Chiudere la finestra *query di esempio* , se visualizzata.

Sul lato sinistro scegliere **Log**. Per visualizzare i log di *controllo Kube* , immettere la query seguente nella casella di testo:

```
AzureDiagnostics
| where Category == "kube-audit"
| project log_s
```

È probabile che vengano restituiti molti log. Per limitare l'ambito della query per visualizzare i log relativi al Pod NGINX creato nel passaggio precedente, aggiungere un'istruzione *where* aggiuntiva per cercare *nginx* , come illustrato nella query di esempio seguente:

```
AzureDiagnostics
| where Category == "kube-audit"
| where log_s contains "nginx"
| project log_s
```

Per visualizzare i log di *Kube-audit-admin* , immettere la query seguente nella casella di testo:

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| project log_s
```

In questo esempio la query Mostra tutti i processi di creazione in *Kube-audit-admin*. Ci sono probabilmente molti risultati restituiti, per limitare l'ambito della query per visualizzare i log relativi al Pod NGINX creato nel passaggio precedente, aggiungere un'istruzione *where* aggiuntiva per cercare *nginx* , come illustrato nella query di esempio seguente.

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| where log_s contains "nginx"
| project log_s
```


Per altre informazioni su come eseguire una query e filtrare i dati di log, vedere [visualizzare o analizzare i dati raccolti con la ricerca log di log Analytics][analyze-log-analytics].

## <a name="log-event-schema"></a>Schema di eventi del log

AKS registra gli eventi seguenti:

* [AzureActivity][log-schema-azureactivity]
* [AzureDiagnostics][log-schema-azurediagnostics]
* [AzureMetrics][log-schema-azuremetrics]
* [ContainerImageInventory][log-schema-containerimageinventory]
* [ContainerInventory][log-schema-containerinventory]
* [ContainerLog][log-schema-containerlog]
* [ContainerNodeInventory][log-schema-containernodeinventory]
* [ContainerServiceLog][log-schema-containerservicelog]
* [Heartbeat][log-schema-heartbeat]
* [InsightsMetrics][log-schema-insightsmetrics]
* [KubeEvents][log-schema-kubeevents]
* [KubeHealth][log-schema-kubehealth]
* [KubeMonAgentEvents][log-schema-kubemonagentevents]
* [KubeNodeInventory][log-schema-kubenodeinventory]
* [KubePodInventory][log-schema-kubepodinventory]
* [KubeServices][log-schema-kubeservices]
* [Prestazioni][log-schema-perf]

## <a name="log-roles"></a>Ruoli di log

| Ruolo                     | Descrizione |
|--------------------------|-------------|
| *aksService*             | Nome visualizzato nel log di controllo per l'operazione del piano di controllo (da hcpService) |
| *MasterClient*           | Nome visualizzato nel log di controllo per MasterClientCertificate, il certificato ottenuto da AZ AKS Get-credentials |
| *nodeclient*             | Nome visualizzato per ClientCertificate, utilizzato dai nodi di Agent |

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato descritto come abilitare e analizzare i log per i componenti master di Kubernetes nel cluster servizio Azure Kubernetes. Per monitorare ulteriormente e risolvere eventuali problemi, è anche possibile [visualizzare i log di Kubelet][kubelet-logs] e [abilitare l'accesso ai nodi SSH][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../azure-monitor/log-query/log-query-overview.md
[analyze-log-analytics]: ../azure-monitor/log-query/log-analytics-tutorial.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[log-schema-azureactivity]: /azure/azure-monitor/reference/tables/azureactivity
[log-schema-azurediagnostics]: /azure/azure-monitor/reference/tables/azurediagnostics
[log-schema-azuremetrics]: /azure/azure-monitor/reference/tables/azuremetrics
[log-schema-containerimageinventory]: /azure/azure-monitor/reference/tables/containerimageinventory
[log-schema-containerinventory]: /azure/azure-monitor/reference/tables/containerinventory
[log-schema-containerlog]: /azure/azure-monitor/reference/tables/containerlog
[log-schema-containernodeinventory]: /azure/azure-monitor/reference/tables/containernodeinventory
[log-schema-containerservicelog]: /azure/azure-monitor/reference/tables/containerservicelog
[log-schema-heartbeat]: /azure/azure-monitor/reference/tables/heartbeat
[log-schema-insightsmetrics]: /azure/azure-monitor/reference/tables/insightsmetrics
[log-schema-kubeevents]: /azure/azure-monitor/reference/tables/kubeevents
[log-schema-kubehealth]: /azure/azure-monitor/reference/tables/kubehealth
[log-schema-kubemonagentevents]: /azure/azure-monitor/reference/tables/kubemonagentevents
[log-schema-kubenodeinventory]: /azure/azure-monitor/reference/tables/kubenodeinventory
[log-schema-kubepodinventory]: /azure/azure-monitor/reference/tables/kubepodinventory
[log-schema-kubeservices]: /azure/azure-monitor/reference/tables/kubeservices
[log-schema-perf]: /azure/azure-monitor/reference/tables/perf