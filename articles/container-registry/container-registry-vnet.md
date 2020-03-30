---
title: Limitare l'accesso con una rete virtuale
description: Consentire l'accesso a un registro contenitore di Azure solo dalle risorse in una rete virtuale di Azure o da intervalli di indirizzi IP pubblici.
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: a6b89b074c25ea0948597ede7e5681b100c7f429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454338"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Limitare l'accesso a un registro contenitori di Azure usando una rete virtuale di Azure o le regole del firewallRestrict access to an Azure container registry using an Azure virtual network or firewall rules

[Rete virtuale](../virtual-network/virtual-networks-overview.md) di Azure offre reti private e sicure per le risorse di Azure e locali. Limitando l'accesso al registro dei contenitori di Azure privato da una rete virtuale di Azure, si garantisce che solo le risorse nella rete virtuale accedano al Registro di sistema. Per gli scenari cross-premise, è anche possibile configurare le regole del firewall per consentire l'accesso al Registro di sistema solo da indirizzi IP specifici.

Questo articolo illustra due scenari per configurare le regole di accesso alla rete in ingresso in un registro contenitori: da una macchina virtuale distribuita in una rete virtuale o dall'indirizzo IP pubblico di una macchina virtuale.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima e [si applicano alcune limitazioni](#preview-limitations). Le anteprime vengono rese disponibili a condizione che l'utente accetti le [condizioni supplementari per l'utilizzo][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.
>

Se invece è necessario configurare le regole di accesso per le risorse per raggiungere un registro contenitori da dietro un firewall, vedere [Configurare le regole per accedere a un registro](container-registry-firewall-access-rules.md)del contenitore di Azure dietro un firewall.


## <a name="preview-limitations"></a>Limiti di anteprima

* Solo un registro contenitori **Premium** può essere configurato con le regole di accesso alla rete. Per informazioni sui livelli del servizio Registro di sistema, vedere [SKU del Registro di sistema del contenitore](container-registry-skus.md)di Azure.For information about registry service tiers, see Azure Container Registry SKUs . 

* Solo un cluster di servizi [Azure Kubernetes](../aks/intro-kubernetes.md) o [una macchina virtuale](../virtual-machines/linux/overview.md) di Azure possono essere usati come host per accedere a un registro contenitori in una rete virtuale. *Altri servizi di Azure, incluse le istanze del contenitore di Azure, non sono attualmente supportati.*

* Le operazioni attività [ACR](container-registry-tasks-overview.md) non sono attualmente supportate in un registro contenitori a cui si accede in una rete virtuale.

* Ogni registro supporta un massimo di 100 regole di rete virtuale.

## <a name="prerequisites"></a>Prerequisiti

* Per usare i passaggi dell'interfaccia della riga di comando di Azure in questo articolo, è necessaria la versione 2.0.58 o successiva dell'interfaccia della riga di comando di Azure.To use the Azure CLI steps in this article, Azure CLI version 2.0.58 or later. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If][azure-cli]you need to install or upgrade, see Install Azure CLI.

* Se non si dispone già di un registro contenitori, crearne uno `hello-world` (SKU Premium richiesto) e eseguire il push di un'immagine di esempio, ad esempio da Docker Hub.If you don't already have a container registry, create one (Premium SKU required) and push a sample image such as docker Hub. Ad esempio, usare il [portale di Azure][quickstart-portal] o l'interfaccia della riga di comando di [Azure][quickstart-cli] per creare un Registro di sistema. 

* Se si vuole limitare l'accesso al Registro di sistema usando una rete virtuale in una sottoscrizione di Azure diversa, è necessario registrare il provider di risorse per il Registro di sistema del contenitore di Azure in tale sottoscrizione. Ad esempio:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>Informazioni sulle regole di rete per un registro contenitori

Un registro contenitore di Azure per impostazione predefinita accetta connessioni tramite Internet dagli host in qualsiasi rete. Con una rete virtuale, è possibile consentire solo alle risorse di Azure, ad esempio un cluster AKS o una macchina virtuale di Azure, di accedere in modo sicuro al Registro di sistema, senza oltrepassare un limite di rete. È inoltre possibile configurare le regole del firewall di rete per consentire solo intervalli di indirizzi IP Internet pubblici specifici. 

Per limitare l'accesso a un Registro di sistema, modificare innanzitutto l'azione predefinita del Registro di sistema in modo da negare tutte le connessioni di rete. Aggiungere quindi le regole di accesso alla rete. I client a cui è stato concesso l'accesso tramite le regole di rete devono continuare a [eseguire l'autenticazione nel registro contenitori](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) ed essere autorizzati ad accedere ai dati.

### <a name="service-endpoint-for-subnets"></a>Endpoint di servizio per le subnetService endpoint for subnets

Per consentire l'accesso da una subnet in una rete virtuale, è necessario aggiungere un endpoint del servizio per il servizio Registro di sistema del contenitore di Azure.To allow access from a subnet in a virtual network, you need to add a [service endpoint](../virtual-network/virtual-network-service-endpoints-overview.md) for the Azure Container Registry service. 

I servizi multi-tenant, ad esempio Il Registro di sistema del contenitore di Azure, usano un singolo set di indirizzi IP per tutti i clienti. Un endpoint del servizio assegna un endpoint per accedere a un registro. Questo endpoint offre al traffico una route ottimale alla risorsa tramite la rete backbone di Azure.This endpoint gives traffic an optimal route to the resource over the Azure backbone network. Con ogni richiesta vengono anche trasmesse le identità della rete virtuale e della subnet.

### <a name="firewall-rules"></a>Regole del firewall

Per le regole di rete IP, fornire intervalli di indirizzi Internet consentiti utilizzando la notazione CIDR, ad esempio *16.17.18.0/24* o un singolo indirizzo IP come *16.17.18.19*. Le regole di rete IP sono consentite solo per gli indirizzi IP *Internet pubblici.* Gli intervalli di indirizzi IP riservati per le reti private (come definito nell'RFC 1918) non sono consentiti nelle regole IP.

## <a name="create-a-docker-enabled-virtual-machine"></a>Creare una macchina virtuale abilitata per DockerCreate a Docker-enabled virtual machine

Per questo articolo, usare una macchina virtuale Ubuntu abilitata per Docker per accedere a un registro contenitore di Azure.For this article, use a Docker-enabled Ubuntu VM to access an Azure container registry. Per usare l'autenticazione di Azure Active Directory nel Registro di sistema, installare anche [l'interfaccia della riga di comando][azure-cli] di Azure nella macchina virtuale. Se si dispone già di una macchina virtuale di Azure, ignorare questo passaggio di creazione.

È possibile usare lo stesso gruppo di risorse per la macchina virtuale e il registro contenitori. Questa configurazione semplifica la pulizia alla fine, ma non è necessaria. Se si sceglie di creare un gruppo di risorse separato per la macchina virtuale e la rete virtuale, eseguire [az group create][az-group-create]. L'esempio seguente crea un gruppo di risorse denominato myResourceGroup nella posizione *westcentralus:The following example* creates a resource group named *myResourceGroup* in the westcentralus location:

```azurecli
az group create --name myResourceGroup --location westus
```

A questo punto distribuire una macchina virtuale predefinita di Ubuntu Azure con [az vm create][az-vm-create]. L'esempio seguente crea una macchina virtuale denominata myDockerVM:The following example creates a VM named *myDockerVM:*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Per creare la macchina virtuale sono necessari alcuni minuti. Dopo aver eseguito il comando, prendere nota del `publicIpAddress` visualizzato dall'interfaccia della riga di comando di Azure. Usare questo indirizzo per stabilire connessioni SSH alla macchina virtuale e, facoltativamente, per una successiva configurazione delle regole del firewall.

### <a name="install-docker-on-the-vm"></a>Installare Docker nella macchina virtuale

Una volta che la macchina virtuale è in esecuzione, stabilire una connessione SSH alla macchina virtuale. Sostituire *publicIpAddress* con l'indirizzo IP pubblico della macchina virtuale.

```bash
ssh azureuser@publicIpAddress
```

Eseguire il comando seguente per installare Docker nella macchina virtuale Ubuntu:Run the following command to install Docker on the Ubuntu VM:

```bash
sudo apt install docker.io -y
```

Dopo l'installazione, eseguire il comando seguente per verificare la corretta esecuzione di Docker nella macchina virtuale:

```bash
sudo docker run -it hello-world
```

Output:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Installare l'interfaccia della riga di comando di Azure

Seguire i passaggi descritti in [Installare l'interfaccia della riga di comando di Azure con APT](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) per installare l'interfaccia della riga di comando di Azure nella macchina virtuale Ubuntu. Per questo articolo, assicurarsi di installare la versione 2.0.58 o successiva.

Uscire dalla connessione SSH.

## <a name="allow-access-from-a-virtual-network"></a>Consentire l'accesso da una rete virtualeAllow access from a virtual network

In questa sezione configurare il registro contenitori per consentire l'accesso da una subnet in una rete virtuale di Azure.In this section, configure your container registry to allow access from a subnet in an Azure virtual network. Vengono forniti passaggi equivalenti tramite l'interfaccia della riga di comando di Azure e il portale di Azure.Equivalent steps using the Azure CLI and Azure portal are provided.

### <a name="allow-access-from-a-virtual-network---cli"></a>Consentire l'accesso da una rete virtuale - CLIAllow access from a virtual network - CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Aggiungere un endpoint del servizio a una subnetAdd a service endpoint to a subnet

Quando si crea una macchina virtuale, Azure per impostazione predefinita crea una rete virtuale nello stesso gruppo di risorse. Il nome della rete virtuale è basato sul nome della macchina virtuale. Ad esempio, se si assegna alla macchina virtuale il nome *myDockerVM*, il nome della rete virtuale predefinita sarà *myDockerVMVNET*, con una subnet denominata *myDockerVMSubnet*. Verificare questo nel portale di Azure o utilizzando il comando az network vnet list:Verify this in the Azure portal or by using the [az network vnet list command:][az-network-vnet-list]

```azurecli
az network vnet list --resource-group myResourceGroup --query "[].{Name: name, Subnet: subnets[0].name}"
```

Output:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

Utilizzare il comando [az network vnet subnet update][az-network-vnet-subnet-update] per aggiungere un endpoint del servizio **Microsoft.ContainerRegistry** alla subnet. Sostituire i nomi della rete virtuale e della subnet nel comando seguente:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Utilizzare il comando [using az network vnet subnet show][az-network-vnet-subnet-show] per recuperare l'ID risorsa della subnet. Questo problema è necessario in un passaggio successivo per configurare una regola di accesso alla rete.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

Output:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="change-default-network-access-to-registry"></a>Modificare l'accesso di rete predefinito al Registro di sistemaChange default network access to registry

Per impostazione predefinita, un registro contenitori di Azure consente le connessioni dagli host in qualsiasi rete. Per limitare l'accesso a una rete selezionata, modificare l'azione predefinita per negare l'accesso. Sostituire il nome del Registro di sistema nel seguente comando [az acr update:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Aggiungere una regola di rete al Registro di sistemaAdd network rule to registry

Usare il comando [az acr network-rule add][az-acr-network-rule-add] per aggiungere una regola di rete al Registro di sistema che consenta l'accesso dalla subnet della macchina virtuale. Sostituire il nome del Registro di sistema del contenitore e l'ID risorsa della subnet nel comando seguente: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Continuare con [Verificare l'accesso al Registro di sistema](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Consentire l'accesso da una rete virtuale - portaleAllow access from a virtual network - portal

#### <a name="add-service-endpoint-to-subnet"></a>Aggiungere l'endpoint del servizio alla subnetAdd service endpoint to subnet

Quando si crea una macchina virtuale, Azure per impostazione predefinita crea una rete virtuale nello stesso gruppo di risorse. Il nome della rete virtuale è basato sul nome della macchina virtuale. Ad esempio, se si assegna alla macchina virtuale il nome *myDockerVM*, il nome della rete virtuale predefinita sarà *myDockerVMVNET*, con una subnet denominata *myDockerVMSubnet*.

Per aggiungere un endpoint del servizio per il Registro di sistema del contenitore di Azure a una subnet:To add a service endpoint for Azure Container Registry to a subnet:

1. Nella casella di ricerca nella parte superiore del portale di [Azure][azure-portal]immettere *reti virtuali.* Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
1. Nell'elenco delle reti virtuali selezionare la rete virtuale in cui viene distribuita la macchina virtuale, ad esempio *myDockerVMVNET*.
1. In **Impostazioni**selezionare **Subnet.**
1. Selezionare la subnet in cui è distribuita la macchina virtuale, ad esempio *myDockerVMSubnet*.
1. In **Endpoint del servizio**selezionare **Microsoft.ContainerRegistry**.
1. Selezionare **Salva**.

![Aggiungere l'endpoint del servizio alla subnetAdd service endpoint to subnet][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Configurare l'accesso di rete per il Registro di sistema

Per impostazione predefinita, un registro contenitori di Azure consente le connessioni dagli host in qualsiasi rete. Per limitare l'accesso alla rete virtuale:

1. Nel portale passare al Registro di sistema del contenitore.
1. In **Impostazioni**selezionare **Firewall e reti virtuali.**
1. Per negare l'accesso per impostazione predefinita, scegliere di consentire l'accesso da **Reti selezionate**. 
1. Selezionare **Aggiungi rete virtuale esistente**e selezionare la rete virtuale e la subnet configurate con un endpoint del servizio. Selezionare **Aggiungi**.
1. Selezionare **Salva**.

![Configurare la rete virtuale per il Registro di sistema del contenitoreConfigure virtual network for container registry][acr-vnet-portal]

Continuare con [Verificare l'accesso al Registro di sistema](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Consentire l'accesso da un indirizzo IP

In questa sezione configurare il registro contenitori per consentire l'accesso da un intervallo o un indirizzo IP specifico. Vengono forniti passaggi equivalenti tramite l'interfaccia della riga di comando di Azure e il portale di Azure.Equivalent steps using the Azure CLI and Azure portal are provided.

### <a name="allow-access-from-an-ip-address---cli"></a>Consentire l'accesso da un indirizzo IP - CLI

#### <a name="change-default-network-access-to-registry"></a>Modificare l'accesso di rete predefinito al Registro di sistemaChange default network access to registry

Se non è già stato fatto, aggiornare la configurazione del Registro di sistema per negare l'accesso per impostazione predefinita. Sostituire il nome del Registro di sistema nel seguente comando [az acr update:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Rimuovi regola di rete dal Registro di sistema

Se in precedenza è stata aggiunta una regola di rete per consentire l'accesso dalla subnet della macchina virtuale, rimuovere l'endpoint del servizio della subnet e la regola di rete. Sostituire il nome del registro contenitori e l'ID risorsa della subnet recuperata in un passaggio precedente nel comando [az acr network-rule remove:][az-acr-network-rule-remove] 

```azurecli
# Remove service endpoint

az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints ""

# Remove network rule

az acr network-rule remove --name mycontainerregistry --subnet <subnet-resource-id>
```

#### <a name="add-network-rule-to-registry"></a>Aggiungere una regola di rete al Registro di sistemaAdd network rule to registry

Usare il comando [az acr network-rule add][az-acr-network-rule-add] per aggiungere una regola di rete al Registro di sistema che consenta l'accesso dall'indirizzo IP della macchina virtuale. Sostituire il nome del Registro di sistema del contenitore e l'indirizzo IP pubblico della macchina virtuale nel comando seguente.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Continuare con [Verificare l'accesso al Registro di sistema](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Consentire l'accesso da un indirizzo IP - portaleAllow access from an IP address - portal

#### <a name="remove-existing-network-rule-from-registry"></a>Rimuovere la regola di rete esistente dal Registro di sistemaRemove existing network rule from registry

Se in precedenza è stata aggiunta una regola di rete per consentire l'accesso dalla subnet della macchina virtuale, rimuovere la regola esistente. Ignorare questa sezione se si vuole accedere al Registro di sistema da una macchina virtuale diversa.

* Aggiornare le impostazioni della subnet per rimuovere l'endpoint del servizio della subnet per il Registro di sistema del contenitore di Azure.Update the subnet settings to remove the subnet's service endpoint for Azure Container Registry. 

  1. Nel [portale di Azure][azure-portal]passare alla rete virtuale in cui viene distribuita la macchina virtuale.
  1. In **Impostazioni**selezionare **Subnet.**
  1. Selezionare la subnet in cui è distribuita la macchina virtuale.
  1. In **Endpoint del servizio**rimuovere la casella di controllo **Microsoft.ContainerRegistry**. 
  1. Selezionare **Salva**.

* Rimuovere la regola di rete che consente alla subnet di accedere al Registro di sistema.

  1. Nel portale passare al Registro di sistema del contenitore.
  1. In **Impostazioni**selezionare **Firewall e reti virtuali.**
  1. In **Reti virtuali**selezionare il nome della rete virtuale e quindi **Rimuovi**.
  1. Selezionare **Salva**.

#### <a name="add-network-rule-to-registry"></a>Aggiungere una regola di rete al Registro di sistemaAdd network rule to registry

1. Nel portale passare al Registro di sistema del contenitore.
1. In **Impostazioni**selezionare **Firewall e reti virtuali.**
1. Se non è già stato fatto, scegliere di consentire l'accesso da **Reti selezionate**. 
1. In **Reti virtuali**verificare che non sia selezionata alcuna rete.
1. In **Firewall**immettere l'indirizzo IP pubblico di una macchina virtuale. In alternativa, immettere un intervallo di indirizzi nella notazione CIDR contenente l'indirizzo IP della macchina virtuale.
1. Selezionare **Salva**.

![Configurare la regola del firewall per il Registro di sistema del contenitore][acr-vnet-firewall-portal]

Continuare con [Verificare l'accesso al Registro di sistema](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Verificare l'accesso al Registro di sistema

Dopo aver atteso alcuni minuti per l'aggiornamento della configurazione, verificare che la macchina virtuale possa accedere al Registro di sistema del contenitore. Creare una connessione SSH alla macchina virtuale ed eseguire il comando [az acr login][az-acr-login] per accedere al Registro di sistema. 

```bash
az acr login --name mycontainerregistry
```

È possibile eseguire operazioni `docker pull` del Registro di sistema, ad esempio eseguire per estrarre un'immagine di esempio dal Registro di sistema. Sostituire un'immagine e un valore di tag appropriati per il Registro di sistema, preceduto dal nome del server di accesso al Registro di sistema (tutti minuscoli):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker estrae correttamente l'immagine nella macchina virtuale.

In questo esempio viene illustrato che è possibile accedere al registro dei contenitori privati tramite la regola di accesso alla rete. Tuttavia, non è possibile accedere al Registro di sistema da un host di accesso diverso che non dispone di una regola di accesso alla rete configurata. Se si tenta di accedere `az acr login` da `docker login` un altro host utilizzando il comando o il comando, l'output è simile al seguente:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Ripristinare l'accesso al Registro di sistema predefinito

Per ripristinare il Registro di sistema per consentire l'accesso per impostazione predefinita, rimuovere tutte le regole di rete configurate. Impostare quindi l'azione predefinita per consentire l'accesso. Vengono forniti passaggi equivalenti tramite l'interfaccia della riga di comando di Azure e il portale di Azure.Equivalent steps using the Azure CLI and Azure portal are provided.

### <a name="restore-default-registry-access---cli"></a>Ripristinare l'accesso al Registro di sistema predefinito - CLI

#### <a name="remove-network-rules"></a>Rimuovere le regole di rete

Per visualizzare un elenco delle regole di rete configurate per il Registro di sistema, eseguire il seguente comando [az acr network-rule list:][az-acr-network-rule-list]

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Per ogni regola configurata, eseguire il comando [az acr network-rule remove][az-acr-network-rule-remove] per rimuoverla. Ad esempio:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet

# Remove a rule that allows access for an IP address or CIDR range such as 23.45.1.0/24.

az acr network-rule remove \
  --name mycontainerregistry \
  --ip-address 23.45.1.0/24
```

#### <a name="allow-access"></a>Consentire l'accesso

Sostituire il nome del Registro di sistema nel seguente comando [az acr update:][az-acr-update]
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Ripristinare l'accesso al Registro di sistema predefinito - portaleRestore default registry access - portal


1. Nel portale passare al Registro di sistema del contenitore e selezionare **Firewall e reti virtuali**.
1. In **Reti virtuali**selezionare ogni rete virtuale e quindi **Rimuovi**.
1. In **Firewall**selezionare ogni intervallo di indirizzi e quindi selezionare l'icona Elimina.
1. In **Consenti accesso da**selezionare Tutte le **reti**. 
1. Selezionare **Salva**.

## <a name="clean-up-resources"></a>Pulire le risorse

Se sono state create tutte le risorse di Azure nello stesso gruppo di risorse e non sono più necessarie, è possibile eliminare le risorse usando un singolo comando [az group delete:If](/cli/azure/group) you created all the Azure resources in the same resource group and no longer need them, you can optionally delete the resources by using a single az group delete command:

```azurecli
az group delete --name myResourceGroup
```

Per pulire le risorse nel portale, passare al gruppo di risorse myResourceGroup.To clean up your resources in the portal, navigate to the myResourceGroup resource group. Dopo aver caricato il gruppo di risorse, fare clic su **Elimina gruppo** di risorse per rimuovere il gruppo di risorse e le risorse archiviate in tale gruppo.

## <a name="next-steps"></a>Passaggi successivi

Sebbene brevemente, questo articolo ha illustrato diverse risorse di rete virtuale e funzionalità. La documentazione di Rete virtuale di Azure descrive in modo esteso gli argomenti seguenti:

* [Rete virtuale](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [Subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [Endpoint di servizioService endpoints](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png
[acr-vnet-portal]: ./media/container-registry-vnet/acr-vnet-portal.png
[acr-vnet-firewall-portal]: ./media/container-registry-vnet/acr-vnet-firewall-portal.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
