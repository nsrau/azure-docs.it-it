---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66148163"
---
**Dischi di macchine virtuali non gestiti Premium: Limiti per ogni account**

| Resource | Limite predefinito |
| --- | --- |
| Capacità totale dischi per account |35 TB |
| Capacità totale snapshot per account |10 TB |
| Larghezza di banda massima per ogni account (ingresso + uscita)<sup>1</sup> |<=50 Gbps |

<sup>1</sup>*ingress* fa riferimento a tutti i dati dalle richieste inviate a un account di archiviazione. *Traffico in uscita* fa riferimento a tutti i dati dalle risposte che vengono ricevute da un account di archiviazione.

**Dischi di macchine virtuali non gestiti Premium: Limiti per disco**

| Tipo di disco di Archiviazione Premium | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Dimensioni disco |128 GiB |512 GiB |1,024 GiB (1 TB) |2,048 GiB (2 TB)|4,095 GiB (4 TB)|
| Numero massimo di IOPS per disco |500 |2\.300 |5\.000 |7\.500 |7\.500 |
| Velocità effettiva massima per disco |100 MB/sec | 150 MB/sec |200 MB/sec |250 MB/sec |250 MB/sec |
| Numero massimo di dischi per account di archiviazione |280 |70 |35 | 17 | 8 |

**Dischi di macchine virtuali non gestiti Premium: Limiti per ogni VM**

| Resource | Limite predefinito |
| --- | --- |
| Numero massimo di IOPS per ogni macchina virtuale |80\.000 IOPS con VM GS5 |
| Velocità effettiva massima per macchina virtuale |2\.000 MB/sec con VM GS5 |

