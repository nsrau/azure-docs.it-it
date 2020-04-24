---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a5e0e459800e7cb57672518597f3d04a74f53118
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008639"
---
Per il momento, i dischi Ultra presentano limitazioni aggiuntive, come indicato di seguito:

Le uniche opzioni di ridondanza dell'infrastruttura attualmente disponibili per i dischi Ultra sono le zone di disponibilità. Le macchine virtuali che usano altre opzioni di ridondanza non possono alleghi un disco Ultra.

La tabella seguente descrive le aree in cui sono disponibili i dischi Ultra, oltre alle opzioni di disponibilità corrispondenti:

> [!NOTE]
> Alcune zone di disponibilità all'interno di queste aree non offrono dischi Ultra.

|Regioni  |Nessuna ridondanza dell'infrastruttura  |Zone di disponibilità  |
|---------|---------|---------|
|Stati Uniti occidentali     |Sì         |No         |
|Stati Uniti occidentali 2    |No         |Sì         |
|Stati Uniti orientali     |No         |Sì         |
|Stati Uniti orientali 2     |No         |Sì         |
|Asia sudorientale     |No         |Sì         |
|Europa settentrionale     |No         |Sì         |
|Europa occidentale     |No         |Sì         |
|Regno Unito meridionale     |No         |Sì         |

- Sono supportate solo nelle serie di macchine virtuali seguenti:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Non tutte le dimensioni della macchina virtuale sono disponibili in ogni area supportata con dischi ultra
- Sono disponibili solo come dischi dati e supportano solo le dimensioni del settore fisico 4K. A causa delle dimensioni del settore nativo 4K del disco Ultra, sono disponibili alcune applicazioni che non saranno compatibili con i dischi Ultra. Un esempio è Oracle Database, che richiede la versione 12,2 o successiva per supportare i dischi Ultra.  
- Possono essere creati solo come dischi vuoti  
- Attualmente non supporta gli snapshot del disco, le immagini di VM, i set di disponibilità, gli host dedicati di Azure o crittografia dischi di Azure
- Attualmente non supporta l'integrazione con backup di Azure o Azure Site Recovery
- Il limite massimo corrente per IOPS nelle VM GA è 80.000.

Azure ultra Disks offre fino a 16 TiB per area per sottoscrizione per impostazione predefinita, ma i dischi Ultra supportano una capacità superiore per richiesta. Per richiedere un aumento della capacità, contattare il supporto tecnico di Azure.