---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 0fda881b805eb3a967cf3b05f6c6df8c65d20730
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905786"
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

<sup>1</sup> la velocità effettiva di un singolo oggetto dipende da diversi fattori, tra cui: concorrenza, dimensioni della richiesta, livello di prestazioni, velocità dell'origine per i caricamenti e destinazione per i download. Per sfruttare i vantaggi offerti dai miglioramenti delle prestazioni dei [BLOB in blocchi con velocità effettiva elevata](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) , usare una dimensione Put Blob o Put Block request di > 4 MiB (> 256 KiB per l'archiviazione BLOB in blocchi a prestazioni Premium o per data Lake storage Gen2).
