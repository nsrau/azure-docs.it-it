---
title: Creare un hub IoT mediante l'interfaccia della riga di comando di Azure | Documentazione Microsoft
description: Informazioni su come usare i comandi dell'interfaccia della riga di comando di Azure per creare un gruppo di risorse e quindi creare un hub Internet delle cose nel gruppo di risorse. Viene anche illustrato come rimuovere l'hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e9757b94330f889869080a0e961ab2728cee86f6
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659931"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Creare un hub IoT usando l'interfaccia della riga di comando di Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Questo articolo illustra come creare un hub IoT usando l'interfaccia della riga di comando di Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Usare l'interfaccia della riga di comando di Azure per creare un gruppo di risorse e quindi aggiungere un hub IoT.

1. Quando si crea un hub IoT, è necessario crearlo in un gruppo di risorse. Usare un gruppo di risorse esistente o eseguire questo [comando per creare un gruppo di risorse](/cli/azure/resource):
    
   ```azurecli-interactive
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > L'esempio precedente crea il gruppo di risorse nella località Stati Uniti occidentali. È possibile visualizzare un elenco di località disponibili eseguendo questo comando: 
   >
   > ```azurecli-interactive
   > az account list-locations -o table
   > ```
   >

2. Eseguire il seguente [comando per creare un hub IoT](/cli/azure/iot/hub#az-iot-hub-create) nel gruppo di risorse, usando un nome globalmente univoco per l'hub IoT:
    
   ```azurecli-interactive
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Il comando precedente crea un hub IoT nel piano tariffario S1 che viene fatturato. Per altre informazioni, vedere [Azure IoT Hub Prezzi](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Rimuovere un hub IoT

È possibile usare l'interfaccia della riga di comando di Azure per [eliminare una singola risorsa](/cli/azure/resource), ad esempio un hub IoT, o eliminare un gruppo di risorse e tutte le risorse correlate, inclusi gli hub IoT.

Per [eliminare un hub](/cli/azure/iot/hub#az-iot-hub-delete)Internet delle cose, eseguire il comando seguente:

```azurecli-interactive
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Per [eliminare un gruppo di risorse](/cli/azure/group#az-group-delete) e tutte le relative risorse, eseguire il comando seguente:

```azurecli-interactive
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di un hub IoT, vedere gli articoli seguenti:

* [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md)
* [Uso del portale di Azure per gestire l'hub IoT](iot-hub-create-through-portal.md)