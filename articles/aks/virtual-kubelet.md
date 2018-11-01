---
title: Eseguire Virtual Kubelet in un cluster di Azure Kubernetes Service (AKS)
description: Informazioni su come usare Virtual Kubelet con Azure Kubernetes Service (AKS) per eseguire contenitori Linux e Windows in istanze di contenitore di Azure.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/14/2018
ms.author: iainfou
ms.openlocfilehash: cd41fba675a0814e6f2a1b17576add7811a803eb
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233481"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Usare Virtual Kubelet con Azure Kubernetes Service (AKS)

Istanze di contenitore di Azure offre un ambiente ospitato per l'esecuzione di contenitori in Azure. Quando si usa Istanze di contenitore di Azure, non è necessario gestire l'infrastruttura di calcolo sottostante, che viene gestita da Azure. Quando si eseguono i contenitori in Istanze di contenitore di Azure, ogni contenitore in esecuzione viene addebitato in base ai secondi effettivi.

Quando si usa il provider Virtual Kubelet per Istanze di contenitore di Azure, è possibile pianificare contenitori sia Linux che Windows in un'istanza di contenitore come se si trattasse di un nodo Kubernetes standard. Questa configurazione consente di sfruttare sia le funzionalità di Kubernetes che il vantaggio in termini di valore e costo di gestione delle istanze di contenitore.

> [!NOTE]
> Virtual Kubelet è un progetto open source sperimentale e deve essere usato in quanto tale. Per contribuire, registrare problemi e leggere altre informazioni su Virtual Kubelet, vedere il [progetto GitHub Virtual Kubelet][vk-github].

Questo documento descrive in dettaglio la configurazione di Virtual Kubelet per le istanze di contenitore in un servizio AKS.

## <a name="prerequisite"></a>Prerequisito

Questo documento presuppone che si abbia già un cluster AKS. Se è necessario un cluster AKS, vedere la [guida introduttiva su Azure Kubernetes Service (AKS)][aks-quick-start].

È anche necessaria l'interfaccia della riga di comando di Azure versione **2.0.33**. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Per installare Virtual Kubelet, è anche necessario [Helm](https://docs.helm.sh/using_helm/#installing-helm).

### <a name="for-rbac-enabled-clusters"></a>Per i cluster che dispongono dell’abilitazione RBAC

Se il cluster AKS è abilitato per RBAC, è necessario creare un account del servizio e un'associazione di ruolo per l'uso con Tiller. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo di Helm][helm-rbac]. Per creare un account del servizio e un'associazione di ruolo, creare un file denominato *rbac-virtualkubelet.yaml* e incollare la definizione seguente:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Applicare l'account del servizio e l'associazione con [kubectl apply][kubectl-apply] e specificare il file *rbac-virtualkubelet.yaml*, come illustrato nell'esempio seguente:

```
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Configurare Helm per l'uso dell'account del servizio Tiller:

```console
helm init --service-account tiller
```

È ora possibile procedere con l'installazione di Virtual Kubelet nel cluster AKS.

## <a name="installation"></a>Installazione

Usare il comando [az aks install-connector][aks-install-connector] per installare Virtual Kubelet. L'esempio seguente distribuisce il connettore sia Linux che Windows.

```azurecli-interactive
az aks install-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet --os-type Both
```

Per il comando `aks install-connector` sono disponibili questi argomenti.

| Argomento: | DESCRIZIONE | Obbligatoria |
|---|---|:---:|
| `--connector-name` | Nome del connettore di Istanze di contenitore di Azure.| Yes |
| `--name` `-n` | Nome del cluster gestito. | Yes |
| `--resource-group` `-g` | Nome del gruppo di risorse. | Yes |
| `--os-type` | Tipo del sistema operativo delle istanze di contenitore. Valori consentiti: Both, Linux, Windows. Valore predefinito: Linux. | No  |
| `--aci-resource-group` | Gruppo di risorse in cui creare i gruppi di contenitori di Istanze di contenitore di Azure. | No  |
| `--location` `-l` | Posizione in cui creare i gruppi di contenitori di Istanze di contenitore di Azure. | No  |
| `--service-principal` | Entità servizio usata per l'autenticazione alle API di Azure. | No  |
| `--client-secret` | Segreto associato all'entità servizio. | No  |
| `--chart-url` | URL di un chart Helm che installa il connettore di Istanze di contenitore di Azure. | No  |
| `--image-tag` | Tag dell'immagine del contenitore Virtual Kubelet. | No  |

## <a name="validate-virtual-kubelet"></a>Convalidare Virtual Kubelet

Per convalidare l'installazione di Virtual Kubelet, restituire un elenco di nodi Kubernetes con il comando [kubectl get nodes][kubectl-get].

```
$ kubectl get nodes

NAME                                    STATUS    ROLES     AGE       VERSION
aks-nodepool1-23443254-0                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-1                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-2                Ready     agent     16d       v1.9.6
virtual-kubelet-virtual-kubelet-linux   Ready     agent     4m        v1.8.3
virtual-kubelet-virtual-kubelet-win     Ready     agent     4m        v1.8.3
```

## <a name="run-linux-container"></a>Eseguire il contenitore Linux

Creare un file denominato `virtual-kubelet-linux.yaml` e copiarlo nel codice YAML seguente. Si noti che vengono usati gli elementi [nodeSelector][node-selector] e [toleration][toleration] per pianificare il contenitore nel nodo.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: linux
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Eseguire l'applicazione con il comando [kubectl create][kubectl-create].

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Usare il comando [kubectl get pods][kubectl-get] con l'argomento `-o wide` per visualizzare un elenco di pod con il nodo pianificato. Si noti che il pod `aci-helloworld` è stato pianificato nel nodo `virtual-kubelet-virtual-kubelet-linux`.

```
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
aci-helloworld-2559879000-8vmjw     1/1       Running   0          39s       52.179.3.180   virtual-kubelet-virtual-kubelet-linux
```

## <a name="run-windows-container"></a>Eseguire il contenitore Windows

Creare un file denominato `virtual-kubelet-windows.yaml` e copiarlo nel codice YAML seguente. Si noti che vengono usati gli elementi [nodeSelector][node-selector] e [toleration][toleration] per pianificare il contenitore nel nodo.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nanoserver-iis
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: microsoft/iis:nanoserver
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: windows
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Eseguire l'applicazione con il comando [kubectl create][kubectl-create].

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Usare il comando [kubectl get pods][kubectl-get] con l'argomento `-o wide` per visualizzare un elenco di pod con il nodo pianificato. Si noti che il pod `nanoserver-iis` è stato pianificato nel nodo `virtual-kubelet-virtual-kubelet-win`.

```
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
nanoserver-iis-868bc8d489-tq4st     1/1       Running   8         21m       138.91.121.91   virtual-kubelet-virtual-kubelet-win
```

## <a name="remove-virtual-kubelet"></a>Rimuovere Virtual Kubelet

Usare il comando [az aks remove-connector][aks-remove-connector] per rimuovere Virtual Kubelet. Sostituire i valori dell'argomento con il nome del connettore, il cluster AKS e il gruppo di risorse cluster AKS.

```azurecli-interactive
az aks remove-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet
```

> [!NOTE]
> Se si verificano errori durante la rimozione di entrambi i connettori del sistema operativo o si desidera rimuovere solo il connettore del sistema operativo Linux o Windows, è possibile specificare manualmente il tipo di sistema operativo. Aggiungere il parametro `--os-type` al precedente comando `az aks remove-connector` e specificare `Windows` o `Linux`.

## <a name="next-steps"></a>Passaggi successivi

In caso di problemi con Virtual Kubelet, vedere [Known quirks and workarounds][vk-troubleshooting] (Soluzioni alternative e trucchi noti). Per segnalare problemi di Virtual Kubelet [aprire un problema in GitHub][vk-issues].

Per altre informazioni su Virtual Kubelet, vedere il [progetto GitHub Virtual Kubelet][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[vk-troubleshooting]: https://github.com/virtual-kubelet/virtual-kubelet#known-quirks-and-workarounds
[vk-issues]: https://github.com/virtual-kubelet/virtual-kubelet/issues
