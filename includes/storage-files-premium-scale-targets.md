---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "71180055"
---
#### <a name="additional-premium-file-share-level-limits"></a>Ulteriori limiti a livello di condivisione file premium

|Area  |Destinazione  |
|---------|---------|
|Aumento/diminuzione dimensioni minime    |1 GiB (in via)      |
|Operazioni di I/O al secondo di base    |1 IOPS per GiB, fino a 100.000|
|Bursting di operazioni di I/O al secondo    |3x IOPS per GiB, fino a 100.000|
|Tasso di uscita         |60 MiB/s - 0,06 - GiB di cui è stato eseguito il provisioning        |
|Tasso di ingresso| 40 MiB/s - 0,04 - GiB di cui è stato eseguito il provisioning |

#### <a name="file-level-limits"></a>Limiti a livello di file

|Area  |File Premium  |File standard |
|---------|---------|---------|
|Dimensione                  |1 TiB         |1 TiB|
|Numero massimo di operazioni di I/O al secondo per fileMax IOPS per file     |5.000         |1.000|
|Maniglie simultanee    |2.000         |2.000|
|Egress  |300 MiB/sec|      Visualizzare i valori di velocità effettiva dei file standardSee standard file throughput values|
|Dati in ingresso  |200 MiB/sec| Visualizzare i valori di velocità effettiva dei file standardSee standard file throughput values|
|Velocità effettiva| Visualizzare i valori di ingresso/uscita dei file premium| Fino a 60 MiB/sec|
