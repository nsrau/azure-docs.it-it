---
title: Intervalli IP autorizzati del server API in Azure Kubernetes Service (AKS)
description: Informazioni su come proteggere il cluster usando un intervallo di indirizzi IP per l'accesso al server API in Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 7dcf962345a2453fca52825c4be33a439d25df54
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68740924"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Anteprima-proteggere l'accesso al server API usando gli intervalli di indirizzi IP autorizzati in Azure Kubernetes Service (AKS)

In Kubernetes, il server API riceve le richieste per eseguire le azioni nel cluster, ad esempio per creare risorse o ridimensionare il numero di nodi. Il server API è il modo centrale per interagire con e gestire un cluster. Per migliorare la sicurezza del cluster e ridurre al minimo gli attacchi, il server API deve essere accessibile solo da un set limitato di intervalli di indirizzi IP.

Questo articolo illustra come usare gli intervalli di indirizzi IP autorizzati del server API per limitare le richieste di controllo del piano. Questa funzionalità è attualmente in anteprima.

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono self-service e acconsentino esplicitamente. Sono disponibili per raccogliere commenti e suggerimenti e bug dalla community. In anteprima, queste funzionalità non sono destinate all'uso in produzione. Le funzionalità nella versione di anteprima pubblica rientrano nel supporto "Best Effort". L'assistenza dei team di supporto tecnico AKS è disponibile solo durante l'orario di ufficio Pacific TimeZone (PST). Per ulteriori informazioni, consultare gli articoli di supporto seguenti:
>
> * [Criteri di supporto AKS][aks-support-policies]
> * [Domande frequenti relative al supporto tecnico Azure][aks-faq]

## <a name="before-you-begin"></a>Prima di iniziare

Gli intervalli IP autorizzati del server API funzionano solo per i nuovi cluster AKS creati dall'utente. Questo articolo illustra come creare un cluster AKS usando l'interfaccia della riga di comando di Azure.

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.61 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [installare l'interfaccia][install-azure-cli]della riga di comando di Azure.

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga comando di aks-preview

Per configurare gli intervalli IP autorizzati del server API, è necessaria la versione dell'estensione dell'interfaccia della riga di comando *AKS-Preview* 0.4.1 o successiva. Installare l'estensione dell'interfaccia della riga di comando di Azure *AKS-Preview* usando il comando [AZ Extension Add][az-extension-add] , quindi verificare la disponibilità di eventuali aggiornamenti tramite il comando [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flag-for-your-subscription"></a>Registra flag funzionalità per la sottoscrizione

Per usare gli intervalli IP autorizzati del server API, abilitare prima di tutto un flag funzionalità per la sottoscrizione. Per registrare il flag della funzionalità *APIServerSecurityPreview* , usare il comando [AZ feature Register][az-feature-register] , come illustrato nell'esempio seguente:

> [!CAUTION]
> Quando si registra una funzionalità in una sottoscrizione, attualmente non è possibile annullare la registrazione di tale funzionalità. Dopo aver abilitato alcune funzionalità di anteprima, è possibile usare i valori predefiniti per tutti i cluster AKS, quindi creati nella sottoscrizione. Non abilitare le funzionalità di anteprima nelle sottoscrizioni di produzione. Usare una sottoscrizione separata per testare le funzionalità di anteprima e raccogliere commenti e suggerimenti.

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllare lo stato della registrazione usando il comando [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft. servizio contenitore* usando il comando [AZ provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Limitazioni

Quando si configurano gli intervalli IP autorizzati del server API, si applicano le limitazioni seguenti:

* Attualmente non è possibile usare Azure Dev Spaces perché è bloccata anche la comunicazione con il server API.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Panoramica degli intervalli IP autorizzati del server API

Il server API Kubernetes è il modo in cui vengono esposte le API Kubernetes sottostanti. Questo componente fornisce l'interazione per gli strumenti di gestione, ad esempio `kubectl` o il dashboard di Kubernetes. AKS fornisce un master cluster a tenant singolo con un server API dedicato. Per impostazione predefinita, al server API viene assegnato un indirizzo IP pubblico ed è necessario controllare l'accesso tramite i controlli degli accessi in base al ruolo (RBAC).

Per proteggere l'accesso al server API o al piano di controllo AKS accessibile pubblicamente, è possibile abilitare e usare gli intervalli di indirizzi IP autorizzati. Questi intervalli IP autorizzati consentono solo gli intervalli di indirizzi IP definiti per la comunicazione con il server API. Una richiesta effettuata al server API da un indirizzo IP che non fa parte di questi intervalli IP autorizzati è bloccata. È necessario continuare a usare il controllo degli accessi in base al ruolo per autorizzare gli utenti e le azioni richieste.

Per altre informazioni sul server API e altri componenti cluster, vedere [concetti di base di Kubernetes per AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>Creare un cluster del servizio Azure Container

Gli intervalli IP autorizzati del server API funzionano solo per i nuovi cluster AKS. Non è possibile abilitare gli intervalli IP autorizzati come parte dell'operazione di creazione del cluster. Se si prova ad abilitare gli intervalli IP autorizzati come parte del processo di creazione del cluster, i nodi del cluster non sono in grado di accedere al server API durante la distribuzione perché l'indirizzo IP in uscita non è definito in quel momento.

Per prima cosa, creare un cluster usando il comando [AZ AKS create][az-aks-create] . Nell'esempio seguente viene creato un cluster a nodo singolo denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-outbound-gateway-for-firewall-rules"></a>Creare un gateway in uscita per le regole del firewall

Per assicurarsi che i nodi in un cluster possano comunicare in modo affidabile con il server API quando si abilitano gli intervalli IP autorizzati nella sezione successiva, creare un firewall di Azure da usare come gateway in uscita. L'indirizzo IP del firewall di Azure viene quindi aggiunto all'elenco degli indirizzi IP del server API autorizzati nella sezione successiva.

> [!WARNING]
> L'uso del firewall di Azure può comportare costi significativi in un ciclo di fatturazione mensile. Il requisito di usare il firewall di Azure dovrebbe essere necessario solo in questo periodo di anteprima iniziale. Per altre informazioni e per la pianificazione dei costi, vedere [prezzi di Azure firewall][azure-firewall-costs].

Per prima cosa, ottenere il nome del gruppo di risorse *MC_* per il cluster AKS e la rete virtuale. Quindi, creare una subnet usando il comando [AZ Network VNET subnet create][az-network-vnet-subnet-create] . Nell'esempio seguente viene creata una subnet denominata *AzureFirewallSubnet* con l'intervallo CIDR di *10.200.0.0/16*:

```azurecli-interactive
# Get the name of the MC_ cluster resource group
MC_RESOURCE_GROUP=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query nodeResourceGroup -o tsv)

# Get the name of the virtual network used by the cluster
VNET_NAME=$(az network vnet list \
    --resource-group $MC_RESOURCE_GROUP \
    --query [0].name -o tsv)

# Create a subnet in the virtual network for Azure Firewall
az network vnet subnet create \
    --resource-group $MC_RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name AzureFirewallSubnet \
    --address-prefixes 10.200.0.0/16
```

Per creare un firewall di Azure, installare l'estensione dell'interfaccia della riga di comando di *Azure-firewall* usando il comando [AZ Extension Add][az-extension-add] . Quindi, creare un firewall usando il comando [AZ Network Firewall create][az-network-firewall-create] . L'esempio seguente crea un firewall di Azure denominato *myAzureFirewall*:

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

A un firewall di Azure viene assegnato un indirizzo IP pubblico in uscita dal traffico. Creare un indirizzo pubblico usando il comando [AZ Network Public-IP create][az-network-public-ip-create] , quindi creare una configurazione IP nel firewall usando il comando [AZ network firewall IP-config create][az-network-firewall-ip-config-create] che applica l'IP pubblico:

```azurecli-interactive
# Create a public IP address for the firewall
FIREWALL_PUBLIC_IP=$(az network public-ip create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallPublicIP \
    --sku Standard \
    --query publicIp.ipAddress -o tsv)

# Associated the firewall with virtual network
az network firewall ip-config create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallIPConfig \
    --vnet-name $VNET_NAME \
    --firewall-name myAzureFirewall \
    --public-ip-address myAzureFirewallPublicIP
```

Creare ora la regola di rete del firewall di Azure per *consentire* tutto il traffico *TCP* usando il comando [AZ Network Firewall Network-Rule create][az-network-firewall-network-rule-create] . Nell'esempio seguente viene creata una regola di rete denominata *AllowTCPOutbound* per il traffico con qualsiasi indirizzo di origine o di destinazione:

```azurecli-interactive
az network firewall network-rule create \
    --resource-group $MC_RESOURCE_GROUP \
    --firewall-name myAzureFirewall \
    --name AllowTCPOutbound \
    --collection-name myAzureFirewallCollection \
    --priority 200 \
    --action Allow \
    --protocols TCP \
    --source-addresses '*' \
    --destination-addresses '*' \
    --destination-ports '*'
```

Per associare il firewall di Azure alla route di rete, ottenere le informazioni sulla tabella di route esistente, l'indirizzo IP interno del firewall di Azure e quindi l'indirizzo IP del server API. Questi indirizzi IP vengono specificati nella sezione successiva per controllare come deve essere indirizzato il traffico per la comunicazione del cluster.

```azurecli-interactive
# Get the AKS cluster route table
ROUTE_TABLE=$(az network route-table list \
    --resource-group $MC_RESOURCE_GROUP \
    --query "[?contains(name,'agentpool')].name" -o tsv)

# Get internal IP address of the firewall
FIREWALL_INTERNAL_IP=$(az network firewall show \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewall \
    --query ipConfigurations[0].privateIpAddress -o tsv)

# Get the IP address of API server endpoint
K8S_ENDPOINT_IP=$(kubectl get endpoints -o=jsonpath='{.items[?(@.metadata.name == "kubernetes")].subsets[].addresses[].ip}')
```

Infine, creare una route nella tabella di route di rete AKS esistente usando il comando [AZ Network Route-Table Route create][az-network-route-table-route-create] che consente al traffico di usare l'appliance del firewall di Azure per la comunicazione del server API.

```azurecli-interactive
az network route-table route create \
    --resource-group $MC_RESOURCE_GROUP \
    --route-table-name $ROUTE_TABLE \
    --name AzureFirewallAPIServer \
    --address-prefix $K8S_ENDPOINT_IP/32 \
    --next-hop-ip-address $FIREWALL_INTERNAL_IP \
    --next-hop-type VirtualAppliance

echo "Public IP address for the Azure Firewall instance that should be added to the list of API server authorized addresses is:" $FIREWALL_PUBLIC_IP
```

Prendere nota dell'indirizzo IP pubblico dell'appliance del firewall di Azure. Questo indirizzo viene aggiunto all'elenco degli intervalli di indirizzi IP autorizzati del server API nella sezione successiva.

## <a name="enable-authorized-ip-ranges"></a>Abilita intervalli IP autorizzati

Per abilitare gli intervalli IP autorizzati del server API, fornire un elenco di intervalli di indirizzi IP autorizzati. Quando si specifica un intervallo CIDR, iniziare con il primo indirizzo IP compreso nell'intervallo. Ad esempio, *137.117.106.90/29* è un intervallo valido, ma assicurarsi di specificare il primo indirizzo IP nell'intervallo, ad esempio *137.117.106.88/29*.

Usare il comando [AZ AKS Update][az-aks-update] e specificare *--API-server-Authorized-IP-Ranges* per consentire. Questi intervalli di indirizzi IP sono in genere intervalli di indirizzi usati dalle reti locali. Aggiungere l'indirizzo IP pubblico del firewall di Azure ottenuto nel passaggio precedente, ad esempio *20.42.25.196/32*.

L'esempio seguente abilita gli intervalli IP autorizzati del server API nel cluster denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*. Gli intervalli di indirizzi IP da autorizzare sono *20.42.25.196/32* (l'indirizzo IP pubblico del firewall di Azure), quindi *172.0.0.0/16* e *168.10.0.0/18*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.0/16,168.10.0.0/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>Aggiornare o disabilitare gli intervalli IP autorizzati

Per aggiornare o disabilitare gli intervalli IP autorizzati, usare di nuovo il comando [AZ AKS Update][az-aks-update] . Specificare l'intervallo CIDR aggiornato che si desidera consentire oppure specificare un intervallo vuoto per disabilitare gli intervalli IP autorizzati del server API, come illustrato nell'esempio seguente:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati abilitati gli intervalli di indirizzi IP autorizzati del server API. Questo approccio è una parte del modo in cui è possibile eseguire un cluster AKS sicuro.

Per altre informazioni, vedere [concetti relativi alla sicurezza per le applicazioni e i cluster in AKS][concepts-security] e [procedure consigliate per la sicurezza e gli aggiornamenti del cluster in AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[azure-firewall-costs]: https://azure.microsoft.com/pricing/details/azure-firewall/

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[create-aks-sp]: kubernetes-service-principal.md#manually-create-a-service-principal
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
