---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76963657"
---
Per incrementare le prestazioni, usare un account di archiviazione diverso per ogni app per le funzioni. Questo accorgimento è particolarmente importante in presenza di funzioni attivate da Hub eventi o Durable Functions, che generano entrambe un volume elevato di transazioni di archiviazione. Quando la logica dell'applicazione interagisce con Archiviazione di Azure, direttamente (usando Storage SDK) o tramite una delle associazioni di archiviazione, è necessario usare un account di archiviazione dedicato. Ad esempio, se è presente una funzione attivata da Hub eventi che scrive alcuni dati nell'archivio BLOB, usare due account di archiviazione, uno per l'app per le funzioni e un altro per i BLOB archiviati dalla funzione.