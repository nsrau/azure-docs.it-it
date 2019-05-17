---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: tamram
ms.openlocfilehash: 213ecee34df46c0a408e7034a07ab864c96b340b
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65751520"
---
| Resource | Condivisioni file Standard | Condivisioni file Premium (anteprima) |
|----------|---------------|------------------------------------------|
| Dimensione massima di una condivisione file | Nessun minimo. pagamento a consumo | 100 giB; il provisioning |
| Dimensioni massime di una condivisione file | 5 TiB | 100 TiB |
| Dimensioni massime di un file in una condivisione file | 1 TiB | 1 TiB |
| Numero massimo di file in una condivisione file | Nessun limite | Nessun limite |
| Numero massimo di IOPS per ogni condivisione | 1.000 OPERAZIONI IOPS | 100.000 IOPS |
| Numero massimo di criteri di accesso archiviati per ogni file condivide | 5 | 5 |
| Velocità effettiva da raggiungere per un'unica condivisione file | Fino a 60 MiB/sec | Vedere premium condivisione in ingresso e in uscita valori del file|
| Numero massimo in uscita per un'unica condivisione file | Vedere velocità effettiva di destinazione condivisione file standard | Fino a 6,204 MiB/s |
| Massimo in ingresso per un'unica condivisione file | Vedere velocità effettiva di destinazione condivisione file standard | Fino a 4.136 MiB/s |
| Numero massimo di handle aperti per ogni file | 2000 handle aperti | 2000 handle aperti |
| Numero massimo di condivisioni snapshot | 200 snapshot di condivisione | 200 snapshot di condivisione |
| Lunghezza massima del nome dell'oggetto (directory e file) | 2048 caratteri | 2048 caratteri |
| Numero massimo di componenti del percorso (nel percorso \A\B\C\D ogni lettera è un componente) | 255 caratteri | 255 caratteri |