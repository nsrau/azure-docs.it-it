---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "78191044"
---
Aggiornare *HttpExample\\\_\_init\_\_.py* in modo che corrisponda al codice seguente, aggiungendo il parametro `msg` alla definizione della funzione e `msg.set(name)` sotto l'istruzione `if name:`.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Il parametro `msg` è un'istanza della [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest). Il relativo metodo `set` scrive un messaggio stringa nella coda, in questo caso il nome passato alla funzione nella stringa di query dell'URL.
