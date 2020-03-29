---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/18/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b819264895e35c6ef4fe9dc5263444dcac17eaa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74935957"
---
Per ora, i dischi ultra hanno limitazioni aggiuntive, sono i seguenti:

- Sono supportate nelle aree seguenti, con un numero variabile di zone di disponibilità per area:
    - Stati Uniti orientali 2
    - Stati Uniti orientali
    - Stati Uniti occidentali 2
    - Asia sudorientale
    - Europa settentrionale
    - Europa occidentale
    - Regno Unito meridionale 
- Possono essere usati solo con le zone di disponibilità (i set di disponibilità e le distribuzioni di macchine virtuali singole all'esterno delle zone non consentono di collegare un disco Ultra)
- Sono supportati solo nella serie di macchine virtuali seguente:Are only supported on the following VM series:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2 (in tisa 2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Non tutte le dimensioni della macchina virtuale sono disponibili in tutte le aree supportate con dischi ultra
- Sono disponibili solo come dischi dati e supportano solo le dimensioni del settore fisico 4k. A causa della dimensione del settore nativo 4K di Ultra Disk, ci sono alcune applicazioni che non saranno compatibili con i dischi ultra. Un esempio potrebbe essere Oracle Database, che richiede la versione 12.2 o successiva per supportare i dischi ultra.  
- Possono essere creati solo come dischi vuoti  
- Non supportare ancora snapshot del disco, immagini di macchine virtuali, set di disponibilità e crittografia del disco di AzureDo not yet support disk snapshots, VM images, availability sets, and Azure disk encryption
- Non supportare ancora l'integrazione con Backup di Azure o Azure Site Recovery
- Il limite massimo corrente per le operazioni di I/O al secondo nelle macchine virtuali GA è 80.000.The current maximum limit for IOPS on GA VMs is 80,000.
- Se si desidera partecipare a un'anteprima limitata di una macchina virtuale in grado di eseguire UltraDiskFeedback@microsoft 160.000 operazioni di I/O al secondo con dischi ultra, inviare un messaggio di posta elettronica a .com