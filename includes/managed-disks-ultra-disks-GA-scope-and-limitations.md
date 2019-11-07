---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c847b110cb821ebe3ca7e706fcaec592dcc58fa8
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73600983"
---
Per il momento, i dischi Ultra presentano limitazioni aggiuntive, come indicato di seguito:

- Sono supportati nelle aree seguenti, con un numero variabile di zone di disponibilità per area:
    - Stati Uniti orientali 2
    - Stati Uniti orientali
    - Stati Uniti occidentali 2
    - Asia sudorientale
    - Europa settentrionale
    - Europa occidentale
    - Regno Unito meridionale 
- Possono essere usati solo con le zone di disponibilità (i set di disponibilità e le distribuzioni di macchine virtuali singole all'esterno delle zone non consentono di collegare un disco Ultra)
- Sono supportate solo nelle serie di macchine virtuali seguenti:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Non tutte le dimensioni della macchina virtuale sono disponibili in ogni area supportata con dischi Ultra.
- Sono disponibili solo come dischi dati e supportano solo dimensioni di settori fisici di 4k  
- Possono essere creati solo come dischi vuoti  
- Non sono ancora supportati gli snapshot del disco, le immagini di macchina virtuale, i set di disponibilità e crittografia dischi di Azure
- Non supporta ancora l'integrazione con backup di Azure o Azure Site Recovery
- Il limite massimo corrente per IOPS nelle VM GA è 80.000.