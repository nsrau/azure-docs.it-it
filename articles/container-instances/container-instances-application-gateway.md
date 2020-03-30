---
title: Indirizzo IP statico per il gruppo di contenitoriStatic IP address for container group
description: Creare un gruppo di contenitori in una rete virtuale e usare un gateway applicazione di Azure per esporre un indirizzo IP front-end statico a un'app Web con contenitoreCreate a container group in a virtual network and use an Azure application gateway to expose a static frontend IP address to a containerized web app
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 5c3a14f93af3ecc614dc296f0a4d2815d7a64a66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481790"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Esporre un indirizzo IP statico per un gruppo di contenitoriExpose a static IP address for a container group

Questo articolo illustra un modo per esporre un indirizzo IP pubblico statico per un gruppo di [contenitori](container-instances-container-groups.md) usando un gateway applicazione di Azure.This article shows one way to expose a static, public IP address for a container group by using an Azure [application gateway](../application-gateway/overview.md). Seguire questi passaggi quando è necessario un punto di ingresso statico per un'app con contenitori con connessione esterna che viene eseguita nelle istanze del contenitore di Azure.Follow these steps when you need a static entry point for an external-facing containerized app that runs in Azure Container Instances. 

In questo articolo si usa l'interfaccia della riga di comando di Azure per creare le risorse per questo scenario:In this article you use the Azure CLI to create the resources for this scenario:

* Una rete virtuale di Azure
* Gruppo di contenitori distribuito [nella rete virtuale (anteprima)](container-instances-vnet.md) che ospita un'app Web di piccole dimensioni
* Un gateway applicazione con un indirizzo IP front-end pubblico, un listener per ospitare un sito Web nel gateway e una route al gruppo di contenitori back-end

Finché il gateway applicazione viene eseguito e il gruppo di contenitori espone un indirizzo IP privato stabile nella subnet delegata della rete, il gruppo di contenitori è accessibile a questo indirizzo IP pubblico.

> [!NOTE]
> Azure addebita per un gateway applicazione in base alla quantità di tempo di provisioning e disponibilità del gateway, nonché alla quantità di dati elaborato. Vedi [prezzi](https://azure.microsoft.com/pricing/details/application-gateway/).

## <a name="create-virtual-network"></a>Creare una rete virtuale

In un caso tipico, si potrebbe già avere una rete virtuale di Azure.In a typical case, you might already have an Azure virtual network. Se non si dispone di uno, crearne uno come mostrato con i seguenti comandi di esempio. La rete virtuale necessita di subnet separate per il gateway applicazione e il gruppo di contenitori.

Se è necessario, creare un gruppo di risorse di Azure.If you need one, create an Azure resource group. Ad esempio:

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

Utilizzare il comando [az network vnet subnet create][az-network-vnet-subnet-create] per creare una subnet per il gruppo di contenitori back-end. Qui è denominato *myACISubnet*.

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Utilizzare il comando [az network public-ip create][az-network-public-ip-create] per creare una risorsa IP pubblica statica. In un passaggio successivo, questo indirizzo viene configurato come front-end del gateway applicazione.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Creare un gruppo di contenitori

Eseguire il [contenitore az][az-container-create] seguente per creare un gruppo di contenitori nella rete virtuale configurata nel passaggio precedente. 

Il gruppo viene distribuito nella subnet *myACISubnet* e contiene una `aci-helloworld` singola istanza denominata *appcontainer* che esegue il pull dell'immagine. Come illustrato in altri articoli della documentazione, questa immagine crea un pacchetto di una piccola app Web scritta in Node.js che serve una pagina HTML statica. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

Se distribuito correttamente, al gruppo di contenitori viene assegnato un indirizzo IP privato nella rete virtuale. Ad esempio, eseguire il seguente comando az container show per recuperare l'indirizzo IP del gruppo:For example, run the following [az container show][az-container-show] command to retrieve the group's IP address:

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

L'output è simile a: `10.0.2.4`.

Per utilizzarlo in un passaggio successivo, salvare l'indirizzo IP in una variabile di ambiente:For use in a later step, save the IP address in an environment variable:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

## <a name="create-application-gateway"></a>Creare il gateway applicazione

Creare un gateway applicazione nella rete virtuale seguendo la procedura della guida introduttiva del [gateway applicazione.](../application-gateway/quick-create-cli.md) Il comando [az network application-gateway create][az-network-application-gateway-create] di following crea un gateway con un indirizzo IP front-end pubblico e una route al gruppo di contenitori back-end. Per informazioni dettagliate sulle impostazioni del gateway, vedere la [documentazione del gateway applicazione.](/azure/application-gateway/)

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


La creazione del gateway applicazione può richiedere fino a 15 minuti. 

## <a name="test-public-ip-address"></a>Testare l'indirizzo IP pubblico
  
Ora è possibile testare l'accesso all'app Web in esecuzione nel gruppo di contenitori dietro il gateway applicazione.

Eseguire il comando [az network public-ip show][az-network-public-ip-show] per recuperare l'indirizzo IP pubblico front-end del gateway:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

L'output è un indirizzo `52.142.18.133`IP pubblico, simile a: .

Per visualizzare l'app Web in esecuzione una volta configurata correttamente, passare all'indirizzo IP pubblico del gateway nel browser. L'accesso riuscito è simile al:

![Screenshot del browser che mostra l'applicazione in esecuzione in un'istanza di contenitore di Azure](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>Passaggi successivi

* Vedere un [modello di avvio rapido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) per creare un gruppo di contenitori con un'istanza del contenitore WordPress come server back-end dietro un gateway applicazione.
* È inoltre possibile configurare un gateway applicazione con un certificato per la terminazione SSL. Vedere la [panoramica](../application-gateway/ssl-overview.md) e [l'esercitazione](../application-gateway/create-ssl-portal.md).
* A seconda dello scenario, è consigliabile usare altre soluzioni di bilanciamento del carico di Azure con le istanze del contenitore di Azure.Depending on your scenario, consider using other Azure load-balancing solutions with Azure Container Instances. Ad esempio, usare [Gestione traffico](../traffic-manager/traffic-manager-overview.md) di Azure per distribuire il traffico tra più istanze del contenitore e tra più aree. Vedere questo [post di blog](https://aaronmsft.com/posts/azure-container-instances/).

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show