---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: d88d166e001e71cabdabc3a3d344adc4da19aa8c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "75453686"
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

È anche possibile rimuovere dal computer di sviluppo i file di esempio clonati.