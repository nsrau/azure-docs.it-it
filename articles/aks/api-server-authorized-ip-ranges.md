---
title: Intervalli IP autorizzati al server API nel servizio Azure Kubernetes (AKS)API server authorized IP ranges in Azure Kubernetes Service (AKS)
description: Informazioni su come proteggere il cluster usando un intervallo di indirizzi IP per l'accesso al server API nel servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 570d842409fc019d24446e091f83402f4c288d7c
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640046"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Proteggere l'accesso al server API usando intervalli di indirizzi IP autorizzati nel servizio Azure Kubernetes (AKS)Secure access to the API server using authorized IP address ranges in Azure Kubernetes Service (AKS)

In Kubernetes, il server API riceve richieste per eseguire azioni nel cluster, ad esempio per creare risorse o ridimensionare il numero di nodi. Il server API è il modo centrale per interagire e gestire un cluster. Per migliorare la sicurezza del cluster e ridurre al minimo gli attacchi, il server API deve essere accessibile solo da un set limitato di intervalli di indirizzi IP.

In questo articolo viene illustrato come utilizzare gli intervalli di indirizzi IP autorizzati al server API per limitare gli indirizzi IP e i CIDR che possono accedere al piano di controllo.

> [!IMPORTANT]
> Nei nuovi cluster, gli intervalli di indirizzi IP autorizzati al server API sono supportati solo nel servizio di bilanciamento del carico SKU *Standard.On* new clusters, API server authorized IP address ranges are only supported on the Standard SKU load balancer. I cluster esistenti con il bilanciamento del carico SKU *di base* e gli intervalli di indirizzi IP autorizzati al server API configurati continueranno a funzionare così come sono, ma non possono essere migrati in un servizio di bilanciamento del carico SKU *Standard.Existing* clusters with the Basic SKU load balancer and API server authorized IP range configured will continue work as is but cannot be migrated to a Standard SKU load balancer. Questi cluster esistenti continueranno a funzionare anche se la loro versione Kubernetes o piano di controllo vengono aggiornati.

## <a name="before-you-begin"></a>Prima di iniziare

Gli intervalli IP autorizzati al server API funzionano solo per i nuovi cluster AKS creati. Questo articolo illustra come creare un cluster AKS usando l'interfaccia della riga di comando di Azure.This article shows you how to create an AKS cluster using the Azure CLI.

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.76 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Panoramica degli intervalli di indirizzi IP autorizzati del server API

Il server API Kubernetes è il modo in cui vengono esposte le API Kubernetes sottostanti. Questo componente fornisce l'interazione per gli strumenti di gestione, ad esempio `kubectl` o il dashboard di Kubernetes. AKS fornisce un master cluster single-tenant, con un server API dedicato. Per impostazione predefinita, al server API viene assegnato un indirizzo IP pubblico ed è consigliabile controllare l'accesso usando i controlli di accesso in base al ruolo.

Per proteggere l'accesso al piano di controllo /server API AKS, altrimenti accessibile pubblicamente, è possibile abilitare e utilizzare intervalli IP autorizzati. Questi intervalli IP autorizzati consentono solo intervalli di indirizzi IP definiti per comunicare con il server API. Una richiesta effettuata al server API da un indirizzo IP che non fa parte di questi intervalli IP autorizzati viene bloccata. Continuare a utilizzare il controllo degli accessi in base al ruolo per autorizzare gli utenti e le azioni richieste.

Per ulteriori informazioni sul server API e su altri componenti del cluster, vedere Concetti di base di [Kubernetes per AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Creare un cluster AKS con intervalli IP autorizzati del server API abilitati

Gli intervalli IP autorizzati al server API funzionano solo per i nuovi cluster AKS. Creare un cluster usando gli [az aks creare][az-aks-create] e specificare il parametro *--api-server-authorized-ip-ranges* per fornire un elenco di intervalli di indirizzi IP autorizzati. Questi intervalli di indirizzi IP sono in genere intervalli di indirizzi utilizzati dalle reti locali o dagli indirizzi IP pubblici. Quando si specifica un intervallo CIDR, iniziare con il primo indirizzo IP nell'intervallo. Ad esempio, *137.117.106.90/29* è un intervallo valido, ma assicurarsi di specificare il primo indirizzo IP nell'intervallo, ad esempio *137.117.106.88/29*.

> [!IMPORTANT]
> Per impostazione predefinita, il cluster usa il servizio di [bilanciamento del carico SKU Standard][standard-sku-lb] che è possibile usare per configurare il gateway in uscita. Quando si abilitano gli intervalli IP autorizzati al server API durante la creazione del cluster, anche l'indirizzo IP pubblico per il cluster è consentito per impostazione predefinita oltre agli intervalli specificati. Se si specifica *""* o nessun valore per *--api-server-authorized-ip-ranges*, gli intervalli IP autorizzati al server API verranno disabilitati. Si noti che se si usa PowerShell, usare *--api-server-authorized-ip-ranges""* (con segno di uguale) per evitare problemi di analisi.

Nell'esempio seguente viene creato un cluster a nodo singolo denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup* con gli intervalli IP autorizzati del server API abilitati. Gli intervalli di indirizzi IP consentiti sono *73.140.245.0/24*:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> È necessario aggiungere questi intervalli a un elenco Consenti:You should add these ranges to an allow list:
> - L'indirizzo IP pubblico del firewall
> - Qualsiasi intervallo che rappresenta le reti da cui verrà amministrato il cluster
> - Se si usano Spazi di sviluppo di Azure nel cluster AKS, è necessario consentire [intervalli aggiuntivi in base all'area.][dev-spaces-ranges]

> Il limite superiore per il numero di intervalli IP che è possibile specificare è 3500. 

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Specificare gli indirizzi IP in uscita per il servizio di bilanciamento del carico SKU StandardSpecify the outbound IPs for the Standard SKU load balancer

Quando si crea un cluster AKS, se si specificano gli indirizzi IP in uscita o i prefissi per il cluster, sono consentiti anche tali indirizzi o prefissi. Ad esempio:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

Nell'esempio precedente, tutti gli indirizzi IP forniti nel parametro *--load-balancer-outbound-ip-prefixes* sono consentiti insieme agli indirizzi IP nel parametro *--api-server-authorized-ip-ranges.*

In alternativa, è possibile specificare il parametro *--load-balancer-outbound-ip-prefixes* per consentire i prefissi IP del servizio di bilanciamento del carico in uscita.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Consentire solo l'IP pubblico in uscita del servizio di bilanciamento del carico SKU StandardAllow only the outbound public IP of the Standard SKU load balancer

Quando si abilitano gli intervalli IP autorizzati al server API durante la creazione del cluster, anche l'ip pubblico in uscita per il servizio di bilanciamento del carico SKU Standard per il cluster è consentito per impostazione predefinita oltre agli intervalli specificati. Per consentire solo l'IP pubblico in uscita del servizio di bilanciamento del carico SKU Standard, utilizzare *0.0.0.0/32* quando si specifica il parametro *--api-server-authorized-ip-ranges.*

Nell'esempio seguente è consentito solo l'indirizzo IP pubblico in uscita del servizio di bilanciamento del carico SKU Standard ed è possibile accedere al server API solo dai nodi all'interno del cluster.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Aggiornare gli intervalli DI indirizzi IP autorizzati del server API di un clusterUpdate a cluster's API server authorized IP ranges

Per aggiornare gli intervalli IP autorizzati del server API in un cluster esistente, utilizzare il comando [az aks update][az-aks-update] e i parametri *--api-server-authorized-ip-ranges*, *--load-balancer-outbound-ip-prefixes*, *--load-balancer-outbound-ips*o *--load-balancer-outbound-ip-prefixes* .

Nell'esempio seguente vengono aggiornati gli intervalli IP autorizzati del server API nel cluster denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*. L'intervallo di indirizzi IP da autorizzare è *73.140.245.0/24*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

È anche possibile usare *0.0.0.0/32* quando si specifica il parametro *--api-server-authorized-ip-ranges* per consentire solo l'indirizzo IP pubblico del servizio di bilanciamento del carico SKU Standard.

## <a name="disable-authorized-ip-ranges"></a>Disabilitare gli intervalli IP autorizzati

Per disabilitare gli intervalli IP autorizzati, utilizzare [az aks update][az-aks-update] e specificare un intervallo vuoto per disabilitare gli intervalli IP autorizzati al server API. Ad esempio:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati abilitati gli intervalli IP autorizzati al server API. Questo approccio è una parte di come è possibile eseguire un cluster AKS protetto.

Per ulteriori informazioni, vedere Concetti relativi alla [sicurezza per applicazioni e cluster in AKS][concepts-security] e Procedure consigliate per la sicurezza e gli aggiornamenti dei cluster in [AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
