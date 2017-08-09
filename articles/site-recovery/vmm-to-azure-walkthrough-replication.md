---
title: Configurare i criteri per la replica in Azure di una macchina virtuale Hyper-V (con VMM) tramite Azure Site Recovery | Microsoft Docs
description: Descrive come configurare i criteri per la replica in Azure di una macchina virtuale Hyper-V (con VMM) tramite Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ee808b20-324b-4198-b831-edb65b95e8b7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: 1bf66eaa272ad1e2bf400707929243662eb1f140
ms.contentlocale: it-it
ms.lasthandoff: 07/26/2017

---
# <a name="step-10-set-up-a-replication-policy-for-hyper-v-vm-replication-with-vmm-to-azure"></a>Passaggio 10: Configurare i criteri per la replica di una macchina virtuale Hyper-V (con VMM) in Azure


Dopo aver configurato il [mapping di rete](vmm-to-azure-walkthrough-network-mapping.md), usare le informazioni in questo articolo per configurare i criteri necessari per la replica in Azure di macchine virtuali Hyper-V locali gestite in cloud di System Center Virtual Machine Manager (VMM) usando il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.

È possibile inviare commenti nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="create-a-policy"></a>Creare un criterio

1. Per creare nuovi criteri di replica, fare clic su **Preparare l'infrastruttura** > **Impostazioni della replica** > **+Crea e associa**.

    ![Rete](./media/vmm-to-azure-walkthrough-replication/gs-replication.png)
2. In **Criteri di creazione e associazione**specificare il nome dei criteri.
3. In **Frequenza di copia**specificare la frequenza con cui replicare i dati differenziali dopo la replica iniziale, ogni 30 secondi oppure ogni 5 o 15 minuti.

    > [!NOTE]
    >  Quando si esegue la replica in archiviazione Premium la frequenza di 30 secondi non è supportata. Il limite è determinato dal numero di snapshot per BLOB (100) supportato dal servizio di archiviazione Premium. [Altre informazioni](../storage/storage-premium-storage.md#snapshots-and-copy-blob)

4. In **Conservazione del punto di ripristino**specificare la durata in ore dell'intervallo di conservazione per ogni punto di ripristino. I computer protetti possono essere ripristinati in qualsiasi punto all'interno di un intervallo.
5. In **Frequenza snapshot coerenti con l'app**specificare la frequenza, da 1 a 12 ore, per la creazione di punti di ripristino contenenti snapshot coerenti con l'applicazione. Hyper-V utilizza due tipi di snapshot, uno snapshot standard che fornisce uno snapshot incrementale dell'intera macchina virtuale e uno snapshot coerente con l'applicazione che accetta uno snapshot temporizzato dei dati dell'applicazione all'interno della macchina virtuale. Negli snapshot coerenti dell'applicazione viene usato il servizio Copia Shadow del volume (VSS) per garantire che le applicazioni siano coerenti durante la creazione dello snapshot. Si noti che un'eventuale abilitazione di snapshot coerenti dell'applicazione influirà sulle prestazioni delle applicazioni in esecuzione nelle macchine virtuali di origine. Assicurarsi che il valore impostato sia inferiore al numero di punti di ripristino aggiuntivi configurati.
6. In **Ora di inizio della replica iniziale** specificare quando deve essere avviata la replica iniziale. La replica avviene sulla larghezza di banda Internet. È quindi consigliabile pianificarla al di fuori dell'orario di lavoro.
7. In **Crittografare i dati archiviati in Azure**specificare se crittografare i dati inattivi in Archiviazione di Azure. Fare quindi clic su **OK**.

    ![Criteri di replica](./media/vmm-to-azure-walkthrough-replication/gs-replication2.png)
8. Quando si creano nuovi criteri, questi vengono associati automaticamente al cloud VMM. Fare clic su **OK**. È possibile associare altri cloud VMM (e le VM in essi contenute) a questi criteri di replica in **Replica** > nome del criterio > **Associate VMM Cloud (Associa cloud VMM)**.

    ![Criteri di replica](./media/vmm-to-azure-walkthrough-replication/policy-associate.png)



## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 11: Abilitare la replica](vmm-to-azure-walkthrough-enable-replication.md)

