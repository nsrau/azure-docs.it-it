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
Per ora, i dischi ultra hanno limitazioni aggiuntive, sono i seguenti:

Le uniche opzioni di ridondanza dell'infrastruttura attualmente disponibili per i dischi ultra sono le zone di disponibilità. Le macchine virtuali che utilizzano altre opzioni di ridondanza non possono collegare un disco ultra.

Nella tabella seguente sono descritte le aree in cui sono disponibili ultradischi, nonché le opzioni di disponibilità corrispondenti:

> [!NOTE]
> Alcune zone di disponibilità all'interno di queste aree non offrono dischi ultra.

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

- Sono supportati solo nella serie di macchine virtuali seguente:Are only supported on the following VM series:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2 (in tisa 2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Non tutte le dimensioni della macchina virtuale sono disponibili in tutte le aree supportate con dischi ultra
- Sono disponibili solo come dischi dati e supportano solo le dimensioni del settore fisico 4k. A causa della dimensione del settore nativo 4K di Ultra Disk, ci sono alcune applicazioni che non saranno compatibili con i dischi ultra. Un esempio potrebbe essere Oracle Database, che richiede la versione 12.2 o successiva per supportare i dischi ultra.  
- Possono essere creati solo come dischi vuoti  
- Attualmente non supporta snapshot del disco, immagini di macchine virtuali, set di disponibilità, host dedicati di Azure o la crittografia del disco di Azure
- Attualmente non supporta l'integrazione con Backup di Azure o Azure Site Recovery
- Il limite massimo corrente per le operazioni di I/O al secondo nelle macchine virtuali GA è 80.000.The current maximum limit for IOPS on GA VMs is 80,000.

I dischi Ultra di Azure offrono fino a 16 TiB per ogni sottoscrizione per sottoscrizione per impostazione predefinita, ma i dischi ultra supportano una capacità maggiore su richiesta. Per richiedere un aumento della capacità, contattare il supporto di Azure.To request an increase in capacity, contact Azure Support.