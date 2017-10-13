---
title: Configurare un insieme di credenziali per la replica Hyper-V (senza System Center VMM) in Azure usando Azure Site Recovery | Microsoft Docs
description: Vengono riepilogati i passaggi necessari per configurare un insieme di credenziali per la replica Hyper-V in Azure usando Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a936b3e2-0dbe-47ac-a98e-5285d96dc786
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: 8212ff011633c3a89d3310e828b6d5f1cda6ce3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="step-7-set-up-a-vault-for-hyper-v-replication"></a>Passaggio 7: Configurare un insieme di credenziali per la replica Hyper-V

Questo articolo descrive come configurare un insieme di credenziali e specificare ciò di cui si vuole eseguire la replica dall'ambiente locale ad Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.


Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]



## <a name="select-a-protection-goal"></a>Scegliere un obiettivo di protezione

Selezionare gli elementi da replicare e la posizione in cui eseguire la replica.

1. Fare clic su **Insiemi di credenziali dei servizi di ripristino** e quindi sull'insieme di credenziali.
2. Nel menu Risorsa fare clic su **Site Recovery** > **Preparare l'infrastruttura** > **Obiettivo di protezione**.
3. In **Obiettivo di protezione** selezionare **To Azure (In Azure)** > **Yes, with Hyper-V (Sì, con Hyper-V)**. Scegliere **No** per confermare che non si sta usando VMM. 

    ![Scegliere gli obiettivi](./media/hyper-v-site-walkthrough-create-vault/choose-goals2.png)



## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 8: Configurare l'origine e la destinazione](hyper-v-site-walkthrough-source-target.md)
