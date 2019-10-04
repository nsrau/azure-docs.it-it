---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a416d1c6e813be558f034e15576c57efa6073788
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968586"
---
**Trasferimenti di dati in uscita**: [trasferimenti di dati in uscita](https://azure.microsoft.com/pricing/details/bandwidth/) (dati in uscita dai datacenter di Azure) vengono fatturati in base all'utilizzo di larghezza di banda.

**Transazioni**: Viene addebitato il numero di transazioni eseguite in un disco gestito Standard. Per le unità SSD standard, ogni operazione di I/O inferiore o uguale a 256 KiB di velocità effettiva viene considerata una singola operazione di I/O. Le operazioni di i/O superiori a 256 KiB di velocità effettiva sono considerate più I/O di dimensioni 256 KiB. Per i dischi rigidi standard, ogni operazione di I/O viene considerata come una singola transazione, indipendentemente dalle dimensioni di I/O.

Per informazioni dettagliate sui prezzi per Managed Disks, inclusi i costi delle transazioni, vedere [prezzi Managed disks](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Costo prenotazione macchina virtuale ultra disk

Le macchine virtuali di Azure sono in grado di indicare se sono compatibili con dischi Ultra. Una macchina virtuale compatibile con dischi Ultra alloca una capacità di larghezza di banda dedicata tra l'istanza della macchina virtuale di calcolo e l'unità di scala di archiviazione a blocchi per ottimizzare le prestazioni e ridurre la latenza. L'aggiunta di questa capacità alla macchina virtuale comporta un addebito per la prenotazione, che viene applicato solo se si abilita la funzionalità per i dischi Ultra nella macchina virtuale senza collegare un disco Ultra. Quando alla macchina virtuale compatibile con i dischi Ultra viene collegato un disco Ultra, questo addebito non viene applicato. L'addebito viene calcolato per ogni vCPU di cui è stato effettuato il provisioning nella macchina virtuale. 

> [!Note]
> Per le [dimensioni delle VM Core vincolate](../articles/virtual-machines/linux/constrained-vcpu.md), la tariffa di prenotazione è basata sul numero effettivo di vCPU e non sui core vincolati. Per Standard_E32-8s_v3, la tariffa di prenotazione sarà basata su 32 core. 

Per informazioni sui prezzi di dischi Ultra, vedere la [pagina dei prezzi di Azure disks](https://azure.microsoft.com/pricing/details/managed-disks/) .
