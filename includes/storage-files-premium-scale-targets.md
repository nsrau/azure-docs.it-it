---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: 368f08272173b019873dfe20e1164d6baf72ff5e
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542638"
---
#### <a name="additional-premium-file-share-level-limits"></a>Limiti dei livelli di condivisione file premium aggiuntive

|Area  |Destinazione  |
|---------|---------|
|Aumentare o ridurre la dimensione minima    |1 GiB      |
|Operazioni di I/O al secondo di base    |1 IOPS GiB, fino a 100.000|
|IOPS bursting    |3 x IOPS per GiB, fino a 100.000|
|Velocità in uscita         |60 MiB/s + 0,06 * provisioning GiB        |
|Velocità in ingresso| 40 MiB/s + 0,04 * provisioning GiB |

#### <a name="file-level-limits"></a>Limiti a livello di file

|Area  |File Premium  |File standard |
|---------|---------|---------|
|Dimensione                  |1 TiB         |1 TiB|
|IOPS Max per ogni file     |5\.000         |1\.000|
|Handle simultanei    |2\.000         |2\.000|
|Ingress  |300 MiB/sec|      Visualizzare i valori di velocità effettiva di file standard|
|Egress   |200 Mib/sec| Visualizzare i valori di velocità effettiva di file standard|
|Velocità effettiva| Vedere i valori dei dati in ingresso/uscita file premium| Fino a 60 MiB/sec|