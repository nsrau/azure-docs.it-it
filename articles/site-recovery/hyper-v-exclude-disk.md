---
title: Escludere i dischi della macchina virtuale Hyper-V dal ripristino di emergenza in Azure con Azure Site Recovery
description: Come escludere i dischi della macchina virtuale Hyper-V dalla replica in Azure con Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 50fb6da2905b2ae27547f25cce3d7a76ca7976b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498129"
---
# <a name="exclude-disks-from-replication"></a>Escludere dischi dalla replica

Questo articolo descrive come escludere i dischi durante la replica di macchine virtuali Hyper-V in Azure.This article describes how to exclude disks when replicating Hyper-V VMs to Azure. È possibile escludere i dischi dalla replica per diversi motivi:You might want to exclude disks from replication for a number of reasons:

- Assicurarsi che i dati non importanti sfornato sul disco escluso non vengano replicati.
- Ottimizzare la larghezza di banda di replica utilizzata, o le risorse sul lato destinazione, escludendo i dischi che non è necessario replicare.
- Salvare le risorse di archiviazione e di rete non replicando i dati non necessari.

Prima di escludere i dischi dalla replica:

- [Altre informazioni](exclude-disks-replication.md) sull'esclusione di dischi.
- Esaminare gli scenari di [esclusione tipici](exclude-disks-replication.md#typical-scenarios) ed [esempi](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) che illustrano come l'esclusione di un disco influisce sulla replica, sul failover e sul failback.

## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare tenere presente quanto segue:

- **Replica**: Per impostazione predefinita vengono replicati tutti i dischi di un computer.
- **Tipo di disco**:
    - È possibile escludere i dischi di base dalla replica.
    - Non è possibile escludere dischi del sistema operativo
    - e non è consigliabile escludere dischi dinamici. Site Recovery non è in grado di identificare il disco rigido virtuale di base o dinamico nella macchina virtuale guest.  Se non si escludono tutti i dischi dinamici del volume dipendenti, il disco dinamico protetto diventa un disco di cui è stato eseguito il failover in una macchina virtuale di cui è stato eseguito il failover e i dati in tale disco non sono accessibili.
- **Aggiunta/rimozione di dischi:** dopo aver abilitato la replica, non è possibile aggiungere/rimuovere/escludere dischi per la replica. Se si vuole aggiungere/rimuovere o escludere un disco, è necessario disabilitare la protezione per la macchina virtuale e quindi abilitarla di nuovo.
- **Failover:** dopo il failover, se è necessario escludere i dischi per poter eliminare i dischi per funzionare, è necessario creare tali dischi manualmente. In alternativa, è possibile integrare l'automazione di Azure in un piano di ripristino per creare il disco durante il failover del computer.
- **Failback:** quando si esegue il failback nel sito locale dopo il failover, non viene eseguito il failback dei dischi creati manualmente in Azure.Failback : When you failback to your on-premises site after failover, disks that disks that diskthat you created manually in Azure are't failback. Ad esempio, se si esegue il failover di tre dischi e si creano due dischi direttamente in una macchina virtuale di Azure, viene eseguito il failback solo di tre dischi di cui è stato eseguito il failover. Non è possibile includere dischi creati manualmente nel failback o nella replica inversa delle macchine virtuali.

## <a name="exclude-disks"></a>Escludere dischi

1. Per escludere i dischi quando si abilita la [replica](site-recovery-hyper-v-site-to-azure.md) per una macchina virtuale Hyper-V, dopo aver selezionato le macchine virtuali da replicare, nella pagina Delle proprietà Abilita**proprietà** >  **replica** > **configura** esaminare la colonna **Dischi da replicare.** Per impostazione predefinita, tutti i dischi sono selezionati per la replica.
2. Se non si desidera replicare un disco specifico, in **Dischi per replicare** deselezionare la selezione per i dischi che si desidera escludere. 

    ![Escludere dischi dalla replica](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>Passaggi successivi
Dopo aver configurato correttamente la distribuzione, vedere [altre informazioni](failover-failback-overview.md) sui diversi tipi di failover.
