---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 2ed88d8abb7cbe96093b68d89030e6e464a35541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75392315"
---
| Risorsa | Destinazione        |
|----------|---------------|
| Dimensione massima del contenitore BLOB singoloMaximum size of single blob container | Uguale alla capacità massima dell'account di archiviazione |
| Numero massimo di blocchi in un BLOB in blocchi o in un BLOB di accodamento | 50.000 blocchi |
| Dimensione massima di un blocco in un BLOB di blocchiMaximum size of a block in a block blob | 100 MiB |
| Dimensione massima di un BLOB in blocchiMaximum size of a block blob | 50.000 X 100 MiB (circa 4,75 TiB) |
| Dimensione massima di un blocco in un BLOB di accodamentoMaximum size of a block in an append blob | 4 MiB |
| Dimensione massima di un BLOB di accodamentoMaximum size of an append blob | 50.000 x 4 MiB (circa 195 GiB) |
| Dimensione massima di un BLOB di pagineMaximum size of a page blob | 8 TiB |
| Numero massimo di criteri di accesso archiviati per contenitore BLOBMaximum number of stored access policies per blob container | 5 |
|Frequenza delle richieste di destinazione per un singolo BLOBTarget request rate for a single blob | Fino a 500 richieste al secondo |
|Velocità effettiva di destinazione per un BLOB a pagina singolaTarget throughput for a single page blob | Fino a 60 MiB al secondo |
|Velocità effettiva di destinazione per un singolo BLOB in blocchiTarget throughput for a single block blob |Fino ai limiti di ingresso/uscita dell'account di archiviazione<sup>1</sup> |

<sup>1</sup> La velocità effettiva per un singolo BLOB dipende da diversi fattori, tra cui, ma non solo: concorrenza, dimensione della richiesta, livello di prestazioni, velocità di origine per i caricamenti e destinazione per i download. Per sfruttare i miglioramenti delle prestazioni dei BLOB con blocchi ad [alta velocità effettiva,](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)caricare BLOB o blocchi di dimensioni maggiori. In particolare, chiamare l'operazione [Put Blob](/rest/api/storageservices/put-blob) o [Put Block](/rest/api/storageservices/put-block) con un BLOB o una dimensione del blocco maggiore di 4 MiB per gli account di archiviazione standard. Per il BLOB in blocchi premium o per gli account di archiviazione Data Lake Storage Gen2, usare un blocco o una dimensione del BLOB maggiore di 256 KiB.
