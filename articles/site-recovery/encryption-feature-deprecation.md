---
title: Deprecazione della funzionalità di crittografia dei dati Azure Site Recovery | Microsoft Docs
description: Dettagli regarig Azure Site Recovery funzionalità di crittografia dei dati
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: rajanaki
ms.openlocfilehash: 5e74466891a5926d8ae8feb3c1c48348ecf3cfe6
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134996"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Funzionalità di crittografia dei dati Site Recovery deprecata

Questo documento descrive i dettagli di deprecazione e l'azione di correzione che è necessario eseguire se si usa la funzionalità di crittografia dei dati Site Recovery durante la configurazione del ripristino di emergenza di macchine virtuali Hyper-V in Azure. 

## <a name="deprecation-information"></a>Informazioni di deprecazione


La funzionalità di crittografia dei dati Site Recovery è disponibile per i clienti che proteggono le VM Hyper-V per garantire che i dati replicati siano protetti da minacce per la sicurezza. Questa funzionalità verrà deprecata entro il **30 dicembre 2019**. Viene sostituita dalla funzionalità [crittografia](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) inattiva più avanzata, che usa [crittografia del servizio di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE). Con SSE i dati vengono crittografati prima di essere salvati in modo permanente nell'archiviazione e decrittografati durante il recupero e, al momento del failover in Azure, le macchine virtuali vengono eseguite dagli account di archiviazione crittografati, consentendo un obiettivo del tempo di recupero migliorato (RTO).

Si noti che, se si è un cliente esistente che utilizza questa funzionalità, si riceveranno le comunicazioni con i dettagli di deprecazione e le procedure di correzione. 


## <a name="what-are-the-implications"></a>Quali sono le implicazioni?

Dopo il **30 dicembre 2019**, le macchine virtuali che usano ancora la funzionalità di crittografia ritirata non saranno autorizzati a eseguire il failover. 

## <a name="required-action"></a>Azione obbligatoria
Per continuare correttamente le operazioni di failover e le repliche seguono i passaggi indicati di seguito:

Seguire questa procedura per ogni macchina virtuale: 
1.  [Disabilitare la replica](https://docs.microsoft.com/azure/site-recovery/site-recovery-manage-registration-and-protection#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).
2.  [Creare nuovi criteri di replica](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-a-replication-policy).
3.  [Abilitare la replica](https://docs.microsoft.com/azure/site-recovery/hyper-v-vmm-azure-tutorial#enable-replication) e selezionare un account di archiviazione con la crittografia SSE abilitata.

Dopo aver completato la replica iniziale negli account di archiviazione con la crittografia SSE abilitata, le macchine virtuali useranno la crittografia dei computer inattivi con Azure Site Recovery.


## <a name="next-steps"></a>Passaggi successivi
Pianificare l'esecuzione dei passaggi correttivi ed eseguirli al più presto. Se sono presenti query relative a questa deprecazione, contattare supporto tecnico Microsoft. Per altre informazioni sullo scenario da Hyper-V ad Azure, vedere [qui](hyper-v-vmm-architecture.md).

