---
title: Gestire IoT Central dall'interfaccia della riga di comando di Azure | Microsoft Docs
description: Questo articolo descrive come creare e gestire l'applicazione IoT Central usando l'interfaccia della riga di comando. È possibile visualizzare, modificare e rimuovere l'applicazione tramite l'interfaccia della riga di comando.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: philmea
ms.openlocfilehash: 201318a5a5680f248b831bb480888f106286fbe1
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660033"
---
# <a name="manage-iot-central-from-azure-cli"></a>Gestire IoT Central dall'interfaccia della riga di comando di Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Invece di creare e gestire applicazioni IoT Central nel sito Web di [azure IOT Central Application Manager](https://aka.ms/iotcentral) , è possibile usare l'interfaccia della riga di comando di [Azure](/cli/azure/) per gestire le applicazioni.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Se è necessario eseguire i comandi dell'interfaccia della riga di comando in una sottoscrizione di Azure diversa, vedere [modificare la sottoscrizione attiva](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription).

## <a name="create-an-application"></a>Creare un'applicazione

Usare il comando [AZ All Central app create](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-create) per creare un'applicazione IoT Central nella sottoscrizione di Azure. Ad esempio:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

Questi comandi creano prima di tutto un gruppo di risorse nell'area Stati Uniti orientali per l'applicazione. La tabella seguente descrive i parametri usati con il comando **AZ All Central app create** :

| Parametro         | Descrizione |
| ----------------- | ----------- |
| resource-group    | Gruppo di risorse che contiene l'applicazione. Questo gruppo di risorse deve già esistere nella sottoscrizione. |
| posizione          | Per impostazione predefinita, questo comando usa il percorso del gruppo di risorse. Attualmente, è possibile creare un'applicazione IoT Central in **Australia**, **Asia Pacifico**, **Europa**, **Stati Uniti**, **Regno Unito** e geografia del **Giappone** . |
| name              | Nome dell'applicazione nel portale di Azure. |
| sottodominio         | Sottodominio nell'URL dell'applicazione. In questo esempio l'URL dell'applicazione è `https://mysubdomain.azureiotcentral.com`. |
| sku               | Attualmente, è possibile usare **ST1** o **ST2**. Vedere [Prezzi di Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| template          | Modello di applicazione da usare. Per altre informazioni, vedere la tabella seguente. |
| display-name      | Nome dell'applicazione visualizzato nell'interfaccia utente. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Visualizzare le proprie applicazioni

Usare il comando [AZ Internet app list](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-list) per elencare le applicazioni IoT Central e visualizzare i metadati.

## <a name="modify-an-application"></a>Modificare un'applicazione

Usare il comando [AZ All Central App Update](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-update) per aggiornare i metadati di un'applicazione IoT Central. Ad esempio, per modificare il nome visualizzato dell'applicazione creata:

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Rimuovere un'applicazione

Usare il comando [AZ All Central app Delete](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-delete) per eliminare un'applicazione IoT Central. Ad esempio:

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire le applicazioni IoT Central di Azure dall'interfaccia della riga di comando di Azure, ecco il passaggio successivo suggerito:

> [!div class="nextstepaction"]
> [Amministrare l'applicazione](howto-administer.md)
