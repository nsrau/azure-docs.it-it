---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: d21709a231f97f1f1dc86837f79457df0cdb2263
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513308"
---
| Gruppi | Condivisioni file Standard | Condivisioni file Premium |
|----------|---------------|------------------------------------------|
| Dimensione massima di una condivisione file | Nessun valore minimo; con pagamento in base al consumo | 100 GiB; provisioning |
| Dimensioni massime di una condivisione file | 100 TiB *, 5 TiB | 100 TiB |
| Dimensioni massime di un file in una condivisione file | 1 TiB | 1 TiB |
| Numero massimo di file in una condivisione file | Senza limiti | Senza limiti |
| Numero massimo di IOPS per condivisione | 10.000 IOPS *, 1.000 IOPS | 100.000 IOPS |
| Numero massimo di criteri di accesso archiviati per ogni condivisione file | 5 | 5 |
| Velocità effettiva di destinazione per una singola condivisione file | fino a 300 MiB/sec *, fino a 60 MiB/sec,  | Vedere valori in ingresso e in uscita di condivisione file Premium|
| Numero massimo in uscita per una singola condivisione file | Vedere velocità effettiva di destinazione della condivisione file standard | Fino a 6.204 MiB/s |
| Ingresso massimo per una singola condivisione file | Vedere velocità effettiva di destinazione della condivisione file standard | Fino a 4.136 MiB/s |
| Numero massimo di handle aperti per ogni file | 2000 handle aperti | 2000 handle aperti |
| Numero massimo di condivisioni snapshot | 200 snapshot di condivisione | 200 snapshot di condivisione |
| Lunghezza massima del nome dell'oggetto (directory e file) | 2\.048 caratteri | 2\.048 caratteri |
| Numero massimo di componenti del percorso (nel percorso \A\B\C\D ogni lettera è un componente) | 255 caratteri | 255 caratteri |

\* non sono disponibili in tutte le aree, vedere [disponibilità a livello](../articles/storage/files/storage-files-planning.md#regional-availability) di area per un elenco delle aree disponibili.