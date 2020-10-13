---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: tamram
ms.openlocfilehash: fe493ca4a2edf16bfda405e8b2c5581c0f745616
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665608"
---
| Risorsa | Destinazione | Destinazione (anteprima) |
|-|-|-|
| Dimensioni massime di un singolo contenitore BLOB | Uguale alla capacità massima dell'account di archiviazione |  |
| Numero massimo di blocchi in un BLOB in blocchi o in un BLOB di accodamento | 50.000 blocchi |  |
| Dimensioni massime di un blocco in un BLOB in blocchi | 100 MiB | 4000 MiB (anteprima) |
| Dimensioni massime di un BLOB in blocchi | 50.000 x 100 MiB (approssimativamente 4,75 TiB) | 50.000 x 4000 MiB (approssimativamente 190,7 TiB) (anteprima) |
| Dimensioni massime di un blocco in un BLOB di accodamento | 4 MiB |  |
| Dimensioni massime di un BLOB di accodamento | 50.000 x 4 MiB (approssimativamente 195 GiB) |  |
| Dimensioni massime di un BLOB di pagine | 8 TiB<sup>2</sup> |  |
| Numero massimo di criteri di accesso archiviati per contenitore BLOB | 5 |  |
| Frequenza delle richieste di destinazione per un singolo BLOB | Fino a 500 richieste al secondo |  |
| Velocità effettiva di destinazione per un singolo BLOB di pagine | Fino a 60 MiB al secondo<sup>2</sup> |  |
| Velocità effettiva di destinazione per un singolo BLOB in blocchi | Fino ai limiti dei dati di ingresso/in uscita dell'account di archiviazione<sup>1</sup> |  |

<sup>1</sup> La velocità effettiva per un singolo BLOB dipende da diversi fattori, tra cui, senza limitazioni, la concorrenza, le dimensioni della richiesta, il livello di prestazioni, la velocità dell'origine per i caricamenti e la destinazione per i download. Per sfruttare le prestazioni più elevate dei [BLOB in blocchi con velocità effettiva elevata](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/), caricare BLOB o blocchi più grandi. In particolare, chiamare l'operazione [Put BLOB](/rest/api/storageservices/put-blob) o [Put Block](/rest/api/storageservices/put-block) con dimensioni dei BLOB o dei blocchi maggiori di 4 MiB per gli account di archiviazione standard. Per i BLOB in blocchi Premium o per gli account di archiviazione Data Lake Storage Gen2, usare dimensioni dei blocchi o dei BLOB maggiori di 256 KiB.

<sup>2</sup> I BLOB di pagine non sono ancora supportati negli account per cui è specificata l'impostazione **Spazio dei nomi gerarchico**.

La tabella seguente illustra le dimensioni massime di blocchi e BLOB consentite dalla versione del servizio.

| Versione del servizio | Dimensioni massime dei blocchi (tramite Put Block) | Dimensioni massime dei BLOB (tramite Put Block List) | Dimensioni massime dei BLOB tramite singola operazione di scrittura (tramite Put BLOB) |
|-|-|-|-|
| Versione 2019-12-12 e successive | 4000 MiB (anteprima) | Approssimativamente 190,7 TiB (blocchi di 4000 MiB x 50.000) (anteprima) | 5000 MiB (anteprima) |
| Dalla versione 2016-05-31 alla versione 2019-07-07 | 100 MiB | Approssimativamente 4,75 TiB (blocchi di 100 MiB x 50.000) | 256 MiB |
| Versioni precedenti alla 2016-05-31 | 4 MiB | Approssimativamente 195 GiB (blocchi di 4 MiB x 50.000) | 64 MiB |
