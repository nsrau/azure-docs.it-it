---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: ae88a6132c79d9d9f930f6445e53b0f0452c9f2a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262608"
---
## <a name="test"></a>Testare la funzione

Usare cURL per testare la funzione distribuita in un computer Mac o Linux oppure usando Bash per Windows. Eseguire il comando cURL seguente, sostituendo il segnaposto `<app_name>` con il nome dell'app per le funzioni. Aggiungere la stringa di query `&name=<yourname>` all'URL.

```bash
curl https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
```  

![Risposta della funzione visualizzata in un browser.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Se nella riga di comando non è disponibile un cURL, immettere lo stesso URL nella barra degli indirizzi del browser Web. Sostituire il segnaposto `<app_name>` con il nome dell'app per le funzioni, aggiungere la stringa di query `&name=<yourname>` all'URL ed eseguire la richiesta.

    https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
   
![Risposta della funzione visualizzata in un browser.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
