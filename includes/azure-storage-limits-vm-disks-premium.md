---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "67180136"
---
**Dischi di macchina virtuale non gestiti Premium: Limiti per account**

| Risorsa | Limite predefinito |
| --- | --- |
| Capacità totale dischi per account |35 TB |
| Capacità totale snapshot per account |10 TB |
| Larghezza di banda massima per account (in ingresso + in uscita)<sup>1</sup> |<=50 Gbps |

<sup>1</sup> Il traffico in *ingresso* si riferisce a tutti i dati delle richieste inviate a un account di archiviazione. In *uscita* si intendono tutti i dati delle risposte ricevute da un account di archiviazione.

**Dischi di macchina virtuale non gestiti Premium: Limiti per disco**

| Tipo di disco di Archiviazione Premium | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Dimensioni disco |128 GiB |512 GiB |GiB 1.024 (1 TB) |GiB 2.048 (2 TB)|GiB 4.095 (4 TB)|
| Numero massimo di IOPS per disco |500 |2\.300 |5\.000 |7\.500 |7\.500 |
| Velocità effettiva massima per disco |100 MB/sec | 150 MB/sec |200 MB/sec |250 MB/sec |250 MB/sec |
| Numero massimo di dischi per account di archiviazione |280 |70 |35 | 17 | 8 |

**Dischi di macchina virtuale non gestiti Premium: Limiti per VM**

| Risorsa | Limite predefinito |
| --- | --- |
| Numero massimo di IOPS per macchina virtuale |80.000 IOPS con VM GS5 |
| Velocità effettiva massima per macchina virtuale |2\.000 MB/sec con VM GS5 |

