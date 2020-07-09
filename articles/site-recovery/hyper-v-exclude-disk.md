---
title: Escludere i dischi delle macchine virtuali Hyper-V dal ripristino di emergenza in Azure con Azure Site Recovery
description: Come escludere i dischi delle macchine virtuali Hyper-V dalla replica in Azure con Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 373cffe96119af4a2fc0d74e2090e6cc24dcaf8f
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86131248"
---
# <a name="exclude-disks-from-replication"></a>Escludere dischi dalla replica

Questo articolo descrive come escludere dischi durante la replica di macchine virtuali Hyper-V in Azure. Potrebbe essere necessario escludere i dischi dalla replica per diversi motivi:

- Assicurarsi che i dati non importanti che vengono rilevati nel disco escluso non vengano replicati.
- Ottimizzare la larghezza di banda di replica utilizzata o le risorse lato destinazione escludendo i dischi che non è necessario replicare.
- Salvare le risorse di archiviazione e di rete non eseguendo la replica dei dati non necessari.

Prima di escludere i dischi dalla replica:

- [Altre informazioni](exclude-disks-replication.md) sull'esclusione di dischi.
- Esaminare gli [scenari di esclusione tipici](exclude-disks-replication.md#typical-scenarios) ed [esempi](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) che illustrano come escludere un disco influisca sulla replica, sul failover e sul failback.

## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare tenere presente quanto segue:

- **Replica**: per impostazione predefinita, vengono replicati tutti i dischi in un computer.
- **Tipo di disco**:
    - È possibile escludere dalla replica dischi di base.
    - Non è possibile escludere dischi del sistema operativo
    - e non è consigliabile escludere dischi dinamici. Site Recovery non è in grado di identificare quale VHD è di base o dinamico nella macchina virtuale guest.  Se non si escludono tutti i dischi del volume dinamici dipendenti, il disco dinamico protetto diventa un disco danneggiato in una macchina virtuale di cui è stato eseguito il failover e i dati su tale disco non sono accessibili.
- **Aggiungi/Rimuovi/Escludi dischi**: dopo aver abilitato la replica, non è possibile aggiungere/rimuovere/escludere dischi per la replica. Se si vuole aggiungere o rimuovere o escludere un disco, è necessario disabilitare la protezione per la macchina virtuale e riabilitarla.
- **Failover**: dopo il failover, se le app di cui è stato eseguito il failover devono escludere i dischi per poter funzionare, è necessario creare tali dischi manualmente. In alternativa, è possibile integrare automazione di Azure in un piano di ripristino per creare il disco durante il failover del computer.
- **Failback**: quando si esegue il failback nel sito locale dopo il failover, i dischi creati manualmente in Azure non vengono restituiti. Se, ad esempio, si esegue il failover di tre dischi e si creano due dischi direttamente in una macchina virtuale di Azure, viene eseguito il failback solo di tre dischi di cui è stato eseguito il failover. Non è possibile includere i dischi creati manualmente nel failback o nella replica inversa delle macchine virtuali.

## <a name="exclude-disks"></a>Escludere dischi

1. Per escludere i dischi quando si [Abilita la replica](./hyper-v-azure-tutorial.md) per una macchina virtuale Hyper-V, dopo aver selezionato le macchine virtuali da replicare, nella pagina Abilita proprietà di **replica**  >  **Properties**  >  **Configura proprietà** verificare la colonna **dischi da replicare** . Per impostazione predefinita, tutti i dischi sono selezionati per la replica.
2. Se non si vuole replicare un disco specifico, in **dischi per replicare** deselezionare la selezione per tutti i dischi che si desidera escludere. 

    ![Escludere dischi dalla replica](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>Passaggi successivi
Dopo aver configurato correttamente la distribuzione, vedere [altre informazioni](failover-failback-overview.md) sui diversi tipi di failover.
