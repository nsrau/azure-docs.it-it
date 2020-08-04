---
author: baanders
description: file di inclusione per Gemelli digitali di Azure - configurare l'estensione IoT più recente
ms.service: digital-twins
ms.topic: include
ms.date: 7/31/2020
ms.author: baanders
ms.openlocfilehash: 984739a728f6ac5e28eeb561e0d7b6ec0485ca13
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496866"
---
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