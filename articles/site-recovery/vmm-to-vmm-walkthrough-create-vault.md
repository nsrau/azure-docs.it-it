---
title: Creare un insieme di credenziali per la replica Hyper-V in un sito secondario con Azure Site Recovery | Microsoft Docs
description: Illustra come creare un insieme di credenziali durante la replica di VM Hyper-V in un sito System Center VMM secondario con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ff65dbfb-cb26-410e-ab48-76971625db08
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 28cfcf12b2e369f96664c163c0b6f2aa8a6ddcb9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="step-5-create-a-vault-for-hyper-v-replication-to-a-secondary-site"></a>Passaggio 5: Creare un insieme di credenziali per la replica Hyper-V in un sito secondario

Dopo la preparazione di [server System Center Virtual Machine Manager (VMM) locali e host/cluster Hyper-V](vmm-to-vmm-walkthrough-vmm-hyper-v.md) per la replica Hyper-V in un sito secondario tramite [Azure Site Recovery](site-recovery-overview.md), è possibile creare un insieme di credenziali di Servizi di ripristino e selezionare lo scenario di replica.

È possibile inviare commenti nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Scegliere un obiettivo di protezione

Selezionare gli elementi da replicare e la posizione in cui eseguire la replica.

1. Fare clic su **Site Recovery** > **Passaggio 1: Preparare l'infrastruttura** > **Obiettivo di protezione**.
2. Selezionare **Nel sito di ripristino**, quindi fare clic su **Yes, with Hyper-V** (Sì, con Hyper-V).
3. Selezionare **Sì** per indicare che si usa VMM per gestire gli host Hyper-V.
4. Selezionare **Sì** se si dispone di un server VMM secondario. Se si distribuisce la replica tra cloud in un unico server VMM, fare clic su **No**. Fare quindi clic su **OK**.

    ![Scegliere gli obiettivi](./media/vmm-to-vmm-walkthrough-create-vault/choose-goals.png)



## <a name="next-steps"></a>Passaggi successivi

Andare al [Passaggio 6: Configurare l'origine e la destinazione della replica](vmm-to-vmm-walkthrough-source-target.md).