---
title: Escludere i dischi vMware VM dal ripristino di emergenza in Azure con Azure Site Recovery
description: Come escludere i dischi vMware VM dalla replica in Azure con Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.date: 12/10/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: cd54da5ee01206e576157435135065189bfb8035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495361"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>Escludere i dischi dalla replica della macchina virtuale VMware in AzureExclude disks from VMware VM replication to Azure

Questo articolo descrive come escludere i dischi durante la replica di macchine virtuali VMware in Azure per il ripristino di emergenza. È possibile escludere i dischi dalla replica per diversi motivi:You might want to exclude disks from replication for a number of reasons:

- Assicurarsi che i dati non importanti sfornato sul disco escluso non vengano replicati.
- Ottimizzare la larghezza di banda di replica utilizzata, o le risorse sul lato destinazione, escludendo i dischi che non è necessario replicare.
- Salvare le risorse di archiviazione e di rete non replicando i dati non necessari.

Prima di escludere i dischi dalla replica:

- [Altre informazioni](exclude-disks-replication.md) sull'esclusione di dischi.
- Esaminare gli scenari di [esclusione tipici](exclude-disks-replication.md#typical-scenarios) ed [esempi](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) che illustrano come l'esclusione di un disco influisce sulla replica, sul failover e sul failback.

## <a name="before-you-start"></a>Prima di iniziare

 Prima di iniziare tenere presente quanto segue:

- **Replica**: Per impostazione predefinita, tutti i dischi di un computer vengono replicati.
- **Tipo di disco**: Solo i dischi di base possono essere esclusi dalla replica. Non è possibile escludere dischi del sistema operativo o dinamici.
- **Servizio Per dispositivi mobili:** per escludere un disco dalla replica, è necessario installare manualmente il servizio Mobility nel computer prima di abilitare la replica. Non è possibile usare l'installazione push, poiché questo metodo installa il servizio Mobility in una macchina virtuale solo dopo l'abilitazione della replica.  
- **Aggiunta/rimozione di dischi:** dopo aver abilitato la replica, non è possibile aggiungere/rimuovere/escludere dischi per la replica. Se si desidera aggiungere/rimuovere o escludere dischi, è necessario disabilitare la protezione per il computer e quindi abilitarla nuovamente.
- **Failover**: dopo il failover, se le app di cui è stato eseguito il failover necessitano di dischi esclusi per funzionare, è necessario creare tali dischi manualmente. In alternativa, è possibile integrare l'automazione di Azure in un piano di ripristino per creare il disco durante il failover del computer.
- **Failback-Windows:** quando si esegue il failback al sito locale dopo il failover, non viene eseguito il failback dei dischi di Windows creati manualmente in Azure. Ad esempio, se si esegue il failover di tre dischi e si creano due dischi direttamente nelle macchine virtuali di Azure, verrà eseguito il failback solo dei tre dischi di cui è stato eseguito il failover.
- **Failback-Linux:** per il failback dei computer Linux, viene eseguito il failback dei dischi creati manualmente in Azure.Failback-Linux : For failback of Linux machines, disk that you create manually in Azure are fail back. Ad esempio, se si esegue il failover di tre dischi e si creano due dischi direttamente nelle macchine virtuali di Azure, verrà eseguito il failback di tutti e cinque. Non è possibile escludere i dischi creati manualmente nel failback o nella riprotezione delle macchine virtuali.



## <a name="exclude-disks-from-replication"></a>Escludere dischi dalla replica

1. Quando si abilita la [replica](site-recovery-hyper-v-site-to-azure.md) per una macchina virtuale VMware, dopo aver selezionato le macchine virtuali che si desidera replicare, nella pagina delle proprietà Abilita**configurazione** **proprietà** >  **replica** > esaminare la colonna **Dischi da replicare.** Per impostazione predefinita, tutti i dischi sono selezionati per la replica.
2. Se non si desidera replicare un disco specifico, in **Dischi per replicare** deselezionare la selezione per i dischi che si desidera escludere. 

    ![Escludere dischi dalla replica](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>Passaggi successivi
Dopo aver configurato correttamente la distribuzione, vedere [altre informazioni](failover-failback-overview.md) sui diversi tipi di failover.
