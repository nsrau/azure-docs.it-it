---
author: baanders
description: file di inclusione per Gemelli digitali di Azure - configurare Cloud Shell e l'estensione IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 8a3efc9ba8fc8ffd8c0eca4340e1948c388c0a13
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494992"
---
Per iniziare a usare Gemelli digitali di Azure in una finestra di [Azure Cloud Shell](https://shell.azure.com) aperta, la prima cosa da fare è accedere e impostare il contesto della shell sulla sottoscrizione per questa sessione. Eseguire questi comandi nell'istanza di Cloud Shell:

```azurecli-interactive
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> Nel comando riportato sopra si può anche usare il nome della sottoscrizione invece dell'ID. 

Se è la prima volta che si usa questa sottoscrizione con Gemelli digitali di Azure, eseguire questo comando per registrarsi con lo spazio dei nomi di Gemelli digitali di Azure. In caso di dubbi, è possibile eseguirlo di nuovo anche se è stato già completato nel passato.

```azurecli-interactive
az provider register --namespace 'Microsoft.DigitalTwins'
```

A questo punto, si aggiungerà l'[**estensione Microsoft Azure IoT per l'interfaccia della riga di comando di Azure**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest&preserve-view=true) all'istanza di Cloud Shell, per abilitare i comandi per l'interazione con Gemelli digitali di Azure e altri servizi IoT. 

[!INCLUDE [digital-twins-cloud-shell-extensions.md](digital-twins-cloud-shell-extensions.md)]

A questo punto si è pronti per usare Gemelli digitali di Azure in Cloud Shell.

Per verificarlo, è possibile eseguire `az dt -h` in qualsiasi momento per visualizzare un elenco dei comandi di Gemelli digitali di Azure di primo livello disponibili.