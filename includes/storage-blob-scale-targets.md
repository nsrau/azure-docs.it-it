---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/20/2019
ms.author: tamram
ms.openlocfilehash: aab17966862c57a52f252b3c4e9b757673078b0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66114765"
---
| Resource | Destinazione        |
|----------|---------------|
| Dimensioni massime del singolo contenitore blob | Uguale a capacità dell'account di archiviazione massimo |
| Numero massimo di blocchi in un blocco di blob o blob di aggiunta | 50\.000 blocchi |
| Dimensioni massime di un blocco in un blob in blocchi | 100 MiB |
| Dimensioni massime di un blob in blocchi | 50\.000 x 100 MiB (circa 4,75 TiB) |
| Dimensioni massime di un blocco in un blob di Accodamento | 4 MiB |
| Dimensioni massime di un blob di Accodamento | 50\.000 x 4 MiB (circa 195 GiB) |
| Dimensioni massime di un blob di pagine | 8 TiB |
| Numero massimo di criteri di accesso archiviati per il contenitore blob | 5 |
|Velocità effettiva da raggiungere per BLOB singolo |Fino a limiti di ingresso/uscita di account di archiviazione<sup>1</sup> |

<sup>1</sup> la velocità effettiva singolo oggetto dipende da diversi fattori, tra cui, ma non solo: concorrenza, le dimensioni delle richieste, livello di prestazioni, velocità di origine per il caricamento e di destinazione per i download. Per poter sfruttare [blob in blocchi ad alta velocità](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) miglioramenti alle prestazioni, usare dimensioni della richiesta Put Blob o Put Block > 4 MiB (> 256 KB per archiviazione blob in blocchi di prestazioni premium o per Data Lake Storage Gen2).
