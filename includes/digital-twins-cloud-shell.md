---
author: baanders
description: file di inclusione per Gemelli digitali di Azure - configurare Cloud Shell e l'estensione IoT
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 8be070826de0334483f4150925c05cb4dfb73f2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85805870"
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

A questo punto, si aggiungerà l'[**estensione Microsoft Azure IoT per l'interfaccia della riga di comando di Azure**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) all'istanza di Cloud Shell, per abilitare i comandi per l'interazione con Gemelli digitali di Azure e altri servizi IoT. 

Eseguire innanzitutto questo comando per visualizzare un elenco di tutte le estensioni già installate.

```azurecli-interactive
az extension list
```

Nell'output cercare il `"name"` campo per ogni voce di elenco per visualizzare i nomi delle estensioni.

Usare l'output per determinare quale dei comandi seguenti eseguire per la configurazione dell'estensione (è possibile eseguirne più di uno).
* Se l'elenco contiene `azure-iot` già l'estensione, Eseguire questo comando per verificare che sia installato l'aggiornamento più recente:

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* Se l'elenco non **contiene** `azure-iot` : è necessario installare l'estensione. Usare questo comando:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* Se l'elenco contiene `azure-iot-cli-ext` : questa è la versione legacy dell'estensione. È necessario installare una sola versione dell'estensione alla volta, quindi è necessario disinstallare l'estensione Legacy. Usare questo comando:

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```

A questo punto si è pronti per usare Gemelli digitali di Azure in Cloud Shell.

Per verificarlo, è possibile eseguire `az dt -h` in qualsiasi momento per visualizzare un elenco dei comandi di Gemelli digitali di Azure di primo livello disponibili.