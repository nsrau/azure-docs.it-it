---
author: dominicbetts
ms.author: dominicbetts
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: 6cd983b6421fc821853aa22d4dc2c297f672d292
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87336897"
---
## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare con altri articoli su Plug and Play IoT, è possibile conservare e riutilizzare le risorse usate in questo articolo. In caso contrario, è possibile eliminare le risorse create in questo articolo per evitare addebiti aggiuntivi.

È possibile eliminare l'hub e il dispositivo registrato contemporaneamente eliminando l'intero gruppo di risorse con il comando dell'interfaccia della riga di comando di Azure seguente. Non usare questo comando se queste risorse condividono un gruppo di risorse con altre risorse che si vogliono conservare.

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```

Per eliminare solo l'hub IoT, eseguire questo comando usando l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Per eliminare solo l'identità del dispositivo registrato con l'hub IoT, eseguire questo comando usando l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

È anche possibile rimuovere dal computer di sviluppo i file di esempio clonati.
