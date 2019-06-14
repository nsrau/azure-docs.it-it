---
title: Gestire IoT Central dal comando di Azure | Microsoft Docs
description: Gestire IoT Central dal comando di Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/07/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 9e5d842cece316bc9c53e1e8583f40a0f222b91d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66151949"
---
# <a name="manage-iot-central-from-azure-cli"></a>Gestire IoT Central dal comando di Azure

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Invece di creare e gestire le applicazioni IoT Central da IoT Central [Gestione applicazioni](https://aka.ms/iotcentral) pagina, è possibile usare [CLI Azure](/cli/azure/) per gestire le applicazioni.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si preferisce per l'esecuzione della riga di comando di Azure nel computer locale, vedere [installare CLI Azure](/cli/azure/install-azure-cli). Quando si esegue il comando di Azure in locale, usare il **account di accesso di az** comando per accedere ad Azure prima di provare i comandi in questo articolo.

## <a name="create-an-application"></a>Creare un'applicazione

Usare la [az iotcentral app creare](/cli/azure/iotcentral/app#az-iotcentral-app-create) comando per creare un'applicazione IoT Central nella sottoscrizione di Azure. Ad esempio:

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

Questi comandi prima di tutto creano un gruppo di risorse nell'area orientale area Stati Uniti per l'applicazione. Nella tabella seguente vengono descritti i parametri usati con il **az iotcentral app creare** comando:

| Parametro         | Descrizione |
| ----------------- | ----------- |
| resource-group    | Gruppo di risorse che contiene l'applicazione. Questo gruppo di risorse deve già esistere nella sottoscrizione. |
| location          | Per impostazione predefinita, questo comando Usa il percorso dal gruppo di risorse. Attualmente è possibile creare un'applicazione IoT Central nelle aree **Stati Uniti orientali**, **Stati Uniti occidentali**, **Europa settentrionale** ed **Europa occidentale**. |
| name              | Nome dell'applicazione nel portale di Azure. |
| subdomain         | Sottodominio nell'URL dell'applicazione. In questo esempio l'URL dell'applicazione è https://mysubdomain.azureiotcentral.com. |
| sku               | L'unico valore attualmente disponibile è **S1** (livello standard). Vedere [Prezzi di Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| template          | Modello di applicazione da usare. Per altre informazioni, vedere la tabella seguente: |
| display-name      | Nome dell'applicazione visualizzato nell'interfaccia utente. |

**Modelli di applicazione**

| Nome modello            | Descrizione |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Crea un'applicazione vuota per l'utente da popolare con i propri modelli di dispositivi e dispositivi. |
| iotc-demo@1.0.0          | Crea un'applicazione che include un modello di dispositivo già creato per un distributore automatico refrigerato. Usare questo modello per iniziare a esplorare Azure IoT Central. |
| iotc-devkit-sample@1.0.0 | Crea un'applicazione con modelli di dispositivo pronti per la connessione a un dispositivo MXChip o Raspberry Pi. Si consiglia di usare questo modello agli sviluppatori di dispositivi che vogliono fare pratica con questi tipi di dispositivi. |

## <a name="view-your-applications"></a>Visualizzare le proprie applicazioni

Usare la [elenco di app iotcentral az](/cli/azure/iotcentral/app#az-iotcentral-app-list) comando per elencare le applicazioni IoT Central e visualizzare i metadati.

## <a name="modify-an-application"></a>Modificare un'applicazione

Usare la [l'aggiornamento dell'app iotcentral az](/cli/azure/iotcentral/app#az-iotcentral-app-update) comando per aggiornare i metadati di un'applicazione IoT Central. Ad esempio, per modificare il nome visualizzato dell'applicazione creata:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Rimuovere un'applicazione

Usare la [delete app iotcentral di az](/cli/azure/iotcentral/app#az-iotcentral-app-delete) comando per eliminare un'applicazione IoT Central. Ad esempio:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire le applicazioni Azure IoT Central della riga di comando di Azure, ecco il passaggio successivo consigliato:

> [!div class="nextstepaction"]
> [Amministrare l'applicazione](howto-administer.md)
