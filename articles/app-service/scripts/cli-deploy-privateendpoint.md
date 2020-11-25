---
title: "Interfaccia della riga di comando: Distribuire un endpoint privato per l'app Web con l'interfaccia della riga di comando di Azure"
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per distribuire un endpoint privato per l'app Web
author: ericgre
ms.assetid: a56faf72-7237-41e7-85ce-da8346f2bcaa
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/06/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 7246bb3453f6e4863faf15dbcbfdf8534481ddad
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561427"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-cli"></a>Creare un'app del servizio app e distribuire un endpoint privato con l'interfaccia della riga di comando di Azure

Questo script di esempio crea un'app nel servizio app con le relative risorse e quindi distribuisce un endpoint privato.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Questa esercitazione richiede la versione 2.0.28 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per poter creare le risorse, è prima necessario creare un gruppo di risorse in cui ospitare l'app Web, la rete virtuale e altri componenti di rete. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group). Con questo esempio viene creato un gruppo di risorse denominato *myResourceGroup* nella località *francecentral*:

```azurecli-interactive
az group create --name myResourceGroup --location francecentral 
```

## <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

È necessario creare un piano di servizio app per ospitare l'app Web.
Per creare un piano di servizio app, usare il comando [az appservice plan create](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create).
Con questo esempio viene creato un piano di servizio app denominato *myAppServicePlan* nella località *francecentral* con SKU *P1V2* e un solo ruolo di lavoro: 

```azurecli-interactive
az appservice plan create \
--name myAppServicePlan \
--resource-group myResourceGroup \
--location francecentral \
--sku P1V2 \
--number-of-workers 1
```

## <a name="create-a-web-app"></a>Creare un'app Web

Dopo aver creato un piano di servizio app, è possibile distribuire un'app Web.
Per creare un'app Web, usare il comando [az appservice plan create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create.
Con questo esempio viene creata un'app Web denominata *mySiteName* nel piano denominato *myAppServicePlan*

```azurecli-interactive
az webapp create \
--name mySiteName \
--resource-group myResourceGroup \
--plan myAppServicePlan
```

## <a name="create-a-vnet"></a>Creare una rete virtuale

Creare una rete virtuale con il comando [az network vnet create](/cli/azure/network/vnet). Con questo esempio viene creata una rete virtuale predefinita denominata *myVNet* con una subnet denominata *mySubnet*:

```azurecli-interactive
az network vnet create \
--name myVNet \
--resource-group myResourceGroup \
--location francecentral \
--address-prefixes 10.8.0.0/16 \
--subnet-name mySubnet \
--subnet-prefixes 10.8.100.0/24
```

## <a name="configure-the-subnet"></a>Configurare la subnet 

È necessario aggiornare la subnet per disabilitare i criteri di rete degli endpoint privati. Aggiornare una configurazione di subnet denominata *mySubnet* con [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
--name mySubnet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--disable-private-endpoint-network-policies true
```

## <a name="create-the-private-endpoint"></a>Creare l'endpoint privato

Per creare l'endpoint privato per l'app Web, usare il comando [az network private-endpoint create](/cli/azure/network/private-endpoint). Con questo esempio viene creato un endpoint privato denominato *myPrivateEndpoint* nella rete virtuale *myVNet* nella subnet *mySubnet* con una connessione denominata *myConnectionName* all'ID risorsa dell'app Web /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp. Il valore del parametro group per il tipo di app Web è *sites*. 

```azurecli-interactive
az network private-endpoint create \
--name myPrivateEndpoint \
--resource-group myResourceGroup \
--vnet-name myVNet \
--subnet mySubnet \
--connection-name myConnectionName \
--private-connection-resource-id /subscriptions/SubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/myWebApp \
--group-id sites
```

## <a name="configure-the-private-zone"></a>Configurare la zona privata

Come ultima operazione, per risolvere il nome DNS dell'app Web, è necessario creare una zona DNS privata denominata *privatelink.azurewebsites.net* collegata alla rete virtuale.


```azurecli-interactive
az network private-dns zone create \
--name privatelink.azurewebsites.net \
--resource-group myResourceGroup

az network private-dns link vnet create \
--name myDNSLink \
--resource-group myResourceGroup \
--registration-enabled false \
--virtual-network myVNet \
--zone-name privatelink.azurewebsites.net

az network private-endpoint dns-zone-group create \
--name myZoneGroup \
--resource-group myResourceGroup \
--endpoint-name myPrivateEndpoint \
--private-dns-zone privatelink.azurewebsites.net \
--zone-name privatelink.azurewebsites.net
```






[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]


## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).
- Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure](../samples-cli.md).