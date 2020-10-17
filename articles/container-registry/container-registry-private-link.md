---
title: Configurare un collegamento privato
description: Configurare un endpoint privato in un registro contenitori e abilitare l'accesso tramite un collegamento privato in una rete virtuale locale. L'accesso ai collegamenti privati è una funzionalità del livello di servizio Premium.
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 6bea4b2a6bedeac9dd0ff36631ba46adf4be4f8f
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148472"
---
# <a name="connect-privately-to-an-azure-container-registry-using-azure-private-link"></a>Connettersi privatamente a un registro contenitori di Azure usando il collegamento privato di Azure


Limitare l'accesso a un registro di sistema assegnando indirizzi IP privati della rete virtuale agli endpoint del registro di sistema e usando il [collegamento privato di Azure](../private-link/private-link-overview.md). Il traffico di rete tra i client nella rete virtuale e gli endpoint privati del registro di sistema attraversa la rete virtuale e un collegamento privato sulla rete dorsale Microsoft, eliminando l'esposizione dalla rete Internet pubblica. Il collegamento privato consente anche l'accesso al registro privato dall'ambiente locale tramite il peering privato di [Azure ExpressRoute](../expressroute/expressroute-introduction.MD) o un [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

È possibile [configurare le impostazioni DNS](../private-link/private-endpoint-overview.md#dns-configuration) per gli endpoint privati del registro di sistema, in modo che le impostazioni vengano risolte nell'indirizzo IP privato allocato del registro di sistema. Con la configurazione DNS, i client e i servizi all'interno della rete possono continuare ad accedere al registro con il nome di dominio completo di questo, ad esempio *myregistry.azurecr.io*. 

Questa funzionalità è disponibile per il livello di servizio **Premium** del registro contenitori. Attualmente, per un registro di sistema è possibile configurare un massimo di 10 endpoint privati. Per informazioni sui livelli di servizio del registro e sui limiti, vedere [livelli di Registro Azure Container](container-registry-skus.md).

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Prerequisiti

* Per usare la procedura dell'interfaccia della riga di comando di Azure descritta in questo articolo, è consigliabile usare l'interfaccia della riga di comando di Azure versione 2.6.0 o successiva. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli]. In alternativa, eseguire la procedura in [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Se non si ha ancora un registro contenitori, crearne uno (è necessario il livello Premium) e [importare](container-registry-import-images.md) un'immagine di esempio, come `hello-world` da Docker Hub. Ad esempio, per creare un registro usare il [portale di Azure][quickstart-portal] oppure l'[interfaccia della riga di comando di Azure][quickstart-cli].
* Per configurare l'accesso al registro usando un collegamento privato in un'altra sottoscrizione di Azure, è necessario registrare il provider di risorse per Registro Azure Container in tale sottoscrizione. Ad esempio:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

Gli esempi dell'interfaccia della riga di comando di Azure in questo articolo usano le variabili di ambiente seguenti. Sostituirle con i valori appropriati per l'ambiente in uso. Tutti gli esempi sono formattati per la shell Bash:

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>Configurare un collegamento privato - Interfaccia della riga di comando

### <a name="get-network-and-subnet-names"></a>Ottenere i nomi della rete e della subnet

Se non sono ancora noti, è necessario ottenere i nomi della rete virtuale e della subnet per cui configurare il collegamento privato. In questo esempio si usa la stessa subnet per la macchina virtuale e l'endpoint privato del registro. In molti scenari, tuttavia, è necessario configurare l'endpoint in una subnet separata. 

Quando si crea una macchina virtuale, per impostazione predefinita Azure crea una rete virtuale nello stesso gruppo di risorse. Il nome della rete virtuale si basa sul nome della macchina virtuale. Se, ad esempio, si rinomina la macchina virtuale in *myDockerVM*, il nome della rete virtuale predefinito sarà *myDockerVMVNET*, con una subnet denominata *myDockerVMSubnet*. Per impostare questi valori nelle variabili di ambiente, eseguire il comando [az network vnet list][az-network-vnet-list]:

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

[Disabilitare i criteri di rete](../private-link/disable-private-endpoint-network-policy.md) come i gruppi di sicurezza di rete nella subnet per l'endpoint privato. Aggiornare la configurazione della subnet con [az network vnet subnet update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Configurare la zona DNS privato

Creare una [zona DNS privata](../dns/private-dns-privatednszone.md) per il dominio del registro contenitori di Azure privato. In passaggi successivi si creeranno record DNS per il dominio del registro in questa zona DNS.

Per usare una zona privata allo scopo di sostituire la risoluzione DNS predefinita per il registro contenitori di Azure, la zona deve essere denominata **privatelink.azurecr.io**. Eseguire il comando [az network private-dns zone create][az-network-private-dns-zone-create] seguente per creare la zona privata:

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Creare un collegamento di associazione

Eseguire [az network private-dns link vnet create][az-network-private-dns-link-vnet-create] per associare la zona privata alla rete virtuale. Questo esempio crea un collegamento denominato *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Creare un endpoint del registro privato

In questa sezione si creerà l'endpoint privato del registro nella rete virtuale. Per prima cosa, ottenere l'ID risorsa del registro:

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

Eseguire il comando [az network private-endpoint create][az-network-private-endpoint-create] per creare l'endpoint privato del registro.

L'esempio seguente crea l'endpoint *myPrivateEndpoint* e la connessione al servizio *myConnection*. Per specificare una risorsa del registro contenitori per l'endpoint, passare `--group-ids registry`:

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

### <a name="get-private-ip-addresses"></a>Ottenere indirizzi IP privati

Eseguire [az network private-endpoint show][az-network-private-endpoint-show] per eseguire una query sull'endpoint per l'ID dell'interfaccia di rete:

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

All'interfaccia di rete in questo esempio sono associati due indirizzi IP privati per il registro contenitori: uno per il registro stesso e uno per l'endpoint dati di questo. I comandi [az resource show][az-resource-show] seguenti ottengono gli indirizzi IP privati per il registro contenitori e l'endpoint dati di questo:

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
> Se il registro è [con replica geografica](container-registry-geo-replication.md), eseguire una query per ottenere l'endpoint dati aggiuntivo per ogni replica del registro.

### <a name="create-dns-records-in-the-private-zone"></a>Creare record DNS nella zona privata

I comandi seguenti creano record DNS nella zona privata per l'endpoint del registro e l'endpoint dati di questo. Se, ad esempio, è presente un registro denominato *myregistry* si nell'area *westeurope*, i nomi degli endpoint sono `myregistry.azurecr.io` e `myregistry.westeurope.data.azurecr.io`. 

> [!NOTE]
> Se il registro è [con replica geografica](container-registry-geo-replication.md), creare record DNS aggiuntivi per l'indirizzo IP dell'endpoint dati di ogni replica.

Eseguire prima di tutto [az network private-dns record-set a create][az-network-private-dns-record-set-a-create] per creare set di record A vuoti per l'endpoint e l'endpoint dati del registro:

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

Eseguire il comando [az network private-dns record-set a add-record][az-network-private-dns-record-set-a-add-record] per creare i record A per l'endpoint del registro di sistema e l'endpoint di dati:

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

## <a name="set-up-private-link---portal"></a>Configurare un collegamento privato - Portale

Configurare un collegamento privato durante la creazione di un registro o aggiungere un collegamento privato a un registro esistente. La procedura seguente presuppone che siano già state configurate una rete virtuale e una subnet con una macchina virtuale per il test. È anche possibile [creare una rete virtuale e una subnet nuove](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint---new-registry"></a>Creare un endpoint privato - Nuovo registro

1. Quando si crea un registro nel portale, nella scheda **Informazioni di base**, in **SKU**, selezionare **Premium**.
1. Selezionare la scheda **Rete**.
1. In **Connettività di rete**selezionare **Endpoint privato** >  **+ Aggiungi**.
1. Immettere o selezionare le informazioni seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Immettere il nome di un gruppo esistente o crearne uno nuovo.|
    | Nome | Immettere un nome univoco. |
    | Sottorisorsa |Selezionare **Registro**|
    | **Rete** | |
    | Rete virtuale| Selezionare la rete virtuale in cui è distribuita la macchina virtuale, ad esempio *myDockerVMVNET*. |
    | Subnet | Selezionare la subnet, ad esempio *myDockerVMSubnet* in cui è distribuita la macchina virtuale. |
    |**Integrazione DNS privato**||
    |Integra con la zona DNS privato |Selezionare **Sì**. |
    |Zona DNS privato |Selezionare *(Nuova) privatelink.azurecr.io* |
    |||
1. Configurare le impostazioni del registro rimanenti e quindi selezionare **Rivedi e crea**.

  ![Creare un registro con un endpoint privato](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>Creare un endpoint privato - Registro esistente

1. Nel portale passare al registro contenitori.
1. In **Impostazioni** selezionare **Rete**.
1. Nella scheda **Endpoint privati** selezionare **+ Endpoint privato**.
1. Nella scheda **Informazioni di base** immettere o selezionare le informazioni seguenti:

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
6. Immettere o selezionare le informazioni seguenti:

    | Impostazione | valore |
    | ------- | ----- |
    |Metodo di connessione  | Selezionare **Connettersi a una risorsa di Azure nella directory**.|
    | Subscription| Selezionare la propria sottoscrizione. |
    | Tipo di risorsa | Selezionare **Microsoft.ContainerRegistry/registries**. |
    | Risorsa |Selezionare il nome del registro|
    |Sottorisorsa di destinazione |Selezionare **Registro**|
    |||
7. Selezionare **Avanti: Configurazione**.
8. Immettere o selezionare le informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    |**Rete**| |
    | Rete virtuale| Selezionare la rete virtuale in cui è distribuita la macchina virtuale, ad esempio *myDockerVMVNET*. |
    | Subnet | Selezionare la subnet, ad esempio *myDockerVMSubnet* in cui è distribuita la macchina virtuale. |
    |**Integrazione DNS privato**||
    |Integra con la zona DNS privato |Selezionare **Sì**. |
    |Zona DNS privato |Selezionare *(Nuova) privatelink.azurecr.io* |
    |||

1. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione. 
2. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

Dopo la creazione dell'endpoint privato, le impostazioni DNS nella zona privata vengono visualizzate nella pagina**Endpoint privati** del portale:

1. Nel portale passare al registro contenitori e selezionare **Impostazioni > Rete**.
1. Nella scheda **Endpoint privati** selezionare l'endpoint privato creato.
1. Nella pagina **Panoramica** rivedere le impostazioni del collegamento e le impostazioni DNS personalizzate.

  ![Impostazioni DNS dell'endpoint](./media/container-registry-private-link/private-endpoint-overview.png)

Il collegamento privato è ora configurato e pronto per l'uso.

## <a name="disable-public-access"></a>Disabilitare l'accesso pubblico

Per molti scenari è necessario disabilitare l'accesso al registro dalle reti pubbliche. Questa configurazione impedisce ai client esterni alla rete virtuale di raggiungere gli endpoint del registro. 

### <a name="disable-public-access---cli"></a>Disabilitare l'accesso pubblico - Interfaccia della riga di comando

Per disabilitare l'accesso pubblico usando l'interfaccia della riga di comando di Azure, eseguire [az acr update][az-acr-update] e impostare `--public-network-enabled` su `false`. 

> [!NOTE]
> L'argomento `public-network-enabled` richiede l'interfaccia della riga di comando di Azure 2.6.0 o versione successiva. 

```azurecli
az acr update --name $REGISTRY_NAME --public-network-enabled false
```


### <a name="disable-public-access---portal"></a>Disabilitare l'accesso pubblico - Portale

1. Nel portale passare al registro contenitori e selezionare **Impostazioni > Rete**.
1. Nella scheda **Accesso pubblico** in **Consenti l'accesso alla rete pubblica** selezionare **Disabilitato**. Selezionare quindi **Salva**.

## <a name="validate-private-link-connection"></a>Convalidare una connessione di collegamento privato

È necessario verificare che le risorse all'interno della subnet dell'endpoint privato si connettano al registro tramite un indirizzo IP privato e che l'integrazione di tali risorse con la zona DNS privato sia corretta.

Per convalidare la connessione di collegamento privato, connettersi tramite SSH alla macchina virtuale configurata nella rete virtuale.

Eseguire un'utilità, ad esempio `nslookup` o, `dig` per cercare l'indirizzo IP del registro di sistema tramite il collegamento privato. Ad esempio:

```bash
dig $REGISTRY_NAME.azurecr.io
```

L'output di esempio illustra l'indirizzo IP del registro nello spazio indirizzi della subnet:

```console
[...]
; <<>> DiG 9.11.3-1ubuntu1.13-Ubuntu <<>> myregistry.azurecr.io
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52155
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;myregistry.azurecr.io.         IN      A

;; ANSWER SECTION:
myregistry.azurecr.io.  1783    IN      CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 10 IN A      10.0.0.7

[...]
```

Confrontare questo risultato con l'indirizzo IP pubblico nell'output di `dig` per lo stesso registro su un endpoint pubblico:

```console
[...]
;; ANSWER SECTION:
myregistry.azurecr.io.  2881    IN  CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 2881 IN CNAME xxxx.xx.azcr.io.
xxxx.xx.azcr.io.    300 IN  CNAME   xxxx-xxx-reg.trafficmanager.net.
xxxx-xxx-reg.trafficmanager.net. 300 IN CNAME   xxxx.westeurope.cloudapp.azure.com.
xxxx.westeurope.cloudapp.azure.com. 10  IN A 20.45.122.144

[...]
```

### <a name="registry-operations-over-private-link"></a>Operazioni sul registro attraverso il collegamento privato

Verificare anche che sia possibile eseguire operazioni sul registro dalla macchina virtuale nella subnet. Creare una connessione SSH alla macchina virtuale ed eseguire [az acr login][az-acr-login] per accedere al registro. A seconda della configurazione della macchina virtuale, può essere necessario anteporre `sudo` ai comandi seguenti.

```bash
az acr login --name $REGISTRY_NAME
```

Eseguire operazioni sul registro, ad esempio `docker pull` per eseguire il pull di un'immagine di esempio dal registro. Sostituire `hello-world:v1` con un'immagine e un valore di tag appropriati per il registro, preceduti dal nome del server di accesso del registro di sistema (tutto minuscolo):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker esegue correttamente il pull dell'immagine nella macchina virtuale.

## <a name="manage-private-endpoint-connections"></a>Gestire le connessioni endpoint privato

Gestire le connessioni endpoint privato del registro usando il portale di Azure o tramite comandi nel gruppo di comandi [az acr private-endpoint-connection][az-acr-private-endpoint-connection]. Le operazioni possibili sono approvazione, eliminazione, elenco, rifiuto o visualizzazione dei dettagli delle connessioni endpoint privato del registro.

Per elencare le connessioni endpoint privato di un registro, ad esempio, eseguire il comando [az acr private-endpoint-connection list][az-acr-private-endpoint-connection-list]. Ad esempio:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

Quando si configura una connessione a un endpoint privato eseguendo la procedura descritta in questo articolo, il registro di sistema accetta automaticamente le connessioni da client e servizi con autorizzazioni RBAC di Azure nel registro di sistema. È possibile configurare l'endpoint in modo che richieda l'approvazione manuale delle connessioni. Per informazioni su come approvare e rifiutare connessioni endpoint privato, vedere [Gestire una connessione endpoint privato](../private-link/manage-private-endpoint.md).

## <a name="add-zone-records-for-replicas"></a>Aggiungere record della zona per le repliche

Come illustrato in questo articolo, quando si aggiunge una connessione a un endpoint privato a un registro di sistema, si creano record DNS nella `privatelink.azurecr.io` zona per il registro di sistema e i relativi endpoint dati nelle aree in cui viene [replicato](container-registry-geo-replication.md)il registro di sistema. 

Se in seguito si aggiunge una nuova replica, è necessario aggiungere manualmente un nuovo record della zona per l'endpoint dati in tale area. Se, ad esempio, si crea una replica di *myregistry* nella posizione *northeurope*, aggiungere un record della zona per `myregistry.northeurope.data.azurecr.io`. Per la procedura, vedere [Creare record DNS nella zona privata](#create-dns-records-in-the-private-zone) in questo articolo.

## <a name="dns-configuration-options"></a>Opzioni di configurazione DNS

L'endpoint privato in questo esempio si integra con una zona DNS privata associata a una rete virtuale di base. Questa installazione usa direttamente il servizio DNS fornito da Azure per risolvere il nome di dominio completo (FQDN) pubblico del registro di sistema nell'indirizzo IP privato nella rete virtuale. 

Il collegamento privato supporta scenari di configurazione DNS aggiuntivi che usano la zona privata, incluse le soluzioni DNS personalizzate. Ad esempio, si potrebbe avere una soluzione DNS personalizzata distribuita nella rete virtuale o in locale in una rete che si connette alla rete virtuale usando un gateway VPN. Per risolvere il nome di dominio completo (FQDN) pubblico del registro di sistema nell'indirizzo IP privato in questi scenari, è necessario configurare un server d'inoltre a livello di server per il servizio DNS di Azure (168.63.129.16). Le opzioni e i passaggi di configurazione esatti dipendono dalle reti esistenti e dal DNS. Per esempi, vedere [configurazione DNS dell'endpoint privato di Azure](../private-link/private-endpoint-dns.md).

## <a name="clean-up-resources"></a>Pulire le risorse

Se tutte le risorse di Azure sono state create nello stesso gruppo di risorse e non sono più necessarie, è possibile eliminare facoltativamente le risorse usando un singolo comando [az group delete](/cli/azure/group):

```azurecli
az group delete --name $RESOURCE_GROUP
```

Per pulire le risorse nel portale, passare al gruppo di risorse personale. Dopo aver caricato il gruppo di risorse, fare clic su **Elimina gruppo di risorse** per rimuovere il gruppo di risorse e le risorse archiviate all'interno di questo.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul collegamento privato, vedere la documentazione sul [collegamento privato di Azure](../private-link/private-link-overview.md).
* Per configurare regole di accesso al registro da dietro un firewall client, vedere [Configurare le regole per accedere a un registro contenitori di Azure dietro un firewall](container-registry-firewall-access-rules.md).

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
