---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 6cf9be653da2dd587b93724b6e319dc4d20686c2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536438"
---
| Risorsa | Condivisioni file Standard | Condivisioni file Premium |
|----------|---------------|------------------------------------------|
| Dimensione massima di una condivisione file | Nessun minimo; pagare come si va | 100 GiB; Provisioning |
| Dimensione massima di una condivisione file | 100 TiB, 5 TiB | 100 TiB |
| Dimensione massima di un file in una condivisione file | 1 TiB | 1 TiB |
| Numero massimo di file in una condivisione file | Nessun limite | Nessun limite |
| Numero massimo di operazioni di I/O al secondo per condivisione | 10.000 IOPS, 1.000 IOPS | 100.000 IOPS |
| Numero massimo di criteri di accesso archiviati per condivisione fileMaximum number of stored access policies per file share | 5 | 5 |
| Velocità effettiva di destinazione per una singola condivisione fileTarget throughput for a single file share | fino a 300 MiB/sec, Fino a 60 MiB/sec ,  | Visualizzare i valori di ingresso ed uscita delle condivisioni di file premiumSee premium file share ingress and gress values|
| Massimo uscita per una singola condivisione fileMaximum egress for a single file share | Vedere la velocità effettiva di destinazione della condivisione file standardSee standard file share target throughput | Fino a 6.204 MiB/s |
| Ingresso massimo per una singola condivisione fileMaximum ingress for a single file share | Vedere la velocità effettiva di destinazione della condivisione file standardSee standard file share target throughput | Fino a 4.136 MiB/s |
| Numero massimo di handle aperti per ogni file | 2000 handle aperti | 2000 handle aperti |
| Numero massimo di condivisioni snapshot | 200 snapshot di condivisione | 200 snapshot di condivisione |
| Lunghezza massima del nome dell'oggetto (directory e file) | 2.048 caratteri | 2.048 caratteri |
| Numero massimo di componenti del percorso (nel percorso \A\B\C\D ogni lettera è un componente) | 255 caratteri | 255 caratteri |

\*Il valore predefinito nelle condivisioni file standard è 5 TiB, vedere [Abilitare e creare condivisioni file](../articles/storage/files/storage-files-how-to-create-large-file-share.md) di grandi dimensioni per i dettagli su come aumentare la scalabilità delle condivisioni file standard fino a 100 TiB.
