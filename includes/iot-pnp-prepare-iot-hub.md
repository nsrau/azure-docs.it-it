---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/17/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 3ac72360db5fb3d075b4caa3208f372f22441c7b
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755704"
---
## <a name="prepare-an-iot-hub"></a>Preparare un hub IoT

Per completare i passaggi di questo articolo, è necessario un hub IoT di Azure nella sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Se si usa l'interfaccia della riga di comando di Azure localmente, accedere prima alla sottoscrizione di Azure usando `az login`. Se si eseguono questi comandi in Azure Cloud Shell, si è connessi automaticamente.

Se si usa l'interfaccia della riga di comando di Azure in locale, è necessario che la versione di `az` sia la **2.8.0** o successiva. Azure Cloud Shell usa la versione più recente. Usare il comando `az --version` per verificare la versione installata nel computer.

Eseguire questo comando per aggiungere l'estensione Microsoft Azure IoT per l'interfaccia della riga di comando di Azure all'istanza:

```azurecli-interactive
az extension add --name azure-iot
```

Se non si dispone già di un hub IoT da usare, eseguire i comandi seguenti per creare un gruppo di risorse e un hub IoT di livello gratuito nella sottoscrizione. Sostituire `<YourIoTHubName>` con il nome dell'hub desiderato:

```azurecli-interactive
az group create --name my-pnp-resourcegroup \
    --location centralus
az iot hub create --name <YourIoTHubName> \
    --resource-group my-pnp-resourcegroup --sku F1
```

> [!NOTE]
> Plug and Play IoT è attualmente disponibile negli hub IoT creati nelle aree Stati Uniti centrali, Europa settentrionale e Giappone orientale. Il supporto di Internet di Plug and Play IoT non è incluso negli hub IoT di livello base.

Eseguire il comando seguente per creare l'identità del dispositivo nell'hub IoT. Sostituire i segnaposto `<YourIoTHubName>` e `<YourDeviceID>` con il _nome dell'hub IoT_ e l' _ID dispositivo_ di propria scelta.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```
