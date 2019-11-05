---
title: Intervalli IP autorizzati del server API in Azure Kubernetes Service (AKS)
description: Informazioni su come proteggere il cluster usando un intervallo di indirizzi IP per l'accesso al server API in Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 8418499cc3e094162ac7483aaa6c71e74db95ae1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472961"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Proteggere l'accesso al server API usando gli intervalli di indirizzi IP autorizzati in Azure Kubernetes Service (AKS)

In Kubernetes, il server API riceve le richieste per eseguire le azioni nel cluster, ad esempio per creare risorse o ridimensionare il numero di nodi. Il server API è il modo centrale per interagire con e gestire un cluster. Per migliorare la sicurezza del cluster e ridurre al minimo gli attacchi, il server API deve essere accessibile solo da un set limitato di intervalli di indirizzi IP.

Questo articolo illustra come usare gli intervalli di indirizzi IP autorizzati del server API per limitare gli indirizzi IP e CIDRs che possono accedere al piano di controllo.

> [!IMPORTANT]
> Nei nuovi cluster, gli intervalli di indirizzi IP autorizzati del server API sono supportati solo nel servizio di bilanciamento del carico SKU *standard* . I cluster esistenti con lo SKU di *base* Load Balancer e gli intervalli di indirizzi IP autorizzati del server API configurati continueranno a funzionare così com'è. I CLUSER esistenti possono anche essere aggiornati e continueranno a funzionare.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si stiano lavorando con cluster che usano [kubenet][kubenet].  Con i cluster basati su [CNI (container Network Interface) di Azure][cni-networking] , non sarà necessaria la tabella di route necessaria per proteggere l'accesso.  Sarà necessario creare manualmente la tabella di route.  Per ulteriori informazioni, vedere [gestione delle tabelle di route](https://docs.microsoft.com/azure/virtual-network/manage-route-table) .

Gli intervalli IP autorizzati del server API funzionano solo per i nuovi cluster AKS creati dall'utente. Questo articolo illustra come creare un cluster AKS usando l'interfaccia della riga di comando di Azure.

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.76 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [installare l'interfaccia][install-azure-cli]della riga di comando di Azure.

## <a name="limitations"></a>Limitazioni

Quando si configurano gli intervalli IP autorizzati del server API, si applicano le limitazioni seguenti:

* Attualmente non è possibile usare Azure Dev Spaces perché è bloccata anche la comunicazione con il server API.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Panoramica degli intervalli IP autorizzati del server API

Il server API Kubernetes è il modo in cui vengono esposte le API Kubernetes sottostanti. Questo componente fornisce l'interazione per gli strumenti di gestione, ad esempio `kubectl` o il dashboard di Kubernetes. AKS fornisce un master cluster a tenant singolo con un server API dedicato. Per impostazione predefinita, al server API viene assegnato un indirizzo IP pubblico ed è necessario controllare l'accesso tramite i controlli degli accessi in base al ruolo (RBAC).

Per proteggere l'accesso al server API o al piano di controllo AKS accessibile pubblicamente, è possibile abilitare e usare gli intervalli di indirizzi IP autorizzati. Questi intervalli IP autorizzati consentono solo gli intervalli di indirizzi IP definiti per la comunicazione con il server API. Una richiesta effettuata al server API da un indirizzo IP che non fa parte di questi intervalli IP autorizzati è bloccata. È necessario continuare a usare il controllo degli accessi in base al ruolo per autorizzare gli utenti e le azioni richieste.

Per altre informazioni sul server API e altri componenti cluster, vedere [concetti di base di Kubernetes per AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>Creare un cluster del servizio Azure Container

Gli intervalli IP autorizzati del server API funzionano solo per i nuovi cluster AKS. Non è possibile abilitare gli intervalli IP autorizzati come parte dell'operazione di creazione del cluster. Se si prova ad abilitare gli intervalli IP autorizzati come parte del processo di creazione del cluster, i nodi del cluster non sono in grado di accedere al server API durante la distribuzione perché l'indirizzo IP in uscita non è definito in quel momento.

Per prima cosa, creare un cluster usando il comando [AZ AKS create][az-aks-create] . Nell'esempio seguente viene creato un cluster a nodo singolo denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-cluster-with-authorized-ip-ranges"></a>Aggiorna cluster con intervalli IP autorizzati

Per impostazione predefinita, il cluster usa il servizio di [bilanciamento del carico SKU standard][standard-sku-lb], che è possibile usare per configurare il gateway in uscita. Usare il comando [AZ Network Public-IP list][az-network-public-ip-list] e specificare il gruppo di risorse del cluster AKS, che in genere inizia con *MC_* . Viene visualizzato l'indirizzo IP pubblico per il cluster, che può essere consentito. Usare il comando [AZ AKS Update][az-aks-update] e specificare il parametro *--API-server-Authorized-IP-Ranges* per consentire l'indirizzo IP del cluster. Ad esempio:

```azurecli-interactive
RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSCluster
```

Per abilitare gli intervalli IP autorizzati del server API, usare il comando [AZ AKS Update][az-aks-update] e specificare il parametro *--API-server-Authorized-IP-Ranges* per fornire un elenco di intervalli di indirizzi IP autorizzati. Questi intervalli di indirizzi IP sono in genere intervalli di indirizzi usati dalle reti locali o dagli indirizzi IP pubblici. Quando si specifica un intervallo CIDR, iniziare con il primo indirizzo IP compreso nell'intervallo. Ad esempio, *137.117.106.90/29* è un intervallo valido, ma assicurarsi di specificare il primo indirizzo IP nell'intervallo, ad esempio *137.117.106.88/29*.

L'esempio seguente abilita gli intervalli IP autorizzati del server API nel cluster denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*. Gli intervalli di indirizzi IP da autorizzare sono *172.0.0.0/16* (intervallo di indirizzi Pod/nodi) e *168.10.0.0/18* (ServiceCidr):

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 172.0.0.0/16,168.10.0.0/18
```

> [!NOTE]
> È necessario aggiungere questi intervalli a un elenco Consenti:
> - Indirizzo IP pubblico del firewall
> - CIDR del servizio
> - Intervallo di indirizzi per le subnet, con i nodi e i Pod
> - Qualsiasi intervallo che rappresenta le reti da cui verrà amministrato il cluster

## <a name="disable-authorized-ip-ranges"></a>Disabilitare gli intervalli IP autorizzati

Per disabilitare gli intervalli IP autorizzati, usare [AZ AKS Update][az-aks-update] e specificare un intervallo vuoto per disabilitare gli intervalli IP autorizzati del server API. Ad esempio:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati abilitati gli intervalli di indirizzi IP autorizzati del server API. Questo approccio è una parte del modo in cui è possibile eseguire un cluster AKS sicuro.

Per altre informazioni, vedere [concetti relativi alla sicurezza per le applicazioni e i cluster in AKS][concepts-security] e [procedure consigliate per la sicurezza e gli aggiornamenti del cluster in AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[standard-sku-lb]: load-balancer-standard.md
