---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334978"
---
**Dischi di macchine virtuali non gestite Premium: limiti per accountPremium unmanaged virtual machine disks: Per-account limits**

| Risorsa | Limite |
| --- | --- |
| Capacità totale dischi per account |35 TB |
| Capacità totale snapshot per account |10 TB |
| Larghezza di banda massima per account (ingresso e uscita)<sup>1</sup> |<=50 Gbps |

<sup>1</sup>*Ingress* si riferisce a tutti i dati delle richieste inviate a un account di archiviazione. *L'uscita* si riferisce a tutti i dati delle risposte ricevute da un account di archiviazione.

**Dischi di macchine virtuali non gestite Premium: limiti per discoPremium unmanaged virtual machine disks: Per-disk limits**

| Tipo di disco di Archiviazione Premium | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Dimensioni disco |128 GiB |512 GiB |1.024 GiB (1 TB) |2.048 GiB (2 TB)|4.095 GiB (4 TB)|
| Numero massimo di operazioni di I/O al secondo per disco |500 |2.300 |5.000 |7.500 |7.500 |
| Velocità effettiva massima per discoMaximum throughput per disk |100 MB/sec | 150 MB/sec |200 MB/sec |250 MB/sec |250 MB/sec |
| Numero massimo di dischi per account di archiviazioneMaximum number of disks per storage account |280 |70 |35 | 17 | 8 |

**Dischi di macchine virtuali non gestite Premium: limiti per macchina virtualePremium unmanaged virtual machine disks: Per-VM limits**

| Risorsa | Limite |
| --- | --- |
| Numero massimo di operazioni di I/O al secondo per macchina virtualeMaximum IOPS per |80.000 IOPS con VM GS5 |
| Velocità effettiva massima per macchina virtualeMaximum throughput per VM |2.000 MB/sec con VM GS5 |

