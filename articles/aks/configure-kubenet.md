---
title: Configurare funzionalità di rete kubenet nel servizio Azure Kubernetes
description: Informazioni su come configurare una rete kubenet (di base) nel servizio Azure Kubernetes per distribuire un cluster del servizio Azure Kubernetes in una rete virtuale e una subnet esistenti.
services: container-service
ms.topic: article
ms.date: 06/02/2020
ms.reviewer: nieberts, jomore
ms.openlocfilehash: c30b82e44833e413c1576bf64e8fef263c58b246
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91264610"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Usare funzionalità di rete kubenet con i propri intervalli di indirizzi IP nel servizio Azure Kubernetes

Per impostazione predefinita, i cluster del servizio Azure Kubernetes usano [kubenet][kubenet] e la creazione di una rete virtuale e di una subnet di Azure avviene automaticamente. Con *kubenet* i nodi ottengono un indirizzo IP dalla subnet della rete virtuale di Azure. I pod ricevono un indirizzo IP da uno spazio indirizzi logicamente diverso per la subnet della rete virtuale di Azure dei nodi. Viene poi configurato il protocollo NAT (Network Address Translation) in modo che i pod possano raggiungere le risorse nella rete virtuale di Azure. L'indirizzo IP di origine del traffico viene convertito tramite NAT nell'indirizzo IP primario del nodo. Questo approccio riduce notevolmente il numero di indirizzi IP che è necessario riservare ai pod nello spazio di indirizzi della rete.

Con [Azure Container Networking Interface (CNI)][cni-networking] ogni pod ottiene un indirizzo IP dalla subnet in modo che vi si possa accedere direttamente. Questi indirizzi IP devono essere univoci nello spazio di indirizzi della rete e devono essere pianificati in anticipo. Ogni nodo ha un parametro di configurazione per il numero massimo di pod che supporta. Il numero equivalente di indirizzi IP per nodo viene quindi riservato anticipatamente per tale nodo. Questo approccio richiede una maggiore pianificazione e spesso conduce all'esaurimento degli indirizzi IP o alla necessità di riconfigurare i cluster in una subnet di dimensioni maggiori man mano che aumentano le richieste dell'applicazione. È possibile configurare il numero massimo di Pod distribuibile in un nodo in fase di creazione del cluster o quando si creano nuovi pool di nodi. Se non si specifica maxPods durante la creazione di nuovi pool di nodi, viene visualizzato il valore predefinito 110 per kubenet.

Questo articolo illustra come usare le funzionalità di rete *kubenet* per creare e usare una subnet di rete virtuale per un cluster del servizio Azure Kubernetes. Per altre informazioni sulle opzioni di rete e le relative considerazioni, vedere [Concetti relativi alla rete per le applicazioni nel servizio Azure Kubernetes][aks-network-concepts].

## <a name="prerequisites"></a>Prerequisiti

* La rete virtuale per il cluster servizio Azure Kubernetes deve consentire la connettività Internet in uscita.
* Non creare più di un cluster servizio Azure Kubernetes nella stessa subnet.
* I cluster AKS non possono usare `169.254.0.0/16` , `172.30.0.0/16` , `172.31.0.0/16` o `192.0.2.0/24` per l'intervallo di indirizzi del servizio Kubernetes, l'intervallo di indirizzi pod o l'intervallo di indirizzi della rete virtuale del cluster.
* L'entità servizio usata dal cluster AKS deve avere almeno un ruolo di [collaboratore rete](../role-based-access-control/built-in-roles.md#network-contributor) nella subnet nella rete virtuale. È inoltre necessario disporre delle autorizzazioni appropriate, ad esempio il proprietario della sottoscrizione, per creare un'entità servizio e assegnare le autorizzazioni. Se si vuole definire un [ruolo personalizzato](../role-based-access-control/custom-roles.md) invece di usare il ruolo predefinito Collaboratore di rete, sono necessarie le autorizzazioni seguenti:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

> [!WARNING]
> Per usare i pool di nodi di Windows Server, è necessario usare Azure CNI. L'uso di kubenet come modello di rete non è disponibile per i contenitori di Windows Server.

## <a name="before-you-begin"></a>Prima di iniziare

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.65 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Panoramica delle funzionalità di rete kubenet con la propria subnet

Molti ambienti sono caratterizzati da reti virtuali e subnet definite con intervalli di indirizzi IP allocati. Queste risorse di rete virtuale vengono usate per supportare più servizi e applicazioni. Per fornire connettività di rete, i cluster del servizio Azure Kubernetes possono usare *kubenet* (funzionalità di rete di base) o Azure CNI (*funzionalità di rete avanzate*).

Con *kubenet* solo i nodi ricevono un indirizzo IP nella subnet della rete virtuale. I pod non possono comunicare tra loro direttamente. Per la connettività tra i pod nei vari nodi vengono usati il routing definito dall'utente e l'inoltro di indirizzi IP. Per impostazione predefinita, la configurazione di inoltro IP e UdR viene creata e gestita dal servizio AKS, ma è possibile scegliere di [importare una tabella di route personalizzata per la gestione di route personalizzata][byo-subnet-route-table]. Si potrebbero anche distribuire i pod dietro un servizio che riceve un indirizzo IP assegnato e bilancia il carico del traffico dell'applicazione. Il diagramma seguente mostra che i nodi del servizio Azure Kubernetes ricevono un indirizzo IP nella subnet della rete virtuale, mentre i pod non lo ricevono:

![Modello di rete Kubenet con un cluster del servizio Azure Kubernetes](media/use-kubenet/kubenet-overview.png)

Azure supporta un massimo di 400 route in un routing definito dall'utente, quindi un cluster del servizio Azure Kubernetes non può avere più di 400 nodi. I [nodi virtuali][virtual-nodes] AKS e i criteri di rete di Azure non sono supportati con *kubenet*.  È possibile usare i [criteri di rete di calice][calico-network-policies], perché sono supportati con kubenet.

Con *Azure CNI* ogni pod riceve un indirizzo IP nella subnet IP e può comunicare direttamente con altri pod e servizi. I cluster possono avere le stesse dimensioni dell'intervallo di indirizzi IP specificato. Questo intervallo deve però essere pianificato in anticipo e tutti gli indirizzi IP vengono utilizzati dai nodi del servizio Azure Kubernetes in base al numero massimo di pod che possono supportare. Le funzionalità e gli scenari di rete avanzati, ad esempio i [nodi virtuali][virtual-nodes] o i criteri di rete (Azure o calice) sono supportati con *Azure CNI*.

### <a name="limitations--considerations-for-kubenet"></a>Limitazioni & considerazioni per kubenet

* Un hop aggiuntivo è necessario nella progettazione di kubenet, che aggiunge una minore latenza alla comunicazione pod.
* Le tabelle di route e le route definite dall'utente sono necessarie per l'uso di kubenet, che consente di aggiungere complessità alle operazioni.
* L'indirizzamento di Pod diretti non è supportato per kubenet a causa della progettazione kubenet.
* A differenza dei cluster CNI di Azure, più cluster kubenet non possono condividere una subnet.
* Le funzionalità **non supportate in kubenet** includono:
   * [Criteri di rete di Azure](use-network-policies.md#create-an-aks-cluster-and-enable-network-policy), ma i criteri di rete di calice sono supportati in kubenet
   * [Pool di nodi di Windows](windows-node-limitations.md)
   * [Componente aggiuntivo nodi virtuali](virtual-nodes-portal.md#known-limitations)

### <a name="ip-address-availability-and-exhaustion"></a>Disponibilità ed esaurimento degli indirizzi IP

Un problema comune con *Azure CNI* è che l'intervallo di indirizzi IP assegnati è troppo piccolo per potervi aggiungere in seguito altri nodi quando si ridimensiona o si aggiorna un cluster. Il team che si occupa della rete potrebbe inoltre non essere in grado di rendere disponibile un intervallo di indirizzi IP abbastanza ampio da supportare le esigenze dell'applicazione previste.

Come compromesso, è possibile creare un cluster del servizio Azure Kubernetes che usa *kubenet* e connettersi a una subnet di rete virtuale esistente. Questo approccio consente ai nodi di ricevere indirizzi IP definiti, senza bisogno di riservare anticipatamente un numero elevato di indirizzi IP per tutti i potenziali pod che potrebbero essere eseguiti nel cluster.

Con *kubenet* è possibile usare un intervallo di indirizzi IP molto più ridotto ed essere in grado di supportare cluster di grandi dimensioni e più richieste delle applicazioni. Ad esempio, anche con un intervallo di indirizzi IP */27* nella subnet, è possibile eseguire un cluster di nodi 20-25 con spazio sufficiente per la scalabilità o l'aggiornamento. Queste dimensioni del cluster supporterebbero fino a *2.200-2.750* pod (con un valore massimo predefinito di 110 pod per nodo). Il numero massimo di pod per nodo che è possibile configurare con *kubenet* in AKS è 110.

I seguenti calcoli di base mettono a confronto la differenza nei modelli di rete:

- **kubenet**: un semplice intervallo di indirizzi IP */24* può supportare fino a *251* nodi nel cluster (ogni subnet di rete virtuale di Azure riserva i primi tre indirizzi IP per le operazioni di gestione)
  - Questo numero di nodi potrebbe supportare fino a *27.610* pod (con un valore massimo predefinito di 110 pod per nodo con *kubenet*)
- **Azure CNI**: lo stesso intervallo di indirizzi IP di base */24* della subnet potrebbe supportare solo un massimo di *8* nodi nel cluster
  - Questo numero di nodi potrebbe supportare solo fino a *240* pod (con un valore massimo predefinito di 30 pod per nodo con *Azure CNI*)

> [!NOTE]
> Questi valori massimi non tengono in considerazione le operazioni di aggiornamento o ridimensionamento. In pratica, non è possibile eseguire il numero massimo di nodi supportati dall'intervallo di indirizzi IP della subnet. Occorre lasciare alcuni indirizzi IP disponibili per l'uso durante le operazioni di ridimensionamento o aggiornamento.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Peering di rete virtuale e connessioni ExpressRoute

Per fornire connettività locale, sia *kubenet* che *Azure-CNI* possono usare il [peering di rete virtuale di Azure][vnet-peering] o le [connessioni ExpressRoute][express-route]. Pianificare con attenzione gli intervalli di indirizzi IP per evitare sovrapposizioni ed errori di routing del traffico. Ad esempio, molte reti locali usano un intervallo di indirizzi *10.0.0.0/8* che viene annunciato sulla connessione ExpressRoute. È consigliabile creare i cluster AKS nelle subnet della rete virtuale di Azure al di fuori di questo intervallo di indirizzi, ad esempio *172.16.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Scegliere un modello di rete da usare

La scelta del plug-in di rete da usare per il cluster del servizio Azure Kubernetes è in genere un compromesso tra le esigenze di flessibilità e di configurazione avanzata. Le considerazioni seguenti sono utili per stabilire quando ogni modello di rete può essere il più appropriato.

Usare *kubenet* quando:

- Lo spazio indirizzi IP è limitato.
- La maggior parte delle comunicazioni dei pod avviene all'interno del cluster.
- Non sono necessarie funzionalità avanzate di AKS, ad esempio nodi virtuali o criteri di rete di Azure.  Usare i [criteri di rete di calice][calico-network-policies].

Usare *Azure CNI* quando:

- È disponibile uno spazio indirizzi IP adeguato.
- La maggior parte delle comunicazioni dei pod avviene con risorse esterne al cluster.
- Non si vuole gestire le route definite dall'utente per la connettività pod.
- Sono necessarie funzionalità avanzate di AKS, ad esempio nodi virtuali o criteri di rete di Azure.  Usare i [criteri di rete di calice][calico-network-policies].

Per ulteriori informazioni sulla scelta del modello di rete da utilizzare, vedere [confrontare i modelli di rete e il relativo ambito di supporto][network-comparisons].

## <a name="create-a-virtual-network-and-subnet"></a>Creare una rete virtuale e una subnet

Per usare *kubenet* e la propria subnet di rete virtuale, creare prima di tutto un gruppo di risorse usando il comando [az group create][az-group-create]. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Se non esiste già una rete virtuale e una subnet da usare, creare queste risorse di rete usando il comando [az network vnet create][az-network-vnet-create]. Nell'esempio seguente la rete virtuale è denominata *myVnet* con il prefisso dell'indirizzo *192.168.0.0/16*. Viene creata una subnet denominata *myAKSSubnet* con il prefisso dell'indirizzo *192.168.1.0/24*.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Creare un'entità servizio e assegnarle le autorizzazioni

Per consentire a un cluster servizio Azure Kubernetes di interagire con altre risorse di Azure viene usata un'entità servizio di Azure Active Directory. L'entità servizio deve avere le autorizzazioni necessarie per gestire la rete virtuale e la subnet usate dai nodi del servizio Azure Kubernetes. Per creare un'entità servizio, usare il comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

L'output di esempio seguente mostra l'ID applicazione e la password dell'entità servizio. Questi valori vengono usati in passaggi aggiuntivi per assegnare un ruolo all'entità servizio e quindi creare il cluster del servizio Azure Kubernetes:

```azurecli
az ad sp create-for-rbac --skip-assignment
```

```output
{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Per assegnare le deleghe corrette nei passaggi rimanenti, usare i comandi [az network vnet show][az-network-vnet-show] e [az network vnet subnet show][az-network-vnet-subnet-show] per ottenere gli ID di risorsa necessari. Questi ID di risorsa vengono archiviati come variabili e vi viene fatto riferimento nei passaggi successivi:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Assegnare ora l'entità servizio per le autorizzazioni di *collaboratore rete* del cluster AKS per la rete virtuale usando il comando [AZ Role Assignment create][az-role-assignment-create] . Specificare il proprio *\<appId>* come illustrato nell'output del comando precedente per creare l'entità servizio:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role "Network Contributor"
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Creare un cluster del servizio Azure Kubernetes nella rete virtuale

A questo punto sono state create una rete virtuale e una subnet e sono state create e assegnate le autorizzazioni per consentire a un'entità servizio di usare tali risorse di rete. Creare ora un cluster del servizio Azure Kubernetes nella rete virtuale e nella subnet usando il comando [az aks create][az-aks-create]. Definire l'entità servizio *\<appId>* e *\<password>* , come illustrato nell'output del comando precedente per creare l'entità servizio.

Durante il processo di creazione del cluster vengono definiti anche gli intervalli di indirizzi IP seguenti:

* *--service-cidr* viene usato per assegnare un indirizzo IP ai servizi interni nel cluster del servizio Azure Kubernetes. Questo intervallo di indirizzi IP deve essere uno spazio degli indirizzi che non è in uso in un ambiente di rete, inclusi eventuali intervalli di rete locali se ci si connette oppure si prevede di connettersi alle reti virtuali di Azure usando Express route o una connessione VPN da sito a sito.

* L'indirizzo *--dns-service-ip* deve essere l'indirizzo *.10* dell'intervallo di indirizzi IP del servizio.

* *--pod-cidr* deve essere uno spazio indirizzi ampio non in uso in qualsiasi altra posizione nell'ambiente di rete. Questo intervallo include gli intervalli di rete locali se ci si connette o si prevede di connettersi alle reti virtuali di Azure usando Express route o una connessione VPN da sito a sito.
    * Questo intervallo di indirizzi deve essere abbastanza ampio da contenere il numero di nodi in base a cui si prevede di aumentare le dimensioni. Non è possibile cambiare questo intervallo di indirizzi dopo la distribuzione del cluster, qualora fossero necessari altri indirizzi per nodi aggiuntivi.
    * L'intervallo di indirizzi IP Pod viene usato per assegnare uno spazio degli indirizzi */24* a ogni nodo del cluster. Nell'esempio seguente, il *--Pod-CIDR* di *10.244.0.0/16* assegna il primo nodo *10.244.0.0/24*, il secondo nodo *10.244.1.0/24*e il terzo nodo *10.244.2.0/24*.
    * Quando il cluster viene ridimensionato o aggiornato, la piattaforma di Azure continua ad assegnare un intervallo di indirizzi IP dei pod a ogni nuovo nodo.
    
* *--Docker-Bridge-Address* consente ai nodi AKS di comunicare con la piattaforma di gestione sottostante. L'indirizzo IP non deve essere compreso nell'intervallo di indirizzi IP della rete virtuale del cluster e non deve sovrapporsi ad altri intervalli di indirizzi in uso nella rete.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

> [!Note]
> Se si vuole abilitare un cluster AKS per includere un [criterio di rete di calice][calico-network-policies] , è possibile usare il comando seguente.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet --network-policy calico \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

Quando si crea un cluster AKS, vengono creati automaticamente un gruppo di sicurezza di rete e una tabella di route. Queste risorse di rete sono gestite dal piano di controllo AKS. Il gruppo di sicurezza di rete viene associato automaticamente alle schede NIC virtuali nei nodi. La tabella di route viene associata automaticamente alla subnet della rete virtuale. Le regole del gruppo di sicurezza di rete e le tabelle di routing vengono aggiornate automaticamente durante la creazione e l'esposizione dei servizi.

## <a name="bring-your-own-subnet-and-route-table-with-kubenet"></a>Portare la propria subnet e la tabella di route con kubenet

Con kubenet è necessario che sia presente una tabella di route nelle subnet del cluster. AKS supporta l'introduzione della propria subnet e della tabella di route esistenti.

Se la subnet personalizzata non contiene una tabella di route, AKS ne crea una per l'utente e aggiunge le relative regole nell'intero ciclo di vita del cluster. Se la subnet personalizzata contiene una tabella di route quando si crea il cluster, AKS riconosce la tabella di route esistente durante le operazioni del cluster e aggiunge/aggiorna le regole di conseguenza per le operazioni del provider di servizi cloud.

> [!WARNING]
> È possibile aggiungere regole personalizzate alla tabella di route personalizzata e aggiornarle. Tuttavia, le regole vengono aggiunte dal provider di servizi cloud Kubernetes che non devono essere aggiornate o rimosse. Regole come 0.0.0.0/0 devono sempre esistere in una determinata tabella di route ed eseguire il mapping alla destinazione del gateway Internet, ad esempio un appliance virtuale di rete o un altro gateway in uscita. Prestare attenzione quando si aggiornano le regole in cui vengono modificate solo le regole personalizzate.

Altre informazioni sulla configurazione di una [tabella di route personalizzata][custom-route-table].

La rete Kubenet richiede regole della tabella di route organizzata per indirizzare correttamente le richieste. A causa di questa progettazione, le tabelle di route devono essere gestite con attenzione per ogni cluster che si basa su di esso. Più cluster non possono condividere una tabella di route perché Pod CIDRs da cluster diversi possono sovrapporsi, causando un routing imprevisto e interrotta. Quando si configurano più cluster nella stessa rete virtuale o si dedica una rete virtuale a ogni cluster, assicurarsi che siano considerate le limitazioni seguenti.

Limitazioni

* Le autorizzazioni devono essere assegnate prima della creazione del cluster, assicurarsi di usare un'entità servizio con autorizzazioni di scrittura per la subnet personalizzata e la tabella di route personalizzata.
* Le identità gestite non sono attualmente supportate con le tabelle di route personalizzate in kubenet.
* Una tabella di route personalizzata deve essere associata alla subnet prima di creare il cluster AKS.
* Non è possibile aggiornare la risorsa della tabella di route associata dopo la creazione del cluster. Anche se non è possibile aggiornare la risorsa della tabella di route, è possibile modificare le regole personalizzate nella tabella di route.
* Ogni cluster AKS deve usare una singola tabella di route univoca per tutte le subnet associate al cluster. Non è possibile riutilizzare una tabella di route con più cluster a causa della possibilità di sovrapposizione di Pod CIDRs e di regole di routing in conflitto.

Dopo aver creato una tabella di route personalizzata e averla associata alla subnet nella rete virtuale, è possibile creare un nuovo cluster AKS che usa la tabella di route.
È necessario usare l'ID subnet per la posizione in cui si prevede di distribuire il cluster AKS. Anche questa subnet deve essere associata alla tabella di route personalizzata.

```azurecli-interactive
# Find your subnet ID
az network vnet subnet list --resource-group
                            --vnet-name
                            [--subscription]
```

```azurecli-interactive
# Create a kubernetes cluster with with a custom subnet preconfigured with a route table
az aks create -g MyResourceGroup -n MyManagedCluster --vnet-subnet-id MySubnetID
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito un cluster del servizio Azure Kubernetes nella propria subnet di rete virtuale, è possibile usare il cluster come di consueto. Inizia a creare [app con Azure Dev Spaces][dev-spaces], [Distribuisci app esistenti con Helm][use-helm]o [Crea nuove app usando Helm][develop-helm].

<!-- LINKS - External -->
[dev-spaces]: ../dev-spaces/index.yml
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[Calico-network-policies]: https://docs.projectcalico.org/v3.9/security/calico-network-policy

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[byo-subnet-route-table]: #bring-your-own-subnet-and-route-table-with-kubenet
[develop-helm]: quickstart-helm.md
[use-helm]: kubernetes-helm.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
[network-comparisons]: concepts-network.md#compare-network-models
[custom-route-table]: ../virtual-network/manage-route-table.md
