---
title: includere il file
description: Includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/25/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 546c21802d275fe99ed2acbf00e32d37db3603f2
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225198"
---
Per il momento, i dischi Ultra presentano limitazioni aggiuntive, come indicato di seguito:

Le uniche opzioni di ridondanza dell'infrastruttura attualmente disponibili per i dischi Ultra sono le zone di disponibilità. Le macchine virtuali che usano altre opzioni di ridondanza non possono alleghi un disco Ultra.

La tabella seguente descrive le aree in cui sono disponibili i dischi Ultra, oltre alle opzioni di disponibilità corrispondenti:

> [!NOTE]
> Se una regione nell'elenco seguente non dispone di zone di disponibilità con supporto di dischi Ultra, le macchine virtuali in tale area devono essere distribuite senza opzioni di ridondanza dell'infrastruttura per poter alleghire un disco Ultra.

|Regioni  |Numero di zone di disponibilità che supportano dischi ultra  |
|---------|---------|
|US Gov Virginia     |nessuno         |
|US Gov Arizona     |nessuno         |
|Stati Uniti centro-meridionali     |nessuno         |
|Stati Uniti centrali     |Tre zone         |
|Stati Uniti occidentali     |nessuno         |
|West US 2    |Tre zone         |
|Stati Uniti orientali     |Tre zone         |
|Stati Uniti orientali 2     |Tre zone         |
|Asia sudorientale     |Tre zone         |
|Asia orientale     |nessuno         |
|Europa settentrionale     |Tre zone          |
|Europa occidentale     |Tre zone          |
|Regno Unito meridionale     |Tre zone          |
|Giappone orientale     |Tre zone         |
|Francia centrale    |Due zone        |
|Brasile meridionale    |nessuno        |
|Australia orientale    |Tre zone        |
|Canada centrale *    |Tre zone        |

\* Contattare il supporto tecnico di Azure per ottenere l'accesso ai zone di disponibilità per questa area.

- Sono supportate solo nelle serie di macchine virtuali seguenti:
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series)
    - [Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series)
    - [Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series)
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