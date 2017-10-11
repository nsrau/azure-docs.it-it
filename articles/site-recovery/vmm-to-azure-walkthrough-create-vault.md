---
title: Configurare un insieme di credenziali per la replica Hyper-V (con System Center VMM) in Azure usando Azure Site Recovery | Microsoft Docs
description: Vengono riepilogati i passaggi necessari per configurare un insieme di credenziali per la replica Hyper-V (con VMM) in Azure usando Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: b3cd6f03-c33c-406d-91d4-5cba69f79abd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: af453ec27ba15ad8c59cf9f544584ad18dc0f74a
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2017
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
3. In **Obiettivo di protezione** selezionare **To Azure (In Azure)** > **Yes, with Hyper-V (Sì, con Hyper-V)**. Scegliere **Sì** per confermare che si sta usando VMM. 

     ![Scegliere gli obiettivi](./media/vmm-to-azure-walkthrough-create-vault/choose-goals.png)



## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 8: Configurare l'origine e la destinazione](vmm-to-azure-walkthrough-source-target.md)
