---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: 8dcb58499113b0b7ae0814419f0a76965a0ed945
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681148"
---
#### <a name="additional-premium-file-share-level-limits"></a>Limiti aggiuntivi a livello di condivisione file Premium

|Area  |Destinazione  |
|---------|---------|
|Aumento/riduzione dimensioni minime    |1 GiB      |
|Operazioni di I/O al secondo di base    |1 IOPS per GiB, fino a 100.000|
|Picchi di IOPS    |3 IOPS per GiB, fino a 100.000|
|Velocità in uscita         |60 MiB/s + 0,06 * GiB con provisioning        |
|Velocità in ingresso| 40 MiB/s + 0,04 * GiB con provisioning |

#### <a name="file-level-limits"></a>Limiti a livello di file

|Area  |File standard  |File Premium  |
|---------|---------|---------|
|Dimensione     |1 TiB         |4 TiB         |
|Numero massimo di IOPS per file      |1\.000         |Fino a 8.000 *         |
|Handle simultanei     |2.000         |2.000         |
|Egress     |Vedere valori di velocità effettiva del file standard         |300 MiB/sec (fino a 1 GiB/s con SMB multicanale Preview) * *         |
|Dati in ingresso     |Vedere valori di velocità effettiva del file standard         |200 MiB/sec (fino a 1 GiB/s con SMB multicanale Preview) * *        |
|Velocità effettiva     |Fino a 60 MiB/sec         |Vedere valori in ingresso/uscita file Premium         |

\*<sup>Si applica alle operazioni di i/o di lettura e scrittura (in genere dimensioni i/o minori <= 64K) Le operazioni sui metadati, ad eccezione delle letture e scritture, possono essere inferiori. </sup>

\*\*<sup>Soggetta a limiti di rete del computer, larghezza di banda disponibile, dimensioni i/o, profondità della coda e altri fattori. Per informazioni dettagliate, vedere [SMB multicanale performance](../articles/storage/files/storage-files-smb-multichannel-performance.md). </sup>
