---
title: Eseguire la migrazione di macchine virtuali di Azure a Managed Disks | Microsoft Docs
description: Eseguire la migrazione di macchine virtuali di Azure create usando dischi non gestiti negli account di archiviazione per l'uso in Managed Disks.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: cynthn
ms.openlocfilehash: 3471792bba92879e446837434cbdba667cf679ad
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Eseguire la migrazione di macchine virtuali di Azure a Managed Disks in Azure

Il servizio Azure Managed Disks semplifica la gestione dell'archiviazione, eliminando la necessità di gestire separatamente gli account.  È anche possibile eseguire la migrazione delle macchine virtuali di Azure esistenti a Managed Disks per trarre vantaggio dalla maggiore affidabilità delle macchine virtuali in un set di disponibilità. Assicura che i dischi delle diverse macchine virtuali in un Set di disponibilità sia sufficientemente isolate una da altra per evitare singoli punti di errore. Colloca automaticamente i dischi di macchine virtuali differenti in un set di disponibilità in diverse unità di scala di archiviazione (indicatori) per limitare l'impatto degli errori di una singola unità causati da anomalie hardware e software.
In base alle specifiche esigenze, è possibile scegliere tra due tipi di opzioni di archiviazione:

- [I dischi Premium gestito](premium-storage.md) sono unità a stato solido (SSD) in base che offre prestazioni elevate, supporto per dischi a bassa latenza per le macchine virtuali in esecuzione di carichi di lavoro dei / O-elevato utilizzo di supporti di archiviazione. Per trarre vantaggio dalla velocità e dalle prestazioni di questi dischi, è possibile migrare a Managed Disks Premium.

- [Managed Disks Standard](standard-storage.md) usa supporti di memorizzazione basati su unità disco rigido (HDD) ed è ideale per le operazioni di sviluppo/test e per altri carichi di lavoro ad accesso sporadico, meno sensibili alla variabilità delle prestazioni.

È possibile eseguire la migrazione a Managed Disks negli scenari seguenti:

| Migrazione...                                            | Link alla documentazione                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Conversione di macchine virtuali autonome e incluse in un set di disponibilità in dischi gestiti   | [Convertire le macchine virtuali per usare i dischi gestiti](convert-unmanaged-to-managed-disks.md) |
| Una singola macchina virtuale dal modello di distribuzione classica a Resource Manager su dischi gestiti     | [Eseguire la migrazione di una singola macchina virtuale](migrate-single-classic-to-resource-manager.md)  | 
| Tutte le macchine virtuali in una rete virtuale dal modello di distribuzione classica a Resource Manager su dischi gestiti     | [Eseguire la migrazione di risorse IaaS dal modello di distribuzione classico a Resource Manager](migration-classic-resource-manager-ps.md) e quindi [convertire una macchina virtuale da dischi non gestiti in dischi gestiti](convert-unmanaged-to-managed-disks.md) | 






## <a name="plan-for-the-conversion-to-managed-disks"></a>Pianificare la conversione a Managed Disks

Questa sezione consente di prendere le decisioni migliori sui tipi di macchina virtuale e disco.


## <a name="location"></a>Località

Selezionare una posizione in cui Azure Managed Disks è disponibile. Se si passa a Managed Disks Premium, assicurarsi anche che l'Archiviazione Premium sia disponibile nell'area di destinazione pianificata. Per informazioni aggiornate sulle località disponibili, vedere [Prodotti in base all'area](https://azure.microsoft.com/regions/#services) .

## <a name="vm-sizes"></a>Dimensioni delle macchine virtuali

Se si sta eseguendo la migrazione a Managed Disks Premium, sarà necessario aggiornare le dimensioni della macchina virtuale alle dimensioni di Archiviazione Premium disponibili nell'area in cui si trova la VM. Esaminare le dimensioni delle macchine virtuali in grado di supportare Archiviazione Premium. Le specifiche delle dimensioni delle VM di Azure sono elencate in [Dimensioni delle macchine virtuali](sizes.md).
Esaminare le caratteristiche delle prestazioni delle Macchine virtuali che usano Archiviazione Premium e scegliere le dimensioni delle VM maggiormente indicate per i propri carichi di lavoro. Assicurarsi che nella macchina virtuale sia disponibile larghezza di banda sufficiente per gestire il traffico dei dischi.

## <a name="disk-sizes"></a>Dimensione disco

**Managed Disks Premium**

È possibile usare sette tipi di dischi gestiti della versione Premium con la macchina virtuale, ognuno con limiti IOP e di velocità effettiva specifici. Tenere in considerazione questi limiti nella scelta del tipo di disco Premium per la macchina virtuale in base alle esigenze dell’applicazione in termini di capacità, prestazioni, scalabilità e carichi di picco.

| Tipo di disco Premium  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Dimensioni disco           | 128 GB| 512 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS per disco       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| Velocità effettiva per disco | 25 MB al secondo  | 50 MB al secondo  | 100 MB al secondo | 125 MB al secondo |150 MB al secondo | 200 MB al secondo | 250 MB al secondo | 250 MB al secondo |

**Managed Disks Standard**

Esistono sette tipi di dischi gestiti della versione Standard che possono essere usati con la macchina virtuale. Si differenziano per capacità ma presentano gli stessi limiti IOP e di velocità effettiva. Scegliere il tipo di disco gestito della versione Standard in base alle esigenze in termini di capacità dell'applicazione.

| Tipo di disco Standard  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Dimensioni disco           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2 TB)    | 4095 GB (4 TB)   | 
| IOPS per disco       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Velocità effettiva per disco | 60 MB al secondo | 60 MB al secondo | 60 MB al secondo | 60 MB al secondo |60 MB al secondo | 60 MB al secondo | 60 MB al secondo | 60 MB al secondo | 

## <a name="disk-caching-policy"></a>Criteri di memorizzazione nella cache su disco

**Managed Disks Premium**

Per impostazione predefinita, il criterio di memorizzazione nella cache su disco è impostato su *Sola lettura* per tutti i dischi di dati Premium e su *Lettura/scrittura* per il disco del sistema operativo Premium collegato alla macchina virtuale. Queste impostazioni di configurazione sono consigliate per ottenere prestazioni ottimali per le operazioni di I/O dell'applicazione. Per i dischi di dati con un utilizzo elevato della scrittura o di sola scrittura (ad esempio i file di log di SQL Server), disabilitare la memorizzazione nella cache su disco in modo da migliorare le prestazioni delle applicazioni.

## <a name="pricing"></a>Prezzi

Esaminare i [prezzi per Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). Il prezzo della versione Premium dei dischi gestiti è uguale a quello della versione Premium dei dischi non gestiti. Tuttavia, il prezzo di Managed Disks Standard è diverso da quello della versione Standard dei dischi non gestiti.



## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Managed Disks](managed-disks-overview.md)
