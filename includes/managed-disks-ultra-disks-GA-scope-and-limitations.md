---
title: includere il file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/25/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: aa9327bd0ba6763aa4e89630611aabb3c5195655
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85448723"
---
Per il momento, i dischi Ultra presentano limitazioni aggiuntive, come indicato di seguito:

Le uniche opzioni di ridondanza dell'infrastruttura attualmente disponibili per i dischi Ultra sono le zone di disponibilità. Le macchine virtuali che usano altre opzioni di ridondanza non possono alleghi un disco Ultra.

La tabella seguente descrive le aree in cui sono disponibili i dischi Ultra, oltre alle opzioni di disponibilità corrispondenti:

> [!NOTE]
> Se una regione nell'elenco seguente non dispone di zone di disponibilità con supporto di dischi Ultra, le macchine virtuali in tale area devono essere distribuite senza opzioni di ridondanza dell'infrastruttura per poter alleghire un disco Ultra.

|Regioni  |Numero di zone di disponibilità che supportano dischi ultra  |
|---------|---------|
|US Gov Virginia     |nessuno         |
|Stati Uniti centro-meridionali     |nessuno         |
|Stati Uniti centrali     |Tre zone         |
|Stati Uniti occidentali     |nessuno         |
|Stati Uniti occidentali 2    |Tre zone         |
|Stati Uniti orientali     |Tre zone         |
|Stati Uniti orientali 2     |Due zone         |
|Asia sudorientale     |Tre zone         |
|Europa settentrionale     |Tre zone          |
|Europa occidentale     |Tre zone          |
|Regno Unito meridionale     |Tre zone          |
|Giappone orientale     |Due zone         |
|Francia centrale    |Due zone        |


- Sono supportate solo nelle serie di macchine virtuali seguenti:
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Non tutte le dimensioni della macchina virtuale sono disponibili in ogni area supportata con dischi ultra
- Sono disponibili solo come dischi dati e supportano solo le dimensioni del settore fisico 4K. A causa delle dimensioni del settore nativo 4K del disco Ultra, sono disponibili alcune applicazioni che non saranno compatibili con i dischi Ultra. Un esempio è Oracle Database, che richiede la versione 12,2 o successiva per supportare i dischi Ultra.  
- Possono essere creati solo come dischi vuoti  
- Attualmente non supporta gli snapshot del disco, le immagini di VM, i set di disponibilità, gli host dedicati di Azure o crittografia dischi di Azure
- Attualmente non supporta l'integrazione con backup di Azure o Azure Site Recovery
- Supporta solo letture non memorizzate nella cache e scritture non memorizzate nella cache
- Il limite massimo corrente per IOPS nelle VM GA è 80.000.

Azure ultra Disks offre fino a 16 TiB per area per sottoscrizione per impostazione predefinita, ma i dischi Ultra supportano una capacità superiore per richiesta. Per richiedere un aumento della capacità, contattare il supporto tecnico di Azure.