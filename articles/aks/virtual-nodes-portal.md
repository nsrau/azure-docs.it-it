---
title: Creare nodi virtuali usando il portale nel servizio Azure Kubernetes (AKS)
description: Informazioni su come usare il portale di Azure per creare un cluster del servizio Azure Kubernetes che usa nodi virtuali per eseguire i pod.
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 696821e12e963292107cad5b22f00a9816a94b25
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616415"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Creare e configurare un cluster del servizio Azure Kubernetes per l'uso di nodi virtuali nel portale di Azure

Per distribuire rapidamente i carichi di lavoro in un cluster del servizio Azure Kubernetes è possibile usare nodi virtuali. I nodi virtuali consentono di effettuare rapidamente il provisioning dei pod applicando al tempo di esecuzione una tariffa al secondo. In uno scenario di ridimensionamento non è necessario attendere che la funzione di scalabilità automatica del cluster del servizio Azure Kubernetes distribuisca i nodi di calcolo delle macchine virtuali per eseguire pod aggiuntivi. I nodi virtuali sono supportati solo con pod e nodi Linux.Virtual nodes are only supported with Linux pods and nodes.

Questo articolo illustra come creare e configurare le risorse della rete virtuale e un cluster del servizio Azure Kubernetes con nodi virtuali abilitati.

## <a name="before-you-begin"></a>Prima di iniziare

I nodi virtuali consentono la comunicazione di rete tra pod eseguiti in istanze del contenitore di Azure (ACI) e nel cluster AKS. Per consentire la comunicazione viene creata una subnet di rete virtuale e vengono assegnate autorizzazioni delegate. I nodi virtuali funzionano solo con i cluster del servizio Azure Kubernetes creati usando reti *avanzate*. Per impostazione predefinita, i cluster del servizio Azure Kubernetes vengono creati con reti *di base*. Questo articolo illustra come creare una rete virtuale e le subnet e quindi distribuire un cluster del servizio Azure Kubernetes che usa reti avanzate.

Se ACI non è stato usato in precedenza, registrare il provider di servizi con la sottoscrizione. È possibile controllare lo status del provider di registrazione di ACI usando il comando [az provider list][az-provider-list], come spiegato nell'esempio seguente:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Il provider *Microsoft.ContainerInstance* deve essere contrassegnato come *Registrato*, come spiegato nell'output di esempio seguente:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Se il provider viene visualizzato come *NotRegistered*, registrare il provider usando [az provider register][az-provider-register] come illustrato nell'esempio seguente:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Disponibilità a livello di area

Per le distribuzioni di nodi virtuali sono supportate le aree seguenti:The following regions are supported for virtual node deployments:

* Australia Est (australiaeast)
* Stati Uniti centrali (centralus)
* Stati Uniti orientali (eastus)
* Stati Uniti orientali 2 (eastus2)
* Giappone Orientale (giapponeest)
* Europa settentrionale (northeurope)
* Sud-est asiatico (sud-estasiatica)
* Stati Uniti centro-occidentali (centro-occidentale)
* Europa occidentale (westeurope)
* Stati Uniti occidentali (westus)
* Stati Uniti occidentali 2 (westus2)

## <a name="known-limitations"></a>Limitazioni note
La funzionalità dei nodi virtuali dipende fortemente dal set di funzionalità di ACI. Gli scenari seguenti non sono ancora supportati con i nodi virtuali

* Utilizzo dell'entità servizio per eseguire il pull delle immagini ACR. [La soluzione consiste](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) nell'utilizzare i [segreti di Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Limitazioni della rete virtuale,](../container-instances/container-instances-vnet.md) inclusi il peering della rete virtuale, i criteri di rete Kubernetes e il traffico in uscita verso Internet con i gruppi di sicurezza di rete.
* Contenitori Init
* [Alias host](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argomenti](../container-instances/container-instances-exec.md#restrictions) per exec in ACIArguments for exec in ACI
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) non distribuirà pod al nodo virtuale
* [I nodi di Windows Server (attualmente in anteprima in AKS)](windows-container-cli.md) non sono supportati insieme ai nodi virtuali. È possibile utilizzare i nodi virtuali per pianificare i contenitori di Windows Server senza la necessità di nodi Windows Server in un cluster AKS.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-an-aks-cluster"></a>Creare un cluster AKS

Nell'angolo superiore sinistro del portale di Azure selezionare **Crea una risorsa** > **Servizio Kubernetes**.

Nella pagina **Informazioni di base** configurare le opzioni seguenti:

- *DETTAGLI DEL PROGETTO*: selezionare una sottoscrizione di Azure, quindi selezionare o creare un gruppo di risorse di Azure, ad esempio *myResourceGroup*. Immettere un **nome cluster Kubernetes**, ad esempio *myAKSCluster*.
- *DETTAGLI CLUSTER*: selezionare un'area, una versione di Kubernetes e il prefisso di nome DNS per il cluster del servizio Azure Kubernetes.
- *PRIMARY NODE POOL*: Selezionare una dimensione VM per i nodi AKS. Le dimensioni della macchina virtuale **non possono** essere modificate dopo che un cluster del servizio Azure Container è stato distribuito.
     - Selezionare il numero di nodi da distribuire nel cluster. Per questo articolo, impostare **Conteggio dei nodi** su *1*. Il numero di nodi **può** essere modificato dopo che il cluster è stato distribuito.

Fare clic su **Avanti: Scala**.

Nella pagina **Scala** selezionare *Abilitato* in **Nodi virtuali**.

![Creare un cluster del servizio Azure Kubernetes e abilitare i nodi virtuali](media/virtual-nodes-portal/enable-virtual-nodes.png)

Per impostazione predefinita viene creata un'entità servizio di Azure Active Directory. Questa entità servizio viene usata per le comunicazioni e l'integrazione del cluster con altri servizi di Azure.

Il cluster è configurato anche per le funzionalità di rete avanzate. I nodi virtuali sono configurati per l'uso della relativa subnet di rete virtuale di Azure. La subnet include autorizzazioni delegate per la connessione delle risorse di Azure nel cluster del servizio Azure Kubernetes. Se non è già presente una subnet delegata, il portale di Azure crea e configura la rete virtuale di Azure e la subnet per l'uso dei nodi virtuali.

Selezionare **Revisione e creazione**. Dopo aver completato la convalida, selezionare **Crea**.

Sono necessari alcuni minuti perché il cluster del servizio Azure Kubernetes venga creato e sia pronto per l'uso.

## <a name="connect-to-the-cluster"></a>Stabilire la connessione al cluster

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Per gestire un cluster Kubernetes, usare [kubectl][kubectl], il client da riga di comando di Kubernetes. Il client `kubectl` è preinstallato in Azure Cloud Shell.

Per aprire Cloud Shell, selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È inoltre possibile avviare Cloud Shell in [https://shell.azure.com/bash](https://shell.azure.com/bash)una scheda separata del browser accedendo a . Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

Per configurare [ per la connessione al cluster Kubernetes, usare il comando az servizio Azure Kubernetes get-credentials][az-aks-get-credentials]`kubectl`. L'esempio seguente ottiene le credenziali per il nome cluster *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Per verificare la connessione al cluster, usare il comando [kubectl get][kubectl-get] per restituire un elenco dei nodi del cluster.

```console
kubectl get nodes
```

L'output di esempio seguente illustra il singolo nodo di macchina virtuale creato e quindi il nodo virtuale per Linux, *virtual-node-aci-linux*:

```output
NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Distribuire un'app di esempio

In Azure Cloud Shell creare un file denominato `virtual-node.yaml` e copiarlo nel codice YAML seguente. Per pianificare il contenitore nel nodo, vengono definiti [nodeSelector][node-selector] e [tolleranza][toleration]. Queste impostazioni consentono di pianificare il pod nel nodo virtuale e verificare che la funzionalità sia abilitata correttamente.

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

Eseguire l'applicazione con il comando [kubectl apply][kubectl-apply].

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Usare il comando [kubectl get pods][kubectl-get] con l'argomento `-o wide` per visualizzare un elenco di pod e il nodo pianificato. Si noti che il pod `virtual-node-helloworld` è stato pianificato nel nodo `virtual-node-linux`.

```console
kubectl get pods -o wide
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Al pod viene assegnato un indirizzo IP interno della subnet di rete virtuale di Azure delegata per l'uso con nodi virtuali.

> [!NOTE]
> Se si usano immagini archiviate nel Registro Azure Container, [configurare e usare un segreto Kubernetes][acr-aks-secrets]. Una limitazione corrente dei nodi virtuali è che non è possibile usare l'autenticazione dell'entità servizio integrata di Azure AD. Se non si usa un segreto, i pod pianificati nei nodi virtuali non si avviano e segnalano l'errore `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Testare il pod del nodo virtuale

Per testare il pod in esecuzione nel nodo virtuale, passare all'applicazione demo con un client Web. Poiché al pod è assegnato un indirizzo IP interno, è possibile eseguire rapidamente il test di questa connettività da un altro pod nel cluster del servizio Azure Kubernetes. Creare un pod test e collegarvi una sessione terminal:

```console
kubectl run -it --rm virtual-node-test --image=debian
```

Installare `curl` nel pod usando `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Ora accedi all'indirizzo `curl`del tuo *http://10.241.0.4*pod utilizzando , ad esempio . Specificare l'indirizzo IP interno visualizzato nel comando `kubectl get pods` precedente:

```console
curl -L http://10.241.0.4
```

Viene visualizzata l'applicazione demo, come illustrato nell'output di esempio condensato seguente:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Chiudere la sessione del terminale nel pod del test con `exit`. Quando viene terminata la sessione, il pod viene eliminato.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato pianificato un pod nel nodo virtuale ed è stato assegnato un indirizzo IP interno privato. In alternativa, è possibile creare una distribuzione di servizio e instradare il traffico al pod attraverso un bilanciamento del carico o un controller in ingresso. Per altre informazioni, vedere l'argomento relativo alla [creazione di un controller di ingresso di base in servizio Azure Kubernetes][aks-basic-ingress].

I nodi virtuali sono un componente di una soluzione di scalabilità nel servizio Azure Kubernetes. Per altre informazioni sulle soluzioni di scalabilità, vedere gli articoli seguenti:

- [Use the Kubernetes horizontal pod autoscaler][aks-hpa] (Usare la scalabilità automatica orizzontale dei pod di Kubernetes)
- [Use the Kubernetes cluster autoscaler][aks-cluster-autoscaler] (Usare la scalabilità automatica dei cluster di Kubernetes)
- [Estrarre l'esempio di scalabilità automatica per i nodi virtualiCheck out the Autoscale sample for Virtual Nodes][virtual-node-autoscale]
- [Per saperne di più sulla libreria open source di Virtual Kubelet][virtual-kubelet-repo]

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
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register