---
title: Distribuire le istanze di contenitore in una rete virtuale di Azure
description: Informazioni su come distribuire gruppi di contenitori in una rete virtuale Azure nuova o esistente.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: a4da7a23d6dcb50164829507130fed145abeebbd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684174"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Distribuire le istanze di contenitore in una rete virtuale di Azure

[Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) fornisce funzionalità di rete sicura e privata per Azure e alle risorse locali. Grazie alla distribuzione di gruppi di contenitori in una rete virtuale di Azure, i contenitori possono comunicare in modo sicuro con altre risorse nella rete virtuale.

Gruppi di contenitori distribuiti in una rete virtuale di Azure abilitano scenari analoghi ai seguenti:

* Comunicazione diretta tra i gruppi di contenitori nella stessa subnet
* Invio dell'output di carichi di lavoro [basati su attività](container-instances-restart-policy.md) dalle istanze di contenitore a un database nella rete virtuale
* Recuperare contenuto per le istanze di contenitore da un [endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md) nella rete virtuale
* Comunicazione tra i contenitori e le macchine virtuali nella rete virtuale
* Comunicazione tra contenitori e risorse locali tramite un [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) oppure [ExpressRoute](../expressroute/expressroute-introduction.md)

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima e [si applicano alcune limitazioni](#preview-limitations). Le anteprime vengono rese disponibili a condizione che l'utente accetti le [condizioni supplementari per l'utilizzo][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="virtual-network-deployment-limitations"></a>Limitazioni alla distribuzione nella rete virtuale

Quando si distribuiscono gruppi di contenitori in una rete virtuale, si applicano alcune limitazioni.

* Per distribuire gruppi di contenitori in una subnet, quest'ultima non può contenere altri tipi di risorse. Rimuovere tutte le risorse presenti in una subnet esistente prima di distribuire gruppi di contenitori nella subnet oppure creare una nuova subnet.
* Non è possibile usare un'[identità gestita](container-instances-managed-identity.md) in un gruppo di contenitori distribuito in una rete virtuale.
* A causa di altre risorse di rete coinvolte, la distribuzione di un gruppo di contenitori in una rete virtuale è leggermente più lenta, in genere, rispetto a quella di un'istanza di contenitore standard.

## <a name="preview-limitations"></a>Limiti di anteprima

Quando questa funzionalità è disponibile in anteprima, le limitazioni seguenti si applicano quando si distribuiscono gruppi di contenitori a una rete virtuale. 

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

I limiti delle risorse del contenitore possono differire dai limiti delle istanze contenitore non in rete in queste aree. Attualmente sono supportati i contenitori Linux per questa funzionalità. È previsto il supporto per la versione Windows.

### <a name="unsupported-networking-scenarios"></a>Scenari di rete non supportati 

* **Azure Load Balancer** -inserimento di un Azure Load Balancer davanti alle istanze di contenitore in un gruppo di contenitori in rete non è supportato
* **Peering reti virtuali** -è possibile eseguire il peering una rete virtuale che contiene una subnet del delegato a istanze di contenitore di Azure a un'altra rete virtuale
* **Tabelle di route** -route definite dall'utente non possono essere configurate in una subnet del delegato a istanze di contenitore di Azure
* **I gruppi di sicurezza di rete** -attualmente non vengono imposte le regole di sicurezza in uscita in Nsg applicato a una subnet delegata a istanze di contenitore di Azure 
* **Etichetta indirizzo IP o DNS pubblica** -gruppi di contenitori distribuiti a una rete virtuale attualmente non supportano i contenitori che espone direttamente a internet con un indirizzo IP pubblico o un nome di dominio completo
* **Risoluzione dei nomi interna** -risoluzione dei nomi per le risorse di Azure nella rete virtuale tramite il DNS interno di Azure non è supportato

L'**eliminazione delle risorse di rete** richiede [passaggi aggiuntivi](#delete-network-resources) dopo la distribuzione di gruppi di contenitori nella rete virtuale.

## <a name="required-network-resources"></a>Risorse di rete necessarie

Per distribuire gruppi di contenitori in una rete virtuale, sono necessarie tre risorse di Rete virtuale di Azure, ovvero la [rete virtuale](#virtual-network) stessa, una [subnet delegata](#subnet-delegated) nella rete virtuale e un [profilo di rete](#network-profile). 

### <a name="virtual-network"></a>Rete virtuale

Una rete virtuale definisce lo spazio degli indirizzi in cui si creano una o più subnet. A questo punto è possibile distribuire le risorse di Azure (ad esempio i gruppi di contenitori) nelle subnet nella rete virtuale.

### <a name="subnet-delegated"></a>Subnet (delegata)

Le subnet segmentano la rete virtuale in spazi di indirizzi separati usabili dalle risorse di Azure contenute. In una rete virtuale possono essere create una o più subnet.

La subnet usata per i gruppi di contenitori può includere solo gruppi di contenitori. Quando si distribuisce un gruppo di contenitori in una subnet, Azure delega tale subnet a Istanze di Azure Container. Dopo che è stata delegata, la subnet può essere usata solo per i gruppi di contenitori. Se si prova a distribuire risorse diverse da gruppi di contenitori in una subnet delegata, l'operazione ha esito negativo.

### <a name="network-profile"></a>Profilo di rete

Un profilo di rete è un modello di configurazione di rete per le risorse di Azure. Il profilo specifica determinate proprietà di rete per la risorsa, ad esempio la subnet in cui deve essere distribuito. Quando si usa per la prima volta il comando [az container create][az-container-create] per distribuire un gruppo di contenitori in una subnet (e quindi una rete virtuale), Azure crea automaticamente un profilo di rete. È quindi possibile usare tale profilo di rete per le distribuzioni future nella subnet. 

Per usare un modello di Resource Manager, il file YAML o un metodo a livello di codice per distribuire un gruppo di contenitori in una subnet, è necessario specificare l'ID risorsa di Resource Manager completo di un profilo di rete. È possibile usare un profilo creato in precedenza usando [az container create][az-container-create] oppure creare un profilo usando un modello di Resource Manager (vedere l'[esempio di modello](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) e le [informazioni di riferimento](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles)). Per ottenere l'ID di un profilo creato in precedenza, usare il comando [az network profile list][az-network-profile-list]. 

Nel diagramma seguente diversi gruppi di contenitori sono stati distribuiti in una subnet delegata a Istanze di Azure Container. Dopo aver distribuito un gruppo di contenitori in una subnet, è possibile distribuire gruppi di contenitori aggiuntivi ala subnet specificando lo stesso profilo di rete.

![Gruppi di contenitori in una rete virtuale][aci-vnet-01]

## <a name="deployment-scenarios"></a>Scenari di distribuzione

È possibile usare [az container create][az-container-create] per distribuire gruppi di contenitori in una nuova rete virtuale e consentire ad Azure di creare automaticamente le risorse di rete necessarie oppure eseguire la distribuzione in una rete virtuale esistente. 

### <a name="new-virtual-network"></a>Nuova rete virtuale

Per eseguire la distribuzione in una nuova rete virtuale e creare automaticamente le risorse di rete per l'utente, specificare quanto segue quando si esegue il comando [az container create][az-container-create]:

* Nome della rete virtuale
* Prefisso dell'indirizzo di rete virtuale in formato CIDR
* Nome della subnet
* Prefisso dell'indirizzo della subnet in formato CIDR

I prefissi degli indirizzi della rete virtuale e della subnet specificare gli spazi degli indirizzi per la rete virtuale e per la subnet, rispettivamente. Tal valori sono rappresentati nella notazione CIDR (Classless Inter-Domain Routing), ad esempio `10.0.0.0/16`. Per altre informazioni sull'uso delle subnet, vedere [Aggiungere, modificare o eliminare le subnet di rete virtuale](../virtual-network/virtual-network-manage-subnet.md).

Dopo aver distribuito il primo gruppo di contenitori con questo metodo, è possibile eseguire la distribuzione nella stessa subnet, specificando i nomi della rete virtuale e delle subnet o il profilo di rete che Azure crea automaticamente per l'utente. Poiché Azure delega la subnet a Istanze di Azure Container, nella subnet è possibile distribuire *solo* gruppi di contenitori.

### <a name="existing-virtual-network"></a>Rete virtuale esistente

Per distribuire un gruppo di contenitori in una rete virtuale esistente:

1. Creare una subnet nella rete virtuale esistente o eliminare *tutte* le altre risorse da una subnet esistente
1. Distribuire un gruppo di contenitori con il comando [az container create][az-container-create] e specificare uno degli elementi seguenti:
   * Nome della rete virtuale e della subnet
   * ID risorsa di rete virtuale e ID risorsa della subnet, che permette di usare una rete virtuale di un gruppo di risorse diverso
   * ID o nome del profilo di rete, che è possibile ottenere tramite [az network profile list][az-network-profile-list]

Dopo aver distribuito il primo gruppo di contenitori in una subnet esistente, Azure delega tale subnet a Istanze di Azure Container. In tale subnet non è più possibile distribuire risorse diverse da gruppi di contenitori.

## <a name="deployment-examples"></a>Esempi di distribuzione

Le sezioni seguenti descrivono come distribuire gruppi di contenitori in una rete virtuale con l'interfaccia della riga di comando di Azure. Gli esempi di comando vengono formattati per la shell **Bash**. Se si preferisce un'altra shell, ad esempio PowerShell o il prompt dei comandi, modificare di conseguenza i caratteri di continuazione di riga.

### <a name="deploy-to-a-new-virtual-network"></a>Eseguire la distribuzione in una nuova rete virtuale

Distribuire in primo luogo un gruppo di contenitori e specificare i parametri per una nuova rete virtuale e una nuova subnet. Quando si specificano questi parametri, Azure crea la rete virtuale e la subnet, delega la subnet a Istanze di Azure Container e crea anche un profilo di rete. Dopo che queste risorse sono state create, il gruppo di contenitori viene distribuito nella subnet.

Eseguire il comando [az container create][az-container-create] che specifica le impostazioni per una nuova rete virtuale e una nuova subnet. È necessario fornire il nome di un gruppo di risorse creato in un'area che [supporta](#preview-limitations) i gruppi di contenitori in una rete virtuale. Questo comando consente di distribuire Microsoft pubblici [aci-helloworld] [ aci-helloworld] contenitore che esegue un piccolo server Web Node. js gestisce una pagina web statico. Nella sezione successiva viene distribuito un secondo gruppo di contenitori nella stessa subnet e viene testata la comunicazione tra le due istanze di contenitore.

```azurecli
az container create \
    --name appcontainer \
    --resource-group myResourceGroup \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --vnet aci-vnet \
    --vnet-address-prefix 10.0.0.0/16 \
    --subnet aci-subnet \
    --subnet-address-prefix 10.0.0.0/24
```

Quando si esegue la distribuzione in una nuova rete virtuale usando questo metodo, la distribuzione può richiedere alcuni minuti per la creazione delle risorse di rete. Dopo la distribuzione iniziale, le distribuzioni di gruppi di contenitori aggiuntivi vengono completate più rapidamente.

### <a name="deploy-to-existing-virtual-network"></a>Eseguire una distribuzione in una rete virtuale esistente

Ora che è stato distribuito un gruppo di contenitori in una nuova rete virtuale, distribuire un secondo gruppo di contenitori nella stessa subnet e verificare la comunicazione tra le due istanze di contenitore.

Ottenere per prima cosa l'indirizzo IP del primo gruppo di contenitori distribuito, ovvero *appcontainer*:

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

L'output deve visualizzare l'indirizzo IP del gruppo di contenitori nella subnet privata:

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

A questo punto impostare `CONTAINER_GROUP_IP` sull'indirizzo IP recuperato con il comando `az container show` ed eseguire il comando `az container create` seguente. Questo secondo contenitore, *commchecker*, esegue un'immagine basata su Alpine Linux ed esegue `wget` sull'indirizzo IP della subnet privata del primo gruppo di contenitori.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-here>

az container create \
    --resource-group myResourceGroup \
    --name commchecker \
    --image alpine:3.5 \
    --command-line "wget $CONTAINER_GROUP_IP" \
    --restart-policy never \
    --vnet aci-vnet \
    --subnet aci-subnet
```

Dopo aver completato la distribuzione del secondo contenitore, eseguire il pull dei log relativi in modo che sia possibile visualizzare l'output del comando `wget`:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Se il secondo contenitore ha comunicato correttamente con il primo, l'output deve essere simile al seguente:

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

L'output del registro illustra che `wget` è stato in grado di connettersi e scaricare il file di indice dal primo contenitore tramite il relativo indirizzo IP privato nella subnet locale. Il traffico di rete tra i due gruppi di contenitori è rimasto nella rete virtuale.

### <a name="deploy-to-existing-virtual-network---yaml"></a>Eseguire una distribuzione in una rete virtuale esistente - YAML

È anche possibile distribuire un gruppo di contenitori in una rete virtuale esistente usando un file YAML. Per distribuire una subnet in una rete virtuale, specificare varie proprietà aggiuntive nel codice YAML:

* `ipAddress`: le impostazioni dell'indirizzo IP per il gruppo di contenitori.
  * `ports`: le porte da aprire, se presenti.
  * `protocol`: il protocollo (TCP o UDP) per la porta aperta.
* `networkProfile`: specifica le impostazioni di rete, ad esempio la rete virtuale e la subnet per una risorsa di Azure.
  * `id`: l'ID completo della risorsa di Resource Manager di `networkProfile`.

Per distribuire un gruppo di contenitori in una rete virtuale con un file YAML, è necessario ottenere l'ID del profilo di rete. Eseguire il comando [az network profile list][az-network-profile-list], specificando il nome del gruppo di risorse che contiene la rete virtuale e la subnet delegata.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

L'output del comando visualizza l'ID di risorsa completo per il profilo di rete:

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Dopo aver ottenuto l'ID del profilo di rete, copiare il codice YAML seguente in un nuovo file denominato *vnet-deploy-aci.yaml*. In `networkProfile` sostituire il valore `id` con l'ID recuperato, quindi salvare il file. Il codice YAML seguente crea un gruppo di contenitori denominato *appcontaineryaml* nella rete virtuale.

```YAML
apiVersion: '2018-09-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/container/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Distribuire il gruppo di contenitori con il comando [az container create][az-container-create], specificando il nome di file YAML per il parametro `--file`:

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

Dopo il completamento della distribuzione, eseguire il comando [az container show][az-container-show] per distribuirne lo stato:

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Pulire le risorse

### <a name="delete-container-instances"></a>Eliminare le istanze di contenitore

Dopo aver usato le istanze di contenitore create, eliminarle con i comandi seguenti:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Eliminare le risorse di rete

L'anteprima iniziale di questa funzionalità richiede altri comandi per eliminare le risorse di rete create in precedenza. Se sono stati usati i comandi di esempio nelle sezioni precedenti di questo articolo per creare la rete virtuale e la subnet, è possibile usare lo script seguente per eliminare le risorse di rete.

Prima di eseguire lo script, impostare la variabile `RES_GROUP` sul nome del gruppo di risorse contenente la rete virtuale e la subnet da eliminare. Lo script viene formattato per la shell Bash. Se si preferisce un'altra shell, ad esempio PowerShell o il prompt dei comandi, è necessario modificare di conseguenza l'assegnazione di variabili e le funzioni di accesso.

> [!WARNING]
> Questo script elimina le risorse. Elimina la rete virtuale e tutte le subnet contenute. Verificare che non sia più necessaria *alcuna* risorsa nella rete virtuale, ad esempio le subnet che contiene, prima di eseguire lo script. Dopo l'eliminazione, **tali risorse non sono recuperabili**.

```azurecli
# Replace <my-resource-group> with the name of your resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Get the service association link (SAL) ID
SAL_ID=$(az network vnet subnet show --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet --query id --output tsv)/providers/Microsoft.ContainerInstance/serviceAssociationLinks/default

# Delete the default SAL ID for the subnet
az resource delete --ids $SAL_ID --api-version 2018-07-01

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet --remove delegations 0

# Delete the subnet
az network vnet subnet delete --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Passaggi successivi

Per distribuire una nuova rete virtuale, subnet, profilo di rete e gruppo di contenitori usando un modello di Resource Manager, vedere [Create an Azure container group with VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
) (Creare un gruppo di contenitori di Azure con VNet).

Sebbene brevemente, questo articolo ha illustrato diverse risorse di rete virtuale e funzionalità. La documentazione di Rete virtuale di Azure descrive in modo esteso gli argomenti seguenti:

* [Rete virtuale](../virtual-network/manage-virtual-network.md)
* [Subnet](../virtual-network/virtual-network-manage-subnet.md)
* [Endpoint servizio](../virtual-network/virtual-network-service-endpoints-overview.md)
* [Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [ExpressRoute](../expressroute/expressroute-introduction.md)

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
