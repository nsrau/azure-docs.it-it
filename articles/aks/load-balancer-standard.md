---
title: Usare un Load Balancer pubblico
titleSuffix: Azure Kubernetes Service
description: Informazioni su come usare un servizio di bilanciamento del carico pubblico con uno SKU standard per esporre i servizi con Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 06/14/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 417ca42e014c0bb197d7dd834b960f25fcfdf468
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87056799"
---
# <a name="use-a-public-standard-load-balancer-in-azure-kubernetes-service-aks"></a>Usare un Load Balancer Standard pubblico in Azure Kubernetes Service (AKS)

Il Azure Load Balancer è una versione L4 del modello OSI (Open Systems Interconnection) che supporta gli scenari in ingresso e in uscita. Distribuisce i flussi in ingresso che arrivano al front-end del servizio di bilanciamento del carico alle istanze del pool back-end.

Un Load Balancer **pubblico** quando integrato con AKS serve a due scopi:

1. Per fornire connessioni in uscita ai nodi del cluster all'interno della rete virtuale AKS. Questo obiettivo viene raggiunto mediante la conversione dell'indirizzo IP privato dei nodi in un indirizzo IP pubblico che fa parte del *pool in uscita*.
2. Per fornire l'accesso alle applicazioni tramite i servizi Kubernetes di tipo `LoadBalancer` . Con esso, è possibile ridimensionare facilmente le applicazioni e creare servizi a disponibilità elevata.

Viene usato un servizio di bilanciamento del carico **interno (o privato)** in cui sono consentiti solo indirizzi IP privati come front-end. I servizi Load Balancer interni vengono usati per bilanciare il carico all'interno di una rete virtuale. È anche possibile accedere a un front-end di bilanciamento del carico da una rete locale in uno scenario ibrido.

Questo documento illustra l'integrazione con il servizio di bilanciamento del carico pubblico. Per l'integrazione di Load Balancer interni, vedere la documentazione del servizio di [bilanciamento del carico interno di AKS](internal-lb.md).

## <a name="before-you-begin"></a>Prima di iniziare

Azure Load Balancer è disponibile in due SKU: *Basic* e *Standard*. Per impostazione predefinita, lo SKU *standard* viene usato quando si crea un cluster AKS. Usare lo SKU *standard* per avere accesso alle funzionalità aggiunte, ad esempio un pool back-end più ampio, [**più pool di nodi**](use-multiple-node-pools.md)e [**zone di disponibilità**](availability-zones.md). Si tratta della SKU Load Balancer consigliata per AKS.

Per altre informazioni sugli SKU *Basic* e *Standard*, vedere [Confronto tra SKU di Azure Load Balancer][azure-lb-comparison].

Questo articolo presuppone che sia presente un cluster AKS con lo SKU *Standard* Azure Load Balancer e illustra come usare e configurare alcune funzionalità e funzionalità del servizio di bilanciamento del carico. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

> [!IMPORTANT]
> Se si preferisce non usare i Azure Load Balancer per fornire la connessione in uscita e si dispone invece del proprio gateway, firewall o proxy a tale scopo, è possibile ignorare la creazione del pool in uscita del servizio di bilanciamento del carico e dei rispettivi indirizzi IP front-end usando il [**tipo in uscita come UserDefinedRouting (UDR)**](egress-outboundtype.md). Il tipo in uscita definisce il metodo in uscita per un cluster e per impostazione predefinita è il tipo: Load Balancer.

## <a name="use-the-public-standard-load-balancer"></a>Usare il servizio di bilanciamento del carico standard pubblico

Dopo la creazione di un cluster AKS con tipo in uscita: Load Balancer (impostazione predefinita), il cluster è pronto per l'uso del servizio di bilanciamento del carico per esporre anche i servizi.

A tale proposito, è possibile creare un servizio pubblico di tipo `LoadBalancer` , come illustrato nell'esempio seguente. Iniziare creando un manifesto del servizio denominato `public-svc.yaml` :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: public-svc
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: public-app
```

Distribuire il manifesto del servizio pubblico usando [kubectl Apply][kubectl-apply] e specificare il nome del manifesto YAML:

```azurecli-interactive
kubectl apply -f public-svc.yaml
```

Il Azure Load Balancer verrà configurato con un nuovo indirizzo IP pubblico che precederà il nuovo servizio. Poiché le Azure Load Balancer possono avere più IP front-end, ogni nuovo servizio distribuito otterrà un nuovo IP front-end dedicato a cui accedere in modo univoco.

È possibile verificare che il servizio sia stato creato e che il servizio di bilanciamento del carico sia configurato eseguendo, ad esempio:

```azurecli-interactive
kubectl get service public-svc
```

```console
NAMESPACE     NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
default       public-svc    LoadBalancer   10.0.39.110    52.156.88.187   80:32068/TCP    52s
```

Quando si visualizzano i dettagli del servizio, l'indirizzo IP pubblico creato per questo servizio nel servizio di bilanciamento del carico viene visualizzato nella colonna *External-IP* . La modifica dell'indirizzo IP da *\<pending\>* a un indirizzo IP pubblico effettivo potrebbe richiedere un paio di minuti, come illustrato nell'esempio precedente.

## <a name="configure-the-public-standard-load-balancer"></a>Configurare il servizio di bilanciamento del carico standard pubblico

Quando si usa il servizio di bilanciamento del carico pubblico con SKU standard, è disponibile un set di opzioni che possono essere personalizzate in fase di creazione o aggiornando il cluster. Queste opzioni consentono di personalizzare il Load Balancer per soddisfare le esigenze dei carichi di lavoro e devono essere esaminati di conseguenza. Con il servizio di bilanciamento del carico standard è possibile:

* Impostare o ridimensionare il numero di indirizzi IP in uscita gestiti
* Portare [indirizzi IP in uscita personalizzati o prefisso IP in uscita](#provide-your-own-outbound-public-ips-or-prefixes)
* Personalizzare il numero di porte in uscita allocate per ogni nodo del cluster
* Configurare l'impostazione di timeout per le connessioni inattive

### <a name="scale-the-number-of-managed-outbound-public-ips"></a>Ridimensionare il numero di indirizzi IP pubblici in uscita gestiti

Azure Load Balancer offre connettività in uscita da una rete virtuale oltre alla connettività in ingresso. Le regole in uscita semplificano la configurazione della conversione degli indirizzi di rete in uscita di Load Balancer Standard pubblico.

Come tutte le regole di Load Balancer, le regole in uscita seguono la stessa sintassi familiare delle regole di bilanciamento del carico e delle regole NAT in ingresso:

***Indirizzi IP e parametri front-end + pool back-end***

Una regola in uscita configura NAT in uscita per tutte le macchine virtuali identificate dal pool back-end da convertire in front-end. E i parametri offrono ulteriore controllo dettagliato sull'algoritmo NAT in uscita.

Anche se una regola in uscita può essere usata con un singolo indirizzo IP pubblico, le regole in uscita semplificano l'impegno di configurazione per la scalabilità di NAT in uscita. È possibile usare più indirizzi IP per gestire scenari su larga scala e le regole in uscita per risolvere i problemi dei modelli soggetti a esaurimento delle porte SNAT. Ogni indirizzo IP aggiuntivo fornito da un front-end fornisce porte temporanee 64K per Load Balancer da usare come porte SNAT. 

Quando si usa un servizio di bilanciamento del carico con SKU *standard* con indirizzi IP pubblici in uscita gestiti, che vengono creati per impostazione predefinita, è possibile ridimensionare il numero di indirizzi IP pubblici in uscita gestiti usando il **`load-balancer-managed-ip-count`** parametro.

Per aggiornare un cluster esistente, eseguire il comando seguente. Questo parametro può essere impostato anche in fase di creazione del cluster, per avere più indirizzi IP pubblici in uscita gestiti.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

L'esempio precedente imposta il numero di indirizzi IP pubblici in uscita gestiti su *2* per il cluster *myAKSCluster* in *myResourceGroup*. 

È anche possibile usare il **`load-balancer-managed-ip-count`** parametro per impostare il numero iniziale di indirizzi IP pubblici in uscita gestiti durante la creazione del cluster aggiungendo il **`--load-balancer-managed-outbound-ip-count`** parametro e impostandolo sul valore desiderato. Il numero predefinito di indirizzi IP pubblici in uscita gestiti è 1.

### <a name="provide-your-own-outbound-public-ips-or-prefixes"></a>Fornire gli indirizzi IP pubblici in uscita o i prefissi

Quando si usa un servizio di bilanciamento del carico con SKU *standard* , per impostazione predefinita il cluster AKS crea automaticamente un indirizzo IP pubblico nel gruppo di risorse dell'infrastruttura gestita da AKS e lo assegna al pool in uscita del servizio di bilanciamento del carico.

Un indirizzo IP pubblico creato da AKS è considerato una risorsa gestita da AKS. Ciò significa che il ciclo di vita di tale indirizzo IP pubblico deve essere gestito da AKS e non richiede alcuna azione da parte dell'utente direttamente sulla risorsa IP pubblico. In alternativa, è possibile assegnare un indirizzo IP pubblico o un prefisso IP pubblico personalizzato al momento della creazione del cluster. Gli IP personalizzati possono essere aggiornati anche nelle proprietà del servizio di bilanciamento del carico di un cluster esistente.

> [!NOTE]
> Gli indirizzi IP pubblici personalizzati devono essere creati e di proprietà dell'utente. Gli indirizzi IP pubblici gestiti creati da AKS non possono essere riutilizzati come IP personalizzato Bring your own perché può causare conflitti di gestione.

Prima di eseguire questa operazione, assicurarsi di soddisfare i [prerequisiti e i vincoli](../virtual-network/public-ip-address-prefix.md#constraints) necessari per configurare gli indirizzi IP in uscita o i prefissi IP in uscita.

#### <a name="update-the-cluster-with-your-own-outbound-public-ip"></a>Aggiornare il cluster con il proprio indirizzo IP pubblico in uscita

Usare il comando [az network public-ip show][az-network-public-ip-show] per elencare gli ID degli indirizzi IP pubblici.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Il comando precedente mostra l'ID dell'indirizzo IP pubblico *myPublicIP* nel gruppo di risorse *myResourceGroup*.

Usare il `az aks update` comando con il **`load-balancer-outbound-ips`** parametro per aggiornare il cluster con gli indirizzi IP pubblici.

Nell'esempio seguente viene usato il `load-balancer-outbound-ips` parametro con gli ID del comando precedente.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

#### <a name="update-the-cluster-with-your-own-outbound-public-ip-prefix"></a>Aggiornare il cluster con il proprio prefisso IP pubblico in uscita

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

#### <a name="create-the-cluster-with-your-own-public-ip-or-prefixes"></a>Creare il cluster con un indirizzo IP pubblico o prefissi

Per supportare scenari come l'aggiunta di endpoint in uscita a un elenco consentiti, è possibile che si vogliano importare indirizzi IP o prefissi IP per l'uscita al momento della creazione del cluster. Aggiungere gli stessi parametri illustrati in precedenza al passaggio di creazione del cluster per definire gli indirizzi IP pubblici e i prefissi degli indirizzi IP all'inizio del ciclo di vita di un cluster.

Per creare un nuovo cluster con gli indirizzi IP pubblici all'inizio, usare il comando *az aks create* con il parametro *load-balancer-outbound-ips*.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Per creare un nuovo cluster con i prefissi degli indirizzi IP pubblici all'inizio, usare il comando *az aks create* con il parametro *load-balancer-outbound-ip-prefixes*.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="configure-the-allocated-outbound-ports"></a>Configurare le porte in uscita allocate

> [!IMPORTANT]
> Se nel cluster sono presenti applicazioni in cui si prevede di stabilire un numero elevato di connessioni a un set limitato di destinazioni, ad esempio. molte istanze front-end che si connettono a un database SQL presentano uno scenario molto suscettibile di rilevare l'esaurimento delle porte SNAT (esaurimento delle porte per la connessione da). Per questi scenari è consigliabile aumentare le porte in uscita allocate e gli IP front-end in uscita nel servizio di bilanciamento del carico. L'aumento dovrebbe considerare che un (1) indirizzo IP aggiuntivo aggiunge porte aggiuntive 64K per la distribuzione in tutti i nodi del cluster.


Se non diversamente specificato, AKS utilizzerà il valore predefinito delle porte in uscita allocate che Load Balancer Standard definisce durante la configurazione. Questo valore è **null** nell'API AKS o **0** nell'API SLB, come illustrato nel comando seguente:

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

Questo output non significa che sono disponibili 0 porte, ma che si sta sfruttando l' [assegnazione automatica delle porte in uscita in base alle dimensioni del pool back-end][azure-lb-outbound-preallocatedports], quindi, ad esempio, se un cluster ha 50 o meno nodi, vengono allocate 1024 porte per ogni nodo, man mano che si aumenta il numero di nodi da questa posizione, si otterranno gradualmente meno porte per nodo.


Per definire o aumentare il numero di porte in uscita allocate, è possibile seguire l'esempio seguente:


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 7 \
    --load-balancer-outbound-ports 4000
```

Questo esempio darebbe 4000 porte in uscita allocate per ogni nodo nel cluster e con 7 indirizzi IP sono disponibili *4000 porte per nodo * 100 nodes = 400.000 Total ports < = 448K Total Ports = 7 IPS * 64K Ports per IP*. Questo consentirebbe di ridimensionare in modo sicuro fino a 100 nodi e di avere un'operazione di aggiornamento predefinita. È fondamentale allocare porte sufficienti per i nodi aggiuntivi necessari per l'aggiornamento e altre operazioni. Per impostazione predefinita, AKS è un nodo buffer per l'aggiornamento. in questo esempio sono necessarie 4000 porte gratuite in un dato momento. Se si usano [valori maxSurge](upgrade-cluster.md#customize-node-surge-upgrade-preview), moltiplicare le porte in uscita per nodo in base al valore di maxSurge.

Per superare in modo sicuro oltre 100 nodi, è necessario aggiungere altri indirizzi IP.


> [!IMPORTANT]
> È necessario [calcolare la quota necessaria e verificare i requisiti][requirements] prima di personalizzare *allocatedOutboundPorts* per evitare problemi di connettività o di ridimensionamento.

È anche possibile usare i **`load-balancer-outbound-ports`** parametri durante la creazione di un cluster, ma è anche necessario specificare **`load-balancer-managed-outbound-ip-count`** , **`load-balancer-outbound-ips`** o **`load-balancer-outbound-ip-prefixes`** .  Ad esempio:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku standard \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 1024 
```

### <a name="configure-the-load-balancer-idle-timeout"></a>Configurare il timeout di inattività del servizio di bilanciamento del carico

Quando si esauriscono le risorse di porte SNAT, i flussi in uscita vengono completati dopo che i flussi esistenti rilasciano le porte SNAT. Load Balancer recupera le porte SNAT quando il flusso viene chiuso e il servizio di bilanciamento del carico configurato da AKS usa un timeout di inattività di 30 minuti per recuperare le porte SNAT dai flussi inattivi.
È anche possibile usare il trasporto (ad esempio, **`TCP keepalives`** ) o **`application-layer keepalives`** per aggiornare un flusso inattivo e reimpostare il timeout di inattività, se necessario. È possibile configurare questo timeout attenendosi all'esempio seguente: 


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-idle-timeout 4
```

Se si prevede di avere numerose connessioni di breve durata e non ci sono connessioni di lunga durata e che potrebbero avere lunghi tempi di inattività, `kubectl proxy` `kubectl port-forward` ad esempio sfruttare o prendere in considerazione l'uso di un valore di timeout basso, ad esempio 4 minuti. Inoltre, quando si utilizzano i keep-alive TCP, è sufficiente abilitarli su un lato della connessione. Ad esempio, è sufficiente abilitarli sul lato server solo per reimpostare il timer di inattività del flusso e non è necessario che entrambi i lati avviino i keep-alive TCP. Si applicano concetti simili anche per il livello dell'applicazione, tra cui le configurazioni client-server di database. Controllare il lato server per individuare le opzioni disponibili per i Keep-Alive specifici dell'applicazione.

> [!IMPORTANT]
> AKS Abilita il reset TCP in inattività per impostazione predefinita e consiglia di mantenerla e di usarla per un comportamento più prevedibile dell'applicazione negli scenari in uso.
> Il protocollo RST TCP viene inviato solo durante la connessione TCP nello stato stabilito. Per altre informazioni, leggere [qui](../load-balancer/load-balancer-tcp-reset.md).

### <a name="requirements-for-customizing-allocated-outbound-ports-and-idle-timeout"></a>Requisiti per la personalizzazione delle porte in uscita allocate e del timeout di inattività

- Il valore specificato per *allocatedOutboundPorts* deve essere anche un multiplo di 8.
- È necessario disporre di una capacità IP in uscita sufficiente in base al numero di macchine virtuali del nodo e alle porte in uscita allocate richieste. Per verificare di disporre di una capacità IP in uscita sufficiente, usare la formula seguente: 
 
*Indirizzi IP in uscita* \* 64.000 \> *macchine virtuali del nodo* \* *desiredAllocatedOutboundPorts*.
 
Se, ad esempio, si dispone di 3 *macchine virtuali del nodo* e di 50.000 *desiredAllocatedOutboundPorts*, è necessario disporre di almeno 3 *indirizzi IP in uscita*. Si consiglia di incorporare una capacità IP in uscita aggiuntiva oltre quella necessaria. Inoltre, è necessario tenere presente il ridimensionamento automatico del cluster e la possibilità di aggiornamenti del pool di nodi quando si calcola la capacità IP in uscita. Per il ridimensionamento automatico del cluster, esaminare il numero di nodi corrente e il numero massimo di nodi e usare il valore più elevato. Per l'aggiornamento, tenere conto di una macchina virtuale del nodo aggiuntiva per ogni pool di nodi che consente l'aggiornamento.
 
- Quando si imposta *IdleTimeoutInMinutes* su un valore diverso da quello predefinito di 30 minuti, considerare per quanto tempo i carichi di lavoro necessiteranno di una connessione in uscita. Tenere presente anche che il valore di timeout predefinito per un'istanza di bilanciamento del carico con SKU *Standard* usata al di fuori del servizio Azure Kubernetes è di 4 minuti. Un valore di *IdleTimeoutInMinutes* che riflette in modo più accurato il carico di lavoro del servizio Azure Kubernetes specifico può contribuire a ridurre l'esaurimento SNAT causato dal collegamento di connessioni non più utilizzate.

> [!WARNING]
> La modifica dei valori di *AllocatedOutboundPorts* e *IdleTimeoutInMinutes* può modificare in modo significativo il comportamento della regola in uscita per il servizio di bilanciamento del carico e non deve essere eseguita con leggerezza, senza conoscere i compromessi e i modelli di connessione dell'applicazione, controllare la [sezione relativa alla risoluzione dei problemi di SNAT di seguito][troubleshoot-snat] ed esaminare le regole in uscita [Load Balancer][azure-lb-outbound-rules-overview] e le [connessioni in uscita in Azure][azure-lb-outbound-connections] prima di aggiornare questi valori per comprendere


## <a name="restrict-inbound-traffic-to-specific-ip-ranges"></a>Limitare il traffico in ingresso a intervalli IP specifici

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

## <a name="maintain-the-clients-ip-on-inbound-connections"></a>Mantenere l'indirizzo IP del client per le connessioni in ingresso

Per impostazione predefinita, un servizio di tipo `LoadBalancer` [in Kubernetes](https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer) e in AKS non rende permanente l'indirizzo IP del client nella connessione al Pod. L'IP di origine nel pacchetto che viene recapitato al Pod sarà l'indirizzo IP privato del nodo. Per mantenere l'indirizzo IP del client, è necessario impostare `service.spec.externalTrafficPolicy` su `local` nella definizione del servizio. Il manifesto seguente mostra un esempio:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  externalTrafficPolicy: Local
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="additional-customizations-via-kubernetes-annotations"></a>Personalizzazioni aggiuntive tramite annotazioni Kubernetes

Di seguito è riportato un elenco di annotazioni supportate per i servizi Kubernetes con tipo `LoadBalancer` . queste annotazioni si applicano solo ai flussi in **ingresso** :

| Annotazione | Valore | Descrizione
| ----------------------------------------------------------------- | ------------------------------------- | ------------------------------------------------------------ 
| `service.beta.kubernetes.io/azure-load-balancer-internal`         | `true` o `false`                     | Consente di specificare se il servizio di bilanciamento del carico deve essere interno. Il valore predefinito è Public se non impostato.
| `service.beta.kubernetes.io/azure-load-balancer-internal-subnet`  | Nome della subnet                    | Specificare la subnet a cui deve essere associato il servizio di bilanciamento del carico interno. Il valore predefinito è la subnet configurata nel file di configurazione cloud se non è impostata.
| `service.beta.kubernetes.io/azure-dns-label-name`                 | Nome dell'etichetta DNS negli indirizzi IP pubblici   | Specificare il nome dell'etichetta DNS per il servizio **pubblico** . Se è impostato su una stringa vuota, la voce DNS nell'indirizzo IP pubblico non verrà usata.
| `service.beta.kubernetes.io/azure-shared-securityrule`            | `true` o `false`                     | Specificare che il servizio deve essere esposto usando una regola di sicurezza di Azure che può essere condivisa con un altro servizio, scambiando la specificità delle regole per un aumento del numero di servizi che possono essere esposti. Questa annotazione si basa sulla funzionalità delle [regole di sicurezza aumentata](../virtual-network/security-overview.md#augmented-security-rules) di Azure dei gruppi di sicurezza di rete. 
| `service.beta.kubernetes.io/azure-load-balancer-resource-group`   | Nome del gruppo di risorse            | Specificare il gruppo di risorse degli indirizzi IP pubblici del servizio di bilanciamento del carico che non si trovano nello stesso gruppo di risorse dell'infrastruttura del cluster (gruppo di risorse nodo).
| `service.beta.kubernetes.io/azure-allowed-service-tags`           | Elenco di tag di servizio consentiti          | Consente di specificare un elenco di [tag di servizio](../virtual-network/security-overview.md#service-tags) consentiti separati da virgola.
| `service.beta.kubernetes.io/azure-load-balancer-tcp-idle-timeout` | Timeout di inattività TCP in minuti          | Specificare l'intervallo di tempo, in minuti, per cui si verificano i timeout di inattività della connessione TCP nel servizio di bilanciamento del carico. Il valore predefinito e il valore minimo è 4. Il valore massimo è 30. Deve essere un valore Integer.
|`service.beta.kubernetes.io/azure-load-balancer-disable-tcp-reset` | `true`                                | Disabilitare `enableTcpReset` per SLB


## <a name="troubleshooting-snat"></a>Risoluzione dei problemi relativi a SNAT

Se si è certi che si stanno iniziando molte connessioni TCP o UDP in uscita allo stesso indirizzo IP e alla stessa porta di destinazione e si osservano le connessioni in uscita con esito negativo o si è informati del supporto per l'esaurimento delle porte SNAT (porte effimere preallocate usate da PAT), sono disponibili diverse opzioni di mitigazione generali. Esaminare queste opzioni e scegliere quella disponibile e ottimale per lo scenario specifico. È possibile che una o più opzioni risultino utili per gestire questo scenario. Per informazioni dettagliate, vedere la [Guida alla risoluzione dei problemi relativi alle connessioni in uscita](../load-balancer/troubleshoot-outbound-connection.md).

Spesso la causa radice dell'esaurimento SNAT è un anti-modello per il modo in cui la connettività in uscita viene stabilita e gestita oppure per come vengono cambiati i timer configurabili rispetto ai valori predefiniti. Leggere attentamente questa sezione.

### <a name="steps"></a>Passaggi
1. Controllare se le connessioni rimangono inattive per molto tempo e si basano sul timeout di inattività predefinito per rilasciare la porta. In caso affermativo, potrebbe essere necessario ridurre il timeout predefinito di 30 minuti per lo scenario.
2. Esaminare il modo in cui l'applicazione crea la connettività in uscita, ad esempio, revisione del codice o acquisizione di pacchetti.
3. Determinare se questa attività è un comportamento previsto o se l'applicazione non funziona correttamente. Usare le [metriche](../load-balancer/load-balancer-standard-diagnostics.md) e i [log](../load-balancer/load-balancer-monitor-log.md) in monitoraggio di Azure per convalidare i risultati. Usare la categoria "failed" per la metrica delle connessioni SNAT, ad esempio.
4. Valutare se vengono seguiti i [modelli](#design-patterns) appropriati.
5. Valutare se l'esaurimento delle porte SNAT deve essere mitigato con [indirizzi IP in uscita aggiuntivi + porte in uscita allocate aggiuntive](#configure-the-allocated-outbound-ports) .

### <a name="design-patterns"></a>Modelli di progettazione
Quando possibile, sfruttare sempre i vantaggi del riutilizzo delle connessioni e del pool di connessioni. Questi modelli eviteranno problemi di esaurimento delle risorse e genereranno un comportamento prevedibile. Le primitive per questi modelli sono reperibili in molti framework e librerie di sviluppo.

- Le richieste atomiche (una richiesta per connessione) non sono in genere una scelta ottimale. Questo anti-modello limita la scalabilità, riduce le prestazioni e diminuisce l'affidabilità. Riutilizzare invece le connessioni HTTP/S per ridurre il numero di connessioni e delle porte SNAT associate. La scalabilità dell'applicazione aumenterà e le prestazioni miglioreranno a causa di handshake ridotti, sovraccarico e costi operativi crittografici quando si usa TLS.
- Se si usa un cluster personalizzato o un DNS personalizzato oppure i server upstream personalizzati in coreDNS si ricordano che DNS può introdurre molti flussi individuali in volume quando il client non memorizza nella cache il risultato dei resolver DNS. Assicurarsi di personalizzare prima coreDNS invece di usare i server DNS personalizzati e definire un valore di memorizzazione nella cache valido.
- I flussi UDP, ad esempio le ricerche DNS, allocano le porte SNAT per la durata del timeout di inattività. Più è lungo il timeout di inattività, maggiore è la pressione sulle porte SNAT. Usare un timeout di inattività breve (ad esempio 4 minuti).
Usare i pool di connessioni per definire il volume di connessioni.
- Non abbandonare mai un flusso TCP e non fare affidamento sui timer TCP per pulire il flusso. Se non si lascia che TCP chiuda in modo esplicito la connessione, lo stato rimane allocato in endpoint e sistemi intermedi e rende le porte SNAT non disponibili per altre connessioni. Questo schema può generare errori dell'applicazione e l'esaurimento SNAT.
- Non cambiare i valori del timer correlati alla chiusura TCP a livello di sistema operativo se non se ne conosce perfettamente l'impatto. Mentre lo stack TCP viene recuperato, le prestazioni dell'applicazione possono essere influenzate negativamente quando gli endpoint di una connessione hanno aspettative non corrispondenti. L'intenzione di modificare i timer è in genere un segno di un problema di progettazione sottostante. Esaminare le raccomandazioni seguenti.


L'esempio precedente aggiorna la regola in modo da consentire solo il traffico esterno in ingresso dall'intervallo *MY_EXTERNAL_IP_RANGE*. Se si sostituisce *MY_EXTERNAL_IP_RANGE* con l'indirizzo IP della subnet interna, il traffico viene limitato solo agli indirizzi IP interni del cluster. Ciò non consentirà ai client esterni al cluster Kubernetes di accedere al servizio di bilanciamento del carico.

## <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Passaggio da un servizio di bilanciamento del carico SKU Basic a uno SKU standard

Se si dispone di un cluster esistente con il servizio Load Balancer con SKU Basic, quando si esegue la migrazione per l'uso di un cluster con il servizio Load Balancer con SKU Standard, è necessario tenere presenti alcune importanti differenze di comportamento.

Ad esempio, l'esecuzione di distribuzioni blu/verde per la migrazione dei cluster è una pratica comune, dato che il tipo di `load-balancer-sku` di un cluster può essere definito solo in fase di creazione del cluster. Tuttavia, i bilanciamenti del carico *SKU Basic* usano gli indirizzi IP dello *SKU Basic* , che non sono compatibili con i bilanciamenti del carico *SKU standard* perché richiedono indirizzi IP *SKU standard* . Quando si esegue la migrazione dei cluster per aggiornare gli SKU di Load Balancer, sarà necessario un nuovo indirizzo IP con uno SKU compatibile per l'indirizzo IP.

Per altre considerazioni su come eseguire la migrazione dei cluster, vedere la [documentazione sulle considerazioni relative alla migrazione](aks-migration.md), in modo da consultare un elenco di argomenti importanti da tenere presenti durante la migrazione. Le limitazioni seguenti includono differenze comportamentali importanti da tenere presenti quando si usano istanze di Load Balancer con SKU Standard nel servizio Azure Kubernetes.

## <a name="limitations"></a>Limitazioni

Quando si creano e si gestiscono cluster del servizio Azure Kubernetes che supportano un'istanza di bilanciamento del carico con SKU *Standard*, si applicano le limitazioni seguenti:

* È necessario almeno un indirizzo IP pubblico o un prefisso dell'indirizzo IP per consentire il traffico in uscita dal cluster del servizio Azure Kubernetes. L'IP pubblico o il prefisso IP è necessario anche per mantenere la connettività tra il piano di controllo e i nodi dell'agente e per mantenere la compatibilità con le versioni precedenti di AKS. Per specificare gli indirizzi IP pubblici o i prefissi degli indirizzi IP con un servizio di bilanciamento del carico con SKU *Standard*, sono disponibili le opzioni seguenti:
    * Specificare gli indirizzi IP pubblici personalizzati.
    * Specificare i prefissi degli indirizzi IP pubblici personalizzati.
    * Specificare un numero massimo di 100 per consentire al cluster del servizio Azure Kubernetes di creare una certa quantità di indirizzi IP pubblici dello SKU *Standard* nello stesso gruppo di risorse creato dal cluster del servizio Azure Kubernetes, che in genere viene denominato con *MC_* all'inizio. Il servizio Azure Kubernetes assegna l'indirizzo IP pubblico all'istanza di bilanciamento del carico con SKU *Standard*. Per impostazione predefinita, un indirizzo IP pubblico verrà creato automaticamente nello stesso gruppo di risorse del cluster del servizio Azure Kubernetes, se non è specificato alcun indirizzo IP pubblico, prefisso di indirizzo IP pubblico o numero di indirizzi IP. È necessario anche consentire gli indirizzi pubblici ed evitare di creare criteri di Azure che vietino la creazione di IP.
* Non è possibile riusare un IP pubblico creato da AKS come indirizzo IP pubblico personalizzato. Tutti gli indirizzi IP personalizzati devono essere creati e gestiti dall'utente.
* La definizione dello SKU per un servizio di bilanciamento del carico può essere eseguita solo quando si crea un cluster del servizio Azure Kubernetes. Non è possibile modificare lo SKU del servizio di bilanciamento del carico dopo che è stato creato un cluster AKS.
* È possibile usare un solo tipo di SKU dell'istanza di bilanciamento del carico (Basic o Standard) in un singolo cluster.
* Le istanze di bilanciamento del carico con SKU *Standard* supportano solo indirizzi IP con SKU *Standard*.


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui servizi Kubernetes sono disponibili nella [documentazione relativa ai servizi Kubernetes][kubernetes-services].

Per altre informazioni sull'uso di Load Balancer interni per il traffico in ingresso, vedere la [documentazione interna di Load Balancer AKS](internal-lb.md).

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
[azure-lb-comparison]: ../load-balancer/skus.md
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[requirements]: #requirements-for-customizing-allocated-outbound-ports-and-idle-timeout
[use-multiple-node-pools]: use-multiple-node-pools.md
[troubleshoot-snat]: #troubleshooting-snat
