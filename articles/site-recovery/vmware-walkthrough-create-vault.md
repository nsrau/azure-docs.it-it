---
title: Configurare un insieme di credenziali per la replica VMware in Azure usando Azure Site Recovery | Microsoft Docs
description: Vengono riepilogati i passaggi necessari per configurare un insieme di credenziali per la replica VMware in Azure usando Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 8bce940e-f19f-4418-8360-aee7b073519a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: dca95ad46b8de587140c3573ba6ed5702a122032
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="step-7-set-up-a-vault-for-vmware-replication-to-azure"></a>Passaggio 7: Configurare un insieme di credenziali per la replica VMware in Azure


Questo articolo descrive come configurare un insieme di credenziali e specificare ciò di cui si vuole eseguire la replica dall'ambiente locale ad Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.


Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).




## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Scegliere un obiettivo di protezione

Selezionare gli elementi da replicare e la posizione in cui eseguire la replica.

1. Fare clic su **Insiemi di credenziali dei servizi di ripristino** e quindi sull'insieme di credenziali.
2. Nel menu Risorsa fare clic su **Site Recovery** > **Preparare l'infrastruttura** > **Obiettivo di protezione**.
3. In **Obiettivo di protezione** selezionare **In Azure** > **Sì, con VMware vSphere Hypervisor**.



## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 8: Configurare l'origine e la destinazione](vmware-walkthrough-source-target.md)
