---
title: Usare un servizio di bilanciamento del carico SKU Standard nel servizio Kubernetes di AzureUse a Standard SKU load balancer in Azure Kubernetes Service (AKS)
description: Informazioni su come usare un servizio di bilanciamento del carico con uno SKU Standard per esporre i servizi con il servizio Azure Kubernetes (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 9c414572e1c3b2f046ae9a14139885e9927ab3bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252909"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Usare un servizio di bilanciamento del carico SKU Standard nel servizio Kubernetes di AzureUse a Standard SKU load balancer in Azure Kubernetes Service (AKS)

Per fornire l'accesso alle applicazioni tramite `LoadBalancer` servizi Kubernetes di tipo in Azure Kubernetes Service (AKS), è possibile usare Azure Load Balancer.To provide access to applications via Kubernetes services of type in Azure Kubernetes Service (AKS), you can use an Azure Load Balancer. Un servizio di bilanciamento del carico in esecuzione in AKS può essere usato come servizio di bilanciamento del carico interno o esterno. Un servizio di bilanciamento del carico interno rende un servizio Kubernetes accessibile solo alle applicazioni in esecuzione nella stessa rete virtuale del cluster AKS. Un servizio di bilanciamento del carico esterno riceve uno o più indirizzi IP pubblici per l'ingresso e rende un servizio Kubernetes accessibile esternamente utilizzando gli indirizzi IP pubblici.

Azure Load Balancer è disponibile in due SKU: *Basic* e *Standard*. Per impostazione predefinita, lo SKU *Standard* viene usato quando si crea un cluster AKS. L'uso di un servizio di bilanciamento del carico SKU *Standard* offre funzionalità e funzionalità aggiuntive, ad esempio dimensioni del pool back-end più grandi e zone di disponibilità. È importante comprendere le differenze tra i servizi di bilanciamento del carico *Standard* e *Basic* prima di scegliere quali usare. Dopo aver creato un cluster AKS, non è possibile modificare lo SKU di bilanciamento del carico per tale cluster. Per altre informazioni sugli SKU *Basic* e *Standard,* vedere Confronto tra SKU del servizio di bilanciamento del carico di Azure.For more information on the Basic and Standard SKUs, see [Azure load balancer SKU comparison][azure-lb-comparison].

Questo articolo presuppone una conoscenza di base dei concetti di Kubernetes e Azure Load Balancer.This article assumes a basic understanding of Kubernetes and Azure Load Balancer concepts. Per altre informazioni, vedere Concetti di base di [Kubernetes per il servizio Azure Kubernetes (AKS)][kubernetes-concepts] e [Che cos'è Azure Load Balancer?][azure-lb].

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo richiede l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.0.81 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If][install-azure-cli]you need to install or upgrade, see Install Azure CLI.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster AKS con il servizio di bilanciamento del carico di Azure SKU *standard.* Se è necessario un cluster servizio Azure Kubernetes, vedere la Guida introduttiva su servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

L'entità servizio cluster AKS deve inoltre disporre dell'autorizzazione per gestire le risorse di rete se si usa una subnet o un gruppo di risorse esistente. In generale, assegnare il ruolo *Collaboratore di rete* all'entità servizio nelle risorse delegate. Per altre informazioni sulle autorizzazioni, vedere [Delegare l'accesso AKS ad altre risorse][aks-sp]di Azure.For more information on permissions, see Delegate AKS access to other Azure resources.

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Passaggio da un bilanciamento del carico SKU di base a uno SKU Standard

Se si dispone di un cluster esistente con Il bilanciamento del carico SKU di base, esistono importanti differenze di comportamento da notare durante la migrazione per utilizzare un cluster con il servizio di bilanciamento del carico SKU Standard.If you have existing cluster with the Basic SKU Load Balancer, there are important behavioral differences to note when migrating to use a cluster with the Standard SKU Load Balancer.

Ad esempio, la creazione di distribuzioni blu/verdi per `load-balancer-sku` la migrazione dei cluster è una pratica comune dato che il tipo di cluster può essere definito solo in fase di creazione del cluster. Tuttavia, i servizi di bilanciamento del carico *SKU* di base usano indirizzi IP *sKU* di base che non sono compatibili con i servizi di bilanciamento del carico *SKU standard* in quanto richiedono indirizzi IP *SKU standard.* Quando si esegue la migrazione dei cluster per aggiornare gli SKU di Load Balancer, sarà necessario un nuovo indirizzo IP con uno SKU indirizzo IP compatibile.

Per altre considerazioni su come eseguire la migrazione dei cluster, vedere la documentazione relativa alle [considerazioni sulla migrazione](aks-migration.md) per visualizzare un elenco di argomenti importanti da considerare durante la migrazione. Le limitazioni seguenti sono anche importanti differenze di comportamento da notare quando si usano i servizi di bilanciamento del carico SKU Standard in AKS.

### <a name="limitations"></a>Limitazioni

Le limitazioni seguenti si applicano quando si creano e gestiscono cluster AKS che supportano un servizio di bilanciamento del carico con lo SKU *Standard:The* following limitations apply when you create and manage AKS clusters that support a load balancer with the Standard SKU:

* È necessario almeno un prefisso IP o IP pubblico per consentire il traffico in uscita dal cluster AKS. Il prefisso IP o IP pubblico è inoltre necessario per mantenere la connettività tra il piano di controllo e i nodi agente, nonché per mantenere la compatibilità con le versioni precedenti di AKS. Sono disponibili le opzioni seguenti per specificare gli indirizzi IP pubblici o i prefissi IP con un servizio di bilanciamento del carico SKU *Standard:You* have the following options for specifying public IPs or IP prefixes with a Standard SKU load balancer:
    * Fornire i propri IP pubblici.
    * Fornire i propri prefissi IP pubblici.
    * Specificare un numero fino a 100 per consentire al cluster AKS di creare che molti indirizzi IP pubblici SKU *Standard* nello stesso gruppo di risorse creato come cluster AKS, che in genere viene denominato con *MC_* all'inizio. AKS assegna l'IP pubblico al servizio di bilanciamento del carico SKU *Standard.* Per impostazione predefinita, un indirizzo IP pubblico verrà creato automaticamente nello stesso gruppo di risorse del cluster AKS, se non viene specificato alcun indirizzo IP pubblico, prefisso IP pubblico o numero di indirizzi IP. È inoltre necessario consentire gli indirizzi pubblici ed evitare di creare criteri di Azure che vietano la creazione di IP.
* Quando si usa lo SKU *Standard* per un servizio di bilanciamento del carico, è necessario usare Kubernetes versione *1.13 o successiva.*
* La definizione dello SKU del servizio di bilanciamento del carico può essere eseguita solo quando si crea un cluster AKS. Non è possibile modificare lo SKU del servizio di bilanciamento del carico dopo la creazione di un cluster AKS.
* È possibile usare un solo tipo di SKU del servizio di bilanciamento del carico (Basic o Standard) in un singolo cluster.
* *Standard* I servizi di bilanciamento del carico SKU supportano solo indirizzi IP SKU *standard.*

## <a name="use-the-standard-sku-load-balancer"></a>Usare il servizio di bilanciamento del carico SKU *StandardUse* the Standard SKU load balancer

Quando si crea un cluster AKS, per impostazione predefinita, il servizio di bilanciamento del carico SKU *Standard* viene usato quando si eseguono servizi in tale cluster. Ad esempio, [la guida introduttiva tramite l'interfaccia della riga di][aks-quickstart-cli] comando di Azure distribuisce un'applicazione di esempio che usa il servizio di bilanciamento del carico SKU *Standard.For* example, the quickstart using the Azure CLI deploys a sample application that uses the Standard SKU load balancer.

> [!IMPORTANT]
> È possibile evitare indirizzi IP pubblici personalizzando una route definita dall'utente. Specificare il tipo in uscita di un cluster AKS come UDR può ignorare il provisioning IP e la configurazione del pool back-end per il servizio di bilanciamento del carico di Azure creato da AKS. Vedere [impostazione di `outboundType` un cluster su 'userDefinedRouting'.](egress-outboundtype.md)

## <a name="configure-the-load-balancer-to-be-internal"></a>Configurare il servizio di bilanciamento del carico come internoConfigure the load balancer to be internal

È inoltre possibile configurare il servizio di bilanciamento del carico in modo che sia interno e non esporre un indirizzo IP pubblico. Per configurare il servizio `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` di bilanciamento del carico come interno, aggiungere come annotazione al servizio *LoadBalancer.To* configure the load balancer as internal, add as an annotation to the LoadBalancer service. È possibile visualizzare un manifesto yaml di esempio e ulteriori dettagli su un servizio di bilanciamento del carico interno [qui][internal-lb-yaml].

## <a name="scale-the-number-of-managed-public-ips"></a>Ridimensionare il numero di indirizzi IP pubblici gestiti

Quando si usa un servizio di bilanciamento del carico SKU *Standard* con indirizzi IP pubblici in uscita gestiti, creati per impostazione predefinita, è possibile ridimensionare il numero di indirizzi IP pubblici in uscita gestiti usando il parametro *load-balancer-managed-ip-count.*

Per aggiornare un cluster esistente, eseguire il comando seguente. Questo parametro può essere impostato anche in fase di creazione del cluster per avere più indirizzi IP pubblici in uscita gestiti.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

Nell'esempio precedente il numero di indirizzi IP pubblici in uscita gestiti viene impostato su *2* per il cluster *myAKSCluster* in *myResourceGroup*. 

È anche possibile usare il parametro *load-balancer-managed-ip-count* per impostare il numero iniziale `--load-balancer-managed-outbound-ip-count` di indirizzi IP pubblici in uscita gestiti durante la creazione del cluster aggiungendo il parametro e impostandolo sul valore desiderato. Il numero predefinito di indirizzi IP pubblici in uscita gestiti è 1.The default number of managed outbound public IPs is 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Fornire i propri indirizzi IP pubblici o prefissi per l'uscita

Quando si usa un servizio di bilanciamento del carico SKU *Standard,* il cluster AKS crea automaticamente un indirizzo IP pubblico nello stesso gruppo di risorse creato per il cluster AKS e assegna l'indirizzo IP pubblico al servizio di bilanciamento del carico SKU *Standard.* In alternativa, è possibile assegnare il proprio ip pubblico al momento della creazione del cluster oppure aggiornare le proprietà del servizio di bilanciamento del carico di un cluster esistente.

Portando più indirizzi IP o prefissi, è possibile definire più servizi di backup quando si definisce l'indirizzo IP dietro un singolo oggetto di bilanciamento del carico. L'endpoint in uscita di nodi specifici dipenderà dal servizio a cui sono associati.

> [!IMPORTANT]
> È necessario usare gli indirizzi IP pubblici dello SKU *Standard* per uscire con lo SKU *Standard* nel servizio di bilanciamento del carico. È possibile verificare lo SKU degli indirizzi IP pubblici utilizzando il comando [az network public-ip show:][az-network-public-ip-show]
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Utilizzare il comando [az network public-ip show][az-network-public-ip-show] per elencare gli ID degli indirizzi IP pubblici.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Il comando precedente mostra l'ID per l'IP pubblico *myPublicIP* nel gruppo di risorse *myResourceGroup.*

Utilizzare il comando *az aks update* con il parametro *load-balancer-outbound-ips* per aggiornare il cluster con gli indirizzi IP pubblici.

Nell'esempio seguente viene utilizzato il parametro *load-balancer-outbound-ips* con gli ID del comando precedente.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

È anche possibile usare prefissi IP pubblici per uscire con il servizio di bilanciamento del carico SKU *Standard.You* can also use public IP prefixes for egress with your Standard SKU load balancer. Nell'esempio seguente viene utilizzato il comando [az network public-ip prefix show][az-network-public-ip-prefix-show] per elencare gli ID dei prefissi IP pubblici:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Il comando precedente mostra l'ID per il prefisso IP pubblico *myPublicIPPrefix* nel gruppo di risorse *myResourceGroup.*

Nell'esempio seguente viene utilizzato il parametro *load-balancer-outbound-ip-prefixes* con gli ID del comando precedente.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> Gli indirizzi IP e i prefissi IP pubblici devono trovarsi nella stessa area e fanno parte della stessa sottoscrizione del cluster AKS. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Definire il proprio IP pubblico o prefissi al momento della creazione del clusterDefine your own public IP or prefixes at cluster create time

È possibile portare i propri indirizzi IP o prefissi IP per l'uscita al momento della creazione del cluster per supportare scenari come la whitelisting degli endpoint in uscita. Aggiungere gli stessi parametri illustrati in precedenza al passaggio di creazione del cluster per definire i propri indirizzi IP e IP pubblici all'inizio del ciclo di vita di un cluster.

Usare il comando *az aks create* con il *parametro load-balancer-outbound-ips* per creare un nuovo cluster con gli indirizzi IP pubblici all'inizio.

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

Usare il comando *az aks create* con il parametro *load-balancer-outbound-ip-prefixes* per creare un nuovo cluster con i prefissi IP pubblici all'inizio.

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
> La sezione seguente è destinata a scenari avanzati di rete su larga scala o per risolvere i problemi di esaurimento SNAT con le configurazioni predefinite. È necessario disporre di un inventario accurato della quota disponibile per le macchine virtuali e gli indirizzi IP prima di modificare *AllocatedOutboundPorts* o *IdleTimeoutInMinutes* dal valore predefinito per mantenere i cluster integri.
> 
> La modifica dei valori per *AllocatedOutboundPorts* e *IdleTimeoutInMinutes* può modificare in modo significativo il comportamento della regola in uscita per il servizio di bilanciamento del carico. Esaminare [le regole in uscita][azure-lb-outbound-rules-overview]di Load Balancer , le regole in uscita di Load Balancer e [le][azure-lb-outbound-rules]connessioni in uscita [in Azure][azure-lb-outbound-connections] prima di aggiornare questi valori per comprendere appieno l'impatto delle modifiche.

Le porte allocate in uscita e i relativi timeout inattivi vengono utilizzati per [SNAT][azure-lb-outbound-connections]. Per impostazione predefinita, il servizio di bilanciamento del carico SKU *Standard* usa [l'assegnazione automatica per il numero di porte in uscita in base alle dimensioni del pool back-end][azure-lb-outbound-preallocatedports] e a un timeout di inattività di 30 minuti per ogni porta. Per visualizzare questi valori, usare [az network lb outbound-rule list][az-network-lb-outbound-rule-list] per visualizzare la regola in uscita per il servizio di bilanciamento del carico:To see these values, use az network lb outbound-rule list to show the outbound rule for the load balancer:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

I comandi precedenti elencheranno la regola in uscita per il servizio di bilanciamento del carico, ad esempio:The previous commands will list the outbound rule for your load balancer, for example:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

Nell'output di esempio viene illustrato il valore predefinito per *AllocatedOutboundPorts* e *IdleTimeoutInMinutes*. Il valore 0 per *AllocatedOutboundPorts* imposta il numero di porte in uscita utilizzando l'assegnazione automatica per il numero di porte in uscita in base alle dimensioni del pool back-end. Ad esempio, se il cluster ha 50 o meno nodi, vengono allocate 1024 porte per ogni nodo.

Considerare la modifica dell'impostazione di *allocatedOutboundPorts* o *IdleTimeoutInMinutes* se si prevede di affrontare l'esaurimento SNAT in base alla configurazione predefinita precedente. Ogni indirizzo IP aggiuntivo abilita 64.000 porte aggiuntive per l'allocazione, tuttavia Azure Standard Load Balancer non aumenta automaticamente le porte per nodo quando vengono aggiunti più indirizzi IP. È possibile modificare questi valori impostando i parametri *load-balancer-outbound-ports* e *load-balancer-idle-timeout.* Ad esempio:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> È necessario [calcolare la quota richiesta][calculate-required-quota] prima di personalizzare *allocatedOutboundPorts* per evitare problemi di connettività o scalabilità. Il valore specificato per *allocatedOutboundPorts* deve essere anche un multiplo di 8.

È anche possibile usare le *porte di bilanciamento* del carico e le porte di bilanciamento del carico e i parametri *load-balancer-idle-timeout* durante la creazione di un cluster, ma è anche necessario specificare anche i prefissi *load-balancer-managed-outbound-ip-count*, *load-balancer-outbound-ips*o *load-balancer-outbound-ip-prefixes.*  Ad esempio:

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

Quando si modificano i parametri *load-balancer-outbound-ports* e *load-balancer-idle-timeout* dal loro valore predefinito, influisce sul comportamento del profilo del bilanciamento del carico, che influisce sull'intero cluster.

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>Quota obbligatoria per la personalizzazione di allocatedOutboundPorts
È necessario disporre di una capacità IP in uscita sufficiente in base al numero di macchine virtuali del nodo e alle porte in uscita allocate desiderate. Per verificare la disponibilità di una capacità IP in uscita sufficiente, utilizzare la formula seguente: 
 
*outboundIP* \* 64.000 \> *nodiVM* \* *desiredAllocatedOutboundPorts*.
 
Ad esempio, se si dispone di 3 *nodeVMs*e 50.000 *desiredAllocatedOutboundPorts*, è necessario disporre di almeno 3 *IP in uscita*. Si consiglia di incorporare ulteriore capacità IP in uscita oltre a quelle necessarie. Inoltre, è necessario tenere conto del cluster autoscaler e della possibilità di aggiornamenti del pool di nodi durante il calcolo della capacità IP in uscita. Per il cluster autoscaler, esaminare il numero di nodi corrente e il numero massimo di nodi e utilizzare il valore più alto. Per l'aggiornamento, tenere conto di una macchina virtuale del nodo aggiuntivo per ogni pool di nodi che consente l'aggiornamento.
 
Quando si imposta *IdleTimeoutInMinutes* su un valore diverso da quello predefinito di 30 minuti, considerare per quanto tempo i carichi di lavoro avranno bisogno di una connessione in uscita. Considerare inoltre che il valore di timeout predefinito per un servizio di bilanciamento del carico SKU *Standard* usato all'esterno di AKS è di 4 minuti. Un valore *IdleTimeoutInMinutes* che riflette in modo più accurato il carico di lavoro AKS specifico può contribuire a ridurre l'esaurimento Di SNAT causato dal collegamento delle connessioni non più in uso.

## <a name="restrict-access-to-specific-ip-ranges"></a>Limitare l'accesso a intervalli IP specifici

Il gruppo di sicurezza di rete (NSG) associato alla rete virtuale per il servizio di bilanciamento del carico, per impostazione predefinita, dispone di una regola per consentire tutto il traffico esterno in ingresso. È possibile aggiornare questa regola per consentire solo intervalli IP specifici per il traffico in ingresso. Il manifesto seguente usa loadBalancerSourceRanges per specificare un nuovo intervallo IP per il traffico esterno in ingresso:The following manifest uses *loadBalancerSourceRanges* to specify a new IP range for inbound external traffic:

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

Nell'esempio precedente viene aggiornata la regola per consentire solo il traffico esterno in ingresso dall'intervallo *di MY_EXTERNAL_IP_RANGE.* Ulteriori informazioni sull'utilizzo di questo metodo per limitare l'accesso al servizio di bilanciamento del carico sono disponibili nella documentazione di [Kubernetes.][kubernetes-cloud-provider-firewall]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui servizi Kubernetes sono disponibili nella [documentazione relativa ai servizi Kubernetes][kubernetes-services].

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
