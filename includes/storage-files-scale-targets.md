---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 9f259c3e403e933c847ac56000b1db2cd594caf5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449950"
---
| Resource | Condivisioni file Standard | Condivisioni file Premium |
|----------|---------------|------------------------------------------|
| Dimensione massima di una condivisione file | Nessun minimo. pagamento a consumo | 100 giB; il provisioning |
| Dimensioni massime di una condivisione file | 5 TiB (GA), 100 TiB (anteprima) | 100 TiB |
| Dimensioni massime di un file in una condivisione file | 1 TiB | 1 TiB |
| Numero massimo di file in una condivisione file | Nessun limite | Nessun limite |
| Numero massimo di IOPS per ogni condivisione | 1\.000 operazioni IOPS (GA), 10.000 IOPS (anteprima) | 100.000 IOPS |
| Numero massimo di criteri di accesso archiviati per ogni file condivide | 5 | 5 |
| Velocità effettiva da raggiungere per un'unica condivisione file | Fino a 60 MiB/sec (GA), fino a 300 MiB/sec (anteprima) | Vedere premium condivisione in ingresso e in uscita valori del file|
| Numero massimo in uscita per un'unica condivisione file | Vedere velocità effettiva di destinazione condivisione file standard | Fino a 6,204 MiB/s |
| Massimo in ingresso per un'unica condivisione file | Vedere velocità effettiva di destinazione condivisione file standard | Fino a 4.136 MiB/s |
| Numero massimo di handle aperti per ogni file | 2000 handle aperti | 2000 handle aperti |
| Numero massimo di condivisioni snapshot | 200 snapshot di condivisione | 200 snapshot di condivisione |
| Lunghezza massima del nome dell'oggetto (directory e file) | 2048 caratteri | 2048 caratteri |
| Numero massimo di componenti del percorso (nel percorso \A\B\C\D ogni lettera è un componente) | 255 caratteri | 255 caratteri |