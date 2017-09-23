---
title: Configurare criteri di replica per la replica di VM VMware in Azure con Azure Site Recovery | Microsoft Docs
description: Vengono riepilogati i passaggi necessari per configurare criteri di replica per la replica di VM VMware in Archiviazione di Azure
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d7874bd8-6626-4668-9ec9-dbd2d26f8f81
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 3c4b7ad16e6a03fb605447def18f7475d502fdd1
ms.contentlocale: it-it
ms.lasthandoff: 06/29/2017

---
# <a name="step-9-set-up-a-replication-policy-for-vmware-vm-replication-to-azure"></a>Passaggio 9: Configurare criteri di replica per la replica di VM VMware in Azure


Questo articolo illustra come configurare criteri di replica quando si esegue la replica di VM VMware in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.


Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="configure-a-policy"></a>Configurare i criteri

Ottenere una rapida panoramica video prima di iniziare:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video2-vCenter-Server-Discovery-and-Replication-Policy/player]

1. Per creare nuovi criteri di replica, fare clic su **Infrastruttura di Site Recovery** > **Criteri di replica** > **+Criteri di replica**.
2. In **Creare i criteri di replica** specificare un nome per i criteri.
3. In **Soglia RPO**specificare il limite per RPO. Questo valore specifica la frequenza con cui vengono creati punti di ripristino dei dati. Se la replica continua supera questo limite, viene generato un avviso.
4. In **Conservazione del punto di ripristino** specificare la durata in ore dell'intervallo di conservazione per ogni punto di ripristino. Le VM replicate possono essere ripristinate in qualsiasi punto all'interno di un intervallo. È supportata la conservazione fino a 24 ore per le macchine replicate in Archiviazione Premium e fino a 72 ore per Archiviazione Standard.
5. In **Frequenza snapshot coerenti con l'app**specificare la frequenza, in minuti, per la creazione di punti di ripristino contenenti snapshot coerenti con l'applicazione. Fare clic su **OK** per creare i criteri.

    ![Criteri di replica](./media/vmware-walkthrough-replication/gs-replication2.png)
8. Quando si creano nuovi criteri, questi vengono associati automaticamente al server di configurazione. Per impostazione predefinita vengono creati automaticamente criteri corrispondenti per il failback. Se, ad esempio, il criterio di replica è **rep-policy**, il criterio di failback sarà **rep-policy-failback**. Questi criteri non vengono usati fino a quando non si avvia un failback da Azure.

## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 10: Installare il servizio Mobility](vmware-walkthrough-install-mobility.md)

