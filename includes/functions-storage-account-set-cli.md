---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262227"
---
### <a name="set-the-storage-account-connection"></a>Impostare la connessione dell'account di archiviazione

Aprire il file local.settings.json e copiare il valore di `AzureWebJobsStorage`, che corrisponde alla stringa di connessione dell'account di archiviazione. Impostare la variabile di ambiente `AZURE_STORAGE_CONNECTION_STRING` sulla stringa di connessione usando il comando Bash seguente:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Con la stringa di connessione impostata sulla variabile di ambiente `AZURE_STORAGE_CONNECTION_STRING`, è possibile accedere all'account di archiviazione senza eseguire ogni volta l'autenticazione.