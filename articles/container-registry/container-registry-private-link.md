---
title: Configura collegamento privato
description: Configurare un endpoint privato in un registro contenitori e abilitare un collegamento privato in una rete virtuale locale
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: de8228d84497e71f24dba3dd4e6162cb6735a8c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498924"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Configurare il collegamento privato di Azure per un registro contenitori di Azure 

È possibile configurare un [endpoint privato](../private-link/private-endpoint-overview.md) per il registro contenitori di Azure in modo che i client in una rete virtuale di Azure accedano in modo sicuro al registro di sistema tramite un [collegamento privato](../private-link/private-link-overview.md). L'endpoint privato usa un indirizzo IP dello spazio di indirizzi della rete virtuale per il registro. Il traffico di rete tra i client nella rete virtuale e il registro di sistema attraversa la rete virtuale e un collegamento privato sulla rete dorsale Microsoft, eliminando l'esposizione dalla rete Internet pubblica.

È possibile [configurare le impostazioni DNS](../private-link/private-endpoint-overview.md#dns-configuration) per l'endpoint privato, in modo che le impostazioni vengano risolte nell'indirizzo IP privato allocato del registro di sistema. Con la configurazione DNS, i client e i servizi nella rete possono continuare ad accedere al registro di sistema con il nome di dominio completo del registro di sistema, ad esempio *MyRegistry.azurecr.io*.

Questa funzionalità è disponibile nel livello di servizio del registro contenitori **Premium** . Per informazioni sui limiti e i livelli di servizio del registro di sistema, vedere [sku container Registry di Azure](container-registry-skus.md).

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima e si applicano alcune [limitazioni](#preview-limitations) . Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="preview-limitations"></a>Limiti di anteprima

* Attualmente, non è possibile configurare un collegamento privato con un endpoint privato in un [registro con replica geografica](container-registry-geo-replication.md). 

## <a name="prerequisites"></a>Prerequisiti

* Per usare i passaggi dell'interfaccia della riga di comando di Azure in questo articolo è consigliabile usare l'interfaccia della riga di comando di Azure versione 2.2.0 Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli]. In alternativa, eseguire in [Azure cloud Shell](../cloud-shell/quickstart.md).
* Se non si ha già un registro contenitori, crearne uno (livello Premium obbligatorio) ed effettuare il push di un' `hello-world` immagine di esempio, ad esempio dall'hub docker. Ad esempio, usare l' [portale di Azure][quickstart-portal] o l' [interfaccia][quickstart-cli] della riga di comando di Azure per creare un registro.
* Se si vuole configurare l'accesso al registro di sistema usando un collegamento privato in un'altra sottoscrizione di Azure, è necessario registrare il provider di risorse per Container Registry di Azure nella sottoscrizione. Ad esempio:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

Gli esempi dell'interfaccia della riga di comando di Azure in questo articolo usano le variabili di ambiente seguenti. Sostituire i valori appropriati per l'ambiente in uso. Tutti gli esempi sono formattati per la shell bash:

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>Creare una macchina virtuale abilitata per Docker

A scopo di test, usare una VM Ubuntu abilitata per Docker per accedere a un registro contenitori di Azure. Per usare l'autenticazione Azure Active Directory per il registro di sistema, installare anche l'interfaccia della riga di comando di [Azure][azure-cli] nella macchina virtuale. Se si dispone già di una macchina virtuale di Azure, ignorare questo passaggio di creazione.

È possibile usare lo stesso gruppo di risorse per la macchina virtuale e il registro contenitori. Questa configurazione semplifica la pulizia alla fine, ma non è obbligatoria. Se si sceglie di creare un gruppo di risorse separato per la macchina virtuale e la rete virtuale, eseguire il comando [AZ Group create][az-group-create]:

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

Distribuire ora una macchina virtuale di Azure Ubuntu predefinita con [AZ VM create][az-vm-create]. L'esempio seguente crea una macchina virtuale denominata *myDockerVM*.

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

Eseguire i comandi seguenti per installare Docker nella VM Ubuntu:

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

## <a name="set-up-private-link---cli"></a>Configurare il collegamento privato-interfaccia della riga di comando

### <a name="get-network-and-subnet-names"></a>Ottenere i nomi di rete e subnet

Se non sono già presenti, saranno necessari i nomi di una rete virtuale e di una subnet per configurare un collegamento privato. In questo esempio si usa la stessa subnet per la macchina virtuale e l'endpoint privato del registro di sistema. In molti scenari, tuttavia, è necessario configurare l'endpoint in una subnet separata. 

Quando si crea una VM, per impostazione predefinita Azure crea una rete virtuale nello stesso gruppo di risorse. Il nome della rete virtuale si basa sul nome della macchina virtuale. Ad esempio, se si specifica il nome della macchina virtuale *myDockerVM*, il nome di rete virtuale predefinito è *myDockerVMVNET*, con una subnet denominata *myDockerVMSubnet*. Per impostare questi valori nelle variabili di ambiente, eseguire il comando [AZ Network VNET list][az-network-vnet-list] :

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

### <a name="disable-network-policies-in-subnet"></a>Disabilitare i criteri di rete nella subnet

[Disabilitare i criteri di rete](../private-link/disable-private-endpoint-network-policy.md) , ad esempio i gruppi di sicurezza di rete nella subnet per l'endpoint privato. Aggiornare la configurazione della subnet con [AZ Network VNET subnet Update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Configurare la zona DNS privata

Creare una zona DNS privata per il dominio del registro contenitori di Azure priviate. Nei passaggi successivi si creeranno i record DNS per il dominio del registro di sistema all'interno di questa zona DNS.

Per usare una zona privata per sostituire la risoluzione DNS predefinita per il registro contenitori di Azure, la zona deve essere denominata **privatelink.azurecr.io**. Eseguire il comando [AZ network private-DNS zone create][az-network-private-dns-zone-create] seguente per creare la zona privata:

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Creare un collegamento di associazione

Eseguire il comando [AZ network private-DNS link VNET create][az-network-private-dns-link-vnet-create] per associare la zona privata alla rete virtuale. Questo esempio crea un collegamento denominato *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Creare un endpoint del registro di sistema privato

In questa sezione creare l'endpoint privato del registro di sistema nella rete virtuale. Per prima cosa, ottenere l'ID risorsa del registro di sistema:

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

Eseguire il comando [AZ network private-endpoint create][az-network-private-endpoint-create] per creare l'endpoint privato del registro di sistema.

Nell'esempio seguente vengono creati l'endpoint *myPrivateEndpoint* e la *connessione al servizio.* Per specificare una risorsa del registro contenitori per l'endpoint, `--group-ids registry`passare:

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

### <a name="get-private-ip-addresses"></a>Ottieni indirizzi IP privati

Eseguire [AZ network private-endpoint Show][az-network-private-endpoint-show] per eseguire una query sull'endpoint per l'ID dell'interfaccia di rete:

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Associato all'interfaccia di rete sono due indirizzi IP privati per il registro contenitori: uno per il registro di sistema e uno per l'endpoint dati del registro di sistema. Eseguire i comandi [AZ Resource Show][az-resource-show] seguenti per ottenere gli indirizzi IP privati per il registro contenitori e l'endpoint dati del registro di sistema:

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

I comandi seguenti consentono di creare record DNS nella zona privata per l'endpoint del registro di sistema e il relativo endpoint dati. Se, ad esempio, si dispone di un registro denominato Registro di *sistema* nell'area *westeurope* , i nomi `myregistry.azurecr.io` degli `myregistry.westeurope.data.azurecr.io`endpoint sono e. 

Eseguire prima di tutto il comando [AZ network private-DNS record-set a create][az-network-private-dns-record-set-a-create] per creare set di record vuoti a per l'endpoint del registro di sistema e l'endpoint di dati:

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

Eseguire il comando [AZ network private-DNS record-set a Add-record][az-network-private-dns-record-set-a-add-record] per creare i record a per l'endpoint del registro di sistema e l'endpoint di dati:

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

## <a name="set-up-private-link---portal"></a>Configurare il collegamento privato-portale

I passaggi seguenti presuppongono che sia già stata configurata una rete virtuale e una subnet con una VM per il test. È anche possibile [creare una nuova rete virtuale e una nuova subnet](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint"></a>Creare un endpoint privato

1. Nel portale passare al registro contenitori.
1. In **Impostazioni**selezionare **connessioni endpoint privato (anteprima)**.
1. Selezionare **+ endpoint privato**.
1. Nella scheda informazioni di **base** immettere o selezionare le informazioni seguenti:

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
    |Metodo di connessione  | Selezionare **Connetti a una risorsa di Azure nella directory**.|
    | Subscription| Selezionare la propria sottoscrizione. |
    | Tipo di risorsa | Selezionare **Microsoft. ContainerRegistry/registrations**. |
    | Risorsa |Selezionare il nome del registro|
    |Sottorisorsa di destinazione |Seleziona **Registro di sistema**|
    |||
7. Selezionare **Avanti: Configurazione**.
8. Immettere o selezionare le informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    |**Funzionalità di rete**| |
    | Rete virtuale| Selezionare la rete virtuale in cui è distribuita la macchina virtuale, ad esempio *myDockerVMVNET*. |
    | Subnet | Selezionare una subnet, ad esempio *myDockerVMSubnet* , in cui è distribuita la macchina virtuale. |
    |**Integrazione di DNS privato**||
    |Integra con la zona DNS privato |Selezionare **Sì**. |
    |Zona DNS privato |Select *(nuovo) privatelink.azurecr.io* |
    |||

1. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione. 
2. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

Dopo la creazione dell'endpoint privato, le impostazioni DNS nella zona privata vengono visualizzate nella pagina **Panoramica** dell'endpoint.

![Impostazioni DNS endpoint](./media/container-registry-private-link/private-endpoint-overview.png)

Il collegamento privato è ora configurato e pronto per l'uso.

## <a name="validate-private-link-connection"></a>Convalida connessione a collegamento privato

È necessario verificare che le risorse all'interno della subnet dell'endpoint privato si connettano al registro di sistema tramite un indirizzo IP privato e abbiano l'integrazione della zona DNS privata corretta.

Per convalidare la connessione al collegamento privato, connettersi tramite SSH alla macchina virtuale configurata nella rete virtuale.

Eseguire il `nslookup` comando per risolvere l'indirizzo IP del registro di sistema tramite il collegamento privato:

```bash
nslookup $registryName.azurecr.io
```

L'output di esempio mostra l'indirizzo IP del registro di sistema nello spazio degli indirizzi della subnet:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Confrontare questo risultato con l'indirizzo IP pubblico nell' `nslookup` output per lo stesso registro di sistema su un endpoint pubblico:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Operazioni del registro di sistema su collegamento privato

Verificare inoltre che sia possibile eseguire operazioni del registro di sistema dalla macchina virtuale nella subnet. Effettuare una connessione SSH alla macchina virtuale ed eseguire [AZ ACR login][az-acr-login] per accedere al registro. A seconda della configurazione della macchina virtuale, potrebbe essere necessario anteporre i comandi seguenti con `sudo`.

```bash
az acr login --name $registryName
```

Eseguire operazioni del registro di `docker pull` sistema come per eseguire il pull di un'immagine di esempio dal registro di sistema. Sostituire `hello-world:v1` con un'immagine e un tag appropriati per il registro di sistema, preceduto dal nome del server di accesso del registro di sistema (tutti minuscoli):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker esegue correttamente il pull dell'immagine nella macchina virtuale.

## <a name="manage-private-endpoint-connections"></a>Gestire le connessioni agli endpoint privati

Gestire le connessioni degli endpoint privati del registro di sistema usando il portale di Azure o usando i comandi nel gruppo di comandi [AZ ACR private-endpoint-Connection][az-acr-private-endpoint-connection] . Le operazioni includono approva, Elimina, elenca, rifiuta o Mostra i dettagli delle connessioni all'endpoint privato del registro di sistema.

Per elencare le connessioni di un endpoint privato di un registro, ad esempio, eseguire il comando [AZ ACR private-endpoint-Connection List][az-acr-private-endpoint-connection-list] . Ad esempio:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

Quando si configura una connessione a un endpoint privato eseguendo la procedura descritta in questo articolo, il registro di sistema accetta automaticamente le connessioni da client e servizi che dispongono di autorizzazioni RBAC nel registro di sistema. È possibile configurare l'endpoint in modo da richiedere l'approvazione manuale delle connessioni. Per informazioni su come approvare e rifiutare le connessioni agli endpoint privati, vedere [gestire una connessione all'endpoint privato](../private-link/manage-private-endpoint.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Se sono state create tutte le risorse di Azure nello stesso gruppo di risorse e non sono più necessarie, è possibile eliminare facoltativamente le risorse usando un singolo comando [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name $resourceGroup
```

Per pulire le risorse nel portale, passare al gruppo di risorse. Una volta caricato il gruppo di risorse, fare clic su **Elimina gruppo di risorse** per rimuovere il gruppo di risorse e le risorse archiviate.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul collegamento privato, vedere la documentazione del [collegamento privato di Azure](../private-link/private-link-overview.md) .
* Un'alternativa al collegamento privato consiste nell'impostare le regole di accesso alla rete per limitare l'accesso al registro di sistema. Per altre informazioni, vedere [limitare l'accesso a un registro contenitori di Azure usando una rete virtuale di Azure o regole del firewall](container-registry-vnet.md).

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
