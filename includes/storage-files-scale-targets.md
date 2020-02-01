---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 8a8619da831dfa5b240bd93d3a046c49cc30affa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901429"
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

\* disponibili nella maggior parte delle aree, vedere [disponibilità a livello](../articles/storage/files/storage-files-planning.md#regional-availability) di area per informazioni dettagliate sulle aree disponibili.
