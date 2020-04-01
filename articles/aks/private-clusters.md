---
title: Creare un cluster di servizi Azure Kubernetes privatiCreate a private Azure Kubernetes Service cluster
description: Informazioni su come creare un cluster di servizio Azure Kubernetes (AKS) privato
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: 87f52c5a749b531e5b0656e0b30ff0fe9c1a57bf
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398058"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Creare un cluster di servizi Azure Kubernetes privatiCreate a private Azure Kubernetes Service cluster

In un cluster privato, il piano di controllo o il server API dispone di indirizzi IP interni definiti nel documento [RFC1918 - Address Allocation for Private Internets.](https://tools.ietf.org/html/rfc1918) Utilizzando un cluster privato, è possibile garantire che il traffico di rete tra il server API e i pool di nodi rimanga solo nella rete privata.

Il piano di controllo o il server API si trova in una sottoscrizione di Azure gestita dal servizio Azure Kubernetes (AKS). Il cluster o il pool di nodi di un cliente si trova nella sottoscrizione del cliente. Il server e il cluster o il pool di nodi possono comunicare tra loro tramite il [servizio Azure Private Link][private-link-service] nella rete virtuale del server API e un endpoint privato esposto nella subnet del cluster AKS del cliente.

## <a name="prerequisites"></a>Prerequisiti

* L'interfaccia della riga di comando di Azure versione 2.2.0 o successiva

## <a name="create-a-private-aks-cluster"></a>Creare un cluster AKS privatoCreate a private AKS cluster

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse o usare un gruppo di risorse esistente per il cluster AKS.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Rete di base predefinita 

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
> Se l'indirizzo del bridge Docker CIDR (172.17.0.1/16) non si concedano conflitti con la subnet CIDR, modificare l'indirizzo del bridge Docker in modo appropriato.

## <a name="options-for-connecting-to-the-private-cluster"></a>Opzioni per la connessione al cluster privato

L'endpoint del server API non ha un indirizzo IP pubblico. Per gestire il server API, è necessario usare una macchina virtuale con accesso alla rete virtuale di Azure (VNet) del cluster AKS. Sono disponibili diverse opzioni per stabilire la connettività di rete al cluster privato.

* Creare una macchina virtuale nella stessa rete virtuale di Azure (VNet) del cluster AKS.
* Usare una macchina virtuale in una rete separata e configurare il peering della [rete virtuale.][virtual-network-peering]  Per ulteriori informazioni su questa opzione, vedere la sezione seguente.
* Utilizzare una connessione [Express Route o VPN.][express-route-or-VPN]

La creazione di una macchina virtuale nella stessa rete virtuale del cluster AKS è l'opzione più semplice.  Express Route e VPN aggiungono costi e richiedono ulteriore complessità di rete.  Il peering della rete virtuale richiede di pianificare gli intervalli CIDR di rete per evitare intervalli sovrapposti.

## <a name="virtual-network-peering"></a>Peering di rete virtuale

Come accennato in precedenza, il peering della rete virtuale è un modo per accedere al cluster privato. Per usare il peering della rete virtuale, è necessario configurare un collegamento tra la rete virtuale e la zona DNS privata.
    
1. Passare al gruppo di risorse MC_ nel portale di Azure.Go to the MC_s resource group in the Azure portal.  
2. Selezionare la zona DNS privata.   
3. Nel riquadro sinistro selezionare il collegamento **Rete virtuale.**  
4. Creare un nuovo collegamento per aggiungere la rete virtuale della macchina virtuale alla zona DNS privata. La disponibilità del collegamento alla zona DNS richiede alcuni minuti.  
5. Tornare al gruppo di risorse MC_ nel portale di Azure.Back back to the MC_z resource group in the Azure portal.  
6. Nel riquadro destro selezionare la rete virtuale. Il nome della rete virtuale è nel formato *aks-vnet-\**.  
7. Nel riquadro sinistro selezionare **Peerings**.  
8. Selezionare **Aggiungi**, aggiungere la rete virtuale della macchina virtuale e quindi creare il peering.  
9. Passare alla rete virtuale in cui si trova la macchina virtuale, selezionare **Peering,** selezionare la rete virtuale AKS e quindi creare il peering. Se gli intervalli di indirizzi nella rete virtuale AKS e nella rete virtuale della macchina virtuale si contradiscono, il peering non riesce. Per altre informazioni, vedere [Peering di rete virtuale][virtual-network-peering].

## <a name="hub-and-spoke-with-custom-dns"></a>Hub e ha parlato con DNS personalizzato

[Le architetture hub e spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) vengono comunemente usate per distribuire reti in Azure.Hub and spoke architectures are commonly used to deploy networks in Azure. In molte di queste distribuzioni, le impostazioni DNS nelle reti virtuali a raggio sono configurate per fare riferimento a un server d'inoltro DNS centrale per consentire la risoluzione DNS locale e basata su Azure.In many of these deployments, DNS settings in the spoke VNets are configured to reference a central DNS forwarder to allow for on-premises and Azure-based DNS resolution. Quando si distribuisce un cluster AKS in un ambiente di rete di questo tipo, è necessario tenere conto di alcune considerazioni speciali.

![Hub del cluster privato e spoke](media/private-clusters/aks-private-hub-spoke.png)

1. Per impostazione predefinita, quando viene eseguito il provisioning di un cluster privato, nel gruppo di risorse gestite del cluster vengono creati un endpoint privato (1) e una zona DNS privata (2). Il cluster utilizza un record A nell'area privata per risolvere l'indirizzo IP dell'endpoint privato per la comunicazione al server API.

2. La zona DNS privata è collegata solo alla rete virtuale a cui sono collegati i nodi del cluster (3). Ciò significa che l'endpoint privato può essere risolto solo dagli host nella rete virtuale collegata. Negli scenari in cui non è configurato alcun DNS personalizzato nella rete virtuale (impostazione predefinita), questo funziona senza problemi poiché gli host puntano a 168.63.129.16 per DNS che può risolvere i record nella zona DNS privata a causa del collegamento.

3. Negli scenari in cui la rete virtuale contenente il cluster dispone di impostazioni DNS personalizzate (4), la distribuzione del cluster non riesce a meno che la zona DNS privata non sia collegata alla rete virtuale che contiene i resolver DNS personalizzati (5). Questo collegamento può essere creato manualmente dopo la creazione dell'area privata durante il provisioning del cluster o tramite l'automazione al rilevamento della creazione della zona tramite Criteri di Azure o altri meccanismi di distribuzione basati su eventi (ad esempio, Griglia di eventi di Azure e Funzioni di Azure).

## <a name="dependencies"></a>Dependencies  

* Il servizio Private Link è supportato solo in Azure Load Balancer standard. Il bilanciamento del carico di Azure di base non è supportato.  
* Per usare un server DNS personalizzato, aggiungere l'IP DNS di Azure 168.63.129.16 come server DNS upstream nel server DNS personalizzato.

## <a name="limitations"></a>Limitazioni 
* Gli intervalli di indirizzi IP autorizzati non possono essere applicati all'endpoint del server API privato, si applicano solo al server API pubblico
* Le zone di disponibilità sono attualmente supportate per alcune aree geografiche, vedere l'inizio di questo documentoAvailability zones are currently supported for certain regions, see the beginning of this document 
* [Le limitazioni del servizio Azure Private Link][private-link-service] si applicano ai cluster privati, agli endpoint privati di Azure e agli endpoint del servizio di rete virtuale, che non sono attualmente supportati nella stessa rete virtuale.
* Nessun supporto per i nodi virtuali in un cluster privato per lo spin privato Istanze del contenitore di Azure (ACI) in una rete virtuale di Azure privata
* Nessun supporto per l'integrazione di Azure DevOps out-of-the-box con cluster privati
* Per i clienti che devono abilitare il Registro di sistema del contenitore di Azure per l'utilizzo con AKS privato, è necessario eseguire il peering della rete virtuale del Registro di sistema del contenitore con la rete virtuale del cluster agente.
* Nessun supporto corrente per Azure Dev Spaces
* Nessun supporto per la conversione di cluster AKS esistenti in cluster privati
* L'eliminazione o la modifica dell'endpoint privato nella subnet del cliente causerà l'interruzione del funzionamento del cluster. 
* Monitoraggio di Azure per i contenitori Live Data non è attualmente supportato.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

