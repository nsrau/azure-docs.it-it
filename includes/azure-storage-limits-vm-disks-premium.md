---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80334978"
---
**Dischi delle macchine virtuali non gestiti Premium: limiti per account**

| Risorsa | Limite |
| --- | --- |
| Capacità totale dischi per account |35 TB |
| Capacità totale snapshot per account |10 TB |
| Larghezza di banda massima per account (ingresso + uscita)<sup>1</sup> |<=50 Gbps |

<sup>1</sup>Si intendono in *ingresso* tutti i dati delle richieste inviate a un account di archiviazione. Si intendono in *uscita* tutti i dati delle richieste ricevute da un account di archiviazione.

**Dischi delle macchine virtuali non gestiti Premium: limiti per disco**

| Tipo di disco di Archiviazione Premium | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Dimensioni del disco |128 GiB |512 GiB |1\.024 GiB (1 TB) |2\.048 GiB (2 TB)|4\.095 GiB (4 TB)|
| Numero massimo di operazioni di I/O al secondo per disco |500 |2\.300 |5\.000 |7\.500 |7\.500 |
| Velocità effettiva massima per disco |100 MB/sec | 150 MB/sec |200 MB/sec |250 MB/sec |250 MB/sec |
| Numero massimo di dischi per account di archiviazione |280 |70 |35 | 17 | 8 |

**Dischi delle macchine virtuali non gestiti Premium: limiti per macchina virtuale**

| Risorsa | Limite |
| --- | --- |
| Numero massimo di operazioni di I/O al secondo per macchina virtuale |80.000 IOPS con VM GS5 |
| Velocità effettiva massima per macchina virtuale |2\.000 MB/sec con VM GS5 |

