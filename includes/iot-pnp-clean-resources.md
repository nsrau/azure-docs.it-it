---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: e6ca830a7224326c17003924b76192b95ba6bcb2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152034"
---
## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di continuare con altri articoli su Plug and Play IoT, non eliminare le risorse usate in questa guida di avvio rapido. In caso contrario, è possibile eliminare le risorse create per questa guida di avvio rapido per evitare addebiti aggiuntivi.

È possibile eliminare l'hub e il dispositivo registrato contemporaneamente eliminando l'intero gruppo di risorse con il comando seguente per l'interfaccia della riga di comando di Azure. Non usare questo comando, tuttavia, se queste risorse condividono un gruppo di risorse con altre risorse disponibili per scopi diversi.

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

È anche possibile scegliere di rimuovere dal computer di sviluppo i file dell'SDK clonati.