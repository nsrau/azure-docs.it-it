---
title: Escludere i dischi delle macchine virtuali VMware dal ripristino di emergenza in Azure con Azure Site Recovery
description: Come escludere i dischi delle macchine virtuali VMware dalla replica in Azure con Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.date: 12/10/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: c4842172ff181b5cdbe7f6fecf69da8755ae43fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86129879"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>Escludere dischi dalla replica di macchine virtuali VMware in Azure

Questo articolo descrive come escludere dischi durante la replica di macchine virtuali VMware in Azure per il ripristino di emergenza. Potrebbe essere necessario escludere i dischi dalla replica per diversi motivi:

- Assicurarsi che i dati non importanti che vengono rilevati nel disco escluso non vengano replicati.
- Ottimizzare la larghezza di banda di replica utilizzata o le risorse lato destinazione escludendo i dischi che non è necessario replicare.
- Salvare le risorse di archiviazione e di rete non eseguendo la replica dei dati non necessari.

Prima di escludere i dischi dalla replica:

- [Altre informazioni](exclude-disks-replication.md) sull'esclusione di dischi.
- Esaminare gli [scenari di esclusione tipici](exclude-disks-replication.md#typical-scenarios) ed [esempi](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) che illustrano come escludere un disco influisca sulla replica, sul failover e sul failback.

## <a name="before-you-start"></a>Prima di iniziare

 Prima di iniziare tenere presente quanto segue:

- **Replica**: per impostazione predefinita, vengono replicati tutti i dischi in un computer.
- **Tipo di disco**: solo i dischi di base possono essere esclusi dalla replica. Non è possibile escludere dischi del sistema operativo o dinamici.
- **Servizio Mobility**: per escludere un disco dalla replica, è necessario installare manualmente il servizio Mobility nel computer prima di abilitare la replica. Non è possibile usare l'installazione push, perché questo metodo installa il servizio Mobility in una macchina virtuale solo dopo aver abilitato la replica.  
- **Aggiungi/Rimuovi/Escludi dischi**: dopo aver abilitato la replica, non è possibile aggiungere/rimuovere/escludere dischi per la replica. Se si desidera aggiungere o rimuovere o escludere dischi, è necessario disabilitare la protezione per il computer e quindi riabilitarla.
- **Failover**: dopo il failover, se le app di cui è stato eseguito il failover necessitano di dischi esclusi per funzionare, è necessario creare tali dischi manualmente. In alternativa, è possibile integrare automazione di Azure in un piano di ripristino per creare il disco durante il failover del computer.
- **Failback-Windows**: quando si esegue il failback nel sito locale dopo il failover, i dischi di Windows creati manualmente in Azure non eseguono il failback. Ad esempio, se si esegue il failover di tre dischi e si creano due dischi direttamente nelle VM di Azure, verrà eseguito il failback solo dei tre dischi sottoposti a failover.
- **Failback-Linux**: per il failback di computer Linux, il failback dei dischi creati manualmente in Azure non viene eseguito. Ad esempio, se si esegue il failover di tre dischi e si creano due dischi direttamente nelle VM di Azure, verrà eseguito il failback di tutti e cinque. Non è possibile escludere i dischi creati manualmente nel failback o nella riprotezione delle macchine virtuali.



## <a name="exclude-disks-from-replication"></a>Escludere dischi dalla replica

1. Quando si [Abilita la replica](./hyper-v-azure-tutorial.md) per una macchina virtuale VMware, dopo aver selezionato le macchine virtuali che si desidera replicare, nella pagina Abilita proprietà di **replica**  >  **Properties**  >  **Configura proprietà** verificare la colonna **dischi da replicare** . Per impostazione predefinita, tutti i dischi sono selezionati per la replica.
2. Se non si vuole replicare un disco specifico, in **dischi per replicare** deselezionare la selezione per tutti i dischi che si desidera escludere. 

    ![Escludere dischi dalla replica](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>Passaggi successivi
Dopo aver configurato correttamente la distribuzione, vedere [altre informazioni](failover-failback-overview.md) sui diversi tipi di failover.
