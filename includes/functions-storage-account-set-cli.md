---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 89e2e64910e33d43c107ee88137de718d020d7d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839087"
---
Aprire il file local.settings.json e copiare il valore di `AzureWebJobsStorage`, che corrisponde alla stringa di connessione dell'account di archiviazione. Impostare la variabile di ambiente `AZURE_STORAGE_CONNECTION_STRING` sulla stringa di connessione usando il comando Bash seguente:

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Con la stringa di connessione impostata sulla variabile di ambiente `AZURE_STORAGE_CONNECTION_STRING`, è possibile accedere all'account di archiviazione senza eseguire ogni volta l'autenticazione.