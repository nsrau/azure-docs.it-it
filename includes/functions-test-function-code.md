---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 44eb25be12b908c8f951cb20948068da3bfc2928
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203174"
---
## <a name="test"></a>Verificare la funzione in Azure

Usare cURL per verificare la funzione distribuita. Usando l'URL copiato nel passaggio precedente, inclusa la chiave della funzione, aggiungere la stringa di query `&name=<yourname>` all'URL.

![Uso di cURL per chiamare la funzione in Azure.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

È anche possibile incollare l'URL copiato, inclusa la chiave della funzione, nella barra degli indirizzi del Web browser. Aggiungere la stringa di query `&name=<yourname>` all'URL prima di eseguire la richiesta.

![Uso di un Web browser per chiamare la funzione.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
