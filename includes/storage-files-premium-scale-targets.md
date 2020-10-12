---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: d704c6026e9d007a7365a3b72649ca509585da4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88057824"
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
|Numero massimo di IOPS per file      |1\.000         |5\.000         |
|Handle simultanei     |2.000         |2.000         |
|Egress     |Vedere valori di velocità effettiva del file standard         |300 MiB/sec         |
|Dati in ingresso     |Vedere valori di velocità effettiva del file standard         |200 MiB/sec         |
|Velocità effettiva     |Fino a 60 MiB/sec         |Vedere valori in ingresso/uscita file Premium         |