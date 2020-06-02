---
title: Usare un'istanza di bilanciamento del carico con SKU Standard
titleSuffix: Azure Kubernetes Service
description: Informazioni su come usare un'istanza di bilanciamento del carico con SKU Standard per esporre i servizi con il servizio Azure Kubernetes.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 14e80f6348772af77c5a53b1d5e9111c4ae8ba9b
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402060"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Usare un'istanza di bilanciamento del carico con SKU Standard nel servizio Azure Kubernetes

Per fornire l'accesso alle applicazioni tramite i servizi Kubernetes di tipo `LoadBalancer` nel servizio Azure Kubernetes, è possibile usare un'istanza di Azure Load Balancer. Un'istanza di bilanciamento del carico in esecuzione sul servizio Azure Kubernetes può essere usata come bilanciamento del carico interno o esterno. Un bilanciamento del carico interno rende accessibile un servizio Kubernetes solo alle applicazioni in esecuzione nella stessa rete virtuale del cluster del servizio Azure Kubernetes. Un bilanciamento del carico esterno riceve uno o più indirizzi IP pubblici per il traffico in ingresso e rende accessibile esternamente un servizio Kubernetes usando gli indirizzi IP pubblici.

Azure Load Balancer è disponibile in due SKU: *Basic* e *Standard*. Per impostazione predefinita, quando si crea un cluster del servizio Azure Kubernetes viene usato lo SKU *Standard*. L'uso di un'istanza di bilanciamento del carico con SKU *Standard* fornisce funzionalità aggiuntive, ad esempio dimensioni del pool back-end maggiori e zone di disponibilità. È importante comprendere le differenze tra le istanze di bilanciamento del carico *Standard* e *Basic* prima di scegliere quale usare. Dopo aver creato un cluster del servizio Azure Kubernetes, non è possibile modificare lo SKU dell'istanza di bilanciamento del carico per quel cluster. Per altre informazioni sugli SKU *Basic* e *Standard*, vedere [Confronto tra SKU di Azure Load Balancer][azure-lb-comparison].

Questo articolo presuppone una conoscenza di base dei concetti relativi a Kubernetes e Azure Load Balancer. Per altre informazioni, vedere [Concetti di base relativi a Kubernetes per il servizio Azure Kubernetes][kubernetes-concepts] e [Informazioni su Azure Load Balancer][azure-lb].

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo richiede la versione 2.0.81 o successive dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che sia presente un cluster del servizio Azure Kubernetes con Azure Load Balancer con SKU *Standard*. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

L'entità servizio del cluster del servizio Azure Kubernetes necessita anche dell'autorizzazione per gestire le risorse di rete se si usa una subnet o un gruppo di risorse esistente. In generale, assegnare il ruolo *Collaboratore rete* all'entità servizio per le risorse delegate. Anziché un'entità servizio, è anche possibile usare l'identità gestita assegnata dal sistema per le autorizzazioni. Per altre informazioni, vedere [Usare le identità gestite](use-managed-identity.md). Per altre informazioni sulle autorizzazioni, vedere [Delegare l'accesso del servizio Azure Kubernetes ad altre risorse di Azure][aks-sp].

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Passaggio da un servizio Load Balancer con SKU Standard a uno con SKU Basic

Se si dispone di un cluster esistente con il servizio Load Balancer con SKU Basic, quando si esegue la migrazione per l'uso di un cluster con il servizio Load Balancer con SKU Standard, è necessario tenere presenti alcune importanti differenze di comportamento.

Ad esempio, l'esecuzione di distribuzioni blu/verde per la migrazione dei cluster è una pratica comune, dato che il tipo di `load-balancer-sku` di un cluster può essere definito solo in fase di creazione del cluster. Tuttavia, le istanze di Load Balancer con *SKU Basic* usano indirizzi IP dello *SKU Basic* non compatibili con le istanze di Load Balancer con *SKU Standard* perché richiedono indirizzi IP con *SKU Standard*. Quando si esegue la migrazione dei cluster per aggiornare gli SKU di Load Balancer, sarà necessario un nuovo indirizzo IP con uno SKU compatibile per l'indirizzo IP.

Per altre considerazioni su come eseguire la migrazione dei cluster, vedere la [documentazione sulle considerazioni relative alla migrazione](aks-migration.md), in modo da consultare un elenco di argomenti importanti da tenere presenti durante la migrazione. Le limitazioni seguenti includono differenze comportamentali importanti da tenere presenti quando si usano istanze di Load Balancer con SKU Standard nel servizio Azure Kubernetes.

### <a name="limitations"></a>Limitazioni

Quando si creano e si gestiscono cluster del servizio Azure Kubernetes che supportano un'istanza di bilanciamento del carico con SKU *Standard*, si applicano le limitazioni seguenti:

* È necessario almeno un indirizzo IP pubblico o un prefisso dell'indirizzo IP per consentire il traffico in uscita dal cluster del servizio Azure Kubernetes. L'indirizzo IP pubblico o il prefisso dell'indirizzo IP è necessario anche per mantenere la connettività tra il piano di controllo e i nodi dell'agente, nonché per mantenere la compatibilità con le versioni precedenti del servizio Azure Kubernetes. Per specificare gli indirizzi IP pubblici o i prefissi degli indirizzi IP con un servizio di bilanciamento del carico con SKU *Standard*, sono disponibili le opzioni seguenti:
    * Specificare gli indirizzi IP pubblici personalizzati.
    * Specificare i prefissi degli indirizzi IP pubblici personalizzati.
    * Specificare un numero massimo di 100 per consentire al cluster del servizio Azure Kubernetes di creare una certa quantità di indirizzi IP pubblici dello SKU *Standard* nello stesso gruppo di risorse creato dal cluster del servizio Azure Kubernetes, che in genere viene denominato con *MC_* all'inizio. Il servizio Azure Kubernetes assegna l'indirizzo IP pubblico all'istanza di bilanciamento del carico con SKU *Standard*. Per impostazione predefinita, un indirizzo IP pubblico verrà creato automaticamente nello stesso gruppo di risorse del cluster del servizio Azure Kubernetes, se non è specificato alcun indirizzo IP pubblico, prefisso di indirizzo IP pubblico o numero di indirizzi IP. È necessario anche consentire gli indirizzi pubblici ed evitare di creare criteri di Azure che vietino la creazione di IP.
* Quando si usa lo SKU *Standard* per un servizio di bilanciamento del carico, è necessario usare Kubernetes versione *1.13 o successive*.
* La definizione dello SKU per un servizio di bilanciamento del carico può essere eseguita solo quando si crea un cluster del servizio Azure Kubernetes. Non è possibile modificare lo SKU dell'istanza di bilanciamento del carico dopo la creazione di un cluster del servizio Azure Kubernetes.
* È possibile usare un solo tipo di SKU dell'istanza di bilanciamento del carico (Basic o Standard) in un singolo cluster.
* Le istanze di bilanciamento del carico con SKU *Standard* supportano solo indirizzi IP con SKU *Standard*.

## <a name="use-the-standard-sku-load-balancer"></a>Usare un'istanza di bilanciamento del carico con SKU *Standard*

Quando si crea un cluster del servizio Azure Kubernetes, per impostazione predefinita, viene usata un'istanza di bilanciamento del carico con SKU *Standard* quando si eseguono i servizi in tale cluster. Ad esempio, l'[avvio rapido che usa l'interfaccia della riga di comando di Azure][aks-quickstart-cli] distribuisce un'applicazione di esempio che usa un'istanza di bilanciamento del carico con SKU *Standard*.

> [!IMPORTANT]
> Gli indirizzi IP pubblici possono essere evitati personalizzando una route definita dall'utente. Specificando il tipo in uscita del cluster del servizio Azure Kubernetes come route definita dall'utente, è possibile ignorare il provisioning dell'indirizzo IP e la configurazione del pool back-end per l'istanza di bilanciamento del carico di Azure creata dal servizio Azure Kubernetes. Vedere [Impostazione di un `outboundType` del cluster su 'userDefinedRouting'](egress-outboundtype.md).

## <a name="configure-the-load-balancer-to-be-internal"></a>Configurare un'istanza di bilanciamento del carico interna

È anche possibile configurare l'istanza di bilanciamento del carico in modo che sia interna e non esponga un indirizzo IP pubblico. Per configurare l'istanza di bilanciamento del carico come interna, aggiungere `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` come annotazione al servizio *Load Balancer*. È possibile visualizzare un esempio di manifesto YAML, oltre a informazioni dettagliate su un'istanza di bilanciamento del carico interna [qui][internal-lb-yaml].

## <a name="scale-the-number-of-managed-public-ips"></a>Ridimensionare il numero di indirizzi IP pubblici gestiti

Quando si usa un'istanza di bilanciamento del carico con SKU *Standard* con indirizzi IP pubblici in uscita gestiti, che vengono creati per impostazione predefinita, è possibile ridimensionare il numero di indirizzi IP pubblici in uscita gestiti usando il parametro *load-balancer-managed-ip-count*.

Per aggiornare un cluster esistente, eseguire il comando seguente. Questo parametro può essere impostato anche in fase di creazione del cluster, per avere più indirizzi IP pubblici in uscita gestiti.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

L'esempio precedente imposta il numero di indirizzi IP pubblici in uscita gestiti su *2* per il cluster *myAKSCluster* in *myResourceGroup*. 

Per impostare il numero iniziale di indirizzi IP pubblici in uscita gestiti durante la creazione, è anche possibile aggiungere il parametro *load-balancer-managed-ip-count* e impostarlo sul valore desiderato. Il numero predefinito di indirizzi IP pubblici in uscita gestiti è 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Specificare gli indirizzi IP pubblici o i prefissi in uscita

Quando si usa un'istanza di bilanciamento del carico con SKU *Standard*, il cluster del servizio Azure Kubernetes crea automaticamente un indirizzo IP pubblico nello stesso gruppo di risorse creato per il cluster del servizio Azure Kubernetes e assegna l'indirizzo IP pubblico all'istanza di bilanciamento del carico con SKU *Standard*. In alternativa, è possibile assegnare un indirizzo IP pubblico personalizzato al momento della creazione del cluster oppure aggiornare le proprietà dell'istanza di bilanciamento del carico di un cluster esistente.

Con l'importazione di più indirizzi IP o prefissi degli indirizzi IP, è possibile definire più servizi di backup quando si definisce l'indirizzo IP dietro un singolo oggetto di bilanciamento del carico. L'endpoint in uscita di nodi specifici dipenderà dal servizio a cui sono associati.

### <a name="pre-requisites-to-bring-your-own-ip-addresses-or-ip-prefixes"></a>Prerequisiti per importare gli indirizzi IP o i prefissi degli indirizzi IP personalizzati
1. È necessario usare gli indirizzi IP pubblici dello SKU *Standard* per l'uscita con l'istanza di bilanciamento del carico con SKU *Standard*. È possibile verificare lo SKU degli indirizzi IP pubblici usando il comando [az network public-ip show][az-network-public-ip-show]:

   ```azurecli-interactive
   az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
   ```
 1. Gli indirizzi IP pubblici e i prefissi degli indirizzi IP devono trovarsi nella stessa area e nella stessa sottoscrizione del cluster del servizio Azure Kubernetes.
 1. Gli indirizzi IP pubblici e i prefissi degli indirizzi IP non possono essere indirizzi IP creati dal servizio Azure Kubernetes come indirizzi IP gestiti. Assicurarsi che tutti gli indirizzi IP specificati come indirizzi IP personalizzati siano stati creati manualmente e non siano il servizio Azure Kubernetes.
 1. Gli indirizzi IP pubblici e i prefissi degli indirizzi IP non possono essere usati da un'altra risorsa o servizio.

 ### <a name="define-your-own-public-ip-or-prefixes-on-an-existing-cluster"></a>Definire un indirizzo IP pubblico o i prefissi in un cluster esistente

Usare il comando [az network public-ip show][az-network-public-ip-show] per elencare gli ID degli indirizzi IP pubblici.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Il comando precedente mostra l'ID dell'indirizzo IP pubblico *myPublicIP* nel gruppo di risorse *myResourceGroup*.

Per aggiornare il cluster con gli indirizzi IP pubblici, usare il comando *az aks update* con il parametro *load-balancer-outbound-ips*.

L'esempio seguente usa il parametro *load-balancer-outbound-ips* con gli ID del comando precedente.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

È anche possibile usare i prefissi degli indirizzi IP pubblici per l'uscita con l'istanza di bilanciamento del carico con SKU *Standard*. L'esempio seguente usa il comando [network public-ip prefix show][az-network-public-ip-prefix-show] per elencare gli ID dei prefissi degli indirizzi IP pubblici:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Il comando precedente mostra l'ID del prefisso dell'indirizzo IP pubblico *myPublicIPPrefix* nel gruppo di risorse *myResourceGroup*.

L'esempio seguente usa il parametro *load-balancer-outbound-ip-prefixes* con gli ID del comando precedente.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Definire un indirizzo IP pubblico o i prefissi in fase di creazione del cluster

Per supportare scenari come l'elenco di elementi consentiti degli endpoint in uscita, è possibile importare indirizzi IP o prefissi degli indirizzi IP in uscita al momento della creazione del cluster. Aggiungere gli stessi parametri illustrati in precedenza al passaggio di creazione del cluster per definire gli indirizzi IP pubblici e i prefissi degli indirizzi IP all'inizio del ciclo di vita di un cluster.

Per creare un nuovo cluster con gli indirizzi IP pubblici all'inizio, usare il comando *az aks create* con il parametro *load-balancer-outbound-ips*.

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

Per creare un nuovo cluster con i prefissi degli indirizzi IP pubblici all'inizio, usare il comando *az aks create* con il parametro *load-balancer-outbound-ip-prefixes*.

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
> La sezione seguente è destinata a scenari avanzati di reti di maggiori dimensioni o per risolvere i problemi di esaurimento SNAT con le configurazioni predefinite. È necessario disporre di un inventario accurato della quota disponibile per le macchine virtuali e gli indirizzi IP prima di modificare *AllocatedOutboundPorts* o *IdleTimeoutInMinutes* rispetto al valore predefinito, per mantenere i cluster integri.
> 
> La modifica dei valori *AllocatedOutboundPorts* e *IdleTimeoutInMinutes* può cambiare in modo significativo il comportamento della regola in uscita per l'istanza di bilanciamento del carico. Per comprendere l'effetto delle modifiche apportate, prima di aggiornare questi valori, esaminare le informazioni relative alle [regole in uscita di Load Balancer][azure-lb-outbound-rules-overview], alle [regole in uscita dell'istanza di bilanciamento del carico][azure-lb-outbound-rules] e alle [connessioni in uscita in Azure][azure-lb-outbound-connections].

Le porte allocate in uscita e i relativi timeout di inattività vengono usati per [SNAT][azure-lb-outbound-connections]. Per impostazione predefinita, l'istanza di bilanciamento del carico con SKU *Standard* usa l'[assegnazione automatica per il numero di porte in uscita in base alle dimensioni del pool back-end][azure-lb-outbound-preallocatedports] e un timeout di inattività di 30 minuti per ogni porta. Per visualizzare questi valori, usare [az network lb outbound-rule list][az-network-lb-outbound-rule-list] che mostra la regola in uscita per l'istanza di bilanciamento del carico:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

I comandi precedenti elencano la regola in uscita per l'istanza di bilanciamento del carico, ad esempio:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

L'output di esempio mostra il valore predefinito per *AllocatedOutboundPorts* e *IdleTimeoutInMinutes*. Il valore 0 per *AllocatedOutboundPorts* imposta il numero di porte in uscita che usano l'assegnazione automatica per il numero di porte in uscita in base alle dimensioni del pool back-end. Se, ad esempio, il cluster ha un numero massimo di 50, vengono allocate 1024 porte per ogni nodo.

Considerare la possibilità di modificare l'impostazione di *allocatedOutboundPorts* o *IdleTimeoutInMinutes* se si prevede di affrontare l'esaurimento SNAT in base alla configurazione predefinita precedente. Ogni indirizzo IP aggiuntivo abilita 64.000 porte aggiuntive per l'allocazione, tuttavia Load Balancer Standard di Azure non aumenta automaticamente le porte per ogni nodo quando vengono aggiunti più indirizzi IP. È possibile modificare questi valori impostando i parametri di *load-balancer-outbound-ports* e *load-balancer-idle-timeout*. Ad esempio:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> È necessario [calcolare la quota necessaria][calculate-required-quota] prima di personalizzare *allocatedOutboundPorts* per evitare problemi di connettività o di ridimensionamento. Il valore specificato per *allocatedOutboundPorts* deve essere anche un multiplo di 8.

Quando si crea un cluster, è anche possibile usare i parametri *load-balancer-outbound-ports* e *load-balancer-idle-timeout*. È, tuttavia, necessario specificare anche *load-balancer-managed-outbound-ip-count*, *load-balancer-outbound-ips*o *load-balancer-outbound-ip-prefixes*.  Ad esempio:

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

Quando si modificano i parametri *load-balancer-outbound-ports* e *load-balancer-idle-timeout* rispetto al loro valore predefinito, si influisce sul comportamento del profilo dell'istanza di bilanciamento del carico con effetti sull'intero cluster.

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>Quota necessaria per la personalizzazione di allocatedOutboundPorts
È necessario disporre di una capacità IP in uscita sufficiente in base al numero di macchine virtuali del nodo e alle porte in uscita allocate desiderate. Per verificare di disporre di una capacità IP in uscita sufficiente, usare la formula seguente: 
 
*Indirizzi IP in uscita* \* 64.000 \> *macchine virtuali del nodo* \* *desiredAllocatedOutboundPorts*.
 
Se, ad esempio, si dispone di 3 *macchine virtuali del nodo* e di 50.000 *desiredAllocatedOutboundPorts*, è necessario disporre di almeno 3 *indirizzi IP in uscita*. Si consiglia di incorporare una capacità IP in uscita aggiuntiva oltre quella necessaria. Inoltre, è necessario tenere presente il ridimensionamento automatico del cluster e la possibilità di aggiornamenti del pool di nodi quando si calcola la capacità IP in uscita. Per il ridimensionamento automatico del cluster, esaminare il numero di nodi corrente e il numero massimo di nodi e usare il valore più elevato. Per l'aggiornamento, tenere conto di una macchina virtuale del nodo aggiuntiva per ogni pool di nodi che consente l'aggiornamento.
 
Quando si imposta *IdleTimeoutInMinutes* su un valore diverso da quello predefinito di 30 minuti, considerare per quanto tempo i carichi di lavoro necessiteranno di una connessione in uscita. Tenere presente anche che il valore di timeout predefinito per un'istanza di bilanciamento del carico con SKU *Standard* usata al di fuori del servizio Azure Kubernetes è di 4 minuti. Un valore di *IdleTimeoutInMinutes* che riflette in modo più accurato il carico di lavoro del servizio Azure Kubernetes specifico può contribuire a ridurre l'esaurimento SNAT causato dal collegamento di connessioni non più utilizzate.

## <a name="restrict-access-to-specific-ip-ranges"></a>Limitare l'accesso a intervalli di indirizzi IP specifici

Per impostazione predefinita, il gruppo di sicurezza di rete (NSG) associato alla rete virtuale per l'istanza di bilanciamento del carico ha una regola per consentire tutto il traffico esterno in ingresso. È possibile aggiornare questa regola in modo da consentire solo intervalli di indirizzi IP specifici per il traffico in ingresso. Il manifesto seguente usa *loadBalancerSourceRanges* per specificare un nuovo intervallo di indirizzi IP per il traffico esterno in ingresso:

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

L'esempio precedente aggiorna la regola in modo da consentire solo il traffico esterno in ingresso dall'intervallo *MY_EXTERNAL_IP_RANGE*. Altre informazioni sull'uso di questo metodo per limitare l'accesso al servizio di bilanciamento del carico sono disponibili nella [documentazione di Kubernetes][kubernetes-cloud-provider-firewall].

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
[azure-lb-comparison]: ../load-balancer/skus.md
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
