---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963657"
---
Per ottimizzare le prestazioni, usare un account di archiviazione separato per ogni app per le funzioni. Questa operazione è particolarmente importante quando si hanno Durable Functions o funzioni attivate da Hub eventi, che generano entrambe un volume elevato di transazioni di archiviazione. Quando la logica dell'applicazione interagisce con archiviazione di Azure, direttamente (usando l'SDK di archiviazione) o tramite una delle associazioni di archiviazione, è necessario usare un account di archiviazione dedicato. Ad esempio, se si dispone di una funzione attivata da Hub eventi che scrive alcuni dati nell'archivio BLOB, usare due account di archiviazione&mdash;uno per l'app per le funzioni e un altro per i BLOB archiviati dalla funzione.