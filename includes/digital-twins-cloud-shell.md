---
author: baanders
description: file di inclusione per Gemelli digitali di Azure - configurare Cloud Shell e l'estensione IoT
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 4aa016294f0ef3bd26f7f3ef6fa374e9367b672d
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85296968"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Configurare una sessione di Cloud Shell

Dopo aver aperto una finestra di Cloud Shell, la prima cosa da fare è accedere e impostare il contesto della shell sulla sottoscrizione per questa sessione. Eseguire questi comandi nell'istanza di Cloud Shell:

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

Se è la prima volta che si usa questa sottoscrizione con Gemelli digitali di Azure, eseguire questo comando per registrarsi con lo spazio dei nomi di Gemelli digitali di Azure. In caso di dubbi, è possibile eseguirlo di nuovo anche se è stato già completato nel passato.

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

A questo punto, si aggiungerà l'[**estensione Microsoft Azure IoT per l'interfaccia della riga di comando di Azure**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) all'istanza di Cloud Shell, per abilitare i comandi per l'interazione con Gemelli digitali di Azure e altri servizi IoT. Usare questo comando per aggiungere l'estensione:

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

Se l'estensione è stata installata in passato, l'output potrebbe indicare che l'estensione azure-iot è già installata. In tal caso, eseguire il comando seguente per verificare che sia installato l'aggiornamento più recente: 

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

A questo punto si è pronti per usare Gemelli digitali di Azure in Cloud Shell.

Per verificarlo, è possibile eseguire `az dt -h` in qualsiasi momento per visualizzare un elenco dei comandi di Gemelli digitali di Azure di primo livello disponibili.