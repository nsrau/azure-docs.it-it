---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: a3f75b7273164abc5318f16e9ab8d9883ff0c0aa
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179929"
---
## <a name="test"></a>Testare la funzione in Azure

Usare cURL per testare la funzione distribuita. Usando l'URL copiato nel passaggio precedente, aggiungere la stringa di query `&name=<yourname>` all'URL, come nell'esempio seguente:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

![Uso di cURL per chiamare la funzione in Azure.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

È anche possibile incollare l'URL copiato nell'indirizzo del Web browser. Aggiungere la stringa di query `&name=<yourname>` all'URL prima di eseguire la richiesta.

![Uso di un Web browser per chiamare la funzione.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
