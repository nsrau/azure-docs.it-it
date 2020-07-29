---
author: baanders
description: file di inclusione per Gemelli digitali di Azure - configurare Cloud Shell e l'estensione IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: b7c91d648c06970d53799c6ff505919dea17b3c0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87032213"
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

Eseguire prima di tutto questo comando per visualizzare un elenco di tutte le estensioni già installate.

```azurecli-interactive
az extension list
```

L'output è una matrice di tutte le estensioni attualmente presenti. Cercare il campo `"name"` per ogni voce di elenco per vedere i nomi delle estensioni.

Usare l'output per determinare quale dei comandi seguenti eseguire per la configurazione dell'estensione (è possibile eseguirne più di uno).
* Se l'elenco contiene `azure-iot`: L'estensione è già installata. Eseguire questo comando per verificare di avere l'aggiornamento più recente e che non siano disponibili altri aggiornamenti:

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* Se l'elenco **non** contiene `azure-iot`: È necessario installare l'estensione. Usare questo comando:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* Se l'elenco contiene `azure-iot-cli-ext`: È installata la versione legacy dell'estensione. Occorre installare una sola versione dell'estensione alla volta, quindi l'estensione legacy deve essere disinstallata. Usare questo comando:

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```

A questo punto si è pronti per usare Gemelli digitali di Azure in Cloud Shell.

Per verificarlo, è possibile eseguire `az dt -h` in qualsiasi momento per visualizzare un elenco dei comandi di Gemelli digitali di Azure di primo livello disponibili.