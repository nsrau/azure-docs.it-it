---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d1931614356a313334d712713965346e843a403d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424925"
---
## <a name="invoke-the-function-on-azure"></a>Richiamare la funzione in Azure

Poiché la funzione usa un trigger HTTP, è possibile richiamarla eseguendo una richiesta HTTP al relativo URL nel browser o con uno strumento come curl. 

# <a name="browser"></a>[Browser](#tab/browser)

Copiare l' **URL di richiamo** completo visualizzato nell'output del comando publish nella barra degli indirizzi di un browser, aggiungendo il parametro di query `&name=Functions`. Nel browser dovrebbe essere visualizzato un output simile a quello visualizzato quando è stata eseguita la funzione in locale.

![Output della funzione eseguita in Azure in un browser](./media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Eseguire [`curl`](https://curl.haxx.se/) con l' **URL di richiamo** , aggiungendo il parametro `&name=Functions`. L'output del comando dovrebbe essere il testo "Hello Functions".

![Output della funzione eseguita in Azure con curl](./media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---
