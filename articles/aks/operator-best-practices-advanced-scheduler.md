---
title: Procedure consigliate per l'operatore - Funzionalità avanzate dell'utilità di pianificazione nel servizio Azure Kubernetes (AKS)
description: Procedure consigliate per l'operatore del cluster per l'uso delle funzionalità avanzate dell'utilità di pianificazione, come taint e tolleranze, selettori di nodo e affinità oppure affinità tra pod e anti-affinità, nel servizio Azure Kubernetes (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: mlearned
ms.openlocfilehash: 4caa4219d2bf7558dbdf71e92e4993722c6e8f6a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614866"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Procedure consigliate per le funzionalità avanzate dell'utilità di pianificazione nel servizio Azure Kubernetes (AKS)

Quando si gestiscono i cluster nel servizio Azure Kubernetes (AKS), spesso è necessario isolare i team e i carichi di lavoro. L'utilità di pianificazione di Kubernetes offre funzionalità avanzate che consentono di controllare quali pod possono essere pianificati su determinati nodi o come distribuire in modo appropriato le applicazioni con più pod all'interno del cluster. 

Questo articolo sulle procedure consigliate è incentrato sulle funzionalità di pianificazione avanzate di Kubernetes per gli operatori del cluster. In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Usare taint e tolleranze per limitare i pod che possono essere pianificati sui nodi
> * Stabilire che i pod vengano eseguiti su determinati nodi con i selettori di nodo o l'affinità tra nodi
> * Suddividere o raggruppare i pod con l'affinità tra pod o l'anti-affinità

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Fornire nodi dedicati tramite taint e tolleranze

**Indicazioni sulle procedure consigliate**. Limitare l'accesso a nodi specifici per le applicazioni a elevato utilizzo di risorse, come i controller di ingresso. Mantenere le risorse dei nodi disponibili per i carichi di lavoro che le richiedono e non consentire la pianificazione di altri carichi di lavoro sui nodi.

Quando si crea il cluster servizio Azure Kubernetes, è possibile distribuire i nodi con supporto GPU o un numero elevato di potenti CPU. Questi nodi vengono spesso usati per i carichi di lavoro di elaborazione dati di grandi dimensioni, ad esempio Machine Learning (ML) o intelligenza artificiale. Poiché questo tipo di hardware è in genere una risorsa nodo costosa da distribuire, limitare i carichi di lavoro che possono essere pianificati su questi nodi. È invece consigliabile dedicare alcuni nodi del cluster per eseguire i servizi in ingresso e impedire altri carichi di lavoro.

Viene fornito il supporto per i diversi nodi con più pool di nodi. Un cluster AKS fornisce uno o più pool di nodi. Supporto per più pool di nodi nel servizio contenitore di AZURE è attualmente in anteprima.

L'utilità di pianificazione di Kubernetes può usare taint e tolleranze per limitare i carichi di lavoro che possono essere eseguiti sui nodi.

* Un **taint** viene applicato a un nodo per indicare che possono essere pianificati solo pod specifici.
* Una **tolleranza** viene quindi applicata a un pod per *tollerare* un taint di un nodo.

Quando si distribuisce un pod in un cluster servizio Azure Kubernetes, Kubernetes pianifica solo i pod sui nodi in cui una tolleranza è allineata con un taint. Ad esempio, si supponga di che avere un pool di nodi del cluster servizio contenitore di AZURE per i nodi con GPU supporta. Si definisce il nome, ad esempio *gpu*, quindi un valore per la pianificazione. Se si imposta questo valore su *NoSchedule*, l'utilità di pianificazione di Kubernetes non può pianificare i pod sul nodo se il pod non definisce la tolleranza appropriata.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

Con un taint applicato ai nodi, si definisce quindi una tolleranza nella specifica del pod che consente la pianificazione sui nodi. L'esempio seguente definisce `sku: gpu` e `effect: NoSchedule` per tollerare il taint applicato al nodo nel passaggio precedente:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Quando questo pod viene distribuito, ad esempio usando `kubectl apply -f gpu-toleration.yaml`, Kubernetes può pianificare correttamente il pod sui nodi con il taint applicato. Questo isolamento logico consente di controllare l'accesso alle risorse all'interno di un cluster.

Quando si applicano i taint, contattare gli sviluppatori e i proprietari delle applicazioni per consentire loro di definire le tolleranze richieste nelle proprie distribuzioni.

Per altre informazioni sulle taints e tolerations, vedere [applicando taints e tolerations][k8s-taints-tolerations].

Per altre informazioni su come usare più pool di nodi nel servizio contenitore di AZURE, vedere [creare e gestire più pool di nodi per un cluster di AKS][use-multiple-node-pools].

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Comportamento di taints e tolerations nel servizio contenitore di AZURE

Quando si aggiorna un pool di nodi nel servizio contenitore di AZURE, taints e tolerations seguono un modello di set di come vengono applicati a nuovi nodi:

- **Cluster predefinito senza supporto di scalabilità di macchine virtuali**
  - Si supponga che si dispone di un cluster a due nodi - *node1* e *node2*. Quando si esegue l'aggiornamento, un nodo aggiuntivo (*node3*) viene creato.
  - Il taints dal *node1* vengono applicate ai *node3*, quindi *node1* viene quindi eliminata.
  - Viene creato un altro nodo nuovo (denominato *node1*, poiché precedente *node1* è stato eliminato) e il *node2* taints vengono applicate al nuovo *node1*. Quindi *node2* viene eliminato.
  - In sostanza *node1* diventa *node3*, e *node2* diventa *node1*.

- **I cluster che usano macchine virtuali di set di scalabilità** (attualmente in anteprima nel servizio contenitore di AZURE)
  - Anche in questo caso, si supponga che si dispone di un cluster a due nodi - *node1* e *node2*. Si aggiorna il pool di nodi.
  - Vengono creati altri due nodi, *node3* e *Nodo4*, mentre il taints vengono passati nello rispettivamente.
  - Originale *node1* e *node2* vengono eliminati.

Quando si ridimensiona un pool di nodi nel servizio contenitore di AZURE, taints e tolerations non hanno da progettazione.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Controllare la pianificazione dei pod tramite selettori di nodo e affinità

**Indicazioni sulle procedure consigliate**. Controllare la pianificazione dei pod sui nodi tramite selettori di nodo, affinità tra nodi o affinità tra pod. Queste impostazioni consentono all'utilità di pianificazione di Kubernetes di isolare in modo logico i carichi di lavoro, ad esempio in base all'hardware nel nodo.

Taint e tolleranze vengono usati per isolare in modo logico le risorse con un limite rigido: se il pod non tollera il taint di un nodo, non viene pianificato su quel nodo. Un approccio alternativo consiste nell'usare i selettori di nodo. Si etichettano i nodi, in modo da indicare l'archiviazione SSD collegata in locale o una quantità elevata di memoria, quindi si definisce un selettore di nodo nella specifica del pod. Kubernetes pianifica quindi tali pod su un nodo corrispondente. A differenza delle tolleranze, i pod senza un selettore di nodo corrispondente possono essere pianificati sui nodi con etichetta. Questo comportamento consente l'utilizzo delle risorse inutilizzate sui nodi, dando tuttavia la priorità ai pod che definiscono il selettore di nodo corrispondente.

Verrà ora preso in esame un esempio di nodi con una quantità elevata di memoria. Questi nodi possono dare priorità ai pod che richiedono una quantità elevata di memoria. Per assicurarsi che le risorse non rimangano inattive, consentono anche l'esecuzione di altri pod.

```console
kubectl label node aks-nodepool1 hardware:highmem
```

Una specifica del pod aggiunge quindi la proprietà `nodeSelector` per definire un selettore di nodo corrispondente all'etichetta impostata su un nodo:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
    nodeSelector:
      hardware: highmem
```

Quando si usano queste opzioni dell'utilità di pianificazione, contattare gli sviluppatori e i proprietari delle applicazioni per consentire loro di definire correttamente le specifiche dei pod.

Per altre informazioni sull'uso dei selettori di nodo, vedere [assegnazione di POD ai nodi][k8s-node-selector].

### <a name="node-affinity"></a>Affinità tra nodi

Un selettore di nodo è un modo semplice per assegnare pod a un determinato nodo. Una maggiore flessibilità è disponibile con l'*affinità tra nodi*. Usando l'affinità tra nodi, si definisce cosa accade se il pod non può essere associato a un nodo. È possibile *richiedere* che l'utilità di pianificazione di Kubernetes trovi una corrispondenza tra un pod e un host con etichetta. In alternativa, è possibile *preferire* una corrispondenza ma consentire la pianificazione del pod su un host differente se la corrispondenza non è disponibile.

Nell'esempio seguente l'affinità tra nodi viene impostata su *requiredDuringSchedulingIgnoredDuringExecution*. Questa affinità richiede che l'utilità di pianificazione di Kubernetes usi un nodo con un'etichetta corrispondente. Se non è disponibile alcun nodo, il pod rimane in attesa che la pianificazione continui. Per consentire la pianificazione del pod su un nodo differente, è invece possibile impostare il valore su *preferredDuringScheduledIgnoreDuringExecution*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: hardware
            operator: In
            values: highmem
```

La parte *IgnoredDuringExecution* dell'impostazione indica che se le etichette del nodo vengono modificate, il pod non deve essere rimosso dal nodo. L'utilità di pianificazione di Kubernetes usa solo le etichette dei nodi aggiornate per la pianificazione dei nuovi pod, e non i pod già pianificati sui nodi.

Per altre informazioni, vedere [affinità e anti-affinità][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Affinità tra pod e anti-affinità

Un ultimo approccio per consentire all'utilità di pianificazione di Kubernetes di isolare in modo logico i carichi di lavoro consiste nell'usare l'affinità tra pod o l'anti-affinità. Le impostazioni definiscono che i pod *non devono* essere pianificati su un nodo con un pod corrispondente esistente o che *devono* essere pianificati. Per impostazione predefinita, l'utilità di pianificazione di Kubernetes tenta di pianificare più pod in un set di repliche tra i nodi. È possibile definire più regole specifiche in base a questo comportamento.

Un buon esempio è un'applicazione Web che usa anche una cache Redis di Azure. È possibile usare le regole di anti-affinità dei pod per fare in modo che l'utilità di pianificazione di Kubernetes distribuisca le repliche tra i nodi. È quindi possibile usare le regole di affinità per assicurarsi che ogni componente dell'app web è stata pianificata nello stesso host di una cache corrispondente. La distribuzione dei pod tra i nodi è simile alla seguente:

| **Nodo 1** | **Nodo 2** | **Nodo 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

Questo esempio è una distribuzione più complessa rispetto all'uso dei selettori di nodo o dell'affinità tra nodi. La distribuzione garantisce il controllo sul modo in cui Kubernetes pianifica i pod sui nodi e isola in modo logico le risorse. Per un esempio completo di questa applicazione web con Cache di Azure, ad esempio Redis, vedere [Colocate POD nello stesso nodo][k8s-pod-affinity].

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato le funzionalità avanzate dell'utilità di pianificazione di Kubernetes. Per altre informazioni sulle operazioni cluster in servizio Azure Kubernetes, vedere le procedure consigliate seguenti:

* [Isolamento multi-tenancy e cluster][aks-best-practices-scheduler]
* [Funzionalità di base dell'utilità di pianificazione di Kubernetes][aks-best-practices-scheduler]
* [Autenticazione e autorizzazione][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-taints-tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[k8s-node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[k8s-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[k8s-pod-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#always-co-located-in-the-same-node

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[use-multiple-node-pools]: use-multiple-node-pools.md
