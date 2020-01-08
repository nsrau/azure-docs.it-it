---
title: Cluster privato del servizio Kubernetes di Azure
description: Informazioni su come creare un cluster Azure Kubernetes Service (AKS) privato
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 12/10/2019
ms.author: mlearned
ms.openlocfilehash: 8af0f998df2a92e51078a2e23806cca07ff08ca3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480086"
---
# <a name="public-preview---private-azure-kubernetes-service-cluster"></a>Anteprima pubblica-cluster di servizi Kubernetes di Azure privato

In un cluster privato il piano di controllo/server API avrà indirizzi IP interni definiti in [RFC1918](https://tools.ietf.org/html/rfc1918).  Usando un cluster privato, è possibile verificare che il traffico di rete tra il server API e i pool di nodi rimanga solo sulla rete privata.

La comunicazione tra il piano di controllo/server API, che si trova in una sottoscrizione di Azure gestita da AKS e il pool di cluster/nodi dei clienti, che si trova in una sottoscrizione del cliente, può comunicare tra loro tramite il [servizio di collegamento privato][private-link-service] nel server API VNET e un endpoint privato esposto nella subnet del cluster AKS del cliente.

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono il consenso esplicito self-service. Le anteprime vengono fornite "così come sono" e "come disponibili" e sono escluse dai contratti di servizio e dalla garanzia limitata. Le anteprime AKS sono parzialmente coperte dal supporto tecnico per il massimo sforzo. Di conseguenza, queste funzionalità non sono destinate all'uso in produzione. Per ulteriori informazioni, vedere gli articoli di supporto seguenti:
>
> * [Criteri di supporto AKS](support-policies.md)
> * [Domande frequenti relative al supporto tecnico Azure](faq.md)

## <a name="before-you-begin"></a>Prima di iniziare

* È necessaria l'interfaccia della riga di comando di Azure versione 2.0.77 o successiva e l'estensione AKS-Preview 0.4.18

## <a name="current-supported-regions"></a>Aree supportate correnti
* Stati Uniti occidentali
* Stati Uniti occidentali 2
* Stati Uniti orientali 2
* Canada centrale
* Europa settentrionale
* Europa occidentale
* Australia orientale

## <a name="install-latest-aks-cli-preview-extension"></a>Installare la versione di anteprima dell'interfaccia della riga di comando AKS

Per usare i cluster privati, è necessaria l'estensione dell'interfaccia della riga di comando *AKS-Preview* 0.4.18 o versione successiva. Installare l'estensione dell'interfaccia della riga di comando di Azure *AKS-Preview* usando il comando [AZ Extension Add][az-extension-add] , quindi verificare la presenza di eventuali aggiornamenti disponibili usando il comando [AZ Extension Update][az-extension-update] ::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> Quando si registra una funzionalità in una sottoscrizione, attualmente non è possibile annullare la registrazione di tale funzionalità. Dopo aver abilitato alcune funzionalità di anteprima, è possibile usare i valori predefiniti per tutti i cluster AKS, quindi creati nella sottoscrizione. Non abilitare le funzionalità di anteprima nelle sottoscrizioni di produzione. Usare una sottoscrizione separata per testare le funzionalità di anteprima e raccogliere commenti e suggerimenti.

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

Potrebbero essere necessari alcuni minuti prima che lo stato venga visualizzato *registrato*. È possibile controllare lo stato della registrazione usando il comando [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

Quando lo stato è registrato, aggiornare la registrazione del provider di risorse *Microsoft. servizio contenitore* usando il comando [AZ provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>Creare un cluster AKS privato

#### <a name="default-basic-networking"></a>Rete di base predefinita 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Dove--Enable-Private-cluster è un flag obbligatorio per un cluster privato 

#### <a name="advanced-networking"></a>Rete avanzata  

```azurecli-interactive
az aks create \ 
    --resource-group <private-cluster-resource-group>\ 
    --name <private-cluster-name> \ 
    --load-balancer-sku standard
    --enable-private-cluster 
    --network-plugin azure \ 
    --vnet-subnet-id <subnet-id> \ 
    --docker-bridge-address 172.17.0.1/16 \ 
    --dns-service-ip 10.2.0.10 \ 
    --service-cidr 10.2.0.0/24 \ 
```
Dove--Enable-Private-cluster è un flag obbligatorio per un cluster privato 

## <a name="steps-to-connect-to-the-private-cluster"></a>Passaggi per la connessione al cluster privato
L'endpoint del server API non ha un indirizzo IP pubblico. Di conseguenza, gli utenti dovranno creare una macchina virtuale di Azure in una rete virtuale e connettersi al server API. I passaggi descritti in

* Ottenere le credenziali per la connessione al cluster

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```
* Creare una macchina virtuale nella stessa VNET del cluster AKS o creare una macchina virtuale in un VNET diverso e peer this VNET con il cluster AKS VNET
* Se si crea una macchina virtuale in un VNET diverso, sarà necessario configurare un collegamento tra questo VNET e la zona DNS privato
    * passare al gruppo di risorse MC_ * nel portale 
    * fare clic sull'area DNS privato 
    * Selezionare il collegamento rete virtuale nel riquadro sinistro
    * creare un nuovo collegamento per aggiungere il VNET della macchina virtuale all'area DNS privato *(sono necessari alcuni minuti per rendere disponibile il collegamento della zona DNS)*
* Connettersi tramite SSH alla macchina virtuale
* Installare lo strumento Kubectl ed eseguire i comandi Kubectl

## <a name="dependencies"></a>Dependencies  
* Solo LB standard-nessun supporto per il servizio di bilanciamento del carico di base  

## <a name="limitations"></a>Limitazioni 
* Le stesse [limitazioni del servizio di collegamento privato di Azure][private-link-service] si applicano a cluster privati, gli endpoint privati di Azure e gli endpoint di servizio della rete virtuale non sono attualmente supportati nella stessa VNET
* Nessun supporto per i nodi virtuali in un cluster privato per la selezione di istanze di ACI privato in una VNET di Azure privata
* Nessun supporto per l'integrazione di Azure DevOps con i cluster privati
* Se i clienti devono consentire ad ACR di funzionare con il servizio contenitore di gestione dei contenitori privato, è necessario eseguire il peering del VNET del registro contenitori di gruppo con il cluster agente VNET
* Nessun supporto corrente per Azure Dev Spaces
* Nessun supporto per la conversione di cluster AKS esistenti in cluster privati  
* Se si elimina o si modifica l'endpoint privato nella subnet del cliente, il cluster smette di funzionare 
* Il monitoraggio di Azure per i contenitori Live Data non è attualmente supportato
* Bring your own DNS non è attualmente supportato


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
