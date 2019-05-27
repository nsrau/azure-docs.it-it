---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 563849d3875ed0156d81770f58340633d90d515b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161227"
---
Di seguito vengono indicate le dimensioni degli oggetti Azure che possono essere scritti. Verificare che tutti i file caricati siano conformi a questi limiti.

| Tipo di oggetto di Azure | Limite di caricamento                                             |
|-------------------|-----------------------------------------------------------|
| BLOB in blocchi        | ~ 4,75 TB                                                 |
| BLOB di pagine         | 1 TB <br> Le dimensioni di tutti i file caricati nel formato BLOB di pagine devono essere multipli integrali di 512 byte per garantire che il caricamento venga completato in modo corretto. <br> I file VHD e VHDX sono allineati a 512 byte. |
| File di Azure         | 1 TB <br> Le dimensioni di tutti i file caricati nel formato BLOB di pagine devono essere multipli integrali di 512 byte per garantire che il caricamento venga completato in modo corretto. <br> I file VHD e VHDX sono allineati a 512 byte. |

> [!IMPORTANT]
> È possibile creare file fino a 5 TB, indipendentemente dal tipo di archiviazione. Tuttavia, se si crea un file di dimensioni maggiori del limite di caricamento definito nella tabella precedente, questo non verrà caricato. Sarà necessario eliminare manualmente il file per recuperare lo spazio.