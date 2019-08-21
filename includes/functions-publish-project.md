---
title: File di inclusione
description: File di inclusione
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9fefba9c79e9c58956243de8db67ca4cf316251c
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949946"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Implementare il progetto dell'app per le funzioni in Azure

Dopo aver creato l'app per le funzioni in Azure, è possibile usare il comando [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) degli strumenti di base per implementare il codice del progetto in Azure. In questi esempi sostituire `<APP_NAME>` con il nome dell'app creata nel passaggio precedente.

### <a name="c--javascript"></a>C\#/JavaScript

```command
func azure functionapp publish <APP_NAME>
```

### <a name="typescript"></a>TypeScript

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

Verrà visualizzato un output simile al seguente, che è stato troncato per migliorarne la leggibilità:

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

Copiare il valore di `Invoke url` per `HttpTrigger`, che è ora possibile usare per testare la funzione in Azure. L'URL contiene un valore della stringa di query `code` che rappresenta la chiave di funzione. Questa chiave rende più difficile ad altri utenti chiamare l'endpoint per il trigger HTTP in Azure.