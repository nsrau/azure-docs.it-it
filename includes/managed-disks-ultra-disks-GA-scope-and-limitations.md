---
title: file di inclusione
description: file di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/18/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: df488c2a3033015175943f7d092beaaa41e34046
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74260775"
---
For now, ultra disks have additional limitations, they are as follows:

- Are supported in the following regions, with a varying number of availability zones per region:
    - Stati Uniti orientali 2
    - Stati Uniti Orientali
    - Stati Uniti occidentali 2
    - SouthEast Asia
    - Europa settentrionale
    - Europa occidentale
    - Regno Unito meridionale 
- Possono essere usati solo con le zone di disponibilità (i set di disponibilità e le distribuzioni di macchine virtuali singole all'esterno delle zone non consentono di collegare un disco Ultra)
- Are only supported on the following VM series:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Not every VM size is available in every supported region with ultra disks.
- Sono disponibili solo come dischi dati e supportano solo dimensioni di settori fisici di 4k  
- Possono essere creati solo come dischi vuoti  
- Do not yet support disk snapshots, VM images, availability sets, and Azure disk encryption
- Do not yet support integration with Azure Backup or Azure Site Recovery
- The current maximum limit for IOPS on GA VMs is 80,000.
- If you would like to participate in a limited preview of a VM that can accomplish 160,000 IOPS with ultra disks, please email UltraDiskFeedback@microsoft .com