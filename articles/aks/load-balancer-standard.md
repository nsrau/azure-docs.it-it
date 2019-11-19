---
title: Usare un servizio di bilanciamento del carico con SKU standard in Azure Kubernetes Service (AKS)
description: Informazioni su come usare un servizio di bilanciamento del carico con uno SKU standard per esporre i servizi con Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: ef826239bc916b4ccf25785f92397286017d00f7
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171395"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Usare un servizio di bilanciamento del carico con SKU standard in Azure Kubernetes Service (AKS)

Per fornire l'accesso alle applicazioni tramite i servizi Kubernetes di tipo `LoadBalancer` in Azure Kubernetes Service (AKS), è possibile usare una Azure Load Balancer. Un servizio di bilanciamento del carico in esecuzione su AKS può essere usato come servizio di bilanciamento del carico interno o esterno. Un servizio di bilanciamento del carico interno rende accessibile un servizio Kubernetes solo alle applicazioni in esecuzione nella stessa rete virtuale del cluster AKS. Un servizio di bilanciamento del carico esterno riceve uno o più indirizzi IP pubblici per il traffico in ingresso e rende accessibile esternamente un servizio Kubernetes usando gli indirizzi IP pubblici.

Azure Load Balancer è disponibile in due SKU: *Basic* e *Standard*. Per impostazione predefinita, quando si crea un cluster AKS viene usato lo SKU *standard* . L'uso di un servizio di bilanciamento del carico con SKU *standard* fornisce funzionalità e funzionalità aggiuntive, ad esempio dimensioni del pool back-end maggiori e zone di disponibilità. Prima di scegliere quale usare, è importante comprendere le differenze tra i bilanciamenti del carico *standard* e *Basic* . Dopo aver creato un cluster AKS, non è possibile modificare lo SKU del servizio di bilanciamento del carico per quel cluster. Per altre informazioni sugli SKU *Basic* e *standard* , vedere [confronto tra SKU di bilanciamento del carico di Azure][azure-lb-comparison].

Questo articolo presuppone una conoscenza di base dei concetti relativi a Kubernetes e Azure Load Balancer. Per altre informazioni, vedere [Kubernetes Core Concepts for Azure Kubernetes Service (AKS)][kubernetes-concepts] e informazioni su [Azure Load Balancer][azure-lb].

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.74 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che sia presente un cluster AKS con lo SKU *Standard* Azure Load Balancer. Se è necessario un cluster AKS, vedere la Guida introduttiva di AKS [usando l'interfaccia della][aks-quickstart-cli] riga di comando di Azure o [l'portale di Azure][aks-quickstart-portal].

L'entità servizio cluster AKS necessita anche dell'autorizzazione per gestire le risorse di rete se si usa una subnet o un gruppo di risorse esistente. In generale, assegnare il ruolo *collaboratore rete* all'entità servizio per le risorse Delegate. Per altre informazioni sulle autorizzazioni, vedere [delega dell'accesso AKS ad altre risorse di Azure][aks-sp].

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Passaggio da uno SKU di base Load Balancer allo SKU standard

Se si dispone di un cluster esistente con lo SKU Basic Load Balancer, quando si esegue la migrazione per l'uso di un cluster con lo Load Balancer SKU standard sono presenti importanti differenze di comportamento.

Ad esempio, l'esecuzione di distribuzioni Blue/Green per la migrazione dei cluster è una pratica comune, dato che il tipo di `load-balancer-sku` di un cluster può essere definito solo in fase di creazione del cluster. Tuttavia, i bilanciamenti del carico *SKU Basic* usano gli indirizzi IP dello *SKU Basic* che non sono compatibili con i bilanciamenti del carico *SKU standard* perché richiedono indirizzi IP *SKU standard* . Quando si esegue la migrazione dei cluster per aggiornare Load Balancer SKU, sarà necessario un nuovo indirizzo IP con lo SKU di un indirizzo IP compatibile.

Per altre considerazioni su come eseguire la migrazione dei cluster, vedere [la documentazione relativa alle considerazioni sulla migrazione](acs-aks-migration.md) per visualizzare un elenco di argomenti importanti da considerare durante la migrazione. Le limitazioni seguenti sono anche importanti differenze comportamentali da tenere presente quando si usa il servizio di bilanciamento del carico SKU standard in AKS.

### <a name="limitations"></a>Limitazioni

Quando si creano e si gestiscono cluster AKS che supportano un servizio di bilanciamento del carico con lo SKU *standard* , si applicano le limitazioni seguenti:

* È necessario almeno un IP pubblico o un prefisso IP per consentire il traffico in uscita dal cluster AKS. L'IP pubblico o il prefisso IP è necessario anche per mantenere la connettività tra il piano di controllo e i nodi dell'agente, nonché per mantenere la compatibilità con le versioni precedenti di AKS. Sono disponibili le opzioni seguenti per specificare gli indirizzi IP pubblici o i prefissi IP con un servizio di bilanciamento del carico SKU *standard* :
    * Fornire gli indirizzi IP pubblici personali.
    * Fornire i prefissi IP pubblici.
    * Specificare un numero massimo di 100 per consentire al cluster AKS di creare che molti indirizzi IP pubblici dello SKU *standard* nello stesso gruppo di risorse creato dal cluster AKS, che in genere è denominato con *MC_* all'inizio. AKS assegna l'indirizzo IP pubblico al servizio di bilanciamento del carico dello SKU *standard* . Per impostazione predefinita, un IP pubblico verrà creato automaticamente nello stesso gruppo di risorse del cluster AKS, se non è specificato alcun indirizzo IP pubblico, prefisso IP pubblico o numero di indirizzi IP. È anche necessario consentire indirizzi pubblici ed evitare di creare criteri di Azure che vietino la creazione di IP.
* Quando si usa lo SKU *standard* per un servizio di bilanciamento del carico, è necessario usare Kubernetes versione *1,13 o successiva*.
* La definizione dello SKU del servizio di bilanciamento del carico può essere eseguita solo quando si crea un cluster AKS. Non è possibile modificare lo SKU del servizio di bilanciamento del carico dopo che è stato creato un cluster AKS.
* È possibile usare un solo tipo di SKU di Load Balancer (Basic o standard) in un singolo cluster.
* *Standard* I bilanciamenti del carico SKU supportano solo indirizzi IP SKU *standard* .

## <a name="configure-the-load-balancer-to-be-internal"></a>Configurare il servizio di bilanciamento del carico come interno

È anche possibile configurare il servizio di bilanciamento del carico in modo che sia interno e non esponga un indirizzo IP pubblico. Per configurare il servizio di bilanciamento del carico come interno, aggiungere `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` come annotazione al servizio *LoadBalancer* . [Qui][internal-lb-yaml]è possibile visualizzare un manifesto YAML di esempio, oltre a informazioni dettagliate su un servizio di bilanciamento del carico interno.

## <a name="scale-the-number-of-managed-public-ips"></a>Ridimensionare il numero di indirizzi IP pubblici gestiti

Quando si usa un servizio di bilanciamento del carico con SKU *standard* con indirizzi IP pubblici in uscita gestiti, che vengono creati per impostazione predefinita, è possibile ridimensionare il numero di indirizzi IP pubblici in uscita gestiti usando il parametro *Load-Balancer-Managed-IP-count* .

Per aggiornare un cluster esistente, eseguire il comando seguente. Questo parametro può essere impostato anche in fase di creazione del cluster per avere più indirizzi IP pubblici in uscita gestiti.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

Nell'esempio precedente il numero di indirizzi IP pubblici in uscita gestiti viene impostato su *2* per il cluster *myAKSCluster* in *myResourceGroup*. 

È anche possibile usare il parametro *Load-Balancer-Managed-IP-count* per impostare il numero iniziale di indirizzi IP pubblici in uscita gestiti durante la creazione del cluster aggiungendo il parametro `--load-balancer-managed-outbound-ip-count` e impostandolo sul valore desiderato. Il numero predefinito di indirizzi IP pubblici in uscita gestiti è 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Fornire gli indirizzi IP pubblici o i prefissi per l'uscita

Quando si usa un servizio di bilanciamento del carico con SKU *standard* , il cluster AKS crea automaticamente un indirizzo IP pubblico nello stesso gruppo di risorse creato per il cluster AKS e assegna l'indirizzo IP pubblico al servizio di bilanciamento del carico dello SKU *standard* . In alternativa, è possibile assegnare un indirizzo IP pubblico personalizzato al momento della creazione del cluster oppure aggiornare le proprietà del servizio di bilanciamento del carico di un cluster esistente.

Con l'introduzione di più indirizzi IP o prefissi, è possibile definire più servizi di backup quando si definisce l'indirizzo IP dietro un singolo oggetto di bilanciamento del carico. L'endpoint in uscita di nodi specifici dipenderà dal servizio a cui sono associati.

> [!IMPORTANT]
> È necessario usare gli indirizzi IP pubblici dello SKU *standard* per l'uscita con lo SKU *standard* del servizio di bilanciamento del carico. È possibile verificare lo SKU degli indirizzi IP pubblici usando il comando [AZ Network Public-IP Show][az-network-public-ip-show] :
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Usare il comando [AZ Network Public-IP Show][az-network-public-ip-show] per elencare gli ID degli indirizzi IP pubblici.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Il comando precedente mostra l'ID per l'indirizzo IP pubblico di *myPublicIP* nel gruppo di risorse *myResourceGroup* .

Usare il comando *AZ AKS Update* con il parametro *Load-Balancer-Outbound-IPS* per aggiornare il cluster con gli indirizzi IP pubblici.

Nell'esempio seguente viene usato il parametro *Load-Balancer-Outbound-IPS* con gli ID del comando precedente.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

È anche possibile usare i prefissi IP pubblici per l'uscita con il servizio di bilanciamento del carico dello SKU *standard* . L'esempio seguente usa il comando [AZ Network Public-IP prefix Show][az-network-public-ip-prefix-show] per elencare gli ID dei prefissi IP pubblici:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Il comando precedente mostra l'ID del prefisso IP pubblico *myPublicIPPrefix* nel gruppo di risorse *myResourceGroup* .

Nell'esempio seguente viene usato il parametro *Load-Balancer-Outbound-IP-Prefixes* con gli ID del comando precedente.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> Gli IP pubblici e i prefissi IP devono trovarsi nella stessa area e nella stessa sottoscrizione del cluster AKS. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Definire un indirizzo IP pubblico o prefissi in fase di creazione del cluster

Per supportare scenari come gli endpoint in uscita dell'elenco elementi consentiti, è possibile importare indirizzi IP o prefissi IP per l'uscita al momento della creazione del cluster. Aggiungere gli stessi parametri mostrati in precedenza al passaggio di creazione del cluster per definire gli IP pubblici e i prefissi IP all'inizio del ciclo di vita di un cluster.

Usare il comando *AZ AKS create* con il parametro *Load-Balancer-Outbound-IPS* per creare un nuovo cluster con gli indirizzi IP pubblici all'inizio.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Usare il comando *AZ AKS create* con il parametro *Load-Balancer-Outbound-IP-Prefixes* per creare un nuovo cluster con i prefissi IP pubblici all'inizio.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="show-the-outbound-rule-for-your-load-balancer"></a>Mostra la regola in uscita per il servizio di bilanciamento del carico

Per visualizzare la regola in uscita creata nel servizio di bilanciamento del carico, usare [AZ Network lb Outbound-Rule list][az-network-lb-outbound-rule-list] e specificare il gruppo di risorse node del cluster AKS:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

I comandi precedenti elencano la regola in uscita per il servizio di bilanciamento del carico, ad esempio:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

Nell'output di esempio, *AllocatedOutboundPorts* è 0. Il valore di *AllocatedOutboundPorts* indica che l'allocazione delle porte SNAT ripristina l'assegnazione automatica in base alle dimensioni del pool back-end. Per informazioni dettagliate, vedere [Load Balancer regole in uscita][azure-lb-outbound-rules] e [connessioni in uscita in Azure][azure-lb-outbound-connections] .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui servizi Kubernetes, vedere la [documentazione relativa ai servizi di Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-rules-overview.md#snatports
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#snat
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
