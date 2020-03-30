---
title: Gestire Centro IoT dall'interfaccia della riga di comando di Azure Documenti Microsoft
description: In questo articolo viene descritto come creare e gestire l'applicazione IoT Central tramite l'interfaccia della riga di comando. È possibile visualizzare, modificare e rimuovere l'applicazione utilizzando l'interfaccia della riga di comando.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: df24a2dc6e9bd058a2f8b1355b8760653ed3128a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365525"
---
# <a name="manage-iot-central-from-azure-cli"></a>Gestire IoT Central dall'interfaccia della riga di comando di AzureManage IoT Central from Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Anziché creare e gestire applicazioni IoT Central nel sito Web della gestione delle applicazioni di [Azure IoT Central,](https://aka.ms/iotcentral) è possibile usare l'interfaccia della riga di comando di [Azure](/cli/azure/) per gestire le applicazioni.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si preferisce eseguire l'interfaccia della riga di comando di Azure nel computer locale, vedere [Installare l'interfaccia della riga di comando](/cli/azure/install-azure-cli)di Azure. Quando si esegue l'interfaccia della riga di comando di Azure in locale, usare il comando **az login** per accedere ad Azure prima di provare i comandi in questo articolo.

> [!TIP]
> Se è necessario eseguire i comandi dell'interfaccia della riga di comando in una sottoscrizione di Azure diversa, vedere [Modificare la sottoscrizione attiva.](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription)

## <a name="install-the-extension"></a>Installare l'estensione

I comandi in questo articolo fanno parte dell'estensione dell'interfaccia della riga di comando **azure-iot.The** commands in this article are part of the azure-iot CLI extension. Eseguire il comando seguente per installare l'estensione:

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="create-an-application"></a>Creare un'applicazione

Usare il comando az iotcentral app create per creare un'applicazione IoT Central nella sottoscrizione di Azure.Use the [az iotcentral app create](/cli/azure/iotcentral/app#az-iotcentral-app-create) command to create an IoT Central application in your Azure subscription. Ad esempio:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iotcentral app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview@1.0.0" \
  --display-name "My Custom Display Name"
```

Questi comandi creano innanzitutto un gruppo di risorse nell'area Stati Uniti orientali per l'applicazione. Nella tabella seguente vengono descritti i parametri utilizzati con il comando **az iotcentral app create:**

| Parametro         | Descrizione |
| ----------------- | ----------- |
| resource-group    | Gruppo di risorse che contiene l'applicazione. Questo gruppo di risorse deve già esistere nella sottoscrizione. |
| posizione          | Per impostazione predefinita, questo comando usa il percorso del gruppo di risorse. Attualmente, è possibile creare un'applicazione IoT Central nelle aree geografiche **Australia**, **Asia Pacifico**, **Europa**, **Stati Uniti**, **Regno Unito**e **Giappone.** |
| name              | Nome dell'applicazione nel portale di Azure. |
| Sottodominio         | Sottodominio nell'URL dell'applicazione. In questo esempio l'URL dell'applicazione è `https://mysubdomain.azureiotcentral.com`. |
| sku               | Attualmente, è possibile utilizzare **ST1** o **ST2.** Vedere [Prezzi di Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| template          | Modello di applicazione da usare. Per altre informazioni, vedere la tabella seguente. |
| display-name      | Nome dell'applicazione visualizzato nell'interfaccia utente. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Visualizzare le proprie applicazioni

Usare il comando [az iotcentral app list](/cli/azure/iotcentral/app#az-iotcentral-app-list) per elencare le applicazioni IoT Central e visualizzare i metadati.

## <a name="modify-an-application"></a>Modificare un'applicazione

Utilizzare il comando [az iotcentral app update](/cli/azure/iotcentral/app#az-iotcentral-app-update) per aggiornare i metadati di un'applicazione IoT Central. Ad esempio, per modificare il nome visualizzato dell'applicazione creata:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Rimuovere un'applicazione

Usare il comando az iotcentral app delete per eliminare un'applicazione IoT Central.Use the [az iotcentral app delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) command to delete an IoT Central application. Ad esempio:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come gestire le applicazioni di Azure IoT Central dall'interfaccia della riga di comando di Azure, ecco il passaggio successivo consigliato:Now that you've learned how to manage Azure IoT Central applications from Azure CLI, here is the suggested next step:

> [!div class="nextstepaction"]
> [Amministrare l'applicazione](howto-administer.md)
