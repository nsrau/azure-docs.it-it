---
author: baanders
description: file di inclusione per Gemelli digitali di Azure - configurare Cloud Shell e l'estensione IoT
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 76480959d94b9e2a2e04857c85b7359105e73e4f
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84611474"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Configurare una sessione di Cloud Shell

Dopo aver aperto una finestra di Cloud Shell, la prima cosa da fare è accedere e impostare il contesto della shell sulla sottoscrizione per questa sessione. 

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

Se è la prima volta che si usa questa sottoscrizione con Gemelli digitali di Azure, eseguire questo comando per registrarsi con lo spazio dei nomi di Gemelli digitali di Azure. In caso di dubbi, è possibile eseguirlo di nuovo anche se è stato già completato nel passato.

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Quindi, eseguire il comando seguente nell'istanza di Cloud Shell per aggiungere l'estensione Microsoft Azure IoT per l'interfaccia della riga di comando di Azure.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

> [!NOTE]
> Questo articolo usa la versione più recente dell'estensione Azure IoT, denominata `azure-iot`. La versione legacy viene chiamata `azure-iot-cli-ext`. Deve essere installata solo una versione alla volta. È possibile usare il comando `az extension list` per convalidare le estensioni attualmente installate.
> Usare `az extension remove --name azure-cli-iot-ext` per rimuovere la versione legacy dell'estensione.
> Usare `az extension add --name azure-iot` per aggiungere la nuova versione dell'estensione. Per visualizzare le estensioni installate, usare `az extension list`.

> [!TIP]
> È possibile eseguire `az dt -h` per visualizzare i comandi di primo livello di Gemelli digitali di Azure.