---
author: baanders
description: file di inclusione per Gemelli digitali di Azure - configurare l'estensione IoT più recente
ms.service: digital-twins
ms.topic: include
ms.date: 7/31/2020
ms.author: baanders
ms.openlocfilehash: 7e43ab6d419d6a08973864838c66aef0941d74f5
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90606413"
---
Eseguire prima di tutto questo comando per visualizzare un elenco di tutte le estensioni già installate.

```azurecli
az extension list
```

L'output è una matrice di tutte le estensioni attualmente presenti. Cercare il campo `"name"` per ogni voce di elenco per vedere i nomi delle estensioni.

Usare l'output per determinare quale dei comandi seguenti eseguire per la configurazione dell'estensione (è possibile eseguirne più di uno).
* Se l'elenco contiene `azure-iot`: L'estensione è già installata. Eseguire questo comando per verificare di avere l'aggiornamento più recente e che non siano disponibili altri aggiornamenti:

   ```azurecli
   az extension update --name azure-iot
   ```

* Se l'elenco **non** contiene `azure-iot`: È necessario installare l'estensione. Usare questo comando:

    ```azurecli
    az extension add --name azure-iot
    ```

* Se l'elenco contiene `azure-iot-cli-ext`: È installata la versione legacy dell'estensione. Occorre installare una sola versione dell'estensione alla volta, quindi l'estensione legacy deve essere disinstallata. Usare questo comando:

   ```azurecli
   az extension remove --name azure-cli-iot-ext
   ```