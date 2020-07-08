---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/23/2020
ms.author: tamram
ms.openlocfilehash: 43a72d915fa5a00c54bef7a06fe3815a7d63f2fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85806123"
---
| Risorsa | Destinazione | Destinazione (anteprima) |
|-|-|-|
| Dimensioni massime di un singolo contenitore BLOB | Uguale alla capacità massima dell'account di archiviazione |  |
| Numero massimo di blocchi in un BLOB in blocchi o in un BLOB di Accodamento | 50.000 blocchi |  |
| Dimensione massima di un blocco in un BLOB in blocchi | 100 MiB | 4000 MiB (anteprima) |
| Dimensioni massime di un BLOB in blocchi | 50.000 X 100 MiB (approssimativamente 4,75 TiB) | 50.000 X 4000 MiB (approssimativamente 190,7 TiB) (anteprima) |
| Dimensione massima di un blocco in un BLOB di Accodamento | 4 MiB |  |
| Dimensioni massime di un BLOB di Accodamento | 50.000 x 4 MiB (circa 195 GiB) |  |
| Dimensioni massime di un BLOB di pagine | 8 TiB |  |
| Numero massimo di criteri di accesso archiviati per ogni contenitore BLOB | 5 |  |
| Frequenza delle richieste di destinazione per un singolo BLOB | Fino a 500 richieste al secondo |  |
| Velocità effettiva di destinazione per un singolo BLOB di pagine | Fino a 60 MiB al secondo |  |
| Velocità effettiva di destinazione per un singolo BLOB in blocchi | Fino a limiti di ingresso/uscita dell'account di archiviazione<sup>1</sup> |  |

<sup>1</sup> la velocità effettiva per un singolo BLOB dipende da diversi fattori, tra cui: concorrenza, dimensioni della richiesta, livello di prestazioni, velocità dell'origine per i caricamenti e destinazione per i download. Per sfruttare i miglioramenti apportati alle prestazioni di [BLOB in blocchi con velocità effettiva elevata](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/), caricare BLOB o blocchi più grandi. In particolare, chiamare l'operazione [Put Blob](/rest/api/storageservices/put-blob) o [Put Block](/rest/api/storageservices/put-block) con un BLOB o una dimensione del blocco maggiore di 4 MiB per gli account di archiviazione standard. Per i BLOB in blocchi Premium o per Data Lake Storage Gen2 account di archiviazione, usare una dimensione del BLOB o del blocco maggiore di 256 KiB.

La tabella seguente descrive le dimensioni massime dei blocchi e dei BLOB consentiti dalla versione del servizio.

| Versione del servizio | Dimensioni massime blocco (tramite blocco put) | Dimensioni massime BLOB (tramite l'elenco Put Block) | Dimensioni massime del BLOB tramite singola operazione di scrittura (tramite Put Blob) |
|-|-|-|-|
| Versione 2019-12-12 e successive | 4000 MiB (anteprima) | Approssimativamente 190,7 TiB (blocchi 4000 MiB X 50.000) (anteprima) | 5000 MiB (anteprima) |
| Dalla versione 2016-05-31 alla versione 2019-07-07 | 100 MiB | Circa 4,75 TiB (blocchi 100 MiB X 50.000) | 256 MiB |
| Versioni precedenti alla 2016-05-31 | 4 MiB | Approssimativamente 195 GiB (4 MB X 50.000 blocchi) | 64 MiB |
