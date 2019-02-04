---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 914c006daf49e22ebec870a549bfdbc63f882647
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55148087"
---
## <a name="test"></a>Testare la funzione

Usare cURL per testare la funzione distribuita in un computer Mac o Linux oppure usando Powershell per Windows. Eseguire il comando cURL seguente, sostituendo il segnaposto `<app_name>` con il nome dell'app per le funzioni. Aggiungere la stringa di query `&name=<yourname>` all'URL.

```powershell
Invoke-WebRequest -Uri "https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>"
```

```bash
curl https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
```  

![Risposta della funzione visualizzata in un browser.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Se nella riga di comando non è disponibile `cURL` o`Invoke-WebRequest`, immettere lo stesso URL nella barra degli indirizzi del Web browser. Sostituire il segnaposto `<app_name>` con il nome dell'app per le funzioni, aggiungere la stringa di query `&name=<yourname>` all'URL ed eseguire la richiesta.

    https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
   
![Risposta della funzione visualizzata in un browser.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
