---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 99314538f90404d7c2b72da0dd2da2d8ac60a08a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59291736"
---
| Risorsa | Condivisioni file Standard | Condivisioni file Premium (anteprima) |
|----------|---------------|------------------------------------------|
| Dimensione massima di una condivisione file | Nessun minimo. pagamento a consumo | 100 giB; il provisioning |
| Dimensioni massime di una condivisione file | 5 TiB | 5 TiB (anteprima pubblica), 100 TiB (anteprima pubblica limitata) |
| Dimensioni massime di un file in una condivisione file | 1 TiB | 1 TiB |
| Numero massimo di file in una condivisione file | Nessun limite | Nessun limite |
| Numero massimo di IOPS per ogni condivisione | 1.000 OPERAZIONI IOPS | 5.120 IOPS base 15,360 Limit burst (anteprima pubblica), 100.000 IOPS (anteprima pubblica limitata)|
| Numero massimo di criteri di accesso archiviati per ogni file condivide | 5 | 5 |
| Velocità effettiva da raggiungere per un'unica condivisione file | Fino a 60 MiB/sec | Vedere premium condivisione in ingresso e in uscita valori del file|
| Numero massimo in uscita per un'unica condivisione file | Vedere velocità effettiva di destinazione condivisione file standard | Fino a 368 MiB/s (anteprima pubblica), fino a 6,204 MiB/s (anteprima pubblica limitata) |
| Massimo in ingresso per un'unica condivisione file | Vedere velocità effettiva di destinazione condivisione file standard | Fino a 245 MiB/s (anteprima pubblica), fino a 4.136 MiB/s (anteprima pubblica limitata) |
| Numero massimo di handle aperti per ogni file | 2000 handle aperti | 2000 handle aperti |
| Numero massimo di condivisioni snapshot | 200 snapshot di condivisione | 200 snapshot di condivisione |
| Lunghezza massima del nome dell'oggetto (directory e file) | 2048 caratteri | 2048 caratteri |
| Numero massimo di componenti del percorso (nel percorso \A\B\C\D ogni lettera è un componente) | 255 caratteri | 255 caratteri |