---
title: Visualizzare i log del controller del servizio Azure Kubernetes
description: Informazioni su come abilitare e visualizzare i log per il nodo master di Kubernetes nel servizio Azure Kubernetes
services: container-service
ms.topic: article
ms.date: 01/03/2019
ms.openlocfilehash: 721ef4f60d263602b01b5957bfb9bc3b5682a2df
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048279"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>Abilitare e controllare i log del nodo master di Kubernetes nel servizio Azure Kubernetes

Con il servizio Azure Kubernetes, i componenti master, ad esempio *kube-apiserver* e *kube-controller-manager* vengono forniti come servizio gestito. Si creano e si gestiscono i nodi che eseguono *kubelet* e il runtime del contenitore e si distribuiscono le applicazioni attraverso il server API Kubernetes gestito. Per facilitare la risoluzione dei problemi dell'applicazione e dei servizi, potrebbe essere necessario visualizzare i log generati da tali componenti master. Questo articolo illustra come usare i log di Monitoraggio di Azure per abilitare i log ed eseguire query dai componenti master di Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede un cluster del servizio Azure Kubernetes esistente in esecuzione nel proprio account di Azure. Se non si dispone ancora di un cluster del servizio Azure Kubernetes, crearne uno usando l'[interfaccia della riga di comando di Azure][cli-quickstart] oppure il [portale di Azure][portal-quickstart]. I log di Monitoraggio di Azure funzionano con cluster del servizio Azure Kubernetes abilitati per il Controllo degli accessi in base al ruolo e non abilitati.

## <a name="enable-resource-logs"></a>Abilitare i log risorse

Per raccogliere e rivedere i dati da più origini, i log di Monitoraggio di Azure forniscono un linguaggio di query e un motore di analisi che offrono informazioni dettagliate per l'ambiente in uso. Viene usata un'area di lavoro per collazionare e analizzare i dati che possa integrarsi con altri servizi di Azure, ad esempio Application Insights e Centro sicurezza. Per usare una piattaforma diversa per analizzare i log, è invece possibile scegliere di inviare i log delle risorse a un account di archiviazione di Azure o a un hub eventi. Per altre informazioni, vedere [Analizzare i dati di log in Monitoraggio di Azure][log-analytics-overview].

I log di monitoraggio di Azure sono abilitati e gestiti nella portale di Azure. Per abilitare la raccolta dei log per i componenti master di Kubernetes nel cluster del servizio Azure Kubernetes, aprire il portale di Azure in un Web browser e completare i passaggi seguenti:

1. Selezionare il gruppo di risorse per il cluster servizio Azure Kubernetes, ad esempio *myResourceGroup*. Non selezionare il gruppo di risorse che contiene le singole risorse del cluster servizio Azure Kubernetes, ad esempio *MC_myResourceGroup_myservizio Azure KubernetesCluster_eastus*.
1. Sul lato sinistro, scegliere **Impostazioni di diagnostica**.
1. Selezionare il cluster AKS, ad esempio *myAKSCluster*, quindi scegliere di **aggiungere l'impostazione di diagnostica**.
1. Immettere un nome, ad esempio *myAKSClusterLogs*, quindi selezionare l'opzione **Invia a Log Analytics**.
1. Selezionare un'area di lavoro esistente o crearne una nuova. Se si crea un'area di lavoro, specificare un nome per l'area di lavoro, un gruppo di risorse e un percorso.
1. Nell'elenco dei log disponibili selezionare i log che si desidera abilitare. Per questo esempio, abilitare i log di *controllo Kube* . I log comuni includono *Kube-apiserver*, *Kube-Controller-Manager*e *Kube-Scheduler*. È possibile restituire e modificare i log raccolti dopo l'abilitazione delle aree di lavoro di Log Analytics.
1. Quando si è pronti, selezionare **Salva** per abilitare la raccolta dei log selezionati.

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
    image: nginx:1.15.5
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

Potrebbero essere necessari alcuni minuti per abilitare e visualizzare i log di diagnostica. Nel portale di Azure passare al cluster AKS e selezionare **logs (registri** ) sul lato sinistro. Chiudere la finestra *query di esempio* , se visualizzata.


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

Per visualizzare altri log, è possibile aggiornare la query per il nome di *Categoria* con *kube-controller-manager* oppure *kube-scheduler*, a seconda dei log aggiuntivi che si vuole abilitare. È possibile usare ulteriori istruzioni *where* per definire gli eventi da cercare.

Per altre informazioni su come eseguire una query e filtrare i dati di log, vedere [visualizzare o analizzare i dati raccolti con la ricerca log di log Analytics][analyze-log-analytics].

## <a name="log-event-schema"></a>Schema di eventi del log

Per semplificare l'analisi dei dati dei log, la tabella seguente illustra lo schema utilizzato per ogni evento:

| Nome campo               | Descrizione |
|--------------------------|-------------|
| *resourceId*             | Risorsa di Azure che ha generato il log |
| *time*                   | Timestamp di quando è stato caricato il log |
| *category*               | Nome del contenitore o componente che ha generato il log |
| *operationName*          | Sempre *Microsoft.ContainerService/managedClusters/diagnosticLogs/Read* |
| *properties.log*         | Query full-text del log dal componente |
| *properties.stream*      | *stderr* o *stdout* |
| *properties.pod*         | Nome del pod da cui proviene il log |
| *properties.containerID* | ID del contenitore Docker da cui proviene questo log |

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
[analyze-log-analytics]: ../azure-monitor/log-query/get-started-portal.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
