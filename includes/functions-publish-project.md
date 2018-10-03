---
title: File di inclusione
description: File di inclusione
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/27/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 1b553cbd720fcb76899844712ce5053af46f7ccb
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452956"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Implementare il progetto dell'app per le funzioni in Azure

Dopo aver creato l'app per le funzioni in Azure, è possibile usare il comando [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) per implementare il codice del progetto in Azure.

```bash
func azure functionapp publish <FunctionAppName>
```

Verrà visualizzata una schermata simile alla seguente, la quale è stata troncata per migliorarne la leggibilità.

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

Ora è possibile testare le funzioni in Azure.