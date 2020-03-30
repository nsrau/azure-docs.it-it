---
title: Configurare il collegamento privato
description: Configurare un endpoint privato in un registro contenitori e abilitare un collegamento privato in una rete virtuale localeSet up a private endpoint on a container registry and enable a private link in a local virtual network
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: de8228d84497e71f24dba3dd4e6162cb6735a8c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498924"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Configurare Azure Private Link per un registro contenitore di AzureConfigure Azure Private Link for an Azure container registry 

È possibile configurare un [endpoint privato](../private-link/private-endpoint-overview.md) per il registro dei contenitori di Azure in modo che i client in una rete virtuale di Azure accedano in modo sicuro al Registro di sistema tramite un [collegamento privato.](../private-link/private-link-overview.md) L'endpoint privato utilizza un indirizzo IP dello spazio di indirizzi della rete virtuale per il Registro di sistema. Il traffico di rete tra i client nella rete virtuale e nel Registro di sistema attraversa la rete virtuale e un collegamento privato nella rete backbone Microsoft, eliminando l'esposizione da Internet pubblico.

È possibile [configurare le impostazioni DNS](../private-link/private-endpoint-overview.md#dns-configuration) per l'endpoint privato, in modo che le impostazioni vengano risolte nell'indirizzo IP privato allocato del Registro di sistema. Con la configurazione DNS, i client e i servizi nella rete possono continuare ad accedere al Registro di sistema dal nome di dominio completo del Registro di sistema, ad esempio *myregistry.azurecr.io*.

Questa funzionalità è disponibile nel livello del servizio registro contenitore **Premium.This** feature is available in the Premium container registry service tier. Per informazioni sui limiti e i livelli del servizio Registro di sistema, vedere SKU del [Registro di sistema del contenitore di Azure.For](container-registry-skus.md)information about registry service tiers and limits, see Azure Container Registry SKUs .

> [!IMPORTANT]
> Questa funzione è attualmente in anteprima e si applicano alcune [limitazioni.](#preview-limitations) Le anteprime vengono rese disponibili a condizione che l'utente accetti le [condizioni supplementari per l'utilizzo][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="preview-limitations"></a>Limiti di anteprima

* Attualmente, non è possibile impostare un collegamento privato con un endpoint privato in un [Registro](container-registry-geo-replication.md)di sistema con replica geografica . 

## <a name="prerequisites"></a>Prerequisiti

* Per usare i passaggi dell'interfaccia della riga di comando di Azure in questo articolo, è consigliabile usare l'interfaccia della riga di comando di Azure versione 2.2.0 o successiva. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure.If][azure-cli]you need to install or upgrade, see Install Azure CLI. Oppure eseguire in [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Se non si dispone già di un registro contenitori, crearne uno `hello-world` (livello Premium richiesto) e eseguire il push di un'immagine di esempio, ad esempio da Docker Hub.If you don't already have a container registry, create one (Premium tier required) and push a sample image such as from Docker Hub. Ad esempio, usare il [portale di Azure][quickstart-portal] o l'interfaccia della riga di comando di [Azure][quickstart-cli] per creare un Registro di sistema.
* Se si vuole configurare l'accesso al Registro di sistema usando un collegamento privato in una sottoscrizione di Azure diversa, è necessario registrare il provider di risorse per il Registro di sistema del contenitore di Azure in tale sottoscrizione. Ad esempio:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

Gli esempi dell'interfaccia della riga di comando di Azure in questo articolo usano le variabili di ambiente seguenti. Sostituire i valori appropriati per l'ambiente. Tutti gli esempi sono formattati per la shell Bash:

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>Creare una macchina virtuale abilitata per DockerCreate a Docker-enabled virtual machine

A scopo di test, usare una macchina virtuale Ubuntu abilitata per Docker per accedere a un registro contenitori di Azure.For test purposes, use a Docker-enabled Ubuntu VM to access an Azure container registry. Per usare l'autenticazione di Azure Active Directory nel Registro di sistema, installare anche [l'interfaccia della riga di comando][azure-cli] di Azure nella macchina virtuale. Se si dispone già di una macchina virtuale di Azure, ignorare questo passaggio di creazione.

È possibile usare lo stesso gruppo di risorse per la macchina virtuale e il registro contenitori. Questa configurazione semplifica la pulizia alla fine, ma non è necessaria. Se si sceglie di creare un gruppo di risorse separato per la macchina virtuale e la rete virtuale, eseguire [az group create][az-group-create]:

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

A questo punto distribuire una macchina virtuale predefinita di Ubuntu Azure con [az vm create][az-vm-create]. Nell'esempio seguente viene creata una macchina virtuale denominata *myDockerVM*.

```azurecli
az vm create \
  --resource-group $resourceGroup \
  --name $vmName \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Per creare la macchina virtuale sono necessari alcuni minuti. Dopo aver eseguito il comando, prendere nota del `publicIpAddress` visualizzato dall'interfaccia della riga di comando di Azure. Usare questo indirizzo per stabilire connessioni SSH alla macchina virtuale.

### <a name="install-docker-on-the-vm"></a>Installare Docker nella macchina virtuale

Una volta che la macchina virtuale è in esecuzione, stabilire una connessione SSH alla macchina virtuale. Sostituire *publicIpAddress* con l'indirizzo IP pubblico della macchina virtuale.

```bash
ssh azureuser@publicIpAddress
```

Eseguire i comandi seguenti per installare Docker nella macchina virtuale Ubuntu:Run the following commands to install Docker on the Ubuntu VM:

```bash
sudo apt-get update
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

Seguire i passaggi descritti in [Installare l'interfaccia della riga di comando di Azure con APT](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) per installare l'interfaccia della riga di comando di Azure nella macchina virtuale Ubuntu. Ad esempio:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Uscire dalla connessione SSH.

## <a name="set-up-private-link---cli"></a>Configura collegamento privato - CLI

### <a name="get-network-and-subnet-names"></a>Ottenere i nomi di rete e subnetGet network and subnet names

Se non sono già presenti, per configurare un collegamento privato sono necessari i nomi di una rete virtuale e di una subnet. In questo esempio si usa la stessa subnet per la macchina virtuale e l'endpoint privato del Registro di sistema. Tuttavia, in molti scenari è necessario configurare l'endpoint in una subnet separata. 

Quando si crea una macchina virtuale, Azure per impostazione predefinita crea una rete virtuale nello stesso gruppo di risorse. Il nome della rete virtuale è basato sul nome della macchina virtuale. Ad esempio, se si assegna alla macchina virtuale il nome *myDockerVM*, il nome della rete virtuale predefinita sarà *myDockerVMVNET*, con una subnet denominata *myDockerVMSubnet*. Impostare questi valori nelle variabili di ambiente eseguendo il comando [az network vnet list:][az-network-vnet-list]

```azurecli
networkName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Name: name}' --output tsv)

subnetName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo networkName=$networkName
echo subnetName=$subnetName
```

### <a name="disable-network-policies-in-subnet"></a>Disabilitare i criteri di rete nella subnetDisable network policies in subnet

[Disabilitare i criteri](../private-link/disable-private-endpoint-network-policy.md) di rete, ad esempio i gruppi di sicurezza di rete nella subnet per l'endpoint privato. Aggiornare la configurazione della subnet con [az network vnet subnet update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Configurare la zona DNS privata

Creare una zona DNS privata per il dominio del Registro di sistema del contenitore di Azure privato. Nei passaggi successivi, creare i record DNS per il dominio del Registro di sistema all'interno di questa zona DNS.

Per utilizzare una zona privata per eseguire l'override della risoluzione DNS predefinita per il registro contenitori di Azure, la zona deve essere denominata **privatelink.azurecr.io**. Eseguire il comando [az network private-dns zone create][az-network-private-dns-zone-create] per creare la zona privata:

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Creare un collegamento di associazione

Eseguire [az network private-dns link vnet create][az-network-private-dns-link-vnet-create] per associare la zona privata alla rete virtuale. In questo esempio viene creato un collegamento denominato *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Creare un endpoint del Registro di sistema privatoCreate a private registry endpoint

In questa sezione creare l'endpoint privato del Registro di sistema nella rete virtuale. Ottenere innanzitutto l'ID risorsa del Registro di sistema:

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

Eseguire il comando [az network private-endpoint create][az-network-private-endpoint-create] per creare l'endpoint privato del Registro di sistema.

Nell'esempio seguente vengono creati l'endpoint *myPrivateEndpoint* e la connessione al servizio *myConnection*. Per specificare una risorsa del `--group-ids registry`Registro di sistema del contenitore per l'endpoint, passare:

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $resourceGroup \
    --vnet-name $networkName \
    --subnet $subnetName \
    --private-connection-resource-id $registryID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>Ottenere indirizzi IP privatiGet private IP addresses

Eseguire [az network private-endpoint show][az-network-private-endpoint-show] per eseguire una query sull'endpoint per l'ID dell'interfaccia di rete:

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Associato all'interfaccia di rete sono due indirizzi IP privati per il registro contenitori: uno per il Registro di sistema stesso e uno per l'endpoint di dati del Registro di sistema. Eseguire i seguenti comandi [az resource show][az-resource-show] per ottenere gli indirizzi IP privati per il registro contenitori e l'endpoint dati del Registro di sistema:

```azurecli
privateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

dataEndpointPrivateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

### <a name="create-dns-records-in-the-private-zone"></a>Creare record DNS nella zona privata

I comandi seguenti creano record DNS nella zona privata per l'endpoint del Registro di sistema e il relativo endpoint di dati. Ad esempio, se si dispone di un Registro di sistema `myregistry.azurecr.io` `myregistry.westeurope.data.azurecr.io`denominato *myregistry* nell'area *westeurope,* i nomi degli endpoint sono e . 

Eseguire innanzitutto [az network private-dns record-set a create][az-network-private-dns-record-set-a-create] per creare set di record A vuoti per l'endpoint del Registro di sistema e l'endpoint dati:

```azurecli
az network private-dns record-set a create \
  --name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup
```

Eseguire il comando [az network private-dns record-set a add-record][az-network-private-dns-record-set-a-add-record] per creare i record A per l'endpoint del Registro di sistema e l'endpoint dati:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $privateIP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $dataEndpointPrivateIP
```

Il collegamento privato è ora configurato e pronto per l'uso.

## <a name="set-up-private-link---portal"></a>Configurare il collegamento privato - portale

I passaggi seguenti presuppongono che si disponga già di una rete virtuale e di una subnet configurata con una macchina virtuale per il test. È inoltre possibile [creare una nuova rete virtuale e una nuova subnet.](../virtual-network/quick-create-portal.md)

### <a name="create-a-private-endpoint"></a>Creare un endpoint privato

1. Nel portale passare al Registro di sistema del contenitore.
1. In **Impostazioni**selezionare **Connessioni endpoint privati (anteprima)**.
1. Selezionare **: Endpoint privato**.
1. Nella scheda **Nozioni di base,** immettere o selezionare le seguenti informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Immettere il nome di un gruppo esistente o crearne uno nuovo.|
    | **Dettagli istanza** |  |
    | Nome | Immettere un nome univoco. |
    |Region|Scegliere un'area,|
    |||
5. Selezionare **Avanti: Risorsa**.
6. Immettere o selezionare le seguenti informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    |Metodo di connessione  | Selezionare **Connetti a una risorsa di Azure nella directory**personale .|
    | Subscription| Selezionare la propria sottoscrizione. |
    | Tipo di risorsa | Selezionare **Microsoft.ContainerRegistry/registri**. |
    | Risorsa |Selezionare il nome del Registro di sistema|
    |Sottorisorsa di destinazione |Selezionare **il Registro di sistema**|
    |||
7. Selezionare **Avanti: Configurazione**.
8. Immettere o selezionare le informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    |**Rete**| |
    | Rete virtuale| Selezionare la rete virtuale in cui viene distribuita la macchina virtuale, ad esempio *myDockerVMVNET*. |
    | Subnet | Selezionare una subnet, ad esempio *myDockerVMSubnet* in cui viene distribuita la macchina virtuale. |
    |**Integrazione DNS privato**||
    |Integra con la zona DNS privato |Selezionare **Sì**. |
    |Zona DNS privato |*Selezionare (Nuovo) privatelink.azurecr.io* |
    |||

1. Selezionare **Revisione e creazione**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione. 
2. Quando viene visualizzato il messaggio **Convalida superata** , selezionare **Crea**.

Dopo aver creato l'endpoint privato, le impostazioni DNS nella zona privata vengono visualizzate nella pagina **Panoramica** dell'endpoint.

![Impostazioni DNS degli endpoint](./media/container-registry-private-link/private-endpoint-overview.png)

Il tuo collegamento privato è ora configurato e pronto per l'uso.

## <a name="validate-private-link-connection"></a>Convalidare la connessione di collegamento privato

È necessario verificare che le risorse all'interno della subnet dell'endpoint privato si connettano al Registro di sistema tramite un indirizzo IP privato e dispongano dell'integrazione della zona DNS privata corretta.

Per convalidare la connessione a collegamento privato, SSH alla macchina virtuale configurata nella rete virtuale.

Eseguire `nslookup` il comando per risolvere l'indirizzo IP del Registro di sistema tramite il collegamento privato:

```bash
nslookup $registryName.azurecr.io
```

L'output di esempio mostra l'indirizzo IP del Registro di sistema nello spazio degli indirizzi della subnet:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Confrontare questo risultato con `nslookup` l'indirizzo IP pubblico nell'output per lo stesso Registro di sistema su un endpoint pubblico:Compare this result with the public IP address in output for the same registry over a public endpoint:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Operazioni del Registro di sistema su collegamento privatoRegistry operations over private link

Verificare inoltre che sia possibile eseguire operazioni del Registro di sistema dalla macchina virtuale nella subnet. Creare una connessione SSH alla macchina virtuale ed eseguire [az acr login][az-acr-login] per accedere al Registro di sistema. A seconda della configurazione della macchina virtuale, `sudo`potrebbe essere necessario anteporre ai comandi seguenti .

```bash
az acr login --name $registryName
```

Eseguire operazioni del `docker pull` Registro di sistema, ad esempio per estrarre un'immagine di esempio dal Registro di sistema. Sostituire `hello-world:v1` con un'immagine e un tag appropriato per il Registro di sistema, preceduto dal nome del server di accesso al Registro di sistema (tutto minuscolo):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker estrae correttamente l'immagine nella macchina virtuale.

## <a name="manage-private-endpoint-connections"></a>Gestire le connessioni endpoint privatiManage private endpoint connections

Gestire le connessioni endpoint private di un Registro di sistema usando il portale di Azure o usando i comandi nel gruppo di comandi [az acr private-endpoint-connection.][az-acr-private-endpoint-connection] Le operazioni includono l'approvazione, l'eliminazione, l'elenco, il rifiuto o la visualizzazione dei dettagli delle connessioni endpoint privati di un Registro di sistema.

Ad esempio, per elencare le connessioni endpoint private di un Registro di sistema, eseguire il comando [az acr private-endpoint-connection list.][az-acr-private-endpoint-connection-list] Ad esempio:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

Quando si configura una connessione endpoint privato utilizzando la procedura descritta in questo articolo, il Registro di sistema accetta automaticamente le connessioni da client e servizi che dispongono di autorizzazioni RBAC per il Registro di sistema. È possibile configurare l'endpoint in modo da richiedere l'approvazione manuale delle connessioni. Per informazioni su come approvare e rifiutare le connessioni agli endpoint privati, vedere [Gestire una connessione endpoint privato.](../private-link/manage-private-endpoint.md)

## <a name="clean-up-resources"></a>Pulire le risorse

Se sono state create tutte le risorse di Azure nello stesso gruppo di risorse e non sono più necessarie, è possibile eliminare le risorse usando un singolo comando [az group delete:If](/cli/azure/group) you created all the Azure resources in the same resource group and no longer need them, you can optionally delete the resources by using a single az group delete command:

```azurecli
az group delete --name $resourceGroup
```

Per pulire le risorse nel portale, passare al gruppo di risorse. Dopo aver caricato il gruppo di risorse, fare clic su **Elimina gruppo** di risorse per rimuovere il gruppo di risorse e le risorse archiviate in tale gruppo.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su Private Link, vedere la documentazione di Azure Private Link.To learn more about Private [Link,](../private-link/private-link-overview.md) see the Azure Private Link documentation.
* Un'alternativa al collegamento privato consiste nell'impostare regole di accesso alla rete per limitare l'accesso al Registro di sistema. Per altre informazioni, vedere [Limitare l'accesso a un registro dei contenitori di Azure usando una rete virtuale di Azure o le regole del firewall.](container-registry-vnet.md)

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms
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
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-approve
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
