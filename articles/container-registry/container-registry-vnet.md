---
title: Limitare l'accesso con un endpoint di servizio
description: Limitare l'accesso a un registro contenitori di Azure usando un endpoint di servizio in una rete virtuale di Azure. L'accesso agli endpoint di servizio è una funzionalità del livello di servizio Premium.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 1fc8d54d677112a9c934f9079e953a7389939bde
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89488669"
---
# <a name="restrict-access-to-a-container-registry-using-a-service-endpoint-in-an-azure-virtual-network"></a>Limitare l'accesso a un registro contenitori usando un endpoint di servizio in una rete virtuale di Azure

In [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) sono disponibili funzionalità per implementare una rete sicura e privata per le risorse locali e di Azure. Un [endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md) consente di proteggere l'indirizzo IP pubblico del registro contenitori limitandolo alla sola rete virtuale. Questo endpoint indirizza il traffico in maniera ottimale alla risorsa sulla rete backbone di Azure. Con ogni richiesta vengono anche trasmesse le identità della rete virtuale e della subnet.

Questo articolo illustra come configurare un endpoint di servizio del registro contenitori (anteprima) in una rete virtuale. 

> [!IMPORTANT]
> Registro Azure Container supporta ora il [collegamento privato di Azure](container-registry-private-link.md), consentendo l'inserimento di endpoint privati da una rete virtuale in un registro. Gli endpoint privati sono accessibili dall'interno della rete virtuale, mediante indirizzi IP privati. Nella maggior parte degli scenari di rete è consigliabile usare endpoint privati anziché endpoint di servizio.

La configurazione di un endpoint di servizio del registro è disponibile nel livello di servizio del registro contenitori **Premium**. Per informazioni sui livelli di servizio e sui limiti del registro contenitori, vedere [Livelli di servizio del Registro Azure Container](container-registry-skus.md).

## <a name="preview-limitations"></a>Limiti di anteprima

* Lo sviluppo futuro di endpoint di servizio per Registro Azure Container non è attualmente pianificato. Si consiglia invece di usare [endpoint privati](container-registry-private-link.md).
* Non è possibile usare il portale di Azure per configurare endpoint di servizio in un registro.
* Per accedere a un registro contenitori tramite un endpoint di servizio, è possibile usare solo un cluster del [servizio Azure Kubernetes](../aks/intro-kubernetes.md) o una [macchina virtuale](../virtual-machines/linux/overview.md) di Azure. *Altri servizi di Azure, incluse le istanze di Container di Azure, non sono supportati.*
* Ogni registro supporta un massimo di 100 regole di accesso alla rete.
* Gli endpoint di servizio per Azure Container Registry non sono supportati nel Cloud Azure per enti pubblici degli Stati Uniti o nel cloud di Azure China.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Prerequisiti

* Per usare i passaggi dell'interfaccia della riga di comando di Azure in questo articolo, è necessaria la versione 2.0.58 o successiva dell'interfaccia della riga di comando di Azure. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

* Se non si dispone già di un registro contenitori, crearne uno (livello Premium obbligatorio) ed effettuare il push di un'immagine di esempio, come `hello-world`, da Docker Hub. Ad esempio, per creare un registro usare il [portale di Azure][quickstart-portal] oppure l'[interfaccia della riga di comando di Azure][quickstart-cli]. 

* Per limitare l'accesso al registro usando un endpoint di servizio in un'altra sottoscrizione di Azure, registrare il provider di risorse per Registro Azure Container nella sottoscrizione. Ad esempio:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="configure-network-access-for-registry"></a>Configurare l'accesso alla rete per il registro

In questa sezione, si vedrà come configurare il registro contenitori per consentire l'accesso da una subnet in una rete virtuale di Azure. Vengono forniti i passaggi equivalenti usando l'interfaccia della riga di comando di Azure e il portale di Azure.

### <a name="allow-access-from-a-virtual-network---cli"></a>Concedere l'accesso da una rete virtuale - Interfaccia della riga di comando

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Aggiungere un endpoint di servizio a una subnet

Quando si crea una macchina virtuale, per impostazione predefinita Azure crea una rete virtuale nello stesso gruppo di risorse. Il nome della rete virtuale si basa sul nome della macchina virtuale. Ad esempio, se si rinomina la macchina virtuale *myDockerVM*, il nome della rete virtuale predefinito sarà *myDockerVMVNET*, con una subnet denominata *myDockerVMSubnet*. Verificare questa impostazione nel portale di Azure o usando il comando [az network vnet list][az-network-vnet-list]:

```azurecli
az network vnet list \
  --resource-group myResourceGroup \
  --query "[].{Name: name, Subnet: subnets[0].name}"
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

Usare il comando [az network vnet subnet update][az-network-vnet-subnet-update] per aggiungere un endpoint di servizio **Microsoft.ContainerRegistry** alla subnet. Sostituire i nomi della rete virtuale e della subnet nel comando seguente:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Usare il comando [az network vnet subnet show][az-network-vnet-subnet-show] per recuperare l'ID risorsa della subnet. Questo sarà necessario in un passaggio successivo per configurare una regola di accesso alla rete.

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

#### <a name="change-default-network-access-to-registry"></a>Modificare la regola predefinita di accesso al registro

Per impostazione predefinita, un registro contenitori di Azure consente le connessioni da host in qualsiasi rete. Per limitare l'accesso a una rete selezionata, modificare l'azione predefinita per negare l'accesso. Sostituire il nome del registro con nel seguente comando [az acr update][az-acr-update]:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Aggiungere una regola di rete al registro

Usare il comando [az acr network-rule add][az-acr-network-rule-add] per aggiungere una regola di rete al registro che consenta l'accesso dalla subnet della macchina virtuale. Sostituire il nome del registro contenitori e l'ID della risorsa della subnet nel comando seguente: 

 ```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --subnet <subnet-resource-id>
```

## <a name="verify-access-to-the-registry"></a>Verificare l'accesso al registro

Dopo alcuni minuti di attesa per l'aggiornamento della configurazione, verificare che la macchina virtuale sia in grado di accedere al registro contenitori. Creare una connessione SSH alla macchina virtuale ed eseguire il comando [az acr login][az-acr-login] per accedere al registro. 

```bash
az acr login --name mycontainerregistry
```

È possibile eseguire operazioni del registro, ad esempio eseguire `docker pull` per eseguire il pull di un'immagine di esempio dal registro. Sostituire un'immagine e un valore di tag appropriati per il registro, preceduto dal nome del server di accesso del registro di sistema (tutto minuscolo):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker esegue correttamente il pull dell'immagine nella macchina virtuale.

Questo esempio dimostra che è possibile accedere al registro contenitori privato tramite la regola di accesso alla rete. Non è tuttavia possibile accedere al registro di sistema da un host di accesso in cui non è configurata una regola di accesso alla rete. Se si tenta di eseguire l'accesso da un altro host usando il comando `az acr login` o `docker login`, l'output sarà simile al seguente:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Ripristinare l'accesso predefinito al registro

Per ripristinare il registro in modo da consentire l'accesso per impostazione predefinita, rimuovere tutte le regole di rete configurate. Impostare quindi l'azione predefinita per consentire l'accesso. Vengono forniti i passaggi equivalenti usando l'interfaccia della riga di comando di Azure e il portale di Azure.

### <a name="restore-default-registry-access---cli"></a>Ripristinare l'accesso predefinito al registro - interfaccia della riga di comando

#### <a name="remove-network-rules"></a>Rimuovere regole di rete

Per visualizzare un elenco di regole di rete configurate per il registro, eseguire il seguente comando [az acr network-rule list][az-acr-network-rule-list]:

```azurecli
az acr network-rule list --name mycontainerregistry 
```

Per ogni regola configurata, eseguire il comando [az acr network-rule remove][az-acr-network-rule-remove] per rimuoverla. Ad esempio:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="allow-access"></a>Consentire l'accesso

Sostituire il nome del registro con nel seguente comando [az acr update][az-acr-update]:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se tutte le risorse di Azure sono state create nello stesso gruppo di risorse e non sono più necessarie, è possibile eliminare facoltativamente le risorse usando un singolo comando [az group delete](/cli/azure/group):

```azurecli
az group delete --name myResourceGroup
```

Per pulire le risorse nel portale, passare al gruppo di risorse myResourceGroup. Dopo aver caricato il gruppo di risorse, fare clic su **Elimina gruppo di risorse** per rimuovere il gruppo di risorse, il registro contenitori e le immagini del contenitore archiviate nel registro.

## <a name="next-steps"></a>Passaggi successivi

* Per limitare l'accesso a un registro usando un endpoint privato in una rete virtuale, vedere [Configurare un collegamento privato di Azure per un registro contenitori di Azure](container-registry-private-link.md).
* Per configurare regole di accesso al registro da dietro un firewall del client, vedere [Configurare le regole per accedere a un registro contenitori di Azure dietro un firewall](container-registry-firewall-access-rules.md).


<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png


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
