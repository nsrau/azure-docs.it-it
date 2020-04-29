---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 6cf9be653da2dd587b93724b6e319dc4d20686c2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536438"
---
| Risorsa | Condivisioni file Standard | Condivisioni file Premium |
|----------|---------------|------------------------------------------|
| Dimensione massima di una condivisione file | Nessun valore minimo; con pagamento in base al consumo | 100 GiB; provisioning |
| Dimensioni massime di una condivisione file | 100 TiB *, 5 TiB | 100 TiB |
| Dimensioni massime di un file in una condivisione file | 1 TiB | 1 TiB |
| Numero massimo di file in una condivisione file | Nessun limite | Nessun limite |
| Numero massimo di IOPS per condivisione | 10.000 IOPS *, 1.000 IOPS | 100.000 IOPS |
| Numero massimo di criteri di accesso archiviati per ogni condivisione file | 5 | 5 |
| Velocità effettiva di destinazione per una singola condivisione file | fino a 300 MiB/sec *, fino a 60 MiB/sec,  | Vedere valori in ingresso e in uscita di condivisione file Premium|
| Numero massimo in uscita per una singola condivisione file | Vedere velocità effettiva di destinazione della condivisione file standard | Fino a 6.204 MiB/s |
| Ingresso massimo per una singola condivisione file | Vedere velocità effettiva di destinazione della condivisione file standard | Fino a 4.136 MiB/s |
| Numero massimo di handle aperti per ogni file | 2000 handle aperti | 2000 handle aperti |
| Numero massimo di condivisioni snapshot | 200 snapshot di condivisione | 200 snapshot di condivisione |
| Lunghezza massima del nome dell'oggetto (directory e file) | 2.048 caratteri | 2.048 caratteri |
| Numero massimo di componenti del percorso (nel percorso \A\B\C\D ogni lettera è un componente) | 255 caratteri | 255 caratteri |

\*Il valore predefinito per le condivisioni file standard è 5 TiB, vedere [abilitare e creare condivisioni file di grandi dimensioni](../articles/storage/files/storage-files-how-to-create-large-file-share.md) per informazioni dettagliate su come aumentare le condivisioni file standard con scalabilità fino a 100 tib.
