---
title: Configurare un insieme di credenziali per la replica di VM di Azure tra aree diverse con Azure Site Recovery | Microsoft Docs
description: Riepilogo dei passaggi necessari per configurare un insieme di credenziali per la replica in Azure tra aree di Azure con Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: 40472189-3d80-4963-b175-8bddcbc2f61f
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: e03d17992ee0b12049636e40188950bcc4a6f31e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="step-4-set-up-a-vault-for-azure-to-azure-replication"></a>Passaggio 4: Configurare un insieme di credenziali per la replica da Azure ad Azure

Al termine della [pianificazione delle reti](azure-to-azure-walkthrough-network.md), usare questo articolo per configurare un insieme di credenziali per le macchine virtuali (VM) di Azure replicate in un'altra area di Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.

- Al termine di questo articolo, si avrà un insieme di credenziali dei servizi di ripristino configurato.
- È possibile inserire commenti alla fine di questo articolo oppure porre domande nel [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



>[!NOTE]
>
> La replica di macchine virtuali di Azure è attualmente in anteprima.




## <a name="create-a-vault"></a>Creare un insieme di credenziali

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

>[!NOTE]
>
> È consigliabile creare l'insieme di credenziali dei servizi di ripristino nella posizione in cui si vuole che vengano replicate le VM. Ad esempio, se la posizione di destinazione è Stati Uniti centrali, creare l'insieme di credenziali in **Stati Uniti centrali**.


## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 5: Abilitare la replica](azure-to-azure-walkthrough-enable-replication.md)
