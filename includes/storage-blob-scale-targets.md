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
| Dimensioni massime di un singolo contenitore BLOB | Uguale alla capacità massima dell'account di archiviazione |
| Numero massimo di blocchi in un BLOB in blocchi o in un BLOB di Accodamento | 50.000 blocchi |
| Dimensione massima di un blocco in un BLOB in blocchi | 100 MiB |
| Dimensioni massime di un BLOB in blocchi | 50.000 X 100 MiB (approssimativamente 4,75 TiB) |
| Dimensione massima di un blocco in un BLOB di Accodamento | 4 MiB |
| Dimensioni massime di un BLOB di Accodamento | 50.000 x 4 MiB (circa 195 GiB) |
| Dimensioni massime di un BLOB di pagine | 8 TiB |
| Numero massimo di criteri di accesso archiviati per ogni contenitore BLOB | 5 |
|Frequenza delle richieste di destinazione per un singolo BLOB | Fino a 500 richieste al secondo |
|Velocità effettiva di destinazione per un singolo BLOB di pagine | Fino a 60 MiB al secondo |
|Velocità effettiva di destinazione per un singolo BLOB in blocchi |Fino a limiti di ingresso/uscita dell'account di archiviazione<sup>1</sup> |

<sup>1</sup> la velocità effettiva per un singolo BLOB dipende da diversi fattori, tra cui: concorrenza, dimensioni della richiesta, livello di prestazioni, velocità dell'origine per i caricamenti e destinazione per i download. Per sfruttare i miglioramenti apportati alle prestazioni di [BLOB in blocchi con velocità effettiva elevata](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/), caricare BLOB o blocchi più grandi. In particolare, chiamare l'operazione [Put Blob](/rest/api/storageservices/put-blob) o [Put Block](/rest/api/storageservices/put-block) con un BLOB o una dimensione del blocco maggiore di 4 MiB per gli account di archiviazione standard. Per i BLOB in blocchi Premium o per Data Lake Storage Gen2 account di archiviazione, usare una dimensione del BLOB o del blocco maggiore di 256 KiB.
