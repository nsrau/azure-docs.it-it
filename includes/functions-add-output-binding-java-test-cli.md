---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 6a359cdd44cc0c0cfbd93bd23b69a67a641c7fbb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "80673299"
---
## <a name="update-the-tests"></a>Aggiornare i test

Poiché l'archetipo crea anche un set di test, è necessario aggiornare questi test per gestire il nuovo parametro `msg` nella firma del metodo `run`.  

Passare al percorso del codice di test, _src/test/java_, aprire il file di progetto *Function.java* e sostituire la riga di codice sotto `//Invoke` con il codice seguente.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::