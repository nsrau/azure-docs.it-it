---
title: Configurare funzionalità di rete kubenet nel servizio Azure Kubernetes
description: Informazioni su come configurare una rete kubenet (di base) nel servizio Azure Kubernetes per distribuire un cluster del servizio Azure Kubernetes in una rete virtuale e una subnet esistenti.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/26/2019
ms.author: mlearned
ms.reviewer: nieberts, jomore
ms.openlocfilehash: e1279261de8e26b9e11f55100ce01277650e251b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615751"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Usare funzionalità di rete kubenet con i propri intervalli di indirizzi IP nel servizio Azure Kubernetes

Per impostazione predefinita, AKS cluster usano [kubenet][kubenet], e una rete virtuale di Azure e una subnet vengono creati automaticamente. Con *kubenet* i nodi ottengono un indirizzo IP dalla subnet della rete virtuale di Azure. I pod ricevono un indirizzo IP da uno spazio di indirizzi diverso dal punto di vista logico nella subnet della rete virtuale di Azure dei nodi. La funzionalità Network Address Translation (NAT) viene quindi configurata in modo che i pod possano raggiungere le risorse nella rete virtuale di Azure. L'indirizzo IP di origine del traffico viene convertito tramite NAT nell'indirizzo IP primario del nodo. Questo approccio riduce notevolmente il numero di indirizzi IP che è necessario riservare ai pod nello spazio di indirizzi della rete.

Con [interfaccia di rete contenitore di Azure (CNI)][cni-networking], ogni pod Ottiene un indirizzo IP dalla subnet ed è possibile accedervi direttamente. Questi indirizzi IP devono essere univoci nello spazio di indirizzi della rete e devono essere pianificati in anticipo. Ogni nodo ha un parametro di configurazione per il numero massimo di pod che supporta. Il numero equivalente di indirizzi IP per nodo viene quindi riservato anticipatamente per tale nodo. Questo approccio richiede una maggiore pianificazione e spesso conduce all'esaurimento degli indirizzi IP o alla necessità di ricompilare i cluster in una subnet di dimensioni maggiori man mano che le esigenze dell'applicazione aumentano.

Questo articolo illustra come usare le funzionalità di rete *kubenet* per creare e usare una subnet di rete virtuale per un cluster del servizio Azure Kubernetes. Per altre informazioni sulle opzioni di rete e considerazioni, vedere [concetti di rete per Kubernetes e AKS][aks-network-concepts].

> [!WARNING]
> Per usare pool di nodi di Windows Server (attualmente in anteprima nel servizio contenitore di AZURE), è necessario usare Azure CNI. L'uso di kubenet come il modello di rete non è disponibile per i contenitori di Windows Server.

## <a name="before-you-begin"></a>Prima di iniziare

È necessario la CLI di Azure versione 2.0.65 o versione successiva installato e configurato. Eseguire  `az --version` per trovare la versione. Se è necessario installare o eseguire l'aggiornamento, vedere [installare CLI Azure][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Panoramica delle funzionalità di rete kubenet con la propria subnet

Molti ambienti sono caratterizzati da reti virtuali e subnet definite con intervalli di indirizzi IP allocati. Queste risorse di rete virtuale vengono usate per supportare più servizi e applicazioni. Per fornire connettività di rete, i cluster del servizio Azure Kubernetes possono usare *kubenet* (funzionalità di rete di base) o Azure CNI (*funzionalità di rete avanzate*).

Con *kubenet* solo i nodi ricevono un indirizzo IP nella subnet della rete virtuale. I pod non possono comunicare tra loro direttamente. Per la connettività tra i pod nei vari nodi vengono usati il routing definito dall'utente e l'inoltro di indirizzi IP. Si potrebbero anche distribuire i pod dietro un servizio che riceve un indirizzo IP assegnato e bilancia il carico del traffico dell'applicazione. Il diagramma seguente mostra che i nodi del servizio Azure Kubernetes ricevono un indirizzo IP nella subnet della rete virtuale, mentre i pod non lo ricevono:

![Modello di rete Kubenet con un cluster del servizio Azure Kubernetes](media/use-kubenet/kubenet-overview.png)

Azure supporta un massimo di 400 route in un routing definito dall'utente, quindi un cluster del servizio Azure Kubernetes non può avere più di 400 nodi. Servizio contenitore di AZURE, ad esempio features [i nodi virtuali][virtual-nodes] o i criteri di rete non sono supportati con *kubenet*.

Con *Azure CNI* ogni pod riceve un indirizzo IP nella subnet IP e può comunicare direttamente con altri pod e servizi. I cluster possono avere le stesse dimensioni dell'intervallo di indirizzi IP specificato. Questo intervallo deve però essere pianificato in anticipo e tutti gli indirizzi IP vengono utilizzati dai nodi del servizio Azure Kubernetes in base al numero massimo di pod che possono supportare. Le funzionalità di rete e scenari avanzati, ad esempio [i nodi virtuali][virtual-nodes] o i criteri di rete sono supportati con *CNI Azure*.

### <a name="ip-address-availability-and-exhaustion"></a>Disponibilità ed esaurimento degli indirizzi IP

Un problema comune con *Azure CNI* è che l'intervallo di indirizzi IP assegnati è troppo piccolo per potervi aggiungere in seguito altri nodi quando si ridimensiona o si aggiorna un cluster. Il team che si occupa della rete potrebbe inoltre non essere in grado di rendere disponibile un intervallo di indirizzi IP abbastanza ampio da supportare le esigenze dell'applicazione previste.

Come compromesso, è possibile creare un cluster del servizio Azure Kubernetes che usa *kubenet* e connettersi a una subnet di rete virtuale esistente. Questo approccio consente ai nodi di ricevere indirizzi IP definiti, senza bisogno di riservare anticipatamente un numero elevato di indirizzi IP per tutti i potenziali pod che potrebbero essere eseguiti nel cluster.

Con *kubenet* è possibile usare un intervallo di indirizzi IP molto più ridotto ed essere in grado di supportare cluster di grandi dimensioni e più richieste delle applicazioni. Ad esempio, anche con un intervallo di indirizzi IP */27* si potrebbe eseguire un cluster di 20-25 nodi con spazio sufficiente per operazioni di ridimensionamento o aggiornamento. Queste dimensioni del cluster supporterebbero fino a *2.200-2.750* pod (con un valore massimo predefinito di 110 pod per nodo). Il numero massimo di POD per ogni nodo che è possibile configurare con *kubenet* nel servizio contenitore di AZURE è impostato su 110.

I seguenti calcoli di base mettono a confronto la differenza nei modelli di rete:

- **kubenet**: un semplice intervallo di indirizzi IP */24* può supportare fino a *251* nodi nel cluster (ogni subnet di rete virtuale di Azure riserva i primi tre indirizzi IP per le operazioni di gestione)
  - Questo numero di nodi potrebbe supportare fino a *27.610* pod (con un valore massimo predefinito di 110 pod per nodo con *kubenet*)
- **Azure CNI**: lo stesso intervallo di indirizzi IP di base */24* della subnet potrebbe supportare solo un massimo di *8* nodi nel cluster
  - Questo numero di nodi potrebbe supportare solo fino a *240* pod (con un valore massimo predefinito di 30 pod per nodo con *Azure CNI*)

> [!NOTE]
> Questi valori massimi non tengono in considerazione le operazioni di aggiornamento o ridimensionamento. In pratica, non è possibile eseguire il numero massimo di nodi supportati dall'intervallo di indirizzi IP della subnet. Occorre lasciare alcuni indirizzi IP disponibili per l'uso durante le operazioni di ridimensionamento o aggiornamento.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Peering di rete virtuale e connessioni ExpressRoute

Per fornire la connettività da sito locale, entrambi *kubenet* e *Azure-CNI* possono usare approcci di rete [peering di rete virtuale di Azure][vnet-peering] or [ExpressRoute connections][express-route]. Pianificare con attenzione gli intervalli di indirizzi IP per evitare sovrapposizioni ed errori di routing del traffico. Ad esempio, molte reti locali usano un intervallo di indirizzi *10.0.0.0/8* che viene annunciato sulla connessione ExpressRoute. Si consiglia di creare i cluster AKS nelle subnet di rete virtuale di Azure di fuori di questo intervallo di indirizzi, ad esempio *172.16.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Scegliere un modello di rete da usare

La scelta del plug-in di rete da usare per il cluster del servizio Azure Kubernetes è in genere un compromesso tra le esigenze di flessibilità e di configurazione avanzata. Le considerazioni seguenti sono utili per stabilire quando ogni modello di rete può essere il più appropriato.

Usare *kubenet* quando:

- Lo spazio indirizzi IP è limitato.
- La maggior parte delle comunicazioni dei pod avviene all'interno del cluster.
- Non sono necessarie funzionalità di rete avanzate come i nodi virtuali o i criteri di rete.

Usare *Azure CNI* quando:

- È disponibile uno spazio indirizzi IP adeguato.
- La maggior parte delle comunicazioni dei pod avviene con risorse esterne al cluster.
- Non si vogliono gestire i routing definiti dall'utente.
- Sono necessarie funzionalità di rete avanzate come i nodi virtuali o i criteri di rete.

Per altre informazioni utili decidere quale modello di rete da usare, vedere [confrontare i modelli di rete e all'ambito supporto][network-comparisons].

> [!NOTE]
> Kuberouter consente di abilitare i criteri di rete quando si usa kubenet e può essere installato come daemonset in un cluster AKS. Tenere presente kube-router è ancora in versione beta e non viene offerto alcun supporto da Microsoft per il progetto.

## <a name="create-a-virtual-network-and-subnet"></a>Creare una rete virtuale e una subnet

Per iniziare a usare *kubenet* e il proprio subnet della rete virtuale, creare innanzitutto un gruppo di risorse usando la [creare il gruppo di az][az-group-create] comando. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Se non si dispone di una rete virtuale esistente e una subnet da usare, creare queste risorse di rete usando il [az network vnet create][az-network-vnet-create] comando. Nell'esempio seguente, la rete virtuale è denominata *myVnet* con il prefisso di indirizzo *192.168.0.0/16*. Viene creata una subnet denominata *myAKSSubnet* con il prefisso indirizzo *192.168.1.0/24*.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Creare un'entità servizio e assegnarle le autorizzazioni

Per consentire a un cluster servizio Azure Kubernetes di interagire con altre risorse di Azure viene usata un'entità servizio di Azure Active Directory. L'entità servizio deve avere le autorizzazioni necessarie per gestire la rete virtuale e la subnet usate dai nodi del servizio Azure Kubernetes. Per creare un'entità servizio, usare il [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] comando:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

L'output di esempio seguente mostra l'ID applicazione e la password dell'entità servizio. Questi valori vengono usati in passaggi aggiuntivi per assegnare un ruolo all'entità servizio e quindi creare il cluster del servizio Azure Kubernetes:

```console
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Per assegnare le deleghe corrette nei passaggi successivi, usare il [show di az network vnet][az-network-vnet-show] and [az network vnet subnet show][az-network-vnet-subnet-show] comandi per ottenere l'ID di risorsa richiesto. Questi ID di risorsa vengono archiviati come variabili e vi viene fatto riferimento nei passaggi successivi:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Assegnare a questo punto l'entità servizio per il cluster AKS *Contributor* le autorizzazioni per la rete virtuale usando la [creazione dell'assegnazione di ruolo az][az-role-assignment-create] comando. Specificare il proprio  *\<appId >* come illustrato nell'output dal comando precedente per creare l'entità servizio:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role Contributor
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Creare un cluster del servizio Azure Kubernetes nella rete virtuale

A questo punto sono state create una rete virtuale e una subnet e sono state create e assegnate le autorizzazioni per consentire a un'entità servizio di usare tali risorse di rete. A questo punto creare un cluster AKS nella rete virtuale e subnet usando il [az aks create][az-aks-create] comando. Definire la propria entità servizio  *\<appId >* e  *\<password >* , come illustrato nell'output dal comando precedente per creare l'entità servizio.

Durante il processo di creazione del cluster vengono definiti anche gli intervalli di indirizzi IP seguenti:

* *--service-cidr* viene usato per assegnare un indirizzo IP ai servizi interni nel cluster del servizio Azure Kubernetes. Questo intervallo di indirizzi IP deve essere uno spazio indirizzi non in uso in qualsiasi altra posizione nell'ambiente di rete. Questo intervallo include qualsiasi intervalli della rete locale se si connettono o si prevede di connettere, reti virtuali di Azure tramite Express Route o una connessione VPN Site-to-Site.

* L'indirizzo *--dns-service-ip* deve essere l'indirizzo *.10* dell'intervallo di indirizzi IP del servizio.

* *--pod-cidr* deve essere uno spazio indirizzi ampio non in uso in qualsiasi altra posizione nell'ambiente di rete. Questo intervallo include qualsiasi intervalli della rete locale se si connettono o si prevede di connettere, reti virtuali di Azure tramite Express Route o una connessione VPN Site-to-Site.
    * Questo intervallo di indirizzi deve essere abbastanza ampio da contenere il numero di nodi in base a cui si prevede di aumentare le dimensioni. Non è possibile cambiare questo intervallo di indirizzi dopo la distribuzione del cluster, qualora fossero necessari altri indirizzi per nodi aggiuntivi.
    * L'intervallo di indirizzi IP dei pod viene usato per assegnare uno spazio indirizzi */24* a ogni nodo del cluster. Nell'esempio seguente, il *-cidr di pod* dei *10.244.0.0/16* assegna il primo nodo *10.244.0.0/24*, il secondo nodo *10.244.1.0/24*e il terzo nodo *10.244.2.0/24*.
    * Quando il cluster viene ridimensionato o aggiornato, la piattaforma di Azure continua ad assegnare un intervallo di indirizzi IP dei pod a ogni nuovo nodo.
    
* Il *: indirizzo di bridge docker* consente di nodi del servizio contenitore di AZURE di comunicare con la piattaforma di gestione sottostante. L'indirizzo IP non deve essere compreso nell'intervallo di indirizzi IP della rete virtuale del cluster e non deve sovrapporsi ad altri intervalli di indirizzi in uso nella rete.

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

Quando si crea un cluster del servizio Azure Kubernetes, vengono creati un gruppo di sicurezza e una tabella di route. Queste risorse di rete gestite dal piano di controllo del servizio contenitore di AZURE. Il gruppo di sicurezza di rete viene associato automaticamente le schede di rete virtuale nei nodi. La tabella di route viene associata automaticamente dalla subnet della rete virtuale. Regole di gruppo di sicurezza di rete e le tabelle di route e vengono aggiornate automaticamente quando si creano ed esporre servizi.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito un cluster del servizio Azure Kubernetes nella propria subnet di rete virtuale, è possibile usare il cluster come di consueto. Introduzione [compilazione di App tramite Azure Dev spazi][dev-spaces] or [using Draft][use-draft], o [distribuire le app tramite Helm][usare helm].

<!-- LINKS - External -->
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

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
[use-helm]: kubernetes-helm.md
[use-draft]: kubernetes-draft.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
[network-comparisons]: concepts-network.md#compare-network-models
