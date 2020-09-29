---
title: includere file
description: Includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7b41105bafedb8eeaffe5f266f5dd824957c57e0
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91441183"
---
Per il momento, i dischi Ultra presentano limitazioni aggiuntive, come indicato di seguito:

Le uniche opzioni di ridondanza dell'infrastruttura attualmente disponibili per i dischi Ultra sono le zone di disponibilità. Le macchine virtuali che usano altre opzioni di ridondanza non possono alleghi un disco Ultra.

La tabella seguente descrive le aree in cui sono disponibili i dischi Ultra, oltre alle opzioni di disponibilità corrispondenti:

> [!NOTE]
> Se una regione nell'elenco seguente non dispone di zone di disponibilità con supporto di dischi Ultra, le macchine virtuali in tale area devono essere distribuite senza opzioni di ridondanza dell'infrastruttura per poter alleghire un disco Ultra.

|Regioni  |Opzioni di ridondanza  |
|---------|---------|
|Brasile meridionale     |Solo macchine virtuali singole (i set di disponibilità e i set di scalabilità di macchine virtuali non sono supportati)|
|India centrale     |Solo macchine virtuali singole (i set di disponibilità e i set di scalabilità di macchine virtuali non sono supportati)|
|Asia orientale     |Solo macchine virtuali singole (i set di disponibilità e i set di scalabilità di macchine virtuali non sono supportati)|
|Germania centro-occidentale     |Solo macchine virtuali singole (i set di disponibilità e i set di scalabilità di macchine virtuali non sono supportati)|
|Corea centrale     |Solo macchine virtuali singole (i set di disponibilità e i set di scalabilità di macchine virtuali non sono supportati)|
|Stati Uniti centro-meridionali    |Solo macchine virtuali singole (i set di disponibilità e i set di scalabilità di macchine virtuali non sono supportati)|
|US Gov Arizona     |Solo macchine virtuali singole (i set di disponibilità e i set di scalabilità di macchine virtuali non sono supportati)|
|US Gov Virginia     |Solo macchine virtuali singole (i set di disponibilità e i set di scalabilità di macchine virtuali non sono supportati)|
|Stati Uniti occidentali     |Solo macchine virtuali singole (i set di disponibilità e i set di scalabilità di macchine virtuali non sono supportati)        |
|Australia centrale    |Solo macchine virtuali singole (i set di disponibilità e i set di scalabilità di macchine virtuali non sono supportati)|
|Australia orientale     |Tre zone di disponibilità         |
|Asia sud-orientale    |Tre zone di disponibilità        |
|Canada centrale *     |Tre zone di disponibilità          |
|Stati Uniti centrali     |Tre zone di disponibilità          |
|Stati Uniti orientali     |Tre zone di disponibilità          |
|Stati Uniti orientali 2     |Tre zone di disponibilità         |
|Francia centrale    |Due zone di disponibilità        |
|Giappone orientale    |Tre zone di disponibilità        |
|Europa settentrionale    |Tre zone di disponibilità        |
|Regno Unito meridionale    |Tre zone di disponibilità        |
|Europa occidentale    | Tre zone di disponibilità|
|Stati Uniti occidentali 2    |Tre zone di disponibilità|

\* Contattare il supporto tecnico di Azure per ottenere l'accesso ai zone di disponibilità per questa area.

- Sono supportate solo nelle serie di macchine virtuali seguenti:
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series)
    - [Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series)
    - [Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [Dasv4](../articles/virtual-machines/dav4-dasv4-series.md#dasv4-series)
    - [Ddsv4](../articles/virtual-machines/ddv4-ddsv4-series.md#ddsv4-series)
    - [Dsv4](../articles/virtual-machines/dv4-dsv4-series.md#dsv4-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Non tutte le dimensioni della macchina virtuale sono disponibili in ogni area supportata con dischi Ultra.
- Sono disponibili solo come dischi dati. 
- Supportare le dimensioni del settore fisico 4K per impostazione predefinita. le dimensioni del settore 512E sono disponibili come offerta disponibile a livello generale, ma è necessario [iscriversi](https://aka.ms/ultradisk512e). La maggior parte delle applicazioni è compatibile con le dimensioni del settore 4K, ma alcune richiedono dimensioni di settore a 512 byte. Un esempio è Oracle Database, che richiede la versione 12,2 o successiva per supportare i dischi nativi 4K. Per le versioni precedenti di Oracle DB, sono richieste dimensioni di settore di 512 byte.
- Può essere creato solo come dischi vuoti.
- Attualmente non supporta gli snapshot del disco, le immagini di VM, i set di disponibilità, gli host dedicati di Azure o crittografia dischi di Azure.
- Attualmente non supporta l'integrazione con backup di Azure o Azure Site Recovery.
- Supporta solo letture non memorizzate nella cache e scritture non memorizzate nella cache.
- Il limite massimo corrente per IOPS nelle VM GA è 80.000.

Azure ultra Disks offre fino a 16 TiB per area per sottoscrizione per impostazione predefinita, ma i dischi Ultra supportano una capacità superiore per richiesta. Per richiedere un aumento della capacità, contattare il supporto tecnico di Azure.
