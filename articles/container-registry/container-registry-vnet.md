---
title: Distribuire un registro contenitori di Azure in una rete virtuale
description: Consentire l'accesso a un'istanza di registro contenitori di Azure solo dalle risorse in una rete virtuale di Azure o da intervalli di indirizzi IP pubblici.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/14/2019
ms.author: danlep
ms.openlocfilehash: 044ff823e34f51257cb6186417535b9b542bc289
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136988"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Limitare l'accesso a un registro contenitori di Azure usando una rete virtuale di Azure o le regole del firewall

[Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) fornisce funzionalità di rete sicura e privata per Azure e alle risorse locali. Distribuendo il registro contenitori privato di Azure in una rete virtuale di Azure, è possibile garantire che solo le risorse nella rete virtuale di accedere Registro di sistema. Per gli scenari cross-premise, è anche possibile configurare le regole del firewall per consentire l'accesso del Registro di sistema solo da indirizzi IP specifici.

Questo articolo illustra due scenari per creare regole di accesso alla rete per limitare l'accesso a un'istanza di registro contenitori di Azure: da una macchina virtuale distribuita nella stessa rete o da indirizzo IP pubblico della macchina virtuale.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima e [si applicano alcune limitazioni](#preview-limitations). Le anteprime vengono rese disponibili a condizione che l'utente accetti le [condizioni supplementari per l'utilizzo][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.
>

## <a name="preview-limitations"></a>Limiti di anteprima

* Solo un **Premium** registro contenitori può essere configurato con regole di accesso di rete. Per informazioni sui livelli di servizio del Registro di sistema, vedere [SKU del registro contenitori di Azure](container-registry-skus.md). 

* Solo un' [Azure Kubernetes Service](../aks/intro-kubernetes.md) cluster o in Azure [macchina virtuale](../virtual-machines/linux/overview.md) utilizzabile come host per accedere a un registro contenitori in una rete virtuale. *Altri servizi di Azure incluse le istanze di contenitore di Azure non sono attualmente supportati.*

* Ogni registro di sistema supporta un massimo di 100 regole della rete virtuale.

## <a name="prerequisites"></a>Prerequisiti

* Usare Azure CLI i passaggi in questo articolo, il comando di Azure versione 2.0.58 o versione successiva è richiesto. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

* Se si ha già un registro contenitori, crearne uno (Premium SKU necessarie) e push, ad esempio un'immagine di esempio `hello-world` dall'Hub Docker. Ad esempio, usare il [portale di Azure] [ quickstart-portal] o la [Azure CLI] [ quickstart-cli] per creare un registro di sistema. 

## <a name="about-network-rules-for-a-container-registry"></a>Informazioni sulle regole di rete per un registro contenitori

Un registro contenitori di Azure per impostazione predefinita accetta le connessioni su internet da host in qualsiasi rete. Con una rete virtuale, è possibile consentire solo le risorse di Azure, ad esempio un cluster del servizio contenitore di AZURE o macchine Virtuali di Azure di accedere in modo sicuro al registro, senza attraversare un limite di rete. È anche possibile configurare regole firewall di rete all'elenco elementi consentiti rete internet pubblica specifici intervalli di indirizzi IP. 

Per limitare l'accesso a un registro di sistema, prima di tutto modificare l'azione predefinita del Registro di sistema in modo che lo nega tutte le connessioni di rete. Aggiungere quindi le regole di accesso di rete. I client concesso l'accesso tramite le regole di rete deve continuare a [l'autenticazione al registro contenitori di](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) ed essere autorizzato ad accedere ai dati.

### <a name="service-endpoint-for-subnets"></a>Endpoint del servizio per le subnet

Per consentire l'accesso da una subnet in una rete virtuale, è necessario aggiungere un [endpoint del servizio](../virtual-network/virtual-network-service-endpoints-overview.md) per il servizio Registro contenitori di Azure. 

Servizi multi-tenant, come registro contenitori di Azure, usano un singolo set di indirizzi IP per tutti i clienti. Un endpoint di servizio assegna un endpoint per accedere a un registro di sistema. Questo endpoint consente il traffico di un itinerario ottimale alla risorsa attraverso la rete backbone di Azure. Con ogni richiesta vengono anche trasmesse le identità della rete virtuale e della subnet.

### <a name="firewall-rules"></a>Regole del firewall

Per le regole di rete IP, specificare internet consentiti gli intervalli di indirizzi usando la notazione CIDR, ad esempio *16.17.18.0/24* o i singoli indirizzi IP, ad esempio *16.17.18.19*. Regole di rete IP sono consentite solo per *pubblica* gli indirizzi IP internet. Gli intervalli di indirizzi IP riservati per le reti private (come definito in RFC 1918) non sono consentiti nelle regole IP.

## <a name="create-a-docker-enabled-virtual-machine"></a>Creare una macchina virtuale abilitata per Docker

Per questo articolo, usare una VM Ubuntu abilitata per Docker incorporata per accedere registro contenitori di Azure. Per usare l'autenticazione di Azure Active Directory per il Registro di sistema, installare anche il [Azure CLI] [ azure-cli] nella macchina virtuale. Se si dispone già di una macchina virtuale di Azure, ignorare questo passaggio della creazione.

È possibile utilizzare stesso gruppo di risorse per la macchina virtuale e il registro contenitori. Questa configurazione semplifica la pulizia al termine, ma non è obbligatorio. Se si sceglie di creare un gruppo di risorse distinto per la macchina virtuale e rete virtuale, eseguire [creare il gruppo di az][az-group-create]. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nel *westcentralus* percorso:

```azurecli
az group create --name myResourceGroup --location westus
```

Distribuire ora un valore predefinito la macchina virtuale con Azure per Ubuntu [az vm create][az-vm-create]. L'esempio seguente crea una macchina virtuale denominata *myDockerVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Per creare la macchina virtuale sono necessari alcuni minuti. Dopo aver eseguito il comando, prendere nota del `publicIpAddress` visualizzato dall'interfaccia della riga di comando di Azure. Usare questo indirizzo per stabilire connessioni SSH alla macchina virtuale e, facoltativamente, per l'installazione di versioni successive delle regole del firewall.

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

Seguire i passaggi descritti in [Installare l'interfaccia della riga di comando di Azure con APT](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) per installare l'interfaccia della riga di comando di Azure nella macchina virtuale Ubuntu. Per questo articolo, assicurarsi di installare versione 2.0.58 o versione successiva.

Chiudere la connessione SSH.

## <a name="allow-access-from-a-virtual-network"></a>Consentire l'accesso da una rete virtuale

In questa sezione, configurare il Registro di contenitori per consentire l'accesso da una subnet in una rete virtuale di Azure. Vengono forniti passaggi equivalenti usando il comando di Azure e il portale di Azure.

### <a name="allow-access-from-a-virtual-network---cli"></a>Consentire l'accesso da una rete virtuale - CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Aggiungere un endpoint del servizio a una subnet

Quando si crea una macchina virtuale, per impostazione predefinita viene creata una rete virtuale nello stesso gruppo di risorse. Il nome della rete virtuale è basato sul nome della macchina virtuale. Ad esempio, se si assegna un nome della macchina virtuale *myDockerVM*, il nome di rete virtuale predefinito è *myDockerVMVNET*, con una subnet denominata *myDockerVMSubnet*. Effettuare questa verifica nel portale di Azure o tramite il [elenco di az network vnet] [ az-network-vnet-list] comando:

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

Usare la [aggiornamento di az network vnet subnet] [ az-network-vnet-subnet-update] comando per aggiungere un **Microsoft. containerregistry** endpoint del servizio alla subnet. Sostituire i nomi della rete virtuale e subnet nel comando seguente:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Usare la [show di az network vnet subnet] [ az-network-vnet-subnet-show] comando per recuperare l'ID risorsa della subnet. Necessaria in un passaggio successivo per configurare una regola di accesso di rete.

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

#### <a name="change-default-network-access-to-registry"></a>Modificare l'accesso di rete predefinito al Registro di sistema

Per impostazione predefinita, un registro contenitori di Azure consente connessioni dall'host in qualsiasi rete. Per limitare l'accesso a una rete selezionata, modificare l'azione predefinita per negare l'accesso. Sostituire il nome del Registro di sistema nel seguente [aggiornamento di az acr] [ az-acr-update] comando:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Aggiungere regola di rete al Registro di sistema

Usare la [Aggiungi regola di rete di az acr] [ az-acr-network-rule-add] comando per aggiungere una regola di rete nel Registro di sistema che consenta l'accesso dalla subnet della macchina virtuale. Sostituire il nome del registro contenitori e l'ID risorsa della subnet nel comando seguente: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Continuare a [verificare l'accesso al Registro di sistema](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Consentire l'accesso da una rete virtuale - portale

#### <a name="add-service-endpoint-to-subnet"></a>Aggiungere endpoint del servizio alla subnet

Quando si crea una macchina virtuale, per impostazione predefinita viene creata una rete virtuale nello stesso gruppo di risorse. Il nome della rete virtuale è basato sul nome della macchina virtuale. Ad esempio, se si assegna un nome della macchina virtuale *myDockerVM*, il nome di rete virtuale predefinito è *myDockerVMVNET*, con una subnet denominata *myDockerVMSubnet*.

Per aggiungere un endpoint del servizio per registro contenitori di Azure a una subnet:

1. Nella casella di ricerca in cima il [portale di Azure][azure-portal], immettere *reti virtuali*. Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca.
1. Nell'elenco delle reti virtuali, selezionare la rete virtuale in cui la macchina virtuale viene distribuita, ad esempio *myDockerVMVNET*.
1. Sotto **le impostazioni**, selezionare **subnet**.
1. Selezionare la subnet in cui la macchina virtuale viene distribuita, ad esempio *myDockerVMSubnet*.
1. Sotto **endpoint di servizio**, selezionare **Microsoft. containerregistry**.
1. Selezionare **Salva**.

![Aggiungere endpoint del servizio alla subnet][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Configurare l'accesso alla rete per il registro

Per impostazione predefinita, un registro contenitori di Azure consente connessioni dall'host in qualsiasi rete. Per limitare l'accesso alla rete virtuale:

1. Nel portale, passare al registro contenitori.
1. Sotto **le impostazioni**, selezionare **Firewall e reti virtuali**.
1. Per negare l'accesso per impostazione predefinita, scegliere di consentire l'accesso da **Reti selezionate**. 
1. Selezionare **Aggiungi rete virtuale esistente**e selezionare la rete virtuale e la subnet è stata configurata con un endpoint del servizio. Selezionare **Aggiungi**.
1. Selezionare **Salva**.

![Configurare la rete virtuale per il registro contenitori][acr-vnet-portal]

Continuare a [verificare l'accesso al Registro di sistema](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Consentire l'accesso da un indirizzo IP

In questa sezione, configurare il Registro di contenitori per consentire l'accesso da una subnet in una rete virtuale di Azure. Vengono forniti passaggi equivalenti usando il comando di Azure e il portale di Azure.

### <a name="allow-access-from-an-ip-address---cli"></a>Consentire l'accesso da un indirizzo IP - CLI

#### <a name="change-default-network-access-to-registry"></a>Modificare l'accesso di rete predefinito al Registro di sistema

Se non già stato fatto, aggiornare la configurazione del Registro di sistema per negare l'accesso per impostazione predefinita. Sostituire il nome del Registro di sistema nel seguente [aggiornamento di az acr] [ az-acr-update] comando:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Rimuovi regola di rete dal Registro di sistema

Se è già presente una regola di rete per consentire l'accesso dalla subnet della macchina virtuale, rimuovere l'endpoint del servizio della subnet e la regola della rete. Sostituire il nome del registro contenitori e l'ID risorsa della subnet è recuperato nel passaggio precedente nel [rimuovere az acr network-rule] [ az-acr-network-rule-remove] comando: 

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

#### <a name="add-network-rule-to-registry"></a>Aggiungere regola di rete al Registro di sistema

Usare la [Aggiungi regola di rete di az acr] [ az-acr-network-rule-add] comando per aggiungere una regola di rete nel Registro di sistema che consenta l'accesso da indirizzo IP della macchina virtuale. Sostituire il nome del registro contenitori e l'indirizzo IP pubblico della macchina virtuale nel comando seguente.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Continuare a [verificare l'accesso al Registro di sistema](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Consentire l'accesso da un indirizzo IP - portale

#### <a name="remove-existing-network-rule-from-registry"></a>Rimuovi regola di rete esistente dal Registro di sistema

Se è già presente una regola di rete per consentire l'accesso dalla subnet della macchina virtuale, rimuovere la regola esistente. Se si desidera accedere Registro di sistema da una VM diversa, ignorare questa sezione.

* Aggiornare le impostazioni di subnet per rimuovere l'endpoint del servizio della subnet per registro contenitori di Azure. 

  1. Nel [portale di Azure][azure-portal], passare alla rete virtuale in cui è distribuita la macchina virtuale.
  1. Sotto **le impostazioni**, selezionare **subnet**.
  1. Selezionare la subnet in cui è distribuita la macchina virtuale.
  1. Sotto **endpoint di servizio**, rimuovere la casella di controllo **Microsoft. containerregistry**. 
  1. Selezionare **Salva**.

* Rimuovere la regola di rete che consente la subnet di accedere al registro.

  1. Nel portale, passare al registro contenitori.
  1. Sotto **le impostazioni**, selezionare **Firewall e reti virtuali**.
  1. Sotto **reti virtuali**, selezionare il nome della rete virtuale e quindi selezionare **rimuovere**.
  1. Selezionare **Salva**.

#### <a name="add-network-rule-to-registry"></a>Aggiungere regola di rete al Registro di sistema

1. Nel portale, passare al registro contenitori.
1. Sotto **le impostazioni**, selezionare **Firewall e reti virtuali**.
1. Se non già stato fatto, scegliere di consentire l'accesso da **reti selezionate**. 
1. Sotto **reti virtuali**, assicurarsi che non sia selezionata alcuna rete.
1. Sotto **Firewall**, immettere l'indirizzo IP pubblico di una macchina virtuale. In alternativa, immettere un intervallo di indirizzi nella notazione CIDR che contiene l'indirizzo IP della macchina virtuale.
1. Selezionare **Salva**.

![Configurare una regola del firewall per il registro contenitori][acr-vnet-firewall-portal]

Continuare a [verificare l'accesso al Registro di sistema](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Verificare l'accesso al Registro di sistema

Dopo aver atteso alcuni minuti aggiornare la configurazione, verificare che la macchina virtuale possa accedere registro contenitori. Stabilire una connessione SSH alla macchina virtuale ed eseguire la [account di accesso di az acr] [ az-acr-login] comando all'account di accesso al Registro di sistema. 

```bash
az acr login --name mycontainerregistry
```

È possibile eseguire operazioni del Registro di sistema, ad esempio eseguire `docker pull` effettuare il pull di un'immagine di esempio dal Registro di sistema. Sostituire con un valore di immagine e tag appropriato per il registro, con preceduto con il nome server di accesso del Registro di sistema (tutte lettere minuscolo):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker è stata effettua il pull dell'immagine alla macchina virtuale.

Questo esempio dimostra che è possibile accedere registro contenitori privato tramite la regola di accesso di rete. Tuttavia, il Registro di sistema non è accessibile da un host di account di accesso diverso che non dispone di una regola di accesso di rete configurata. Se si tenta di eseguire l'accesso da un altro host con il `az acr login` comando o `docker login` comando, l'output è simile al seguente:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Ripristinare l'accesso del Registro di sistema predefinito

Per ripristinare il Registro di sistema per consentire l'accesso per impostazione predefinita, rimuove le regole di rete configurate. Configurare quindi l'azione predefinita per consentire l'accesso. Vengono forniti passaggi equivalenti usando il comando di Azure e il portale di Azure.

### <a name="restore-default-registry-access---cli"></a>Ripristinare l'accesso del Registro di sistema predefinito - CLI

#### <a name="remove-network-rules"></a>Rimuovere le regole di rete

Per visualizzare un elenco di regole di rete configurate per il registro, eseguire il comando seguente [elenco di az acr network-rule] [ az-acr-network-rule-list] comando:

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Per ogni regola che è configurato, eseguire la [rimuovere az acr network-rule] [ az-acr-network-rule-remove] comando per rimuoverlo. Ad esempio: 

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

Sostituire il nome del Registro di sistema nel seguente [aggiornamento di az acr] [ az-acr-update] comando:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Ripristino del Registro di sistema accesso predefinito - portale


1. Nel portale, passare al registro contenitori e selezionare **Firewall e reti virtuali**.
1. Sotto **reti virtuali**, selezionare ogni rete virtuale e quindi selezionare **rimuovere**.
1. Sotto **Firewall**, selezionare ogni intervallo di indirizzi e quindi selezionare l'icona di eliminazione.
1. Sotto **consentire l'accesso da**, selezionare **tutte le reti**. 
1. Selezionare **Salva**.

## <a name="clean-up-resources"></a>Pulire le risorse

Se sono state create tutte le risorse di Azure nella stessa risorsa gruppo e non sono più necessari, è facoltativamente possibile eliminare le risorse tramite una singola [eliminazione gruppo az](/cli/azure/group#az_group_delete) comando:

```azurecli
az group delete --name myResourceGroup
```

Per pulire le risorse nel portale, passare al gruppo di risorse myResourceGroup. Dopo aver caricato il gruppo di risorse, fare clic su **Elimina gruppo di risorse** per rimuovere il gruppo di risorse e le risorse archiviate vengono eliminati.

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
[az-group-create]: /cli/azure/group#az_group_create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com