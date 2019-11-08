---
title: Intervalli IP autorizzati del server API in Azure Kubernetes Service (AKS)
description: Informazioni su come proteggere il cluster usando un intervallo di indirizzi IP per l'accesso al server API in Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 11/05/2019
ms.author: mlearned
ms.openlocfilehash: 558c04be77f911f40be9e8880950d1670a3c169e
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747741"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Proteggere l'accesso al server API usando gli intervalli di indirizzi IP autorizzati in Azure Kubernetes Service (AKS)

In Kubernetes, il server API riceve le richieste per eseguire le azioni nel cluster, ad esempio per creare risorse o ridimensionare il numero di nodi. Il server API è il modo centrale per interagire con e gestire un cluster. Per migliorare la sicurezza del cluster e ridurre al minimo gli attacchi, il server API deve essere accessibile solo da un set limitato di intervalli di indirizzi IP.

Questo articolo illustra come usare gli intervalli di indirizzi IP autorizzati del server API per limitare gli indirizzi IP e CIDRs che possono accedere al piano di controllo.

> [!IMPORTANT]
> Nei nuovi cluster, gli intervalli di indirizzi IP autorizzati del server API sono supportati solo nel servizio di bilanciamento del carico SKU *standard* . I cluster esistenti con lo SKU di *base* Load Balancer e gli intervalli di indirizzi IP autorizzati del server API configurati continueranno a funzionare così com'è. I CLUSER esistenti possono anche essere aggiornati e continueranno a funzionare.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si stiano lavorando con cluster che usano [kubenet][kubenet].  Con i cluster basati su [CNI (container Network Interface) di Azure][cni-networking] , non sarà necessaria la tabella di route necessaria per proteggere l'accesso.  Sarà necessario creare manualmente la tabella di route.  Per ulteriori informazioni sulla gestione delle tabelle di route, vedere [creare, modificare o eliminare una tabella di route][route-tables].

Gli intervalli IP autorizzati del server API funzionano solo per i nuovi cluster AKS creati dall'utente. Questo articolo illustra come creare un cluster AKS usando l'interfaccia della riga di comando di Azure.

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.76 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [installare l'interfaccia][install-azure-cli]della riga di comando di Azure.

## <a name="limitations"></a>Limitazioni

Quando si configurano gli intervalli IP autorizzati del server API, si applicano le limitazioni seguenti:

* Attualmente non è possibile usare Azure Dev Spaces perché è bloccata anche la comunicazione con il server API.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Panoramica degli intervalli IP autorizzati del server API

Il server API Kubernetes è il modo in cui vengono esposte le API Kubernetes sottostanti. Questo componente fornisce l'interazione per gli strumenti di gestione, ad esempio `kubectl` o il dashboard di Kubernetes. AKS fornisce un master cluster a tenant singolo con un server API dedicato. Per impostazione predefinita, al server API viene assegnato un indirizzo IP pubblico ed è necessario controllare l'accesso tramite i controlli degli accessi in base al ruolo (RBAC).

Per proteggere l'accesso al server API o al piano di controllo AKS accessibile pubblicamente, è possibile abilitare e usare gli intervalli di indirizzi IP autorizzati. Questi intervalli IP autorizzati consentono solo gli intervalli di indirizzi IP definiti per la comunicazione con il server API. Una richiesta effettuata al server API da un indirizzo IP che non fa parte di questi intervalli IP autorizzati è bloccata. Continuare a utilizzare RBAC per autorizzare gli utenti e le azioni richieste.

Per altre informazioni sul server API e altri componenti cluster, vedere [concetti di base di Kubernetes per AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Creare un cluster AKS con gli intervalli di indirizzi IP autorizzati del server API abilitati

Gli intervalli IP autorizzati del server API funzionano solo per i nuovi cluster AKS. Creare un cluster usando [AZ AKS create][az-aks-create] e specificare il parametro *--API-server-Authorized-IP-Ranges* per fornire un elenco di intervalli di indirizzi IP autorizzati. Questi intervalli di indirizzi IP sono in genere intervalli di indirizzi usati dalle reti locali o dagli indirizzi IP pubblici. Quando si specifica un intervallo CIDR, iniziare con il primo indirizzo IP compreso nell'intervallo. Ad esempio, *137.117.106.90/29* è un intervallo valido, ma assicurarsi di specificare il primo indirizzo IP nell'intervallo, ad esempio *137.117.106.88/29*.

> [!IMPORTANT]
> Per impostazione predefinita, il cluster usa il servizio di [bilanciamento del carico SKU standard][standard-sku-lb] che è possibile usare per configurare il gateway in uscita. Quando si abilitano gli intervalli IP autorizzati del server API durante la creazione del cluster, l'indirizzo IP pubblico per il cluster è consentito anche per impostazione predefinita, oltre agli intervalli specificati. Se si specifica *""* o nessun valore per *--API-server-Authorized-IP-Ranges*, gli intervalli di indirizzi IP autorizzati del server API verranno disabilitati.

L'esempio seguente crea un cluster a nodo singolo denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup* con gli intervalli di indirizzi IP autorizzati del server API abilitati. Gli intervalli di indirizzi IP consentiti sono *73.140.245.0/24*:

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
> È necessario aggiungere questi intervalli a un elenco Consenti:
> - Indirizzo IP pubblico del firewall
> - Qualsiasi intervallo che rappresenta le reti da cui verrà amministrato il cluster

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Specificare gli IP in uscita per il servizio di bilanciamento del carico SKU standard

Quando si crea un cluster AKS, se si specificano gli indirizzi IP in uscita o i prefissi per il cluster, sono consentiti anche questi indirizzi o prefissi. Ad esempio:

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

Nell'esempio precedente sono consentiti tutti gli indirizzi IP specificati nel parametro-- *Load-Balancer-Outbound-IP-prefissi* insieme agli indirizzi IP nel parametro *--API-server-Authorized-IP-Ranges* .

In alternativa, è possibile specificare il parametro *--Load-Balancer-Outbound-IP-prefissi* per consentire i PREfissi IP del servizio di bilanciamento del carico in uscita.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Consenti solo l'IP pubblico in uscita del servizio di bilanciamento del carico dello SKU standard

Quando si abilitano gli intervalli IP autorizzati del server API durante la creazione del cluster, l'indirizzo IP pubblico in uscita per il servizio di bilanciamento del carico dello SKU standard per il cluster è consentito anche per impostazione predefinita, oltre agli intervalli specificati. Per consentire solo l'IP pubblico in uscita del servizio di bilanciamento del carico dello SKU standard, usare *0.0.0.0/32* quando si specifica il parametro *--API-server-Authorized-IP-Ranges* .

Nell'esempio seguente è consentito solo l'IP pubblico in uscita del servizio di bilanciamento del carico dello SKU standard ed è possibile accedere al server API solo dai nodi all'interno del cluster.

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

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Aggiornare gli intervalli IP autorizzati del server API del cluster

Per aggiornare gli intervalli di indirizzi IP autorizzati del server API in un cluster esistente, usare il comando [AZ AKS Update][az-aks-update] e usare *--API-server-Authorized-IP-Ranges*, *--Load-Balancer-Outbound-IP-prefissis*, *--Load-Balancer-Outbound-IPS*, o *--Load-Balancer-Outbound-IP-prefissi* parametri.

L'esempio seguente aggiorna gli intervalli IP autorizzati del server API nel cluster denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*. L'intervallo di indirizzi IP da autorizzare è *73.140.245.0/24*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

È anche possibile usare *0.0.0.0/32* quando si specifica il parametro *--API-server-Authorized-IP-Ranges* per consentire solo l'indirizzo IP pubblico del servizio di bilanciamento del carico dello SKU standard.

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
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
