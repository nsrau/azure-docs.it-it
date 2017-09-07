---
title: Configurare i criteri di replica per la replica di VM Hyper-V (senza System Center VMM) in Azure con Azure Site Recovery | Microsoft Docs
description: Vengono riepilogati i passaggi necessari per configurare criteri di replica per la replica di VM Hyper-V in Archiviazione di Azure
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 1777e0eb-accb-42b5-a747-11272e131a52
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: ca5bec5cf1152e6259b9fe7a869edd2d62b88e1a
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---

# <a name="step-9-set-up-a-replication-policy-for-hyper-v-vm-replication-to-azure"></a>Passaggio 9: Configurare criteri di replica per la replica di VM Hyper-V in Azure

Questo articolo illustra come configurare criteri di replica quando si esegue la replica di VM Hyper-V in Azure (senza System Center VMM) usando il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.


Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="about-snapshots"></a>Informazioni sugli snapshot

Hyper-V utilizza due tipi di snapshot, uno snapshot standard che fornisce uno snapshot incrementale dell'intera macchina virtuale e uno snapshot coerente con l'applicazione che accetta uno snapshot temporizzato dei dati dell'applicazione all'interno della macchina virtuale.
    - Negli snapshot coerenti dell'applicazione viene usato il servizio Copia Shadow del volume (VSS) per garantire che le applicazioni siano coerenti durante la creazione dello snapshot.
    - L'abilitazione di snapshot coerenti con l'applicazione influirà sulle prestazioni delle applicazioni in esecuzione nelle macchine virtuali di origine. Assicurarsi che il valore impostato sia inferiore al numero di punti di ripristino aggiuntivi configurati.

## <a name="set-up-a-replication-policy"></a>Configurare criteri di replica

1. Per creare nuovi criteri di replica, fare clic su **Preparare l'infrastruttura** > **Impostazioni della replica** > **+Crea e associa**.

    ![Rete](./media/hyper-v-site-walkthrough-replication/gs-replication.png)
2. In **Criteri di creazione e associazione**specificare il nome dei criteri.
3. In **Frequenza di copia**specificare la frequenza con cui replicare i dati differenziali dopo la replica iniziale, ogni 30 secondi oppure ogni 5 o 15 minuti.

    > [!NOTE]
    > Quando si esegue la replica in archiviazione Premium la frequenza di 30 secondi non è supportata. Il limite è determinato dal numero di snapshot per BLOB (100) supportato dal servizio di archiviazione Premium. [Altre informazioni](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob).

4. In **Conservazione del punto di recupero** specificare la durata in ore dell'intervallo di conservazione per ogni punto di recupero. Le macchine virtuali possono essere ripristinate in qualsiasi punto all'interno di un intervallo.
5. In **Frequenza snapshot coerenti con l'app** specificare la frequenza, da 1 a 12 ore, per la creazione di punti di ripristino contenenti snapshot coerenti con l'applicazione.
6. In **Ora di inizio della replica iniziale** specificare quando deve essere avviata la replica iniziale. La replica avviene sulla larghezza di banda Internet. È quindi consigliabile pianificarla al di fuori dell'orario di lavoro. Fare quindi clic su **OK**.

    ![Criteri di replica](./media/hyper-v-site-walkthrough-replication/gs-replication2.png)

Quando si creano nuovi criteri, questi vengono associati automaticamente al sito Hyper-V. È possibile associare un sito Hyper-V e le VM che contiene a più criteri di replica in **Replica** > nome-criteri > **Associate Hyper-V Site** (Associa sito Hyper-V).



## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 10: Abilitare la replica](hyper-v-site-walkthrough-enable-replication.md)

