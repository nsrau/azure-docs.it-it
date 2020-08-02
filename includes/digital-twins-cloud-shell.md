---
author: baanders
description: file di inclusione per Gemelli digitali di Azure - configurare Cloud Shell e l'estensione IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: b664303d86f8588fc210b11b363b21d523d63295
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87507495"
---
Per iniziare a usare Gemelli digitali di Azure in una finestra di [Azure Cloud Shell](https://shell.azure.com) aperta, la prima cosa da fare è accedere e impostare il contesto della shell sulla sottoscrizione per questa sessione. Eseguire questi comandi nell'istanza di Cloud Shell:

```azurecli
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> Nel comando riportato sopra si può anche usare il nome della sottoscrizione invece dell'ID. 

Se è la prima volta che si usa questa sottoscrizione con Gemelli digitali di Azure, eseguire questo comando per registrarsi con lo spazio dei nomi di Gemelli digitali di Azure. In caso di dubbi, è possibile eseguirlo di nuovo anche se è stato già completato nel passato.

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

A questo punto, si aggiungerà l'[**estensione Microsoft Azure IoT per l'interfaccia della riga di comando di Azure**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) all'istanza di Cloud Shell, per abilitare i comandi per l'interazione con Gemelli digitali di Azure e altri servizi IoT. 

[!INCLUDE [digital-twins-cloud-shell-extensions.md](digital-twins-cloud-shell-extensions.md)]

A questo punto si è pronti per usare Gemelli digitali di Azure in Cloud Shell.

Per verificarlo, è possibile eseguire `az dt -h` in qualsiasi momento per visualizzare un elenco dei comandi di Gemelli digitali di Azure di primo livello disponibili.