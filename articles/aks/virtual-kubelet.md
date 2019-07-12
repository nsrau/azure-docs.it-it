---
title: Eseguire Virtual Kubelet in un cluster del servizio Azure Kubernetes
description: Informazioni su come usare Virtual Kubelet con il servizio Azure Kubernetes per eseguire contenitori Linux e Windows in Istanze di Azure Container.
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: f18992be353d2d6cc739412d98ccd97d5e78d4c7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613869"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Usare Virtual Kubelet con il servizio Azure Kubernetes

Istanze di Azure Container offre un ambiente ospitato per l'esecuzione di contenitori in Azure. Quando si usa Istanze di contenitore di Azure, non è necessario gestire l'infrastruttura di calcolo sottostante, che viene gestita da Azure. Quando si eseguono i contenitori in Istanze di contenitore di Azure, ogni contenitore in esecuzione viene addebitato in base ai secondi effettivi.

Quando si usa il provider Virtual Kubelet per Istanze di Azure Container, è possibile pianificare contenitori sia Linux che Windows in un'istanza di contenitore come se si trattasse di un nodo Kubernetes standard. Questa configurazione consente di sfruttare sia le funzionalità di Kubernetes che il vantaggio in termini di valore e costo di gestione delle istanze di contenitore.

> [!NOTE]
> Il servizio Azure Kubernetes dispone ora del supporto incorporato per la pianificazione dei contenitori in ACI, detti *nodi virtuali*. Questi nodi virtuali supportano attualmente le istanze di contenitore di Linux. Se si ha l'esigenza di pianificare istanze di contenitore Windows, è possibile continuare a usare Virtual Kubelet. In caso contrario, è consigliabile usare i nodi virtuali invece delle istruzioni manuali di Virtual Kubelet indicate in questo articolo. È possibile iniziare a usare i nodi virtuali usando il [Azure CLI][virtual-nodes-cli] or [Azure portal][virtual-nodes-portal].
>
> Virtual Kubelet è un progetto open source sperimentale e deve essere usato in quanto tale. Per poter inviare contributi, problemi di file e leggere ulteriori informazioni su virtual kubelet, vedere la [progetto Virtual Kubelet GitHub][vk-github].

## <a name="before-you-begin"></a>Prima di iniziare

Questo documento presuppone che si abbia già un cluster servizio Azure Kubernetes. Se è necessario un cluster del servizio contenitore di AZURE, vedere la [Guida introduttiva di Azure Kubernetes Service (AKS)][aks-quick-start].

È inoltre necessaria la versione di Azure CLI **2.0.65** o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Per installare Virtual Kubelet, installare e configurare [Helm][aks-helm] nel cluster AKS. Assicurarsi che sia di Tiller [configurato per l'uso con Kubernetes RBAC](#for-rbac-enabled-clusters), se necessario.

### <a name="register-container-instances-feature-provider"></a>Registrare il provider di funzionalità di istanze di contenitore

Se non è stato precedentemente usato il servizio di istanza di contenitore di Azure (ACI), registrare il provider di servizi con la sottoscrizione. È possibile controllare lo stato della registrazione del provider ACI utilizzando il [elenco di provider di az][az-provider-list] comando, come illustrato nell'esempio seguente:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Il provider *Microsoft.ContainerInstance* deve essere contrassegnato come *Registrato*, come spiegato nell'output di esempio seguente:

```console
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Se il provider viene visualizzato come *NotRegistered*, registrare il provider usando la [register di az provider][az-provider-register] come illustrato nell'esempio seguente:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

### <a name="for-rbac-enabled-clusters"></a>Per i cluster che dispongono dell’abilitazione RBAC

Se il cluster servizio Azure Kubernetes è abilitato per RBAC, è necessario creare un account del servizio e un'associazione di ruolo per l'uso con Tiller. Per altre informazioni, vedere [controllo degli accessi in base al ruolo di Helm][helm-rbac]. Per creare un account del servizio e un'associazione di ruolo, creare un file denominato *rbac-virtualkubelet.yaml* e incollare la definizione seguente:

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

Applicare l'account del servizio e stabilisce un'associazione [kubectl applicare][kubectl-apply] e specificare le *rbac-virtuale-kubelet.yaml* file, come illustrato nell'esempio seguente:

```console
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Configurare Helm per l'uso dell'account del servizio Tiller:

```console
helm init --service-account tiller
```

È ora possibile procedere con l'installazione di Virtual Kubelet nel cluster servizio Azure Kubernetes.

## <a name="installation"></a>Installazione

Usare la [az aks install-connettore][aks-install-connector] comando per installare Virtual Kubelet. L'esempio seguente distribuisce il connettore sia Linux che Windows.

```azurecli-interactive
az aks install-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

Questi argomenti sono disponibili per il [az aks install-connettore][aks-install-connector] comando.

| Argomento: | Descrizione | Obbligatoria |
|---|---|:---:|
| `--connector-name` | Nome del connettore di Istanze di contenitore di Azure.| Sì |
| `--name` `-n` | Nome del cluster gestito. | Sì |
| `--resource-group` `-g` | Nome del gruppo di risorse. | Sì |
| `--os-type` | Tipo di sistema operativo delle istanze di contenitore. Valori consentiti: Entrambi, Linux, Windows. Valore predefinito: Linux. | No |
| `--aci-resource-group` | Gruppo di risorse in cui creare i gruppi di contenitori di Istanze di contenitore di Azure. | No |
| `--location` `-l` | Posizione in cui creare i gruppi di contenitori di Istanze di contenitore di Azure. | No |
| `--service-principal` | Entità servizio usata per l'autenticazione alle API di Azure. | No |
| `--client-secret` | Segreto associato all'entità servizio. | No |
| `--chart-url` | URL di un chart Helm che installa il connettore di Istanze di contenitore di Azure. | No |
| `--image-tag` | Tag dell'immagine del contenitore Virtual Kubelet. | No |

## <a name="validate-virtual-kubelet"></a>Convalidare Virtual Kubelet

Per verificare che sia stato installato Virtual Kubelet, restituire un elenco di nodi Kubernetes usando il [kubectl ottenere nodi][kubectl-get] comando:

```console
$ kubectl get nodes

NAME                                             STATUS   ROLES   AGE   VERSION
aks-nodepool1-56577038-0                         Ready    agent   11m   v1.12.8
virtual-kubelet-virtual-kubelet-linux-eastus     Ready    agent   39s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
virtual-kubelet-virtual-kubelet-windows-eastus   Ready    agent   37s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
```

## <a name="run-linux-container"></a>Eseguire il contenitore Linux

Creare un file denominato `virtual-kubelet-linux.yaml` e copiarlo nel codice YAML seguente. Si noti che un [nodeSelector][node-selector] and [toleration][toleration] vengono usati per pianificare il contenitore nel nodo.

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

Eseguire l'applicazione con il [kubectl create][kubectl-create] comando.

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Usare la [kubectl get pods][kubectl-get] con il `-o wide` argomento di output di un elenco dei POD con il nodo pianificato. Si noti che il pod `aci-helloworld` è stato pianificato nel nodo `virtual-kubelet-virtual-kubelet-linux`.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
aci-helloworld-7b9ffbf946-rx87g   1/1     Running   0          22s     52.224.147.210   virtual-kubelet-virtual-kubelet-linux-eastus
```

## <a name="run-windows-container"></a>Eseguire il contenitore Windows

Creare un file denominato `virtual-kubelet-windows.yaml` e copiarlo nel codice YAML seguente. Si noti che un [nodeSelector][node-selector] and [toleration][toleration] vengono usati per pianificare il contenitore nel nodo.

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

Eseguire l'applicazione con il [kubectl create][kubectl-create] comando.

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Usare la [kubectl get pods][kubectl-get] con il `-o wide` argomento di output di un elenco dei POD con il nodo pianificato. Si noti che il pod `nanoserver-iis` è stato pianificato nel nodo `virtual-kubelet-virtual-kubelet-windows`.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
nanoserver-iis-5d999b87d7-6h8s9   1/1     Running   0          47s     52.224.143.39    virtual-kubelet-virtual-kubelet-windows-eastus
```

## <a name="remove-virtual-kubelet"></a>Rimuovere Virtual Kubelet

Usare la [az aks remove-connector][aks-remove-connector] comando per rimuovere Virtual Kubelet. Sostituire i valori dell'argomento con il nome del connettore, il cluster servizio Azure Kubernetes e il gruppo di risorse cluster servizio Azure Kubernetes.

```azurecli-interactive
az aks remove-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

> [!NOTE]
> Se si verificano errori durante la rimozione di entrambi i connettori del sistema operativo o si desidera rimuovere solo il connettore del sistema operativo Linux o Windows, è possibile specificare manualmente il tipo di sistema operativo. Aggiungere il parametro `--os-type` al precedente comando `az aks remove-connector` e specificare `Windows` o `Linux`.

## <a name="next-steps"></a>Passaggi successivi

Per rilevare possibili problemi con Virtual Kubelet, vedere la [noti quirks e soluzioni alternative][vk-troubleshooting]. To report problems with the Virtual Kubelet, [open a GitHub issue][vk-issues].

Altre informazioni su Virtual Kubelet nel [progetto Virtual Kubelet GitHub][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector
[virtual-nodes-cli]: virtual-nodes-cli.md
[virtual-nodes-portal]: virtual-nodes-portal.md
[aks-helm]: kubernetes-helm.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[vk-troubleshooting]: https://github.com/virtual-kubelet/virtual-kubelet#known-quirks-and-workarounds
[vk-issues]: https://github.com/virtual-kubelet/virtual-kubelet/issues
