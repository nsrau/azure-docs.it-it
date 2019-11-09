---
title: Creare un hub IoT mediante l'interfaccia della riga di comando di Azure | Documentazione Microsoft
description: Informazioni su come usare i comandi dell'interfaccia della riga di comando di Azure per creare un gruppo di risorse e quindi creare un hub Internet delle cose nel gruppo di risorse. Viene anche illustrato come rimuovere l'hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e7253747d95917e34725cc7c6c034b1306f65e50
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889507"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Creare un hub IoT usando l'interfaccia della riga di comando di Azure

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Questo articolo illustra come creare un hub IoT usando l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa procedura è necessario disporre di una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Accedere all'account Azure e impostarlo

Se si esegue l'interfaccia della riga di comando di Azure in locale anziché usare Cloud Shell, è necessario accedere al proprio account Azure.

Al prompt dei comandi eseguire il [comando per l'accesso](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli):

   ```azurecli
   az login
   ```

Seguire le istruzioni per l'autenticazione tramite il codice e accedere all'account Azure con un Web browser.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Usare l'interfaccia della riga di comando di Azure per creare un gruppo di risorse e quindi aggiungere un hub IoT.

1. Quando si crea un hub IoT, è necessario crearlo in un gruppo di risorse. Usare un gruppo di risorse esistente o eseguire questo [comando per creare un gruppo di risorse](https://docs.microsoft.com/cli/azure/resource):
    
   ```azurecli
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > L'esempio precedente crea il gruppo di risorse nella località Stati Uniti occidentali. È possibile visualizzare un elenco di località disponibili eseguendo questo comando: 
   >
   >``` bash
   >az account list-locations -o table
   >```
   >

2. Eseguire questo [comando per creare un hub IoT](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) nel gruppo di risorse, usando un nome globalmente univoco per l'hub IoT:
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Il comando precedente crea un hub IoT nel piano tariffario S1 che viene fatturato. Per altre informazioni, vedere [Azure IoT Hub Prezzi](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Rimuovere un hub IoT

È possibile usare l'interfaccia della riga di comando di Azure per [eliminare una singola risorsa](https://docs.microsoft.com/cli/azure/resource), ad esempio un hub IoT, o eliminare un gruppo di risorse e tutte le risorse correlate, inclusi gli hub IoT.

Per [eliminare un hub IoT](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete), eseguire questo comando:

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Per [eliminare un gruppo di risorse](https://docs.microsoft.com/cli/azure/group#az-group-delete) e tutte le risorse correlate, eseguire questo comando:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di un hub IoT, vedere gli articoli seguenti:

* [Guida per sviluppatori dell'hub IoT](iot-hub-devguide.md)
* [Uso del portale di Azure per gestire l'hub IoT](iot-hub-create-through-portal.md)
