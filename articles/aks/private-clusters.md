---
title: Creare un cluster privato del servizio Azure Kubernetes
description: Informazioni su come creare un cluster privato del servizio Azure Kubernetes
services: container-service
ms.topic: article
ms.date: 7/17/2020
ms.openlocfilehash: 4ebc5e44f491b5ff5950a13771fe3d7179b6fc9f
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143096"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Creare un cluster privato del servizio Azure Kubernetes

In un cluster privato, il piano di controllo o il server API dispongono di indirizzi IP interni definiti nel documento [RFC1918 - Indirizzo di allocazione per Internet privati](https://tools.ietf.org/html/rfc1918). Usando un cluster privato, è possibile verificare che il traffico di rete tra il server API e i pool di nodi rimanga solo sulla rete privata.

Il piano di controllo o il server API si trova in una sottoscrizione di Azure gestita dal servizio Azure Kubernetes. Un cluster o un pool di nodi del cliente si trova nella sottoscrizione del cliente. Il server e il cluster o il pool di nodi possono comunicare tra loro tramite il [servizio di collegamento privato di Azure][private-link-service] nella rete virtuale del server API e un endpoint privato esposto nella subnet del cluster AKS del cliente.

## <a name="region-availability"></a>Aree di disponibilità

Il cluster privato è disponibile nelle aree pubbliche in cui [è supportato AKS](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).

> [!NOTE]
> Sono supportati i siti di Azure per enti pubblici, tuttavia US Gov Texas non è attualmente supportato a causa del supporto del collegamento privato mancante.

## <a name="prerequisites"></a>Prerequisiti

* Interfaccia della riga di comando di Azure, versione 2.2.0 o successiva

## <a name="create-a-private-aks-cluster"></a>Creare un cluster AKS privato

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse o usarne uno esistente per il cluster AKS.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Funzionalità di rete di base predefinita 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Dove *--enable-private-cluster* è un flag obbligatorio per un cluster privato. 

### <a name="advanced-networking"></a>Rete avanzata  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Dove *--enable-private-cluster* è un flag obbligatorio per un cluster privato. 

> [!NOTE]
> Se il CIDR dell'indirizzo del bridge Docker (172.17.0.1/16) si scontra con il CIDR della subnet, modificare l'indirizzo del bridge Docker in modo appropriato.

## <a name="options-for-connecting-to-the-private-cluster"></a>Opzioni per la connessione al cluster privato

L'endpoint del server API non ha un indirizzo IP pubblico. Per gestire il server API sarà necessario usare una macchina virtuale che abbia accesso alla rete virtuale di Azure del cluster AKS (VNet). Sono disponibili diverse opzioni per stabilire la connettività di rete al cluster privato.

* Creare una rete virtuale nella stessa rete virtuale di Azure (VNet) del cluster AKS.
* Usare una macchina virtuale in una rete separata e configurare il [Peering di rete virtuale][virtual-network-peering].  Per altre informazioni su questa opzione, vedere la sezione riportata di seguito.
* Usare una connessione [ExpressRoute o VPN][express-route-or-VPN].

La creazione di una macchina virtuale nella stessa VNET del cluster AKS è l'opzione più semplice.  Express Route e VPN aggiungono costi e richiedono una maggiore complessità di rete.  Il peering di rete virtuale richiede la pianificazione degli intervalli CIDR della rete per assicurarsi che non siano presenti intervalli sovrapposti.

## <a name="virtual-network-peering"></a>Peering di rete virtuale

Come indicato in precedenza, il peering di rete virtuale è un modo per accedere al cluster privato. Per usare il peering di rete virtuale, è necessario configurare un collegamento tra la rete virtuale e la zona DNS privata.
    
1. Passare al gruppo di risorse del nodo nel portale di Azure.  
2. Selezionare la zona DNS privato.   
3. Nel riquadro di sinistra selezionare il collegamento **Rete virtuale**.  
4. Creare un nuovo collegamento per aggiungere la rete virtuale della macchina virtuale alla zona DNS privata. Sono necessari alcuni minuti per rendere disponibile il collegamento per la zona DNS.  
5. Nella portale di Azure passare al gruppo di risorse che contiene la rete virtuale del cluster.  
6. Nel riquadro a destra, selezionare la rete virtuale. Il nome della rete virtuale è nel modulo *aks-vnet-\** .  
7. Nel riquadro a sinistra selezionare **Peering**.  
8. Selezionare **Aggiungi**, aggiungere la rete virtuale della macchina virtuale, quindi creare il peering.  
9. Passare alla rete virtuale in cui è presente la macchina virtuale, selezionare **Peering**, selezionare la rete virtuale AKS, quindi creare il peering. Se gli intervalli di indirizzi nella rete virtuale AKS e nella rete virtuale della macchina virtuale sono in conflitto, il peering ha esito negativo. Per altre informazioni, vedere [Peering di rete virtuale][virtual-network-peering].

## <a name="hub-and-spoke-with-custom-dns"></a>Hub e spoke con DNS personalizzato

[Le architetture hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) vengono comunemente usate per distribuire le reti in Azure. In molte di queste distribuzioni, le impostazioni DNS nelle reti virtuali spoke sono configurate in modo da fare riferimento a un server d'inoltro DNS centrale per consentire la risoluzione DNS locale e basata su Azure. Quando si distribuisce un cluster AKS in un ambiente di rete di questo tipo è necessario tenere conto di alcune considerazioni speciali.

![Hub e spoke del cluster privato](media/private-clusters/aks-private-hub-spoke.png)

1. Per impostazione predefinita, quando viene eseguito il provisioning di un cluster privato, nel gruppo di risorse gestite del cluster vengono creati un endpoint privato (1) e una zona DNS privata (2). Il cluster usa un record A nella zona privata per risolvere l'indirizzo IP dell'endpoint privato per la comunicazione con il server API.

2. La zona DNS privata è collegata solo alla VNet a cui sono collegati i nodi del cluster (3). Ciò significa che l'endpoint privato può essere risolto solo dagli host in tale VNet collegata. Negli scenari in cui non è configurato alcun DNS personalizzato in VNet (impostazione predefinita), funziona senza problemi come host Point in 168.63.129.16 per DNS, che può risolvere i record nella zona DNS privata a causa del collegamento.

3. Negli scenari in cui la VNet che contiene il cluster dispone di impostazioni DNS personalizzate (4), la distribuzione del cluster ha esito negativo a meno che la zona DNS privata non sia collegata alla VNet che contiene i resolver DNS personalizzati (5). Questo collegamento può essere creato manualmente dopo la creazione della zona privata durante il provisioning del cluster o tramite automazione al rilevamento della creazione della zona usando i meccanismi di distribuzione basati su eventi, ad esempio griglia di eventi di Azure e funzioni di Azure.

## <a name="dependencies"></a>Dependencies  

* Il servizio di collegamento privato è supportato solo su Azure Load Balancer standard. Azure Load Balancer di base non è supportato.  
* Per usare un server DNS personalizzato, aggiungere l'IP DNS di Azure 168.63.129.16 come server DNS upstream nel server DNS personalizzato.

## <a name="limitations"></a>Limitazioni 
* Gli intervalli autorizzati IP non possono essere applicati all'endpoint server dell'API privata, ma si applicano solo al server API pubblico
* [Zone di disponibilità][availability-zones] sono attualmente supportate per determinate aree geografiche. 
* [Le limitazioni del servizio Collegamento privato di Azure][private-link-service] si applicano ai cluster privati.
* Nessun supporto per gli agenti ospitati da Microsoft di Azure DevOps con cluster privati. Si consiglia di usare gli [agenti self-hosted][devops-agents]. 
* Per i clienti che devono consentire al Registro contenitori di Azure di lavorare con il servizio Azure Kubernetes, è necessario eseguire il peering della rete virtuale Registro contenitori con la rete virtuale del cluster di agenti.
* Nessun supporto corrente per Azure Dev Spaces
* Nessun supporto per la conversione di cluster AKS esistenti in cluster privati
* Se si elimina o si modifica l'endpoint privato nella subnet del cliente, il cluster smette di funzionare. 
* Non è attualmente supportato Monitoraggio di Azure per i contenitori Live Data.
* Il contratto di servizio relativo al tempo di attività non è attualmente supportato.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents?view=azure-devops
[availability-zones]: availability-zones.md
