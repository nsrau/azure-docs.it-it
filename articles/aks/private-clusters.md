---
title: Creare un cluster di servizi Kubernetes di Azure privato
description: Informazioni su come creare un cluster Azure Kubernetes Service (AKS) privato
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: 0a05bd15fff97d4f0020f6ce82ee90a2fe995edf
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944207"
---
# <a name="create-a-private-azure-kubernetes-service-cluster-preview"></a>Creare un cluster privato del servizio Kubernetes di Azure (anteprima)

In un cluster privato il piano di controllo o il server API dispone di indirizzi IP interni definiti nel documento [RFC1918-Address Allocation for Private Internets](https://tools.ietf.org/html/rfc1918) . Usando un cluster privato, è possibile verificare che il traffico di rete tra il server API e i pool di nodi rimanga solo sulla rete privata.

Il piano di controllo o il server API si trova in una sottoscrizione di Azure gestita da Azure Kubernetes Service (AKS). Un cluster o un pool di nodi del cliente si trova nella sottoscrizione del cliente. Il server e il cluster o il pool di nodi possono comunicare tra loro tramite il [servizio di collegamento privato di Azure][private-link-service] nella rete virtuale del server API e un endpoint privato esposto nella subnet del cluster AKS del cliente.

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono self-service e sono offerte in base al consenso esplicito. Le anteprime vengono fornite *così come sono* e sono *disponibili* e sono escluse dal contratto di servizio (SLA) e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto tecnico per il *massimo sforzo* . Pertanto, le funzionalità non sono destinate all'uso in produzione. Per ulteriori informazioni, vedere gli articoli di supporto seguenti:
>
> * [Criteri di supporto AKS](support-policies.md)
> * [Domande frequenti relative al supporto tecnico Azure](faq.md)

## <a name="prerequisites"></a>Prerequisites

* L'interfaccia della riga di comando di Azure versione 2.0.77 o successiva e l'estensione dell'interfaccia della riga di comando di Azure AKS Preview 0.4.18

## <a name="currently-supported-regions"></a>Aree attualmente supportate

* Australia orientale
* Australia sud-orientale
* Brasile meridionale
* Canada centrale
* Canada orientale
* Cenral US
* Asia orientale
* Stati Uniti orientali
* Stati Uniti orientali 2
* Stati Uniti orientali 2 EUAP
* Francia centrale
* Germania settentrionale
* Giappone orientale
* Giappone occidentale
* Corea centrale
* Corea meridionale
* Stati Uniti centro-settentrionali
* Europa settentrionale
* Europa settentrionale
* Stati Uniti centro-meridionali
* Regno Unito meridionale
* Europa occidentale
* Stati Uniti occidentali
* Stati Uniti occidentali 2
* Stati Uniti orientali 2

## <a name="currently-supported-availability-zones"></a>Attualmente supportato zone di disponibilità

* Stati Uniti centrali
* Stati Uniti orientali
* Stati Uniti orientali 2
* Francia centrale
* Giappone orientale
* Europa settentrionale
* Asia sud-orientale
* Regno Unito meridionale
* Europa occidentale
* Stati Uniti occidentali 2

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>Installare l'estensione più recente dell'interfaccia della riga di comando di Azure AKS

Per usare i cluster privati, è necessaria l'estensione dell'interfaccia della riga di comando di Azure AKS Preview 0.4.18 o versione successiva. Installare l'estensione dell'interfaccia della riga di comando di Azure AKS anteprima usando il comando [AZ Extension Add][az-extension-add] , quindi verificare la presenza di eventuali aggiornamenti disponibili usando il comando [AZ Extension Update][az-extension-update] seguente:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> Quando si registra una funzionalità in una sottoscrizione, attualmente non è possibile annullare la registrazione di tale funzionalità. Dopo aver abilitato alcune funzionalità di anteprima, è possibile usare le impostazioni predefinite per tutti i cluster AKS creati nella sottoscrizione. Non abilitare le funzionalità di anteprima nelle sottoscrizioni di produzione. Usare una sottoscrizione separata per testare le funzionalità di anteprima e raccogliere commenti e suggerimenti.

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

Potrebbero essere necessari alcuni minuti prima che lo stato della registrazione venga visualizzato come *registrato*. È possibile controllare lo stato usando il comando [AZ feature list][az-feature-list] seguente:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

Quando lo stato è registrato, aggiornare la registrazione del provider di risorse *Microsoft. servizio contenitore* usando il comando [AZ provider Register][az-provider-register] seguente:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
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
6. Nel riquadro di destra selezionare la rete virtuale. Il nome della rete virtuale ha il formato *AKS-VNET-\** .  
7. Nel riquadro sinistro selezionare **peering**.  
8. Selezionare **Aggiungi**, aggiungere la rete virtuale della VM e quindi creare il peering.  
9. Passare alla rete virtuale in cui è presente la VM, selezionare **peering**, selezionare la rete virtuale AKS e quindi creare il peering. Se gli intervalli di indirizzi nella rete virtuale AKS e nello scontro della rete virtuale della macchina virtuale, il peering ha esito negativo. Per altre informazioni, vedere [peering di rete virtuale][virtual-network-peering].

## <a name="dependencies"></a>Dependencies  
* Il servizio di collegamento privato è supportato solo su Azure Load Balancer standard. Il Azure Load Balancer di base non è supportato.  
* Per usare un server DNS personalizzato, distribuire un server AD con DNS da trasmettere a questo 168.63.129.16 IP

## <a name="limitations"></a>Limitazioni 
* Gli intervalli autorizzati IP non possono essere applicati all'endpoint server dell'API privata, ma si applicano solo al server API pubblico
* Zone di disponibilità sono attualmente supportate per determinate aree, vedere l'inizio di questo documento. 
* Le [limitazioni del servizio di collegamento privato di Azure][private-link-service] si applicano a cluster privati, endpoint privati di Azure e endpoint di servizio della rete virtuale, che non sono attualmente supportati nella stessa rete virtuale.
* Nessun supporto per i nodi virtuali in un cluster privato per la rotazione di istanze di contenitore di Azure private in una rete virtuale di Azure privata
* Nessun supporto per l'integrazione di Azure DevOps con i cluster privati
* Per i clienti che devono consentire a Azure Container Registry di lavorare con il servizio contenitore di Azure privato, è necessario eseguire il peering della rete virtuale Container Registry con la rete virtuale del cluster di agenti.
* Nessun supporto corrente per Azure Dev Spaces
* Nessun supporto per la conversione di cluster AKS esistenti in cluster privati
* Se si elimina o si modifica l'endpoint privato nella subnet del cliente, il cluster smette di funzionare. 
* Il monitoraggio di Azure per i contenitori Live Data non è attualmente supportato.
* *Bring your own DNS* non è attualmente supportato.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

