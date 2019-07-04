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
ms.openlocfilehash: 48bb91b3b2e9a31de63e515edb857bc2a170ea79
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179932"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Implementare il progetto dell'app per le funzioni in Azure

Dopo aver creato l'app per le funzioni in Azure, è possibile usare il comando [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) degli strumenti di base per implementare il codice del progetto in Azure. Nel comando seguente sostituire `<APP_NAME>` con il nome dell'app creata nel passaggio precedente.

```bash
func azure functionapp publish <APP_NAME>
```

Verrà visualizzata una schermata simile alla seguente, che è stata troncata per migliorarne la leggibilità.

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

Copiare il valore dell'URL di richiamo per HttpTrigger, che è ora possibile usare per testare la funzione in Azure. L'URL contiene un valore della stringa di query `code` che rappresenta la chiave di funzione. Questa chiave rende più difficile ad altri utenti chiamare l'endpoint per il trigger HTTP in Azure.