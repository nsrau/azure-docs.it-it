---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: ae431d27af4a1717a32e348ba852f9b2b9fe9331
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87451242"
---
| Risorsa | Condivisioni file Standard | Condivisioni file Premium |
|----------|---------------|------------------------------------------|
| Dimensione massima di una condivisione file | Nessun limite minimo; pagamento in base al consumo | 100 GiB; con provisioning |
| Dimensioni massime di una condivisione file | 100 TiB*, 5 TiB | 100 TiB |
| Dimensioni massime di un file in una condivisione file | 1 TiB | 4 TiB |
| Numero massimo di file in una condivisione file | Nessun limite | Nessun limite |
| Numero massimo di operazioni di I/O al secondo per condivisione | 10.000 IOPS*, 1.000 IOPS | 100.000 IOPS |
| Numero massimo di criteri di accesso archiviati per ogni condivisione file | 5 | 5 |
| Velocità effettiva di destinazione per una singola condivisione di file | Fino a 300 MiB/sec*, fino a 60 MiB/sec ,  | Vedere i valori in ingresso e in uscita della condivisione file Premium|
| Valore massimo in uscita per una singola condivisione file | Vedere la velocità effettiva di destinazione della condivisione file Standard | Fino a 6.204 MiB/s |
| Valore massimo in ingresso per una singola condivisione file | Vedere la velocità effettiva di destinazione della condivisione file Standard | Fino a 4.136 MiB/s |
| Numero massimo di handle aperti per ogni file o directory | 2000 handle aperti | 2000 handle aperti |
| Numero massimo di condivisioni snapshot | 200 snapshot di condivisione | 200 snapshot di condivisione |
| Lunghezza massima del nome dell'oggetto (directory e file) | 2\.048 caratteri | 2\.048 caratteri |
| Numero massimo di componenti del percorso (nel percorso \A\B\C\D ogni lettera è un componente) | 255 caratteri | 255 caratteri |

\* Il valore predefinito per le condivisioni file Standard è 5 TiB. Vedere [Abilitare e creare condivisioni file di grandi dimensioni](../articles/storage/files/storage-files-how-to-create-large-file-share.md) per informazioni su come aumentare fino a 100 TiB le dimensioni delle condivisioni file Standard.
