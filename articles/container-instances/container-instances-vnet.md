---
title: Distribuire un gruppo di contenitori in rete virtuale di Azure
description: Informazioni su come distribuire un gruppo di contenitori in una rete virtuale di Azure nuova o esistente usando l'interfaccia della riga di comando di Azure.
ms.topic: article
ms.date: 07/02/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: c5eff486299a3974e8fb9b4b12d6bb74880c48d8
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89569391"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Distribuire le istanze di contenitore in una rete virtuale di Azure

In [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) sono disponibili funzionalità per implementare una rete sicura e privata per le risorse locali e di Azure. Grazie alla distribuzione di gruppi di contenitori in una rete virtuale di Azure, i contenitori possono comunicare in modo sicuro con altre risorse nella rete virtuale.

Questo articolo illustra come usare il comando [AZ container create][az-container-create] nell'interfaccia della riga di comando di Azure per distribuire gruppi di contenitori in una nuova rete virtuale o in una rete virtuale esistente. 

Per gli scenari e le limitazioni di rete, vedere [scenari di rete virtuale e risorse per istanze di contenitore di Azure](container-instances-virtual-network-concepts.md).

> [!IMPORTANT]
> La distribuzione del gruppo di contenitori in una rete virtuale è disponibile a livello generale per i contenitori Linux, nella maggior parte delle aree in cui sono disponibili istanze di contenitore di Azure. Per informazioni dettagliate, vedere [aree e disponibilità delle risorse](container-instances-virtual-network-concepts.md#where-to-deploy). 

Gli esempi in questo articolo sono formattati per la shell bash. Se si preferisce un'altra shell, ad esempio PowerShell o il prompt dei comandi, modificare di conseguenza i caratteri di continuazione di riga.


## <a name="deploy-to-new-virtual-network"></a>Eseguire una distribuzione in una nuova rete virtuale

Per eseguire la distribuzione in una nuova rete virtuale e creare automaticamente le risorse di rete per l'utente, specificare quanto segue quando si esegue il comando [az container create][az-container-create]:

* Nome della rete virtuale
* Prefisso dell'indirizzo di rete virtuale in formato CIDR
* Nome della subnet
* Prefisso dell'indirizzo della subnet in formato CIDR

I prefissi degli indirizzi della rete virtuale e della subnet specificare gli spazi degli indirizzi per la rete virtuale e per la subnet, rispettivamente. Tal valori sono rappresentati nella notazione CIDR (Classless Inter-Domain Routing), ad esempio `10.0.0.0/16`. Per altre informazioni sull'uso delle subnet, vedere [Aggiungere, modificare o eliminare le subnet di rete virtuale](../virtual-network/virtual-network-manage-subnet.md).

Dopo aver distribuito il primo gruppo di contenitori con questo metodo, è possibile eseguire la distribuzione nella stessa subnet, specificando i nomi della rete virtuale e delle subnet o il profilo di rete che Azure crea automaticamente per l'utente. Poiché Azure delega la subnet a Istanze di Azure Container, nella subnet è possibile distribuire *solo* gruppi di contenitori.

### <a name="example"></a>Esempio

Il comando [AZ container create][az-container-create] seguente specifica le impostazioni per una nuova rete virtuale e una nuova subnet. Specificare il nome di un gruppo di risorse creato in un'area in cui sono [disponibili](container-instances-region-availability.md#availability---virtual-network-deployment)le distribuzioni dei gruppi di contenitori in una rete virtuale. Questo comando distribuisce il contenitore Microsoft [ACI-HelloWorld][aci-helloworld] pubblico che esegue un piccolo Node.js server Web che serve una pagina Web statica. Nella sezione successiva viene distribuito un secondo gruppo di contenitori nella stessa subnet e viene testata la comunicazione tra le due istanze di contenitore.

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

Quando si esegue la distribuzione in una nuova rete virtuale usando questo metodo, la distribuzione può richiedere alcuni minuti per la creazione delle risorse di rete. Dopo la distribuzione iniziale, le distribuzioni di gruppi di contenitori aggiuntivi alla stessa subnet vengono completate più rapidamente.

## <a name="deploy-to-existing-virtual-network"></a>Eseguire una distribuzione in una rete virtuale esistente

Per distribuire un gruppo di contenitori in una rete virtuale esistente:

1. Creare una subnet all'interno della rete virtuale esistente, usare una subnet esistente in cui è già distribuito un gruppo di contenitori o usare una subnet esistente svuotata di *tutte* le altre risorse
1. Distribuire un gruppo di contenitori con il comando [az container create][az-container-create] e specificare uno degli elementi seguenti:
   * Nome della rete virtuale e della subnet
   * ID risorsa di rete virtuale e ID risorsa della subnet, che permette di usare una rete virtuale di un gruppo di risorse diverso
   * ID o nome del profilo di rete, che è possibile ottenere tramite [az network profile list][az-network-profile-list]

### <a name="example"></a>Esempio

Nell'esempio seguente viene distribuito un secondo gruppo di contenitori nella stessa subnet creata in precedenza e viene verificata la comunicazione tra le due istanze del contenitore.

Ottenere per prima cosa l'indirizzo IP del primo gruppo di contenitori distribuito, ovvero *appcontainer*:

```azurecli
az container show --resource-group myResourceGroup \
  --name appcontainer \
  --query ipAddress.ip --output tsv
```

L'output Visualizza l'indirizzo IP del gruppo di contenitori nella subnet private. Ad esempio:

```console
10.0.0.4
```

A questo punto impostare `CONTAINER_GROUP_IP` sull'indirizzo IP recuperato con il comando `az container show` ed eseguire il comando `az container create` seguente. Questo secondo contenitore, *commchecker*, esegue un'immagine basata su Alpine Linux ed esegue `wget` sull'indirizzo IP della subnet privata del primo gruppo di contenitori.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-address>

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

Se il secondo contenitore ha comunicato correttamente con il primo, l'output è simile al seguente:

```console
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

L'output del registro illustra che `wget` è stato in grado di connettersi e scaricare il file di indice dal primo contenitore tramite il relativo indirizzo IP privato nella subnet locale. Il traffico di rete tra i due gruppi di contenitori è rimasto nella rete virtuale.

### <a name="example---yaml"></a>Esempio: YAML

È anche possibile distribuire un gruppo di contenitori in una rete virtuale esistente usando un file YAML, un [modello di gestione risorse](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)o un altro metodo programmatico, ad esempio con Python SDK. 

Quando si usa un file YAML, ad esempio, è possibile eseguire la distribuzione in una rete virtuale con una subnet delegata alle istanze di contenitore di Azure. Specificare le proprietà seguenti:

* `ipAddress`: Impostazioni dell'indirizzo IP privato per il gruppo di contenitori.
  * `ports`: porte da aprire, se presenti.
  * `protocol`: protocollo (TCP o UDP) per la porta aperta.
* `networkProfile`: Impostazioni di rete per la rete virtuale e la subnet.
  * `id`: ID completo della risorsa di Resource Manager dell'elemento `networkProfile`.

Per ottenere l'ID del profilo di rete, eseguire il comando [AZ network profile list][az-network-profile-list] , specificando il nome del gruppo di risorse che contiene la rete virtuale e la subnet delegata.

``` azurecli
az network profile list --resource-group myResourceGroup \
  --query [0].id --output tsv
```

Output di esempio:

```console
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Dopo aver ottenuto l'ID del profilo di rete, copiare il codice YAML seguente in un nuovo file denominato *vnet-deploy-aci.yaml*. In `networkProfile` sostituire il valore `id` con l'ID recuperato, quindi salvare il file. Il codice YAML seguente crea un gruppo di contenitori denominato *appcontaineryaml* nella rete virtuale.

```YAML
apiVersion: '2019-12-01'
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
    id: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Distribuire il gruppo di contenitori con il comando [AZ container create][az-container-create] , specificando il nome del file YAML per il `--file` parametro:

```azurecli
az container create --resource-group myResourceGroup \
  --file vnet-deploy-aci.yaml
```

Al termine della distribuzione, eseguire il comando [AZ container Show][az-container-show] per visualizzarne lo stato. Output di esempio:

```console
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

Questa funzionalità richiede attualmente diversi comandi aggiuntivi per eliminare le risorse di rete create in precedenza. Se sono stati usati i comandi di esempio nelle sezioni precedenti di questo articolo per creare la rete virtuale e la subnet, è possibile usare lo script seguente per eliminare le risorse di rete. Lo script presuppone che il gruppo di risorse contenga una singola rete virtuale con un singolo profilo di rete.

Prima di eseguire lo script, impostare la variabile `RES_GROUP` sul nome del gruppo di risorse contenente la rete virtuale e la subnet da eliminare. Se non è stato usato il nome suggerito in precedenza, aggiornare il nome della rete virtuale `aci-vnet` . Lo script viene formattato per la shell Bash. Se si preferisce un'altra shell, ad esempio PowerShell o il prompt dei comandi, è necessario modificare di conseguenza l'assegnazione di variabili e le funzioni di accesso.

> [!WARNING]
> Questo script elimina le risorse. Elimina la rete virtuale e tutte le subnet contenute. Verificare che non sia più necessaria *alcuna* risorsa nella rete virtuale, ad esempio le subnet che contiene, prima di eseguire lo script. Dopo l'eliminazione, **tali risorse non sono recuperabili**.

```azurecli
# Replace <my-resource-group> with the name of your resource group
# Assumes one virtual network in resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
# Assumes one profile in virtual network
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Passaggi successivi

Per distribuire una nuova rete virtuale, subnet, profilo di rete e gruppo di contenitori usando un modello di Resource Manager, vedere [Create an Azure container group with VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
) (Creare un gruppo di contenitori di Azure con VNet).

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
