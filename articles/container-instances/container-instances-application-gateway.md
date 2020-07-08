---
title: Indirizzo IP statico per il gruppo di contenitori
description: Creare un gruppo di contenitori in una rete virtuale e usare un gateway applicazione di Azure per esporre un indirizzo IP front-end statico a un'app Web in contenitori
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: a27cf20b7d04fedb0b9e0ab408de24d37f2935c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84299163"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Esporre un indirizzo IP statico per un gruppo di contenitori

Questo articolo illustra un modo per esporre un indirizzo IP pubblico statico per un [gruppo di contenitori](container-instances-container-groups.md) usando un [gateway applicazione](../application-gateway/overview.md)di Azure. Seguire questa procedura quando è necessario un punto di ingresso statico per un'app con contenitore esterno che viene eseguita in istanze di contenitore di Azure. 

In questo articolo si usa l'interfaccia della riga di comando di Azure per creare le risorse per questo scenario:

* Una rete virtuale di Azure
* Un gruppo di contenitori distribuito [nella rete virtuale](container-instances-vnet.md) che ospita una piccola app Web
* Un gateway applicazione con un indirizzo IP front-end pubblico, un listener per ospitare un sito Web sul gateway e una route per il gruppo di contenitori back-end

Finché viene eseguito il gateway applicazione e il gruppo di contenitori espone un indirizzo IP privato stabile nella subnet delegata della rete, il gruppo di contenitori è accessibile da questo indirizzo IP pubblico.

> [!NOTE]
> Azure addebita il costo di un gateway applicazione in base alla quantità di tempo di provisioning e disponibilità del gateway, oltre alla quantità di dati elaborati. Vedi i [prezzi](https://azure.microsoft.com/pricing/details/application-gateway/).

## <a name="create-virtual-network"></a>Creare una rete virtuale

In un caso tipico, è possibile che si disponga già di una rete virtuale di Azure. Se non è disponibile, crearne uno come illustrato con i comandi di esempio seguenti. La rete virtuale necessita di subnet separate per il gateway applicazione e il gruppo di contenitori.

Se necessario, creare un gruppo di risorse di Azure. Ad esempio:

```azureci
az group create --name myResourceGroup --location eastus
```

Creare una rete virtuale con il comando [az network vnet create][az-network-vnet-create]. Questo comando crea la subnet *myAGSubnet* nella rete.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

Usare il comando [AZ Network VNET subnet create][az-network-vnet-subnet-create] per creare una subnet per il gruppo di contenitori back-end. Qui è denominato *myACISubnet*.

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Usare il comando [AZ Network Public-IP create][az-network-public-ip-create] per creare una risorsa IP pubblico statica. In un passaggio successivo, questo indirizzo viene configurato come front-end del gateway applicazione.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Creare un gruppo di contenitori

Eseguire il comando [AZ container create][az-container-create] seguente per creare un gruppo di contenitori nella rete virtuale configurata nel passaggio precedente. 

Il gruppo viene distribuito nella subnet *myACISubnet* e contiene una singola istanza denominata *appcontainer* che esegue il pull dell' `aci-helloworld` immagine. Come illustrato negli altri articoli della documentazione, questa immagine crea un pacchetto di un'app Web di piccole dimensioni scritta in Node.js che funge da pagina HTML statica. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

Quando la distribuzione è stata completata, al gruppo di contenitori viene assegnato un indirizzo IP privato nella rete virtuale. Ad esempio, eseguire il comando [AZ container Show][az-container-show] seguente per recuperare l'indirizzo IP del gruppo:

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

L'output è simile a: `10.0.2.4`.

Per l'uso in un passaggio successivo, salvare l'indirizzo IP in una variabile di ambiente:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

## <a name="create-application-gateway"></a>Creare il gateway applicazione

Creare un gateway applicazione nella rete virtuale attenendosi alla procedura descritta nella [Guida introduttiva del gateway applicazione](../application-gateway/quick-create-cli.md). Il comando [AZ Network Application-Gateway create][az-network-application-gateway-create] seguente crea un gateway con un indirizzo IP front-end pubblico e una route per il gruppo di contenitori back-end. Per informazioni dettagliate sulle impostazioni del gateway, vedere la [documentazione del gateway applicazione](/azure/application-gateway/) .

```azurecli
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroup \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-protocol http \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$ACI_IP" 
```


La creazione del gateway applicazione da parte di Azure può richiedere fino a 15 minuti. 

## <a name="test-public-ip-address"></a>Test indirizzo IP pubblico
  
A questo punto è possibile testare l'accesso all'app Web in esecuzione nel gruppo di contenitori dietro il gateway applicazione.

Eseguire il comando [AZ Network Public-IP Show][az-network-public-ip-show] per recuperare l'indirizzo IP pubblico front-end del gateway:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

L'output è un indirizzo IP pubblico, simile a: `52.142.18.133` .

Per visualizzare l'app Web in esecuzione quando la configurazione è stata completata, passare all'indirizzo IP pubblico del gateway nel browser. L'accesso riuscito è simile al seguente:

![Screenshot del browser che mostra l'applicazione in esecuzione in un'istanza di contenitore di Azure](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>Passaggi successivi

* Vedere un [modello di avvio rapido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) per creare un gruppo di contenitori con un'istanza del contenitore Wordpress come server back-end dietro un gateway applicazione.
* È anche possibile configurare un gateway applicazione con un certificato per la terminazione SSL. Vedere la [Panoramica](../application-gateway/ssl-overview.md) e l' [esercitazione](../application-gateway/create-ssl-portal.md).
* A seconda dello scenario, prendere in considerazione l'uso di altre soluzioni di bilanciamento del carico di Azure con istanze di contenitore di Azure. Ad esempio, usare [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md) per distribuire il traffico tra più istanze di contenitore e tra più aree. Vedere questo [post di blog](https://aaronmsft.com/posts/azure-container-instances/).

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show