---
title: Limitazione dell'accesso con una rete virtuale
description: Consentire l'accesso a un registro contenitori di Azure solo da risorse in una rete virtuale di Azure o da intervalli di indirizzi IP pubblici.
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: a6b89b074c25ea0948597ede7e5681b100c7f429
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454338"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Limitare l'accesso a un registro contenitori di Azure usando una rete virtuale di Azure o regole del firewall

[Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) offre reti private e sicure per le risorse di Azure e locali. Limitando l'accesso al registro contenitori di Azure privato da una rete virtuale di Azure, è possibile garantire che solo le risorse nella rete virtuale accedano al registro di sistema. Per gli scenari cross-premise, è anche possibile configurare le regole del firewall per consentire l'accesso al registro di sistema solo da indirizzi IP specifici.

Questo articolo illustra due scenari per configurare le regole di accesso alla rete in ingresso in un registro contenitori: da una macchina virtuale distribuita in una rete virtuale o da un indirizzo IP pubblico della macchina virtuale.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima e [si applicano alcune limitazioni](#preview-limitations). Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.
>

Se invece è necessario configurare regole di accesso per le risorse per raggiungere un registro contenitori da dietro un firewall, vedere [configurare le regole per accedere a un registro contenitori di Azure dietro un firewall](container-registry-firewall-access-rules.md).


## <a name="preview-limitations"></a>Limiti di anteprima

* Solo un registro contenitori **Premium** può essere configurato con regole di accesso alla rete. Per informazioni sui livelli di servizio del registro di sistema, vedere [sku container Registry di Azure](container-registry-skus.md). 

* Solo un cluster di [Servizi Kubernetes di Azure](../aks/intro-kubernetes.md) o una [macchina virtuale](../virtual-machines/linux/overview.md) di Azure può essere usato come host per accedere a un registro contenitori in una rete virtuale. *Altri servizi di Azure, incluse le istanze di contenitore di Azure, non sono attualmente supportati.*

* Le operazioni delle [attività ACR](container-registry-tasks-overview.md) non sono attualmente supportate in un registro contenitori a cui si accede in una rete virtuale.

* Ogni registro supporta un massimo di 100 regole della rete virtuale.

## <a name="prerequisites"></a>prerequisiti

* Per usare i passaggi dell'interfaccia della riga di comando di Azure in questo articolo, è necessaria la versione 2.0.58 o successiva di Azure CLI. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

* Se non si dispone già di un registro contenitori, crearne uno (SKU Premium obbligatorio) ed effettuare il push di un'immagine di esempio, ad esempio `hello-world` dall'hub docker. Ad esempio, usare l' [portale di Azure][quickstart-portal] o l' [interfaccia][quickstart-cli] della riga di comando di Azure per creare un registro. 

* Se si vuole limitare l'accesso al registro di sistema usando una rete virtuale in un'altra sottoscrizione di Azure, è necessario registrare il provider di risorse per Container Registry di Azure nella sottoscrizione. Ad esempio:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>Informazioni sulle regole di rete per un registro contenitori

Per impostazione predefinita, un registro contenitori di Azure accetta connessioni su Internet dagli host in qualsiasi rete. Con una rete virtuale, è possibile consentire solo a risorse di Azure, ad esempio un cluster AKS o una macchina virtuale di Azure, di accedere in modo sicuro al registro di sistema, senza superare un limite di rete. È anche possibile configurare le regole del firewall di rete per consentire solo specifici intervalli di indirizzi IP Internet pubblici. 

Per limitare l'accesso a un registro, modificare prima l'azione predefinita del registro di sistema in modo da negare tutte le connessioni di rete. Quindi, aggiungere le regole di accesso alla rete. I client che hanno concesso l'accesso tramite le regole di rete devono continuare a eseguire l' [autenticazione nel registro contenitori](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) ed essere autorizzati ad accedere ai dati.

### <a name="service-endpoint-for-subnets"></a>Endpoint servizio per le subnet

Per consentire l'accesso da una subnet in una rete virtuale, è necessario aggiungere un [endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md) per il servizio container Registry di Azure. 

I servizi multi-tenant, come Azure Container Registry, usano un unico set di indirizzi IP per tutti i clienti. Un endpoint di servizio assegna un endpoint per accedere a un registro. Questo endpoint fornisce al traffico una route ottimale alla risorsa tramite la rete backbone di Azure. Con ogni richiesta vengono anche trasmesse le identità della rete virtuale e della subnet.

### <a name="firewall-rules"></a>Regole del firewall

Per le regole di rete IP, specificare gli intervalli di indirizzi Internet consentiti usando la notazione CIDR, ad esempio *16.17.18.0/24* o un singolo indirizzo IP come *16.17.18.19*. Le regole di rete IP sono consentite solo per indirizzi IP Internet *pubblici* . Gli intervalli di indirizzi IP riservati per le reti private (come definito nella RFC 1918) non sono consentiti nelle regole IP.

## <a name="create-a-docker-enabled-virtual-machine"></a>Creare una macchina virtuale abilitata per Docker

Per questo articolo, usare una VM Ubuntu abilitata per Docker per accedere a un registro contenitori di Azure. Per usare l'autenticazione Azure Active Directory per il registro di sistema, installare anche l'interfaccia della riga di comando di [Azure][azure-cli] nella macchina virtuale. Se si dispone già di una macchina virtuale di Azure, ignorare questo passaggio di creazione.

È possibile usare lo stesso gruppo di risorse per la macchina virtuale e il registro contenitori. Questa configurazione semplifica la pulizia alla fine, ma non è obbligatoria. Se si sceglie di creare un gruppo di risorse separato per la macchina virtuale e la rete virtuale, eseguire il comando [AZ Group create][az-group-create]. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *westcentralus* :

```azurecli
az group create --name myResourceGroup --location westus
```

Distribuire ora una macchina virtuale di Azure Ubuntu predefinita con [AZ VM create][az-vm-create]. L'esempio seguente crea una macchina virtuale denominata *myDockerVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Per creare la macchina virtuale sono necessari alcuni minuti. Dopo aver eseguito il comando, prendere nota del `publicIpAddress` visualizzato dall'interfaccia della riga di comando di Azure. Usare questo indirizzo per stabilire connessioni SSH alla macchina virtuale e, facoltativamente, per configurare le regole del firewall in un secondo momento.

### <a name="install-docker-on-the-vm"></a>Installare Docker nella macchina virtuale

Una volta che la macchina virtuale è in esecuzione, stabilire una connessione SSH alla macchina virtuale. Sostituire *publicIpAddress* con l'indirizzo IP pubblico della macchina virtuale.

```bash
ssh azureuser@publicIpAddress
```

Eseguire il comando seguente per installare Docker nella VM Ubuntu:

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

## <a name="allow-access-from-a-virtual-network"></a>Consentire l'accesso da una rete virtuale

In questa sezione configurare il registro contenitori per consentire l'accesso da una subnet in una rete virtuale di Azure. Vengono forniti i passaggi equivalenti usando l'interfaccia della riga di comando di Azure e portale di Azure.

### <a name="allow-access-from-a-virtual-network---cli"></a>Consentire l'accesso da una rete virtuale-interfaccia della riga di comando

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Aggiungere un endpoint di servizio a una subnet

Quando si crea una VM, per impostazione predefinita Azure crea una rete virtuale nello stesso gruppo di risorse. Il nome della rete virtuale si basa sul nome della macchina virtuale. Ad esempio, se si specifica il nome della macchina virtuale *myDockerVM*, il nome di rete virtuale predefinito è *myDockerVMVNET*, con una subnet denominata *myDockerVMSubnet*. Verificare questa impostazione nel portale di Azure o usando il comando [AZ Network VNET list][az-network-vnet-list] :

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

Usare il comando [AZ Network VNET subnet Update][az-network-vnet-subnet-update] per aggiungere un endpoint di servizio **Microsoft. ContainerRegistry** alla subnet. Sostituire i nomi della rete virtuale e della subnet con il comando seguente:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Usare il comando [AZ Network VNET subnet Show][az-network-vnet-subnet-show] per recuperare l'ID risorsa della subnet. Questa operazione è necessaria in un passaggio successivo per configurare una regola di accesso alla rete.

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

#### <a name="change-default-network-access-to-registry"></a>Modificare l'accesso di rete predefinito al registro di sistema

Per impostazione predefinita, un registro contenitori di Azure consente le connessioni da host in qualsiasi rete. Per limitare l'accesso a una rete selezionata, modificare l'azione predefinita per negare l'accesso. Sostituire il nome del registro di sistema con il comando [AZ ACR Update][az-acr-update] seguente:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Aggiungi regola di rete al registro di sistema

Usare il comando [AZ ACR Network-rule add][az-acr-network-rule-add] per aggiungere una regola di rete al registro di sistema che consente l'accesso dalla subnet della VM. Sostituire il nome del registro contenitori e l'ID risorsa della subnet nel comando seguente: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Continuare a [verificare l'accesso al registro di sistema](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Consentire l'accesso da una rete virtuale-portale

#### <a name="add-service-endpoint-to-subnet"></a>Aggiungi endpoint servizio alla subnet

Quando si crea una VM, per impostazione predefinita Azure crea una rete virtuale nello stesso gruppo di risorse. Il nome della rete virtuale si basa sul nome della macchina virtuale. Ad esempio, se si specifica il nome della macchina virtuale *myDockerVM*, il nome di rete virtuale predefinito è *myDockerVMVNET*, con una subnet denominata *myDockerVMSubnet*.

Per aggiungere un endpoint di servizio per Container Registry di Azure a una subnet:

1. Nella casella di ricerca nella parte superiore del [portale di Azure][azure-portal]immettere *reti virtuali*. Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
1. Dall'elenco di reti virtuali selezionare la rete virtuale in cui è distribuita la macchina virtuale, ad esempio *myDockerVMVNET*.
1. In **Impostazioni**selezionare **Subnet**.
1. Selezionare la subnet in cui è distribuita la macchina virtuale, ad esempio *myDockerVMSubnet*.
1. In **endpoint servizio**selezionare **Microsoft. ContainerRegistry**.
1. Selezionare **Salva**.

![Aggiungi endpoint servizio alla subnet][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Configurare l'accesso alla rete per il registro di sistema

Per impostazione predefinita, un registro contenitori di Azure consente le connessioni da host in qualsiasi rete. Per limitare l'accesso alla rete virtuale:

1. Nel portale passare al registro contenitori.
1. In **Impostazioni**selezionare **firewall e reti virtuali**.
1. Per negare l'accesso per impostazione predefinita, scegliere di consentire l'accesso da **Reti selezionate**. 
1. Selezionare **Aggiungi rete virtuale esistente**e selezionare la rete virtuale e la subnet configurate con un endpoint del servizio. Selezionare **Aggiungi**.
1. Selezionare **Salva**.

![Configurare la rete virtuale per il registro contenitori][acr-vnet-portal]

Continuare a [verificare l'accesso al registro di sistema](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Consenti accesso da un indirizzo IP

In questa sezione configurare il registro contenitori per consentire l'accesso da un intervallo o un indirizzo IP specifico. Vengono forniti i passaggi equivalenti usando l'interfaccia della riga di comando di Azure e portale di Azure.

### <a name="allow-access-from-an-ip-address---cli"></a>Consentire l'accesso da un indirizzo IP-CLI

#### <a name="change-default-network-access-to-registry"></a>Modificare l'accesso di rete predefinito al registro di sistema

Se non è già stato fatto, aggiornare la configurazione del registro di sistema per negare l'accesso per impostazione predefinita. Sostituire il nome del registro di sistema con il comando [AZ ACR Update][az-acr-update] seguente:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Rimuovere la regola di rete dal registro di sistema

Se in precedenza è stata aggiunta una regola di rete per consentire l'accesso dalla subnet della VM, rimuovere l'endpoint del servizio e la regola di rete della subnet. Sostituire il nome del registro contenitori e l'ID risorsa della subnet recuperata in un passaggio precedente del comando [AZ ACR Network-Rule Remove][az-acr-network-rule-remove] : 

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

#### <a name="add-network-rule-to-registry"></a>Aggiungi regola di rete al registro di sistema

Usare il comando [AZ ACR Network-rule add][az-acr-network-rule-add] per aggiungere una regola di rete al registro di sistema che consenta l'accesso dall'indirizzo IP della macchina virtuale. Sostituire il nome del registro contenitori e l'indirizzo IP pubblico della macchina virtuale con il comando seguente.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Continuare a [verificare l'accesso al registro di sistema](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Consentire l'accesso da un indirizzo IP-portale

#### <a name="remove-existing-network-rule-from-registry"></a>Rimuovi regola di rete esistente dal registro di sistema

Se in precedenza è stata aggiunta una regola di rete per consentire l'accesso dalla subnet della VM, rimuovere la regola esistente. Ignorare questa sezione se si vuole accedere al registro di sistema da una macchina virtuale diversa.

* Aggiornare le impostazioni della subnet per rimuovere l'endpoint del servizio della subnet per Azure Container Registry. 

  1. Nella [portale di Azure][azure-portal]passare alla rete virtuale in cui è distribuita la macchina virtuale.
  1. In **Impostazioni**selezionare **Subnet**.
  1. Selezionare la subnet in cui è distribuita la macchina virtuale.
  1. In **endpoint servizio**rimuovere la casella di controllo per **Microsoft. ContainerRegistry**. 
  1. Selezionare **Salva**.

* Rimuovere la regola di rete che consente alla subnet di accedere al registro di sistema.

  1. Nel portale passare al registro contenitori.
  1. In **Impostazioni**selezionare **firewall e reti virtuali**.
  1. In **reti virtuali**selezionare il nome della rete virtuale e quindi fare clic su **Rimuovi**.
  1. Selezionare **Salva**.

#### <a name="add-network-rule-to-registry"></a>Aggiungi regola di rete al registro di sistema

1. Nel portale passare al registro contenitori.
1. In **Impostazioni**selezionare **firewall e reti virtuali**.
1. Se non è già stato fatto, scegliere di consentire l'accesso dalle **reti selezionate**. 
1. In **reti virtuali**assicurarsi che non sia selezionata alcuna rete.
1. In **Firewall**immettere l'indirizzo IP pubblico di una macchina virtuale. In alternativa, immettere un intervallo di indirizzi nella notazione CIDR che contiene l'indirizzo IP della macchina virtuale.
1. Selezionare **Salva**.

![Configurare la regola del firewall per il registro contenitori][acr-vnet-firewall-portal]

Continuare a [verificare l'accesso al registro di sistema](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Verificare l'accesso al registro di sistema

Dopo alcuni minuti di attesa per l'aggiornamento della configurazione, verificare che la macchina virtuale possa accedere al registro contenitori. Creare una connessione SSH alla macchina virtuale ed eseguire il comando [AZ ACR login][az-acr-login] per accedere al registro. 

```bash
az acr login --name mycontainerregistry
```

È possibile eseguire operazioni del registro di sistema, ad esempio Esegui `docker pull` per eseguire il pull di un'immagine di esempio dal registro di sistema. Sostituire un'immagine e un valore di tag appropriati per il registro di sistema, preceduto dal nome del server di accesso del registro di sistema (tutti minuscoli):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker esegue correttamente il pull dell'immagine nella macchina virtuale.

Questo esempio dimostra che è possibile accedere al registro contenitori privato tramite la regola di accesso alla rete. Non è tuttavia possibile accedere al registro di sistema da un host di accesso diverso in cui non è configurata una regola di accesso alla rete. Se si tenta di eseguire l'accesso da un altro host usando il comando `az acr login` o `docker login`, l'output sarà simile al seguente:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Ripristinare l'accesso predefinito al registro di sistema

Per ripristinare il registro di sistema in modo da consentire l'accesso per impostazione predefinita, rimuovere tutte le regole di rete configurate. Impostare quindi l'azione predefinita per consentire l'accesso. Vengono forniti i passaggi equivalenti usando l'interfaccia della riga di comando di Azure e portale di Azure.

### <a name="restore-default-registry-access---cli"></a>Ripristinare l'accesso predefinito al registro di sistema-CLI

#### <a name="remove-network-rules"></a>Rimuovere le regole di rete

Per visualizzare un elenco di regole di rete configurate per il registro, eseguire il comando [AZ ACR Network-Rule list][az-acr-network-rule-list] seguente:

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Per ogni regola configurata, eseguire il comando [AZ ACR Network-Rule Remove][az-acr-network-rule-remove] per rimuoverlo. Ad esempio:

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

Sostituire il nome del registro di sistema con il comando [AZ ACR Update][az-acr-update] seguente:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Ripristinare l'accesso predefinito al registro di sistema-portale


1. Nel portale passare al registro contenitori e selezionare **firewall e reti virtuali**.
1. In **reti virtuali**selezionare ogni rete virtuale e quindi fare clic su **Rimuovi**.
1. In **Firewall**selezionare ogni intervallo di indirizzi, quindi selezionare l'icona Elimina.
1. In **Consenti accesso da**selezionare **tutte le reti**. 
1. Selezionare **Salva**.

## <a name="clean-up-resources"></a>Pulire le risorse

Se sono state create tutte le risorse di Azure nello stesso gruppo di risorse e non sono più necessarie, è possibile eliminare facoltativamente le risorse usando un singolo comando [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name myResourceGroup
```

Per pulire le risorse nel portale, passare al gruppo di risorse myResourceGroup. Una volta caricato il gruppo di risorse, fare clic su **Elimina gruppo di risorse** per rimuovere il gruppo di risorse e le risorse archiviate.

## <a name="next-steps"></a>Passaggi successivi

Sebbene brevemente, questo articolo ha illustrato diverse risorse di rete virtuale e funzionalità. La documentazione di Rete virtuale di Azure descrive in modo esteso gli argomenti seguenti:

* [Rete virtuale](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [Subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [Endpoint servizio](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

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
