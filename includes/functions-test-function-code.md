---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 9c972508c98e87771154bd26cc166c6bea4201ee
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279527"
---
## <a name="test"></a>Verificare la funzione in Azure

Per verificare la funzione distribuita è possibile usare un Web browser.  Copiare l'URL, inclusa la chiave della funzione, nella barra degli indirizzi del Web browser. Aggiungere la stringa di query `&name=<yourname>` all'URL prima di eseguire la richiesta.

![Uso di un Web browser per chiamare la funzione.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  

In alternativa, è possibile usare cURL per verificare la funzione distribuita. Usando l'URL copiato nel passaggio precedente, inclusa la chiave della funzione, aggiungere la stringa di query `&name=<yourname>` all'URL.

![Uso di cURL per chiamare la funzione in Azure.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

