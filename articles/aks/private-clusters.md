---
title: Creare un cluster di servizi Kubernetes di Azure privato
description: Informazioni su come creare un cluster Azure Kubernetes Service (AKS) privato
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: 4f0d702a213c4c34024c043edc50d25e6696cbc1
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610939"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Creare un cluster di servizi Kubernetes di Azure privato

In un cluster privato il piano di controllo o il server API dispone di indirizzi IP interni definiti nel documento [RFC1918-Address Allocation for Private Internets](https://tools.ietf.org/html/rfc1918) . Usando un cluster privato, è possibile verificare che il traffico di rete tra il server API e i pool di nodi rimanga solo sulla rete privata.

Il piano di controllo o il server API si trova in una sottoscrizione di Azure gestita da Azure Kubernetes Service (AKS). Un cluster o un pool di nodi del cliente si trova nella sottoscrizione del cliente. Il server e il cluster o il pool di nodi possono comunicare tra loro tramite il [servizio di collegamento privato di Azure][private-link-service] nella rete virtuale del server API e un endpoint privato esposto nella subnet del cluster AKS del cliente.

## <a name="prerequisites"></a>Prerequisiti

* L'interfaccia della riga di comando di Azure versione 2.2.0 o successiva

## <a name="create-a-private-aks-cluster"></a>Creare un cluster AKS privato

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse o usare un gruppo di risorse esistente per il cluster AKS.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Rete di base predefinita 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Dove *--Enable-Private-cluster* è un flag obbligatorio per un cluster privato. 

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
Dove *--Enable-Private-cluster* è un flag obbligatorio per un cluster privato. 

> [!NOTE]
> Se l'indirizzo CIDR del Bridge Docker (172.17.0.1/16) si scontra con la CIDR della subnet, modificare l'indirizzo del Bridge Docker in modo appropriato.

## <a name="options-for-connecting-to-the-private-cluster"></a>Opzioni per la connessione al cluster privato

L'endpoint del server API non ha un indirizzo IP pubblico. Per gestire il server API, sarà necessario usare una macchina virtuale che abbia accesso alla rete virtuale di Azure del cluster AKS (VNet). Sono disponibili diverse opzioni per stabilire la connettività di rete al cluster privato.

* Creare una VM nella stessa rete virtuale di Azure (VNet) del cluster AKS.
* Usare una macchina virtuale in una rete separata e configurare il [peering di rete virtuale][virtual-network-peering].  Per altre informazioni su questa opzione, vedere la sezione seguente.
* Usare una connessione [Express route o VPN][express-route-or-VPN] .

La creazione di una macchina virtuale nella stessa VNET del cluster AKS è l'opzione più semplice.  Express Route e VPN aggiungono costi e richiedono una maggiore complessità di rete.  Il peering di rete virtuale richiede la pianificazione degli intervalli CIDR della rete per assicurarsi che non siano presenti intervalli sovrapposti.

## <a name="virtual-network-peering"></a>Peering di rete virtuale

Come indicato in precedenza, il peering di VNet è un modo per accedere al cluster privato. Per usare il peering VNet è necessario configurare un collegamento tra la rete virtuale e la zona DNS privata.
    
1. Passare al gruppo di risorse MC_ * nel portale di Azure.  
2. Selezionare la zona DNS privata.   
3. Nel riquadro sinistro selezionare il collegamento **rete virtuale** .  
4. Creare un nuovo collegamento per aggiungere la rete virtuale della VM alla zona DNS privata. Sono necessari alcuni minuti per rendere disponibile il collegamento per la zona DNS.  
5. Tornare al gruppo di risorse MC_ * nella portale di Azure.  
6. Nel riquadro di destra selezionare la rete virtuale. Il nome della rete virtuale è nel formato *AKS-VNET-\**.  
7. Nel riquadro sinistro selezionare **peering**.  
8. Selezionare **Aggiungi**, aggiungere la rete virtuale della VM e quindi creare il peering.  
9. Passare alla rete virtuale in cui è presente la VM, selezionare **peering**, selezionare la rete virtuale AKS e quindi creare il peering. Se gli intervalli di indirizzi nella rete virtuale AKS e nello scontro della rete virtuale della macchina virtuale, il peering ha esito negativo. Per altre informazioni, vedere [peering di rete virtuale][virtual-network-peering].

## <a name="hub-and-spoke-with-custom-dns"></a>Hub e spoke con DNS personalizzato

[Le architetture Hub e spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) sono comunemente usate per distribuire le reti in Azure. In molte di queste distribuzioni, le impostazioni DNS in spoke reti virtuali sono configurate per fare riferimento a un server d'accesso DNS centrale per consentire la risoluzione DNS locale e basata su Azure. Quando si distribuisce un cluster AKS in un ambiente di rete di questo tipo, è necessario tenere conto di alcune considerazioni speciali.

![Hub e spoke del cluster privato](media/private-clusters/aks-private-hub-spoke.png)

1. Per impostazione predefinita, quando viene eseguito il provisioning di un cluster privato, nel gruppo di risorse gestite del cluster vengono creati un endpoint privato (1) e una zona DNS privata (2). Il cluster usa un record A nella zona privata per risolvere l'indirizzo IP dell'endpoint privato per la comunicazione con il server API.

2. La zona DNS privata è collegata solo alla VNet a cui sono collegati i nodi del cluster (3). Ciò significa che l'endpoint privato può essere risolto solo dagli host nel VNet collegato. Negli scenari in cui non è configurato alcun DNS personalizzato in VNet (impostazione predefinita), funziona senza problemi come host Point in 168.63.129.16 per DNS, che può risolvere i record nella zona DNS privata a causa del collegamento.

3. Negli scenari in cui il VNet che contiene il cluster ha impostazioni DNS personalizzate (4), la distribuzione del cluster ha esito negativo a meno che la zona DNS privata non sia collegata al VNet che contiene i resolver DNS personalizzati (5). Questo collegamento può essere creato manualmente dopo la creazione della zona privata durante il provisioning del cluster o tramite automazione al rilevamento della creazione della zona usando criteri di Azure o altri meccanismi di distribuzione basati su eventi, ad esempio griglia di eventi di Azure e funzioni di Azure.

## <a name="dependencies"></a>Dipendenze  

* Il servizio di collegamento privato è supportato solo su Azure Load Balancer standard. Il Azure Load Balancer di base non è supportato.  
* Per usare un server DNS personalizzato, aggiungere il 168.63.129.16 IP DNS di Azure come server DNS upstream nel server DNS personalizzato.

## <a name="limitations"></a>Limitazioni 
* Gli intervalli autorizzati IP non possono essere applicati all'endpoint server dell'API privata, ma si applicano solo al server API pubblico
* Zone di disponibilità sono attualmente supportate per determinate aree, vedere l'inizio di questo documento. 
* Le [limitazioni del servizio di collegamento privato di Azure][private-link-service] si applicano ai cluster privati.
* Nessun supporto per i nodi virtuali in un cluster privato per la rotazione di istanze di contenitore di Azure private in una rete virtuale di Azure privata
* Nessun supporto per l'integrazione di Azure DevOps con i cluster privati
* Per i clienti che devono consentire a Azure Container Registry di lavorare con il servizio contenitore di Azure privato, è necessario eseguire il peering della rete virtuale Container Registry con la rete virtuale del cluster di agenti.
* Nessun supporto corrente per Azure Dev Spaces
* Nessun supporto per la conversione di cluster AKS esistenti in cluster privati
* Se si elimina o si modifica l'endpoint privato nella subnet del cliente, il cluster smette di funzionare. 
* Il monitoraggio di Azure per i contenitori Live Data non è attualmente supportato.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /azure/private-link/private-link-service-overview#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

