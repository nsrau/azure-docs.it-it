---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: c2bd10ab4c98fe2e77332c3cc2566ab2f0c7ad42
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87841923"
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
|Dimensione                  |4 TiB         |1 TiB|
|Numero massimo di IOPS per file     |5\.000         |1\.000|
|Handle simultanei    |2\.000         |2\.000|
|Egress  |300 MiB/sec|      Vedere valori di velocità effettiva del file standard|
|Dati in ingresso  |200 MiB/sec| Vedere valori di velocità effettiva del file standard|
|Velocità effettiva| Vedere valori in ingresso/uscita file Premium| Fino a 60 MiB/sec|
