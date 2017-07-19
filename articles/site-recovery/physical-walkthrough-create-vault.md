---
title: Configurare un insieme di credenziali per la replica di server fisici in Azure usando Azure Site Recovery | Microsoft Docs
description: Vengono riepilogati i passaggi necessari per configurare un insieme di credenziali per la replica di server fisici in Azure usando Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d99f2605-f417-4995-be77-5323136b814f
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: deb5ad0495edc969b374795eeb2698326dd4ff4d
ms.contentlocale: it-it
ms.lasthandoff: 06/29/2017


---
# <a name="step-6-set-up-a-vault-for-physical-server-replication-to-azure"></a>Passaggio 6: Configurare un insieme di credenziali per la replica di server fisici in Azure


Questo articolo descrive come configurare un insieme di credenziali. Per creare l'insieme di credenziali e specificare ciò di cui si vuole eseguire la replica dall'ambiente locale in Azure, usare il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.


Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).




## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Scegliere un obiettivo di protezione

Selezionare gli elementi da replicare e la posizione in cui eseguire la replica.

1. Fare clic su **Insiemi di credenziali dei servizi di ripristino** e quindi sull'insieme di credenziali.
2. Nel menu Risorsa fare clic su **Site Recovery** > **Preparare l'infrastruttura** > **Obiettivo di protezione**.
3. In **Obiettivo di protezione** selezionare **To Azure (In Azure)** > **Non virtualizzato/Altro**.


## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 7: Configurare l'origine e la destinazione](physical-walkthrough-source-target.md)

