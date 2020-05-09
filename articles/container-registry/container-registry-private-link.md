---
title: Configura collegamento privato
description: Configurare un endpoint privato in un registro contenitori e abilitare l'accesso tramite un collegamento privato in una rete virtuale locale
ms.topic: article
ms.date: 05/07/2020
ms.openlocfilehash: 46ec816d85a528fd3208026ef76dff8470154767
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982439"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Configurare il collegamento privato di Azure per un registro contenitori di Azure 

Limitare l'accesso a un registro di sistema assegnando indirizzi IP privati della rete virtuale agli endpoint del registro di sistema tramite il [collegamento privato di Azure](../private-link/private-link-overview.md). Il traffico di rete tra i client nella rete virtuale e il registro di sistema attraversa la rete virtuale e un collegamento privato sulla rete dorsale Microsoft, eliminando l'esposizione dalla rete Internet pubblica.

È possibile [configurare le impostazioni DNS](../private-link/private-endpoint-overview.md#dns-configuration) per l'endpoint privato, in modo che le impostazioni vengano risolte nell'indirizzo IP privato allocato del registro di sistema. Con la configurazione DNS, i client e i servizi nella rete possono continuare ad accedere al registro di sistema con il nome di dominio completo del registro di sistema, ad esempio *MyRegistry.azurecr.io*.

Questa funzionalità è disponibile nel livello di servizio del registro contenitori **Premium** . Per informazioni sui limiti e i livelli di servizio del registro di sistema, vedere [livelli di container Registry di Azure](container-registry-skus.md).

## <a name="things-to-know"></a>Informazioni importanti

* Attualmente, l'analisi delle immagini con il Centro sicurezza di Azure non è disponibile in un registro configurato con un endpoint privato.

## <a name="prerequisites"></a>Prerequisiti

* Per usare i passaggi dell'interfaccia della riga di comando di Azure in questo articolo è consigliabile usare l'interfaccia della riga di comando di Azure versione 2.2.0 Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli]. In alternativa, eseguire in [Azure cloud Shell](../cloud-shell/quickstart.md).
* Se non si ha già un registro contenitori, crearne uno (livello Premium obbligatorio) e [importare](container-registry-import-images.md) un'immagine `hello-world` di esempio, ad esempio dall'hub docker. Ad esempio, usare l' [portale di Azure][quickstart-portal] o l' [interfaccia][quickstart-cli] della riga di comando di Azure per creare un registro.
* Per configurare l'accesso al registro di sistema usando un collegamento privato in un'altra sottoscrizione di Azure, è necessario registrare il provider di risorse per Container Registry di Azure nella sottoscrizione. Ad esempio:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

Gli esempi dell'interfaccia della riga di comando di Azure in questo articolo usano le variabili di ambiente seguenti. Sostituire i valori appropriati per l'ambiente in uso. Tutti gli esempi sono formattati per la shell bash:

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>Configurare il collegamento privato-interfaccia della riga di comando

### <a name="get-network-and-subnet-names"></a>Ottenere i nomi di rete e subnet

Se non sono già presenti, saranno necessari i nomi di una rete virtuale e di una subnet per configurare un collegamento privato. In questo esempio si usa la stessa subnet per la macchina virtuale e l'endpoint privato del registro di sistema. In molti scenari, tuttavia, è necessario configurare l'endpoint in una subnet separata. 

Quando si crea una VM, per impostazione predefinita Azure crea una rete virtuale nello stesso gruppo di risorse. Il nome della rete virtuale si basa sul nome della macchina virtuale. Ad esempio, se si specifica il nome della macchina virtuale *myDockerVM*, il nome di rete virtuale predefinito è *myDockerVMVNET*, con una subnet denominata *myDockerVMSubnet*. Per impostare questi valori nelle variabili di ambiente, eseguire il comando [AZ Network VNET list][az-network-vnet-list] :

```azurecli
NETWORK_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Name: name}' --output tsv)

SUBNET_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo NETWORK_NAME=$NETWORK_NAME
echo SUBNET_NAME=$SUBNET_NAME
```

### <a name="disable-network-policies-in-subnet"></a>Disabilitare i criteri di rete nella subnet

[Disabilitare i criteri di rete](../private-link/disable-private-endpoint-network-policy.md) , ad esempio i gruppi di sicurezza di rete nella subnet per l'endpoint privato. Aggiornare la configurazione della subnet con [AZ Network VNET subnet Update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Configurare la zona DNS privata

Creare una zona DNS privata per il dominio del registro contenitori di Azure privato. Nei passaggi successivi si creeranno i record DNS per il dominio del registro di sistema in questa zona DNS.

Per usare una zona privata per sostituire la risoluzione DNS predefinita per il registro contenitori di Azure, la zona deve essere denominata **privatelink.azurecr.io**. Eseguire il comando [AZ network private-DNS zone create][az-network-private-dns-zone-create] seguente per creare la zona privata:

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Creare un collegamento di associazione

Eseguire il comando [AZ network private-DNS link VNET create][az-network-private-dns-link-vnet-create] per associare la zona privata alla rete virtuale. Questo esempio crea un collegamento denominato *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Creare un endpoint del registro di sistema privato

In questa sezione creare l'endpoint privato del registro di sistema nella rete virtuale. Per prima cosa, ottenere l'ID risorsa del registro di sistema:

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

Eseguire il comando [AZ network private-endpoint create][az-network-private-endpoint-create] per creare l'endpoint privato del registro di sistema.

Nell'esempio seguente vengono creati l'endpoint *myPrivateEndpoint* e la *connessione al servizio.* Per specificare una risorsa del registro contenitori per l'endpoint, `--group-ids registry`passare:

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $NETWORK_NAME \
    --subnet $SUBNET_NAME \
    --private-connection-resource-id $REGISTRY_ID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>Ottieni indirizzi IP privati

Eseguire [AZ network private-endpoint Show][az-network-private-endpoint-show] per eseguire una query sull'endpoint per l'ID dell'interfaccia di rete:

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Associato all'interfaccia di rete in questo esempio sono due indirizzi IP privati per il registro contenitori: uno per il registro di sistema e uno per l'endpoint dati del registro di sistema. I comandi [AZ Resource Show][az-resource-show] seguenti ottengono gli indirizzi IP privati per il registro contenitori e l'endpoint dati del registro di sistema:

```azurecli
PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

> [!NOTE]
> Se il registro di sistema è con [replica geografica](container-registry-geo-replication.md), eseguire una query per l'endpoint di dati aggiuntivo per ogni replica del registro di sistema.

### <a name="create-dns-records-in-the-private-zone"></a>Creare record DNS nella zona privata

I comandi seguenti consentono di creare record DNS nella zona privata per l'endpoint del registro di sistema e il relativo endpoint dati. Se, ad esempio, si dispone di un registro denominato Registro di *sistema* nell'area *westeurope* , i nomi `myregistry.azurecr.io` degli `myregistry.westeurope.data.azurecr.io`endpoint sono e. 

> [!NOTE]
> Se il registro di sistema è con [replica geografica](container-registry-geo-replication.md), creare record DNS aggiuntivi per l'indirizzo IP dell'endpoint dati di ogni replica.

Eseguire prima di tutto il comando [AZ network private-DNS record-set a create][az-network-private-dns-record-set-a-create] per creare set di record vuoti a per l'endpoint del registro di sistema e l'endpoint di dati:

```azurecli
az network private-dns record-set a create \
  --name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP
```

Eseguire il comando [AZ network private-DNS record-set a Add-record][az-network-private-dns-record-set-a-add-record] per creare i record a per l'endpoint del registro di sistema e l'endpoint di dati:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

Il collegamento privato è ora configurato e pronto per l'uso.

## <a name="set-up-private-link---portal"></a>Configurare il collegamento privato-portale

Configurare un collegamento privato quando si crea un registro di sistema o aggiungere un collegamento privato a un registro esistente. I passaggi seguenti presuppongono che sia già stata configurata una rete virtuale e una subnet con una VM per il test. È anche possibile [creare una nuova rete virtuale e una nuova subnet](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint---new-registry"></a>Creare un endpoint privato-nuovo registro di sistema

1. Quando si crea un registro di sistema nel portale, nella scheda **nozioni di base** , in **SKU**, selezionare **Premium**.
1. Selezionare la scheda **rete** .
1. In **connettività di rete**selezionare **endpoint** > privato **+ Aggiungi**.
1. Immettere o selezionare le seguenti informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Immettere il nome di un gruppo esistente o crearne uno nuovo.|
    | Nome | Immettere un nome univoco. |
    | Sottorisorsa |Seleziona **Registro di sistema**|
    | **Funzionalità di rete** | |
    | Rete virtuale| Selezionare la rete virtuale in cui è distribuita la macchina virtuale, ad esempio *myDockerVMVNET*. |
    | Subnet | Selezionare una subnet, ad esempio *myDockerVMSubnet* , in cui è distribuita la macchina virtuale. |
    |**Integrazione di DNS privato**||
    |Integra con la zona DNS privato |Selezionare **Sì**. |
    |Zona DNS privato |Select *(nuovo) privatelink.azurecr.io* |
    |||
1. Configurare le impostazioni del registro di sistema rimanenti, quindi selezionare **Verifica + crea**.

  ![Crea registro con endpoint privato](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>Creare un endpoint privato-registro di sistema esistente

1. Nel portale passare al registro contenitori.
1. In **Impostazioni**selezionare **rete**.
1. Nella scheda **endpoint privati** selezionare **+ endpoint privato**.
1. Nella scheda informazioni di **base** immettere o selezionare le informazioni seguenti:

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Immettere il nome di un gruppo esistente o crearne uno nuovo.|
    | **Dettagli istanza** |  |
    | Nome | Immettere un nome. |
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

    | Impostazione | valore |
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

Dopo la creazione dell'endpoint privato, le impostazioni DNS nella zona privata vengono visualizzate nella pagina **endpoint privati** nel portale:

1. Nel portale passare al registro contenitori e selezionare **impostazioni > rete**.
1. Nella scheda **endpoint privati** selezionare l'endpoint privato creato.
1. Nella pagina **Panoramica** esaminare le impostazioni del collegamento e le impostazioni DNS personalizzate.

  ![Impostazioni DNS endpoint](./media/container-registry-private-link/private-endpoint-overview.png)

Il collegamento privato è ora configurato e pronto per l'uso.

## <a name="disable-public-access"></a>Disabilitare l'accesso pubblico

Per molti scenari, disabilitare l'accesso al registro di sistema dalle reti pubbliche. Questa configurazione impedisce ai client esterni alla rete virtuale di raggiungere gli endpoint del registro di sistema. Per disabilitare l'accesso pubblico tramite il portale:

1. Nel portale passare al registro contenitori e selezionare **impostazioni > rete**.
1. Nella scheda **accesso pubblico** selezionare **disabilitato**in **Consenti accesso pubblico**. Selezionare quindi **Salva**.

## <a name="validate-private-link-connection"></a>Convalida connessione a collegamento privato

È necessario verificare che le risorse all'interno della subnet dell'endpoint privato si connettano al registro di sistema tramite un indirizzo IP privato e abbiano l'integrazione della zona DNS privata corretta.

Per convalidare la connessione al collegamento privato, connettersi tramite SSH alla macchina virtuale configurata nella rete virtuale.

Eseguire il `nslookup` comando per risolvere l'indirizzo IP del registro di sistema tramite il collegamento privato:

```bash
nslookup $REGISTRY_NAME.azurecr.io
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
az acr login --name $REGISTRY_NAME
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
  --registry-name $REGISTRY_NAME 
```

Quando si configura una connessione a un endpoint privato eseguendo la procedura descritta in questo articolo, il registro di sistema accetta automaticamente le connessioni da client e servizi che dispongono di autorizzazioni RBAC nel registro di sistema. È possibile configurare l'endpoint in modo da richiedere l'approvazione manuale delle connessioni. Per informazioni su come approvare e rifiutare le connessioni agli endpoint privati, vedere [gestire una connessione all'endpoint privato](../private-link/manage-private-endpoint.md).

## <a name="add-zone-records-for-replicas"></a>Aggiungere record di zona per le repliche

Come illustrato in questo articolo, quando si aggiunge una connessione all'endpoint privato a un registro di sistema, i `privatelink.azurecr.io` record DNS nella zona vengono creati per il registro di sistema e i relativi endpoint di dati nelle aree in cui viene [replicato](container-registry-geo-replication.md)il registro di sistema. 

Se in un secondo momento si aggiunge una nuova replica, è necessario aggiungere manualmente un nuovo record di zona per l'endpoint dati in tale area. Ad esempio, se si crea una replica di *Registro di sistema* nel percorso *northeurope* , aggiungere un record di zona `myregistry.northeurope.data.azurecr.io`per. Per i passaggi, vedere [creare record DNS nella zona privata](#create-dns-records-in-the-private-zone) in questo articolo.

## <a name="clean-up-resources"></a>Pulire le risorse

Se sono state create tutte le risorse di Azure nello stesso gruppo di risorse e non sono più necessarie, è possibile eliminare facoltativamente le risorse usando un singolo comando [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name $RESOURCE_GROUP
```

Per pulire le risorse nel portale, passare al gruppo di risorse. Una volta caricato il gruppo di risorse, fare clic su **Elimina gruppo di risorse** per rimuovere il gruppo di risorse e le risorse archiviate.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul collegamento privato, vedere la documentazione del [collegamento privato di Azure](../private-link/private-link-overview.md) .
* Se è necessario configurare regole di accesso al registro di sistema da dietro un firewall client, vedere [configurare le regole per accedere a un registro contenitori di Azure dietro un firewall](container-registry-firewall-access-rules.md).

<!-- LINKS - external -->
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
[az-acr-update]: /cli/azure/acr#az-acr-update
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
