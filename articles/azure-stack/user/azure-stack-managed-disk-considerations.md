---
title: Differenze e considerazioni per Managed Disks in Azure Stack | Microsoft Docs
description: Informazioni sulle differenze e considerazioni quando si lavora con Managed Disks in Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: jiahan
ms.openlocfilehash: 3445974cf832b7ed594f704615482e1d9b0e351c
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159367"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack Managed Disks: differenze e considerazioni

Questo articolo riepiloga le differenze tra note [Managed Disks di Azure Stack](azure-stack-manage-vm-disks.md) e [Managed Disks per Azure](../../virtual-machines/windows/managed-disks-overview.md). Per altre informazioni sulle differenze generali tra Azure e Azure Stack, vedere la [considerazioni chiave](azure-stack-considerations.md) articolo.

Managed Disks semplifica la gestione dei dischi per le macchine virtuali IaaS gestendo il [gli account di archiviazione](../azure-stack-manage-storage-accounts.md) associati ai dischi della macchina virtuale.

> [!Note]  
> Managed Disks in Azure Stack è disponibile da 1808 update. Si è abilitato per impostazione predefinita durante la creazione di macchine virtuali usando il portale di Azure Stack da 1811 update.
  

## <a name="cheat-sheet-managed-disk-differences"></a>Foglio informativo: Differenze di disco gestito

| Funzionalità | Azure (globale) | Azure Stack |
| --- | --- | --- |
|Crittografia per dati inattivi |Crittografia del Servizio archiviazione di Azure (SSE), crittografia dischi di Azure (ADE)     |Crittografia AES a 128 bit BitLocker      |
|Image          | Supporta l'immagine personalizzata gestita |Non è ancora supportata|
|Opzioni di backup |Supporto servizio Backup di Azure |Non è ancora supportata |
|Opzioni di ripristino di emergenza |Supporto per Azure Site Recovery |Non è ancora supportata|
|Tipi di disco     |Unità SSD Premium SSD Standard (anteprima) e unità disco rigido Standard |Premium SSD, HDD Standard |
|Dischi Premium  |Supporto completo |Può essere eseguito il provisioning, ma nessun limite delle prestazioni o garanzia  |
|IOPs di dischi Premium  |Dipende dalle dimensioni del disco  |2300 IOPs per disco |
|Velocità effettiva di dischi Premium |Dipende dalle dimensioni del disco |145 MB al secondo per disco |
|Dimensioni disco  |Disco Premium di Azure: P4 (32 GiB) a P80 (32 TiB)<br>Disco SSD Standard di Azure: E10 (128 GiB) a E80 (32 TiB)<br>Disco di Azure Standard HDD: S4 (32 GiB) a S80 (32 TiB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GiB<br>M15: 256 GiB<br>M20: 512 GiB<br>L30: 1024 GiB |
|Copia di snapshot di dischi|Lo snapshot di Azure collegati a una macchina virtuale in esecuzione supportata dischi gestiti|Non è ancora supportata |
|Analisi delle prestazioni di dischi |Aggregare le metriche e metriche per disco supportate |Non è ancora supportata |
|Migrazione      |Fornisce lo strumento per eseguire la migrazione da non gestiti Azure Resource Manager le macchine virtuali esistenti senza dover ricreare la macchina virtuale  |Non è ancora supportata |

> [!NOTE]  
> Dischi IOPs e velocità effettiva in Azure Stack è un numero limite di utilizzo anziché un numero con provisioning, che potrebbe interessate da carichi di lavoro in esecuzione in Azure Stack e hardware.

## <a name="metrics"></a>Metriche

Esistono anche differenze con le metriche di archiviazione:

- Con Azure Stack, i dati delle transazioni metriche di archiviazione non viene fatta distinzione della larghezza di banda di rete interna o esterna.
- Azure Stack transazione i dati in metriche di archiviazione non includono l'accesso alle macchine virtuali nei dischi montati.

## <a name="api-versions"></a>Versioni dell'API

Azure Stack Managed Disks supporta le versioni dell'API seguente:

- 2017-03-30

## <a name="known-issues"></a>Problemi noti

Dopo aver applicato gli aggiornamenti dopo 1808, si potrebbero riscontrare i problemi seguenti durante la distribuzione di macchine virtuali con Managed Disks:

- Se la sottoscrizione è stata creata prima dell'aggiornamento 1808, distribuzione di una VM con Managed Disks potrei avere esito negativo con un messaggio di errore interno. Per risolvere l'errore, seguire questi passaggi per ogni sottoscrizione:
   1. Nel portale Tenant, passare a **sottoscrizioni** e individuare la sottoscrizione. Fare clic su **provider di risorse**, quindi fare clic su **Microsoft. COMPUTE**, quindi fare clic su **registrare nuovamente**.
   2. Nella stessa sottoscrizione, passare a **controllo di accesso (IAM)** e verificare che **Azure Stack-Managed Disks** sia elencato.
- Se è stato configurato un ambiente multi-tenant, la distribuzione di macchine virtuali in una sottoscrizione associata a una directory guest potrebbe non riuscire con un messaggio di errore interno. Per risolvere l'errore, seguire questa procedura nel [questo articolo](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) riconfigurare tutte le directory di guest.


## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su macchine virtuali di Azure Stack](azure-stack-compute-overview.md)
