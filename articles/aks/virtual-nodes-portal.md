---
title: Creare nodi virtuali usando il portale nel servizio Azure Kubernetes (AKS)
description: Informazioni su come usare il portale di Azure per creare un cluster del servizio Azure Kubernetes che usa nodi virtuali per eseguire i pod.
services: container-service
author: mlearned
ms.topic: conceptual
ms.service: container-service
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 8752d888e24e7135d488be6d1b377070a30fe4eb
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613833"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Creare e configurare un cluster del servizio Azure Kubernetes per l'uso di nodi virtuali nel portale di Azure

Per distribuire rapidamente i carichi di lavoro in un cluster del servizio Azure Kubernetes è possibile usare nodi virtuali. I nodi virtuali consentono di effettuare rapidamente il provisioning dei pod applicando al tempo di esecuzione una tariffa al secondo. In uno scenario di ridimensionamento non è necessario attendere che la funzione di scalabilità automatica del cluster del servizio Azure Kubernetes distribuisca i nodi di calcolo delle macchine virtuali per eseguire pod aggiuntivi. I nodi virtuali sono supportati solo con i nodi e i POD di Linux.

Questo articolo illustra come creare e configurare le risorse della rete virtuale e un cluster del servizio Azure Kubernetes con nodi virtuali abilitati.

## <a name="before-you-begin"></a>Prima di iniziare

I nodi virtuali abilitano la comunicazione di rete tra i pod eseguiti in ACI e nel cluster del servizio Azure Kubernetes. Per consentire la comunicazione viene creata una subnet di rete virtuale e vengono assegnate autorizzazioni delegate. I nodi virtuali funzionano solo con i cluster del servizio Azure Kubernetes creati usando reti *avanzate*. Per impostazione predefinita, i cluster del servizio Azure Kubernetes vengono creati con reti *di base*. Questo articolo illustra come creare una rete virtuale e le subnet e quindi distribuire un cluster del servizio Azure Kubernetes che usa reti avanzate.

Se ACI non è stato usato in precedenza, registrare il provider di servizi con la sottoscrizione. È possibile controllare lo stato della registrazione del provider ACI utilizzando il [elenco di provider di az][az-provider-list] comando, come illustrato nell'esempio seguente:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Il provider *Microsoft.ContainerInstance* deve essere contrassegnato come *Registrato*, come spiegato nell'output di esempio seguente:

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Se il provider viene visualizzato come *NotRegistered*, registrare il provider con [az provider register] [az-provider-register], come illustrato nell'esempio seguente:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Disponibilità internazionale

Per distribuzioni a nodo virtuale sono supportate le aree seguenti:

* Australia orientale (australiaeast)
* Stati Uniti centrali (centralus)
* Stati Uniti orientali (eastus)
* Stati Uniti orientali 2 (eastus2)
* Giappone orientale (japaneast)
* Europa settentrionale (northeurope)
* Asia sud-orientale (southeastasia)
* Stati Uniti centrali (westcentralus)
* Europa occidentale (westeurope)
* Stati Uniti occidentali (westus)
* Stati Uniti occidentali 2 (westus2)

## <a name="known-limitations"></a>Limitazioni note
Funzionalità di nodi virtuali è dipende in larga misura dal set di funzionalità di ACI. Gli scenari seguenti non sono ancora supportati con i nodi virtuali

* Utilizzo di entità servizio di pull le immagini di contenitori di AZURE. [Soluzione alternativa](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) consiste nell'usare [segreti Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Limitazioni di rete virtuale](../container-instances/container-instances-vnet.md) inclusi peering reti virtuali, i criteri di rete Kubernetes e il traffico in uscita a internet con gruppi di sicurezza di rete.
* Contenitori di Init
* [Alias dell'host](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Gli argomenti](../container-instances/container-instances-exec.md#restrictions) per exec in ACI
* [Daemonset](concepts-clusters-workloads.md#statefulsets-and-daemonsets) non distribuirà i POD per nodo virtuale
* [I nodi di Windows Server (attualmente in anteprima nel servizio contenitore di AZURE)](windows-container-cli.md) non sono supportati insieme ai nodi virtuali. È possibile usare i nodi virtuali per pianificare i contenitori di Windows Server senza la necessità di nodi di Windows Server in un cluster AKS.

## <a name="sign-in-to-azure"></a>Accedi ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Creare un cluster del servizio Azure Container

Nell'angolo superiore sinistro del portale di Azure, selezionare **Crea una risorsa** > **Servizio Kubernetes**.

Nella pagina **Informazioni di base** configurare le opzioni seguenti:

- *DETTAGLI DEL PROGETTO*: Selezionare una sottoscrizione di Azure, quindi selezionare o creare un gruppo di risorse di Azure, ad esempio *myResourceGroup*. Immettere un **nome cluster Kubernetes**, ad esempio *myAKSCluster*.
- *DETTAGLI DEI CLUSTER*: Selezionare un'area, una versione di Kubernetes e il prefisso di nome DNS per il cluster del servizio Azure Kubernetes.
- *POOL DI NODI PRIMARI*: Selezionare le dimensioni di macchina virtuale per i nodi del servizio Azure Kubernetes. Le dimensioni della macchina virtuale **non possono** essere modificate dopo che un cluster del servizio Azure Container è stato distribuito.
     - Selezionare il numero di nodi da distribuire nel cluster. Per questo articolo, impostare **Conteggio dei nodi** su *1*. Il numero di nodi **può** essere modificato dopo che il cluster è stato distribuito.

Fare clic su **Avanti: Scalabilità**.

Nel **scalabilità** pagina, selezionare *Enabled* sotto **nodi virtuali**.

![Creare un cluster del servizio Azure Kubernetes e abilitare i nodi virtuali](media/virtual-nodes-portal/enable-virtual-nodes.png)

Per impostazione predefinita viene creata un'entità servizio di Azure Active Directory. Questa entità servizio viene usata per le comunicazioni e l'integrazione del cluster con altri servizi di Azure.

Il cluster è configurato anche per le funzionalità di rete avanzate. I nodi virtuali sono configurati per l'uso della relativa subnet di rete virtuale di Azure. La subnet include autorizzazioni delegate per la connessione delle risorse di Azure nel cluster del servizio Azure Kubernetes. Se non è già presente una subnet delegata, il portale di Azure crea e configura la rete virtuale di Azure e la subnet per l'uso dei nodi virtuali.

Selezionare **Rivedi e crea**. Dopo aver completato la convalida, selezionare **Crea**.

Sono necessari alcuni minuti perché il cluster del servizio Azure Kubernetes venga creato e sia pronto per l'uso.

## <a name="connect-to-the-cluster"></a>Connettersi al cluster

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Per gestire un cluster Kubernetes, usare [kubectl][kubectl], il client da riga di comando di Kubernetes. Il client `kubectl` è preinstallato in Azure Cloud Shell.

Per aprire Cloud Shell, selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

Usare la [az aks get-credentials][az-aks-get-credentials] comando per configurare `kubectl` per connettersi al cluster Kubernetes. L'esempio seguente ottiene le credenziali per il nome cluster *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Per verificare la connessione al cluster, usare il comando [kubectl get][kubectl-get] per restituire un elenco dei nodi del cluster.

```azurecli-interactive
kubectl get nodes
```

L'output di esempio seguente illustra il singolo nodo di macchina virtuale creato e quindi il nodo virtuale per Linux, *virtual-node-aci-linux*:

```
$ kubectl get nodes

NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Distribuire un'app di esempio

In Azure Cloud Shell creare un file denominato `virtual-node.yaml` e copiarlo nel codice YAML seguente. Per pianificare il contenitore sul nodo, un [nodeSelector][node-selector] and [toleration][toleration] sono definiti. Queste impostazioni consentono di pianificare il pod nel nodo virtuale e verificare che la funzionalità sia abilitata correttamente.

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
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Eseguire l'applicazione con il [kubectl applicare][kubectl-apply] comando.

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Usare la [kubectl get pods][kubectl-get] con il `-o wide` argomento per restituire un elenco di POD e il nodo pianificato. Si noti che il pod `virtual-node-helloworld` è stato pianificato nel nodo `virtual-node-linux`.

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Al pod viene assegnato un indirizzo IP interno della subnet di rete virtuale di Azure delegata per l'uso con nodi virtuali.

> [!NOTE]
> Se si usano immagini archiviate nel registro contenitori di Azure, [configurare e usare un segreto Kubernetes][acr-aks-secrets]. Una limitazione corrente di nodi virtuali è che è possibile utilizzare Azure integrato autenticazione dell'entità servizio Active Directory. Se non si usa un segreto, i pod pianificati nei nodi virtuali non si avviano e segnalano l'errore `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Testare il pod del nodo virtuale

Per testare il pod in esecuzione nel nodo virtuale, passare all'applicazione demo con un client Web. Poiché al pod è assegnato un indirizzo IP interno, è possibile eseguire rapidamente il test di questa connettività da un altro pod nel cluster del servizio Azure Kubernetes. Creare un pod test e collegarvi una sessione terminal:

```azurecli-interactive
kubectl run -it --rm virtual-node-test --image=debian
```

Installare `curl` nel pod usando `apt-get`:

```azurecli-interactive
apt-get update && apt-get install -y curl
```

Accedere all'indirizzo del pod usando `curl`, ad esempio *http://10.241.0.4* . Specificare l'indirizzo IP interno visualizzato nel comando `kubectl get pods` precedente:

```azurecli-interactive
curl -L http://10.241.0.4
```

Viene visualizzata l'applicazione demo, come illustrato nell'output di esempio condensato seguente:

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Chiudere la sessione del terminale nel pod del test con `exit`. Quando viene terminata la sessione, il pod viene eliminato.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato pianificato un pod nel nodo virtuale ed è stato assegnato un indirizzo IP interno privato. In alternativa, è possibile creare una distribuzione di servizio e instradare il traffico al pod attraverso un bilanciamento del carico o un controller in ingresso. Per altre informazioni, vedere [creare un controller di ingresso di base nel servizio contenitore di AZURE][aks-basic-ingress].

I nodi virtuali sono un componente di una soluzione di scalabilità nel servizio Azure Kubernetes. Per altre informazioni sulle soluzioni di scalabilità, vedere gli articoli seguenti:

- [Usare la scalabilità automatica orizzontale dei pod Kubernetes][aks-hpa]
- [Usare la scalabilità automatica del cluster Kubernetes][aks-cluster-autoscaler]
- [Estrarre l'esempio di scalabilità automatica per i nodi virtuali][virtual-node-autoscale]
- [Per ulteriori informazioni su Virtual Kubelet libreria open source][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[acr-aks-secrets]: ../container-registry/container-registry-auth-aks.md#access-with-kubernetes-secret
[az-provider-list]: /cli/azure/provider#az-provider-list