---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: a3f75b7273164abc5318f16e9ab8d9883ff0c0aa
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132193"
---
## <a name="test"></a>Testare la funzione in Azure

Usare cURL per testare la funzione distribuita. Usando l'URL copiato nel passaggio precedente, aggiungere la stringa di query `&name=<yourname>` all'URL, come nell'esempio seguente:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

![Uso di cURL per chiamare la funzione in Azure.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

È anche possibile incollare l'URL copiato nell'indirizzo del Web browser. Aggiungere la stringa di query `&name=<yourname>` all'URL prima di eseguire la richiesta.

![Uso di un Web browser per chiamare la funzione.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
