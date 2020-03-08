---
title: Usare un servizio di bilanciamento del carico con SKU standard in Azure Kubernetes Service (AKS)
description: Informazioni su come usare un servizio di bilanciamento del carico con uno SKU standard per esporre i servizi con Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 9c414572e1c3b2f046ae9a14139885e9927ab3bb
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374681"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Usare un servizio di bilanciamento del carico con SKU standard in Azure Kubernetes Service (AKS)

Per fornire l'accesso alle applicazioni tramite i servizi Kubernetes di tipo `LoadBalancer` in Azure Kubernetes Service (AKS), è possibile usare una Azure Load Balancer. Un servizio di bilanciamento del carico in esecuzione su AKS può essere usato come servizio di bilanciamento del carico interno o esterno. Un servizio di bilanciamento del carico interno rende accessibile un servizio Kubernetes solo alle applicazioni in esecuzione nella stessa rete virtuale del cluster AKS. Un servizio di bilanciamento del carico esterno riceve uno o più indirizzi IP pubblici per il traffico in ingresso e rende accessibile esternamente un servizio Kubernetes usando gli indirizzi IP pubblici.

Azure Load Balancer è disponibile in due SKU: *Basic* e *Standard*. Per impostazione predefinita, quando si crea un cluster AKS viene usato lo SKU *standard* . L'uso di un servizio di bilanciamento del carico con SKU *standard* fornisce funzionalità e funzionalità aggiuntive, ad esempio dimensioni del pool back-end maggiori e zone di disponibilità. Prima di scegliere quale usare, è importante comprendere le differenze tra i bilanciamenti del carico *standard* e *Basic* . Dopo aver creato un cluster AKS, non è possibile modificare lo SKU del servizio di bilanciamento del carico per quel cluster. Per altre informazioni sugli SKU *Basic* e *standard* , vedere [confronto tra SKU di bilanciamento del carico di Azure][azure-lb-comparison].

Questo articolo presuppone una conoscenza di base dei concetti relativi a Kubernetes e Azure Load Balancer. Per altre informazioni, vedere [Kubernetes Core Concepts for Azure Kubernetes Service (AKS)][kubernetes-concepts] e informazioni su [Azure Load Balancer][azure-lb].

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.81 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che sia presente un cluster AKS con lo SKU *Standard* Azure Load Balancer. Se è necessario un cluster AKS, vedere la Guida introduttiva di AKS [usando l'interfaccia della][aks-quickstart-cli] riga di comando di Azure o [l'portale di Azure][aks-quickstart-portal].

L'entità servizio cluster AKS necessita anche dell'autorizzazione per gestire le risorse di rete se si usa una subnet o un gruppo di risorse esistente. In generale, assegnare il ruolo *collaboratore rete* all'entità servizio per le risorse Delegate. Per altre informazioni sulle autorizzazioni, vedere [delega dell'accesso AKS ad altre risorse di Azure][aks-sp].

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Passaggio da uno SKU di base Load Balancer allo SKU standard

Se si dispone di un cluster esistente con lo SKU Basic Load Balancer, quando si esegue la migrazione per l'uso di un cluster con lo Load Balancer SKU standard sono presenti importanti differenze di comportamento.

Ad esempio, l'esecuzione di distribuzioni Blue/Green per la migrazione dei cluster è una pratica comune, dato che il tipo di `load-balancer-sku` di un cluster può essere definito solo in fase di creazione del cluster. Tuttavia, i bilanciamenti del carico *SKU Basic* usano gli indirizzi IP dello *SKU Basic* che non sono compatibili con i bilanciamenti del carico *SKU standard* perché richiedono indirizzi IP *SKU standard* . Quando si esegue la migrazione dei cluster per aggiornare Load Balancer SKU, sarà necessario un nuovo indirizzo IP con lo SKU di un indirizzo IP compatibile.

Per altre considerazioni su come eseguire la migrazione dei cluster, vedere [la documentazione relativa alle considerazioni sulla migrazione](aks-migration.md) per visualizzare un elenco di argomenti importanti da considerare durante la migrazione. Le limitazioni seguenti sono anche importanti differenze comportamentali da tenere presente quando si usa il servizio di bilanciamento del carico SKU standard in AKS.

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

## <a name="use-the-standard-sku-load-balancer"></a>Usare il servizio di bilanciamento del carico SKU *standard*

Quando si crea un cluster AKS, per impostazione predefinita viene usato il servizio di bilanciamento del carico SKU *standard* quando si eseguono i servizi in tale cluster. Ad esempio, [la Guida introduttiva usando l'interfaccia della][aks-quickstart-cli] riga di comando di Azure distribuisce un'applicazione di esempio che usa il servizio di bilanciamento del carico SKU *standard* .

> [!IMPORTANT]
> Gli indirizzi IP pubblici possono essere evitati personalizzando una route definita dall'utente (UDR). Specificando il tipo in uscita del cluster AKS come UDR, è possibile ignorare il provisioning IP e la configurazione del pool back-end per il servizio di bilanciamento del carico di Azure creato da AKS. Vedere [impostazione del `outboundType` di un cluster su' userDefinedRouting '](egress-outboundtype.md).

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

## <a name="configure-outbound-ports-and-idle-timeout"></a>Configurare le porte in uscita e il timeout di inattività

> [!WARNING]
> La sezione seguente è destinata ad scenari avanzati di rete di maggiori dimensioni o per risolvere i problemi di esaurimento SNAT con le configurazioni predefinite. È necessario disporre di un inventario accurato della quota disponibile per le macchine virtuali e gli indirizzi IP prima di modificare *AllocatedOutboundPorts* o *IdleTimeoutInMinutes* dal relativo valore predefinito per mantenere i cluster integri.
> 
> La modifica dei valori di *AllocatedOutboundPorts* e *IdleTimeoutInMinutes* può modificare in modo significativo il comportamento della regola in uscita per il servizio di bilanciamento del carico. Esaminare le regole in uscita [Load Balancer][azure-lb-outbound-rules-overview], le regole in uscita del servizio di [bilanciamento del carico][azure-lb-outbound-rules]e le [connessioni in uscita in Azure][azure-lb-outbound-connections] prima di aggiornare questi valori per comprendere completamente l'effetto delle modifiche.

Per [SNAT][azure-lb-outbound-connections]vengono usate le porte allocate in uscita e i relativi timeout di inattività. Per impostazione predefinita, il servizio di bilanciamento del carico dello SKU *standard* usa l' [assegnazione automatica per il numero di porte in uscita in base alle dimensioni del pool back-end][azure-lb-outbound-preallocatedports] e un timeout di inattività di 30 minuti per ogni porta. Per visualizzare questi valori, usare [AZ Network lb Outbound-Rule list][az-network-lb-outbound-rule-list] per visualizzare la regola in uscita per il servizio di bilanciamento del carico:

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

L'output di esempio mostra il valore predefinito per *AllocatedOutboundPorts* e *IdleTimeoutInMinutes*. Il valore 0 per *AllocatedOutboundPorts* imposta il numero di porte in uscita che usano l'assegnazione automatica per il numero di porte in uscita in base alle dimensioni del pool back-end. Se, ad esempio, il cluster ha 50 o meno nodi, vengono allocate 1024 porte per ogni nodo.

Provare a modificare l'impostazione di *allocatedOutboundPorts* o *IdleTimeoutInMinutes* se si prevede di affrontare l'esaurimento del SNAT in base alla configurazione predefinita precedente. Ogni indirizzo IP aggiuntivo Abilita 64.000 porte aggiuntive per l'allocazione, tuttavia il Load Balancer Standard di Azure non aumenta automaticamente le porte per nodo quando vengono aggiunti più indirizzi IP. È possibile modificare questi valori impostando i parametri *Load-Balancer-Outbound-Ports* e *Load-Balancer-Idle-timeout* . Ad esempio:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> È necessario [calcolare la quota necessaria][calculate-required-quota] prima di personalizzare *allocatedOutboundPorts* per evitare problemi di connettività o di ridimensionamento. Il valore specificato per *allocatedOutboundPorts* deve essere anche un multiplo di 8.

Quando si crea un cluster, è anche possibile usare i parametri *Load-Balancer-Ports-Outbound-Ports* e *Load-Balancer-Idle-timeout* . Tuttavia, è necessario specificare anche Load-Balancer-Managed-Outbound- *IP-count*, *Load-Balancer-* Outbound-IP- *prefissi* .  Ad esempio:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

Quando si modificano i parametri del servizio di bilanciamento del carico- *porte in uscita* e del servizio di bilanciamento del carico per il *timeout di inattività* , il comportamento del profilo del servizio di bilanciamento del carico influiscono sull'intero cluster.

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>Quota necessaria per la personalizzazione di allocatedOutboundPorts
È necessario disporre di una capacità IP in uscita sufficiente in base al numero di macchine virtuali del nodo e alle porte in uscita allocate desiderate. Per convalidare la capacità IP in uscita sufficiente, usare la formula seguente: 
 
*outboundIPs* \* 64.000 \> *nodeVMs* \* *desiredAllocatedOutboundPorts*.
 
Ad esempio, se si dispone di 3 *nodeVMs*e 50.000 *desiredAllocatedOutboundPorts*, è necessario disporre di almeno 3 *outboundIPs*. È consigliabile incorporare ulteriore capacità IP in uscita oltre a quella necessaria. Inoltre, è necessario tenere conto della funzionalità di scalabilità automatica del cluster e della possibilità di aggiornamenti del pool di nodi durante il calcolo della capacità IP in uscita. Per il ridimensionamento automatico del cluster, esaminare il numero di nodi corrente e il numero massimo di nodi e usare il valore più elevato. Per l'aggiornamento, tenere conto di una macchina virtuale del nodo aggiuntiva per ogni pool di nodi che consente l'aggiornamento.
 
Quando si imposta *IdleTimeoutInMinutes* su un valore diverso da quello predefinito di 30 minuti, considerare per quanto tempo i carichi di lavoro necessiteranno di una connessione in uscita. Prendere in considerazione anche il valore di timeout predefinito per un servizio di bilanciamento del carico SKU *standard* usato al di fuori di AKS è 4 minuti. Un valore *IdleTimeoutInMinutes* che riflette in modo più accurato il carico di lavoro AKS specifico può contribuire a ridurre l'esaurimento di SNAT causato dalla legatura di connessioni non più utilizzate.

## <a name="restrict-access-to-specific-ip-ranges"></a>Limitazione dell'accesso a intervalli IP specifici

Per impostazione predefinita, il gruppo di sicurezza di rete (NSG) associato alla rete virtuale per il servizio di bilanciamento del carico ha una regola per consentire tutto il traffico esterno in ingresso. È possibile aggiornare questa regola in modo da consentire solo intervalli IP specifici per il traffico in ingresso. Il manifesto seguente usa *loadBalancerSourceRanges* per specificare un nuovo intervallo IP per il traffico esterno in ingresso:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

L'esempio precedente aggiorna la regola in modo da consentire solo il traffico esterno in ingresso dall'intervallo di *MY_EXTERNAL_IP_RANGE* . Altre informazioni sull'uso di questo metodo per limitare l'accesso al servizio di bilanciamento del carico sono disponibili nella [documentazione di Kubernetes][kubernetes-cloud-provider-firewall].

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui servizi Kubernetes, vedere la [documentazione relativa ai servizi di Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-cloud-provider-firewall]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-cloud-provider-firewall/#restrict-access-for-loadbalancer-service
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
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-rules-overview.md#snatports
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#snat
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-rules-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[calculate-required-quota]: #required-quota-for-customizing-allocatedoutboundports
