---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76963657"
---
Per ottimizzare le prestazioni, usare un account di archiviazione separato per ogni app per le funzioni. Ciò è particolarmente importante quando si dispone di funzioni durevoli o funzioni attivate Hub eventi, che generano entrambi un volume elevato di transazioni di archiviazione. Quando la logica dell'applicazione interagisce con Archiviazione di Azure, direttamente (tramite l'SDK di archiviazione) o tramite una delle associazioni di archiviazione, è consigliabile usare un account di archiviazione dedicato. Ad esempio, se si dispone di una funzione attivata da Hub&mdash;eventi che scrive alcuni dati nell'archiviazione BLOB, usare due account di archiviazione uno per l'app per le funzioni e un altro per i BLOB archiviati dalla funzione.