---
title: Gestire IoT Central dall'interfaccia della riga di comando di Azure | Microsoft Docs
description: Questo articolo descrive come creare e gestire l'applicazione IoT Central usando l'interfaccia della riga di comando. È possibile visualizzare, modificare e rimuovere l'applicazione tramite l'interfaccia della riga di comando.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: ddf1cfb1cb3997431b83812d03535ed990c88922
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73806879"
---
# <a name="manage-iot-central-from-azure-cli"></a>Gestire IoT Central dall'interfaccia della riga di comando di Azure

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Invece di creare e gestire applicazioni IoT Central nel sito Web di [azure IOT Central Application Manager](https://aka.ms/iotcentral) , è possibile usare l'interfaccia della riga di comando di [Azure](/cli/azure/) per gestire le applicazioni.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si preferisce eseguire l'interfaccia della riga di comando di Azure nel computer locale, vedere [installare l'interfaccia della](/cli/azure/install-azure-cli)riga di comando di Azure. Quando si esegue l'interfaccia della riga di comando di Azure in locale, usare il comando **AZ login** per accedere ad Azure prima di provare i comandi in questo articolo.

## <a name="create-an-application"></a>Creare un'applicazione

Usare il comando [AZ iotcentral app create](/cli/azure/iotcentral/app#az-iotcentral-app-create) per creare un'applicazione IoT Central nella sottoscrizione di Azure. Ad esempio:

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
  --sku S1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

Questi comandi creano prima di tutto un gruppo di risorse nell'area Stati Uniti orientali per l'applicazione. La tabella seguente descrive i parametri usati con il comando **AZ iotcentral app create** :

| Parametro         | Descrizione |
| ----------------- | ----------- |
| resource-group    | Gruppo di risorse che contiene l'applicazione. Questo gruppo di risorse deve già esistere nella sottoscrizione. |
| location          | Per impostazione predefinita, questo comando usa il percorso del gruppo di risorse. Attualmente, è possibile creare un'applicazione IoT Central nelle aree **Stati Uniti orientali**, **Stati Uniti occidentali**, **Europa settentrionale**o **Europa occidentale** oppure in **Australia** o **Asia Pacifico** geografie. |
| name              | Nome dell'applicazione nel portale di Azure. |
| sottodominio         | Sottodominio nell'URL dell'applicazione. In questo esempio l'URL dell'applicazione è https://mysubdomain.azureiotcentral.com. |
| sku               | L'unico valore attualmente disponibile è **S1** (livello standard). Vedere [Prezzi di Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| template          | Modello di applicazione da usare. Per altre informazioni, vedere la tabella seguente: |
| display-name      | Nome dell'applicazione visualizzato nell'interfaccia utente. |

**Modelli di applicazione**

| Nome modello            | Descrizione |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Crea un'applicazione vuota per l'utente da popolare con i propri modelli di dispositivi e dispositivi. |
| iotc-demo@1.0.0          | Crea un'applicazione che include un modello di dispositivo già creato per un distributore automatico refrigerato. Usare questo modello per iniziare a esplorare Azure IoT Central. |
| iotc-devkit-sample@1.0.0 | Crea un'applicazione con modelli di dispositivo pronti per la connessione a un dispositivo MXChip o Raspberry Pi. Si consiglia di usare questo modello agli sviluppatori di dispositivi che vogliono fare pratica con questi tipi di dispositivi. |

> [!NOTE]
> Il modello di **applicazione di anteprima** è attualmente disponibile solo nelle aree **Europa settentrionale** e **Stati Uniti centrali** .

## <a name="view-your-applications"></a>Visualizzare le proprie applicazioni

Usare il comando [AZ iotcentral app list](/cli/azure/iotcentral/app#az-iotcentral-app-list) per elencare le applicazioni IoT Central e visualizzare i metadati.

## <a name="modify-an-application"></a>Modificare un'applicazione

Usare il comando [AZ iotcentral App Update](/cli/azure/iotcentral/app#az-iotcentral-app-update) per aggiornare i metadati di un'applicazione IoT Central. Ad esempio, per modificare il nome visualizzato dell'applicazione creata:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Rimuovere un'applicazione

Usare il comando [AZ iotcentral app Delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) per eliminare un'applicazione IoT Central. Ad esempio:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire le applicazioni IoT Central di Azure dall'interfaccia della riga di comando di Azure, ecco il passaggio successivo suggerito:

> [!div class="nextstepaction"]
> [Amministrare l'applicazione](howto-administer.md)
