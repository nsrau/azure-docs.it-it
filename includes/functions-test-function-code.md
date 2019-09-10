---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 9381f436aaeedb31732f853a6c4765ac43c6a752
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70170855"
---
## <a name="test"></a>Testare la funzione in Azure

Usare cURL per testare la funzione distribuita. Usando l'URL copiato nel passaggio precedente, inclusa la chiave della funzione, aggiungere la stringa di query `&name=<yourname>` all'URL.

![Uso di cURL per chiamare la funzione in Azure.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

È anche possibile incollare l'URL copiato, inclusa la chiave della funzione, nell'indirizzo del Web browser. Aggiungere la stringa di query `&name=<yourname>` all'URL prima di eseguire la richiesta.

![Uso di un Web browser per chiamare la funzione.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
