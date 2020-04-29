---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334978"
---
**Dischi di macchina virtuale non gestiti Premium: limiti per account**

| Risorsa | Limite |
| --- | --- |
| Capacità totale dischi per account |35 TB |
| Capacità totale snapshot per account |10 TB |
| Larghezza di banda massima per account (in ingresso + in uscita)<sup>1</sup> |<=50 Gbps |

<sup>1</sup>il traffico in*ingresso* si riferisce a tutti i dati delle richieste inviate a un account di archiviazione. In *uscita* si intendono tutti i dati delle risposte ricevute da un account di archiviazione.

**Dischi di macchina virtuale non gestiti Premium: limiti per disco**

| Tipo di disco di Archiviazione Premium | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Dimensioni disco |128 GiB |512 GiB |GiB 1.024 (1 TB) |GiB 2.048 (2 TB)|GiB 4.095 (4 TB)|
| Numero massimo di IOPS per disco |500 |2\.300 |5\.000 |7\.500 |7\.500 |
| Velocità effettiva massima per disco |100 MB/sec | 150 MB/sec |200 MB/sec |250 MB/sec |250 MB/sec |
| Numero massimo di dischi per account di archiviazione |280 |70 |35 | 17 | 8 |

**Dischi di macchina virtuale non gestiti Premium: limiti per VM**

| Risorsa | Limite |
| --- | --- |
| Numero massimo di IOPS per macchina virtuale |80.000 IOPS con VM GS5 |
| Velocità effettiva massima per macchina virtuale |2.000 MB/sec con VM GS5 |

