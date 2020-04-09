---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 6a359cdd44cc0c0cfbd93bd23b69a67a641c7fbb
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673299"
---
## <a name="update-the-tests"></a>Aggiornare i test

Poiché l'archetipo crea anche un set di test, è necessario aggiornare questi test per gestire il nuovo parametro `msg` nella firma del metodo `run`.  

Passare al percorso del codice di test, _src/test/java_, aprire il file di progetto *Function.java* e sostituire la riga di codice sotto `//Invoke` con il codice seguente.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::