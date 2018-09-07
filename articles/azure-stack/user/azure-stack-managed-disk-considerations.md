---
title: Differenze e considerazioni per Managed Disks in Azure Stack | Microsoft Docs
description: Informazioni sulle differenze e considerazioni quando si lavora con Managed Disks in Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: 4fd2a26d9119e52fc75918abc1ca97e6f9888169
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028811"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack Managed Disks: Differenze e considerazioni
Questo articolo riepiloga le differenze note tra Managed Disks di Azure Stack e Managed Disks di Azure. Per altre informazioni sulle differenze generali tra Azure e Azure Stack, vedere la [considerazioni chiave](azure-stack-considerations.md) articolo.

Managed Disks semplifica la gestione dei dischi per le macchine virtuali IaaS gestendo il [gli account di archiviazione](/azure/azure-stack/azure-stack-manage-storage-accounts) associati ai dischi della macchina virtuale.
  

## <a name="cheat-sheet-virtual-machine-differences"></a>Foglio informativo: macchina virtuale differenze

| Funzionalità | Azure (globale) | Azure Stack |
| --- | --- | --- |
|Crittografia per dati inattivi |Crittografia del Servizio archiviazione di Azure (SSE), crittografia dischi di Azure (ADE)     |Crittografia AES a 128 bit BitLocker      |
|Image          | Supporta l'immagine personalizzata gestita |Non è ancora supportata|
|Opzioni di backup |Supporto servizio Backup di Azure |Non è ancora supportata |
|Opzioni di ripristino di emergenza |Supporto per Azure Site Recovery |Non è ancora supportata|
|Tipi di disco     |Unità SSD Premium SSD Standard (anteprima) e unità disco rigido Standard |Premium SSD, HDD Standard |
|Dischi Premium  |Supporto completo |Può essere eseguito il provisioning, ma nessun limite delle prestazioni o garanzia  |
|Dischi Premium  |Numero di IOPs  |Dipende dalla dimensione 2300 IOPs per disco del disco |
|Velocità effettiva di dischi Premium |Dipende dalle dimensioni del disco |145 MB al secondo per disco |
|Dimensioni massime del disco  |4 TB       |1 TB       |
|Analisi delle prestazioni di dischi |Aggregare le metriche e metriche per disco supportate |Non è ancora supportata |
|Migrazione      |Fornisce lo strumento per eseguire la migrazione da esistenti non gestito VM di Resource Manager di Azure senza la necessità di ricreare la macchina virtuale  |Non è ancora supportata |


## <a name="metrics"></a>Metriche
Esistono anche differenze con le metriche di archiviazione:
- Con Azure Stack, i dati delle transazioni metriche di archiviazione non viene fatta alcuna distinzione della larghezza di banda di rete interna o esterna.
- Azure Stack transazione i dati in metriche di archiviazione non includono l'accesso alle macchine virtuali nei dischi montati.


## <a name="api-versions"></a>Versioni dell'API
Azure Stack Managed Disks supporta le versioni dell'API seguente:
- 2017-03-30


## <a name="next-steps"></a>Passaggi successivi
[Informazioni su macchine virtuali di Azure Stack](azure-stack-compute-overview.md)
