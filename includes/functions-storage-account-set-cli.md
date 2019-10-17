---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 07780c94960c581ce69e4fdd45b346c75ab8b098
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329595"
---
### <a name="set-the-storage-account-connection"></a>Impostare la connessione dell'account di archiviazione

Aprire il file local.settings.json e copiare il valore di `AzureWebJobsStorage`, che corrisponde alla stringa di connessione dell'account di archiviazione. Impostare la variabile di ambiente `AZURE_STORAGE_CONNECTION_STRING` sulla stringa di connessione usando il comando Bash seguente:

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Con la stringa di connessione impostata sulla variabile di ambiente `AZURE_STORAGE_CONNECTION_STRING`, è possibile accedere all'account di archiviazione senza eseguire ogni volta l'autenticazione.