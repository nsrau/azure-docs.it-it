---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2019
ms.locfileid: "71180055"
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

|Area  |File Premium  |File standard |
|---------|---------|---------|
|Size                  |1 TiB         |1 TiB|
|Numero massimo di IOPS per file     |5\.000         |1\.000|
|Handle simultanei    |2\.000         |2\.000|
|Egress  |300 MiB/sec|      Vedere valori di velocità effettiva del file standard|
|Ingress  |200 MiB/sec| Vedere valori di velocità effettiva del file standard|
|Velocità effettiva| Vedere valori in ingresso/uscita file Premium| Fino a 60 MiB/sec|
