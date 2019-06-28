---
title: API server autorizzato intervalli di indirizzi IP in Azure Kubernetes Service (AKS)
description: Informazioni su come proteggere il cluster usando un intervallo di indirizzi IP per l'accesso al server API in Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: 9ec48c8ed924293a5ffea903fe03a9830dcd1184
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329427"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Anteprima - proteggere l'accesso al server API usando autorizzato intervalli di indirizzi IP in Azure Kubernetes Service (AKS)

In Kubernetes, il server API riceve le richieste per eseguire azioni nel cluster, ad esempio per creare risorse oppure aumentare il numero di nodi. Il server API è lo strumento centrale per interagire con e gestire un cluster. Per migliorare la sicurezza del cluster e ridurre al minimo gli attacchi, è possibile che solo il server API deve essere accessibile da un set limitato di intervalli di indirizzi IP.

Questo articolo illustra come usare gli intervalli di indirizzi IP di server autorizzato di API per limitare le richieste al piano di controllo. Questa funzionalità è attualmente in anteprima.

> [!IMPORTANT]
> Funzionalità di anteprima del servizio contenitore di AZURE sono self-service, fornire il consenso esplicito. Vengono fornite per raccogliere commenti e suggerimenti e bug dalla community. In fase di anteprima, queste funzionalità non sono destinate all'uso di produzione. Le funzionalità in anteprima pubblica rientrano nel supporto "best effort". Assistenza dai team di supporto tecnico di AKS è disponibile durante le ore lavorative Pacifico (PST) solo timezone. Per altre informazioni, vedere i seguenti articoli di supporto:
>
> * [Criteri di supporto servizio contenitore di AZURE][aks-support-policies]
> * [Domande frequenti relative al supporto tecnico Azure][aks-faq]

## <a name="before-you-begin"></a>Prima di iniziare

Server dell'API funzionano solo per i nuovi cluster servizio contenitore di AZURE che creano gli intervalli IP autorizzati. Questo articolo illustra come creare un cluster AKS usando il comando di Azure.

È necessario la CLI di Azure versione 2.0.61 o versione successiva installato e configurato. Eseguire  `az --version` per trovare la versione. Se è necessario installare o eseguire l'aggiornamento, vedere [installare CLI Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga comando di aks-preview

Per configurare gli intervalli IP di server autorizzato, API, sono necessari i *aks-preview* CLI versione dell'estensione 0.4.1 o versione successiva. Installare il *aks-preview* estensione di comando di Azure usando la [Aggiungi estensione az][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] comando:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flag-for-your-subscription"></a>Registrare i flag funzionalità per la sottoscrizione

Per usare l'API server autorizzato intervalli di indirizzi IP, abilitare prima di tutto un flag funzionalità per la sottoscrizione. Per registrare il *APIServerSecurityPreview* flag delle funzionalità, utilizzare il [register funzionalità az][az-feature-register] seguente come mostrato nell'esempio seguente:

> [!CAUTION]
> Quando si registra una funzionalità in una sottoscrizione, attualmente non è possibile annullare la registrazione tale funzionalità. Dopo aver abilitato alcune funzionalità di anteprima, potrebbero essere utilizzati i valori predefiniti per tutti i cluster AKS quindi creati nella sottoscrizione. Non abilitare la funzionalità di anteprima nella sottoscrizione di produzione. Usare una sottoscrizione separata per le funzionalità di anteprima di test e raccogliere commenti e suggerimenti.

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllarne lo stato di registrazione usando il [elenco delle funzionalità az][az-feature-list] comando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione dei *containerservice* provider di risorse usando la [register di az provider][az-provider-register] comando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Limitazioni

Le limitazioni seguenti si applicano quando si configurano gli intervalli IP di server autorizzato API:

* Attualmente, è possibile usare spazi di sviluppo di Azure perché la comunicazione con il server dell'API viene ugualmente bloccata.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Panoramica dell'API del server autorizzati intervalli di indirizzi IP

Il server API Kubernetes è come vengono esposte le APIs Kubernetes sottostante. Questo componente fornisce l'interazione per gli strumenti di gestione, ad esempio `kubectl` o il dashboard di Kubernetes. Servizio contenitore di AZURE fornisce un master a tenant singolo cluster, con un server API dedicato. Per impostazione predefinita, il server dell'API viene assegnato un indirizzo IP pubblico e si deve controllare l'accesso usando i controlli di accesso basato sui ruoli (RBAC).

Per proteggere l'accesso al piano di controllo servizio contenitore di AZURE in caso contrario, è accessibile pubblicamente / API del server, è possibile abilitare e usare intervalli di indirizzi IP autorizzati. Questi intervalli IP autorizzati consentono solo definito gli intervalli di indirizzi IP comunicare con il server API. Una richiesta effettuata al server API da un indirizzo IP che non fa parte di questi intervalli IP autorizzati viene bloccata. È consigliabile continuare a usare RBAC quindi autorizzare gli utenti e le azioni che richiedono.

Per altre informazioni sul server di API e altri componenti del cluster, vedere [Kubernetes concetti fondamentali per AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>Creare un cluster del servizio Azure Container

Gli intervalli IP di server autorizzato di API funzionano solo per i nuovi cluster AKS. Come parte del cluster creare operazione non è possibile abilitare gli intervalli IP autorizzati. Se si prova ad abilitare gli intervalli IP autorizzati come parte del cluster creare il processo, i nodi del cluster sono in grado di accedere al server API durante la distribuzione, come l'indirizzo IP in uscita non è definito a quel punto.

Innanzitutto, creare un cluster usando il [az aks create][az-aks-create] comando. L'esempio seguente crea un cluster a nodo singolo denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*.

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

Per garantire che i nodi in un cluster in modo affidabile possono comunicare con il server dell'API quando si abilita autorizzati gli intervalli IP nella sezione successiva, creare un firewall di Azure per l'uso del gateway in uscita. L'indirizzo IP del firewall di Azure viene quindi aggiunto all'elenco di indirizzi IP del server API autorizzati nella sezione successiva.

> [!WARNING]
> L'uso di Firewall di Azure può comportare costi significativi su un ciclo di fatturazione mensile. La necessità di usare il Firewall di Azure solo non è necessaria in questo periodo di anteprima iniziale. Per altre informazioni e la pianificazione dei costi, vedere [prezzi di Azure Firewall][azure-firewall-costs].

Innanzitutto, ottenere il *MC _* nome gruppo di risorse per il cluster AKS e la rete virtuale. Quindi, creare una subnet mediante il [subnet di az network vnet create][az-network-vnet-subnet-create] comando. L'esempio seguente crea una subnet denominata *AzureFirewallSubnet* con l'intervallo CIDR della *10.200.0.0/16*:

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

Per creare un Firewall di Azure, installare il *firewall di azure* estensione dell'interfaccia della riga usando la [Aggiungi estensione az][az-extension-add] command. Then, create a firewall using the [az network firewall create][az-network-firewall-create] comando. L'esempio seguente crea un firewall di Azure denominato *myAzureFirewall*:

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

Un firewall di Azure viene assegnato un indirizzo IP pubblico che attraversa il traffico in uscita. Creare un indirizzo pubblico usando il [az network public-ip create][az-network-public-ip-create] command, then create an IP configuration on the firewall using the [az network firewall ip-config create][az-network-firewall-ip-config-create] che applica l'indirizzo IP pubblico:

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

Ora creare la regola firewall di Azure network *consentire* tutte le *TCP* traffico utilizzando il [az network firewall network-rule create][az-network-firewall-network-rule-create] comando. L'esempio seguente crea una regola di rete denominata *AllowTCPOutbound* per il traffico con un indirizzo di origine o di destinazione:

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

Per associare il firewall di Azure con la route di rete, ottenere le informazioni sulla tabella di route esistenti, l'indirizzo IP interno del firewall di Azure e quindi l'indirizzo IP del server API. Questi indirizzi IP vengono specificati nella sezione successiva per controllare la modalità deve essere indirizzato il traffico per le comunicazioni del cluster.

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

Infine, creare una route nel esistente AKS rete route nella tabella usando il [route di az network route-table create][az-network-route-table-route-create] comando che consente il traffico all'uso dell'appliance firewall di Azure per la comunicazione tra server API.

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

Prendere nota dell'indirizzo IP pubblico dell'appliance Firewall di Azure. Questo indirizzo viene aggiunto all'elenco di intervalli IP server autorizzato API nella sezione successiva.

## <a name="enable-authorized-ip-ranges"></a>Consentire intervalli di indirizzi IP autorizzati

Per consentire gli intervalli IP di server autorizzato, API, è fornire un elenco di intervalli di indirizzi IP autorizzati. Quando si specifica un intervallo CIDR, iniziare con il primo indirizzo IP compreso nell'intervallo. Ad esempio, *137.117.106.90/29* è un intervallo valido, ma assicurarsi di specificare il primo indirizzo IP dell'intervallo, ad esempio *137.117.106.88/29*.

Usare [aggiornare az aks][az-aks-update] comando e specificare il *- api-server-autorizzato a intervalli di indirizzi ip* consentire o meno. Questi intervalli di indirizzi IP sono in genere gli intervalli di indirizzi usati per le reti locali. Aggiungere l'indirizzo IP pubblico del proprio firewall Azure ottenuto nel passaggio precedente, ad esempio *20.42.25.196/32*.

L'esempio seguente Abilita gli intervalli IP di server autorizzato API nei cluster denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*. Sono gli intervalli di indirizzi IP per autorizzare *20.42.25.196/32* (il firewall di Azure indirizzo IP pubblico), quindi *172.0.0.10/16* e *168.10.0.10/18*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.10/16,168.10.0.10/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>Aggiornare o disabilitare intervalli di indirizzi IP autorizzati

Per aggiornare o disabilitare intervalli di indirizzi IP autorizzati, anche in questo caso usare [az aks aggiornare][az-aks-update] comando. Specificare l'intervallo CIDR aggiornato che si vuole consentire o specificare un intervallo vuoto per disabilitare il server API autorizzato gli intervalli IP, come illustrato nell'esempio seguente:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato abilitato API intervalli di indirizzi IP server autorizzato. Questo approccio è una parte del modo in cui è possibile eseguire un cluster AKS sicuro.

Per altre informazioni, vedere [concetti relativi alla sicurezza per le applicazioni e i cluster di AKS][concepts-security] and [Best practices for cluster security and upgrades in AKS][operator-best-practices-cluster-security].

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
[az-extension-list]: /cli/azure/extension#az-extension-list
[az-extension-update]: /cli/azure/extension#az-extension-update
