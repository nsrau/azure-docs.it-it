---
title: Creare nodi virtuali usando l'interfaccia della riga di comando di Azure nel servizio Azure Kubernetes
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per creare un cluster del servizio Azure Kubernetes che usa nodi virtuali per eseguire i pod.
services: container-service
author: iainfoulds
ms.topic: conceptual
ms.service: container-service
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: b149ba2bccb4bfb6f459b177096afcccbbfc3051
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66742780"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Creare e configurare un cluster del servizio Azure Kubernetes per l'uso di nodi virtuali tramite l'interfaccia della riga di comando di Azure

Per ridimensionare rapidamente i carichi di lavoro dell'applicazione in un cluster del servizio Azure Kubernetes è possibile usare nodi virtuali. I nodi virtuali consentono di effettuare rapidamente il provisioning dei pod applicando al tempo di esecuzione una tariffa al secondo. Non è necessario attendere che la funzione di scalabilità automatica del cluster del servizio Azure Kubernetes distribuisca i nodi di calcolo delle macchine virtuali per eseguire pod aggiuntivi. I nodi virtuali sono supportati solo con i nodi e i POD di Linux.

Questo articolo illustra come creare e configurare le risorse della rete virtuale e un cluster del servizio Azure Kubernetes e quindi abilitare i nodi virtuali.

## <a name="before-you-begin"></a>Prima di iniziare

I nodi virtuali abilitano la comunicazione di rete tra i pod eseguiti in ACI e nel cluster del servizio Azure Kubernetes. Per consentire la comunicazione viene creata una subnet di rete virtuale e vengono assegnate autorizzazioni delegate. I nodi virtuali funzionano solo con i cluster del servizio Azure Kubernetes creati usando reti *avanzate*. Per impostazione predefinita, i cluster del servizio Azure Kubernetes vengono creati con reti *di base*. Questo articolo illustra come creare una rete virtuale e le subnet e quindi distribuire un cluster del servizio Azure Kubernetes che usa reti avanzate.

Se ACI non è stato usato in precedenza, registrare il provider di servizi con la sottoscrizione. È possibile controllare lo status del provider di registrazione di ACI usando il comando [az provider list][az-provider-list], come spiegato nell'esempio seguente:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Il provider *Microsoft.ContainerInstance* deve essere contrassegnato come *Registrato*, come spiegato nell'output di esempio seguente:

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Se il provider viene visualizzato come *NotRegistered*, registrare il provider usando [az provider register][az-provider-register] come illustrato nell'esempio seguente:

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

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

Per aprire Cloud Shell, selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

Se si preferisce installare e usare l'interfaccia della riga di comando in locale, è necessaria l'interfaccia della riga di comando di Azure versione 2.0.49 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un gruppo logico in cui le risorse di Azure vengono distribuite e gestite. Creare un gruppo di risorse con il comando [az group create][az-group-create]. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *westus*.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Crea rete virtuale

Creare una rete virtuale con il comando [az network vnet create][az-network-vnet-create]. L'esempio seguente crea il nome di rete virtuale *myVnet* con il prefisso di indirizzo *10.0.0.0/8* e una subnet denominata *myAKSSubnet*. Per impostazione predefinita, il prefisso di indirizzo di questa subnet è *10.240.0.0/16*:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Creare ora una subnet aggiuntiva per i nodi virtuali usando il comando [az network vnet subnet create][az-network-vnet-subnet-create]. L'esempio seguente crea una subnet denominata *myVirtualNodeSubnet* con il prefisso di indirizzo *10.241.0.0/16*.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal"></a>Creare un'entità servizio

Per consentire a un cluster servizio Azure Kubernetes di interagire con altre risorse di Azure viene usata un'entità servizio di Azure Active Directory. Questa entità servizio può essere creata automaticamente dall'interfaccia della riga di comando di Azure o dal portale di Azure oppure è possibile crearne una in anticipo e assegnarle autorizzazioni aggiuntive.

Creare un'entità servizio usando il comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. Il parametro `--skip-assignment` limita l'assegnazione di autorizzazioni aggiuntive.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

L'output è simile all'esempio seguente:

```
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Prendere nota di *appId* e *password*. Questi valori vengono usati nei passaggi successivi.

## <a name="assign-permissions-to-the-virtual-network"></a>Assegnare le autorizzazioni alla rete virtuale

Per consentire al cluster di usare e gestire la rete virtuale, è necessario concedere all'entità servizio del servizio Azure Kubernetes i diritti necessari per usare le risorse di rete.

Ottenere innanzitutto l'ID risorsa della rete virtuale usando [az network vnet show][az-network-vnet-show]:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Per concedere l'accesso corretto al cluster del servizio Azure Kubernetes per l'uso della rete virtuale, creare un'assegnazione di ruolo usando il comando [az role assignment create][az-role-assignment-create]. Sostituire `<appId`> e `<vnetId>` con i valori raccolti nei due passaggi precedenti.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Creare un cluster del servizio Azure Container

Distribuire un cluster del servizio Azure Kubernetes nella subnet del servizio creata nel passaggio precedente. Ottenere l'ID della subnet usando il comando [az network vnet subnet show][az-network-vnet-subnet-show]:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Usare il comando [az servizio Azure Kubernetes create][az-aks-create] per creare un cluster del servizio Azure Container. L'esempio seguente crea un cluster denominato *myAKSCluster* con un nodo. Sostituire `<subnetId>` con l'ID ottenuto nel passaggio precedente e quindi `<appId>` e `<password>` con 

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id <subnetId> \
    --service-principal <appId> \
    --client-secret <password>
```

Dopo alcuni minuti, il comando viene completato e restituisce le informazioni in formato JSON sul cluster.

## <a name="enable-virtual-nodes-addon"></a>Abilitare un componente aggiuntivo per i nodi virtuali

Per abilitare i nodi virtuali, usare il comando [az aks enable-addons][az-aks-enable-addons]. L'esempio seguente usa la subnet denominata *myVirtualNodeSubnet* creata nel passaggio precedente:

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```

## <a name="connect-to-the-cluster"></a>Connettersi al cluster

Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az servizio Azure Kubernetes get-credentials][az-aks-get-credentials]. Con questo passaggio si scaricano le credenziali e si configura l'interfaccia della riga di comando di Kubernetes per il loro uso.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Per verificare la connessione al cluster, usare il comando [kubectl get][kubectl-get] per restituire un elenco dei nodi del cluster.

```console
kubectl get nodes
```

L'output di esempio seguente illustra il singolo nodo di macchina virtuale creato e quindi il nodo virtuale per Linux, *virtual-node-aci-linux*:

```
$ kubectl get nodes

NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Distribuire un'app di esempio

Creare un file denominato `virtual-node.yaml` e copiarlo nel codice YAML seguente. Per pianificare il contenitore nel nodo, vengono definiti [nodeSelector][node-selector] e [tolleranza][toleration].

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

```console
kubectl apply -f virtual-node.yaml
```

Usare il comando [kubectl get pods][kubectl-get] con l'argomento `-o wide` per visualizzare un elenco di pod e il nodo pianificato. Si noti che il pod `aci-helloworld` è stato pianificato nel nodo `virtual-node-aci-linux`.

```
$ kubectl get pods -o wide

NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Al pod viene assegnato un indirizzo IP interno della subnet di rete virtuale di Azure delegata per l'uso con nodi virtuali.

> [!NOTE]
> Se si usano immagini archiviate nel Registro Azure Container, [configurare e usare un segreto Kubernetes][acr-aks-secrets]. Una limitazione corrente di nodi virtuali è che è possibile utilizzare Azure integrato autenticazione dell'entità servizio Active Directory. Se non si usa un segreto, i pod pianificati nei nodi virtuali non si avviano e segnalano l'errore `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Testare il pod del nodo virtuale

Per testare il pod in esecuzione nel nodo virtuale, passare all'applicazione demo con un client Web. Poiché al pod è assegnato un indirizzo IP interno, è possibile eseguire rapidamente il test di questa connettività da un altro pod nel cluster del servizio Azure Kubernetes. Creare un pod test e collegarvi una sessione terminal:

```console
kubectl run -it --rm virtual-node-test --image=debian
```

Installare `curl` nel pod usando `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Accedere all'indirizzo del pod usando `curl`, ad esempio *http://10.241.0.4* . Specificare l'indirizzo IP interno visualizzato nel comando `kubectl get pods` precedente:

```console
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

## <a name="remove-virtual-nodes"></a>Rimuovere i nodi virtuali

Se si vuole interrompere l'uso di nodi virtuali, è possibile disabilitarli usando il comando [az aks disable-addons][az aks disable-addons]. 

In primo luogo, eliminare il pod helloworld in esecuzione nel nodo virtuale:

```azurecli-interactive
kubectl delete -f virtual-node.yaml
```

Il comando seguente consente di disattivare i nodi virtuali Linux:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

A questo punto, rimuovere le risorse di rete virtuale e il gruppo di risorse:

```azurecli-interactive
# Change the name of your resource group, cluster and network resources as needed
RES_GROUP=myResourceGroup
AKS_CLUSTER=myAKScluster
AKS_VNET=myVnet
AKS_SUBNET=myVirtualNodeSubnet

# Get AKS node resource group
NODE_RES_GROUP=$(az aks show --resource-group $RES_GROUP --name $AKS_CLUSTER --query nodeResourceGroup --output tsv)

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $NODE_RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Get the service association link (SAL) ID
SAL_ID=$(az network vnet subnet show --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --query id --output tsv)/providers/Microsoft.ContainerInstance/serviceAssociationLinks/default

# Delete the default SAL ID for the subnet
az resource delete --ids $SAL_ID --api-version 2018-07-01

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --remove delegations 0
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato pianificato un pod nel nodo virtuale ed è stato assegnato un indirizzo IP interno privato. In alternativa, è possibile creare una distribuzione di servizio e instradare il traffico al pod attraverso un bilanciamento del carico o un controller in ingresso. Per altre informazioni, vedere l'argomento relativo alla [creazione di un controller di ingresso di base in servizio Azure Kubernetes][aks-basic-ingress].

I nodi virtuali sono spesso un componente di una soluzione di scalabilità nel servizio Azure Kubernetes. Per altre informazioni sulle soluzioni di scalabilità, vedere gli articoli seguenti:

- [Use the Kubernetes horizontal pod autoscaler][aks-hpa] (Usare la scalabilità automatica orizzontale dei pod di Kubernetes)
- [Use the Kubernetes cluster autoscaler][aks-cluster-autoscaler] (Usare la scalabilità automatica dei cluster di Kubernetes)
- [Estrarre l'esempio di scalabilità automatica per i nodi virtuali][virtual-node-autoscale]
- [Per ulteriori informazioni su Virtual Kubelet libreria open source][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks disable-addons]: /cli/azure/aks#az-aks-disable-addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[acr-aks-secrets]: ../container-registry/container-registry-auth-aks.md#access-with-kubernetes-secret
