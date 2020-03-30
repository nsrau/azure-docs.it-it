---
title: Eseguire il failback delle macchine virtuali VMware/server fisici da Azure con Azure Site Recovery
description: Informazioni su come eseguire il failback nel sito locale dopo il failover in Azure durante il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: cd4cc90fb102d517a47ba458619e22b8921dd498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495320"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>Eseguire il failback delle macchine virtuali VMware nel sito locale

In questo articolo viene descritto come eseguire il failback delle macchine virtuali di Azure in un sito locale, dopo il [failover](site-recovery-failover.md) delle macchine virtuali locali in Azure con [Azure Site Recovery.](site-recovery-overview.md) Dopo il failback in locale, si abilita la replica in modo che le macchine virtuali locali inizino la replica in Azure.After failback to on-premises, you enable replication so that the on-premises VMs started replicating to Azure.

## <a name="before-you-start"></a>Prima di iniziare

1. Informazioni sul failback di [VMware](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Assicurarsi di aver esaminato e completato i passaggi per la preparazione del [failback](vmware-azure-prepare-failback.md)e che tutti i componenti necessari siano distribuiti. I componenti includono un server di elaborazione in Azure, un server di destinazione master locale e una connessione VPN da sito a sito (o peering privato ExpressRoute) per il failback.
3. Assicurarsi di aver completato i [requisiti](vmware-azure-reprotect.md#before-you-begin) per la riprotezione e il failback e di aver abilitato la [riprotezione](vmware-azure-reprotect.md#enable-reprotection) delle macchine virtuali di Azure, in modo che vengano replicate da Azure al sito locale. Per eseguire il failback, è necessario che le macchine virtuali si trovano in uno stato replicato.




## <a name="run-a-failover-to-fail-back"></a>Eseguire un failover per eseguire il failbackRun a failover to failback

1. Assicurarsi che le macchine virtuali di Azure siano nuovamente protette e che vengano replicate nel sito locale. 
    - Una macchina virtuale richiede almeno un punto di ripristino per poter eseguire il failback.
    - Se si esegue il failback di un piano di ripristino, tutti i computer del piano devono avere almeno un punto di ripristino.
2. Nell'insieme di credenziali > **elementi replicati**selezionare la macchina virtuale. Fare clic con il pulsante destro del mouse sulla macchina virtuale > **failover non pianificato**.
3. In **Conferma failover** verificare che la direzione di failover sia da Azure.
4. Selezionare il punto di ripristino da usare per il failover.
    - È consigliabile utilizzare il punto di ripristino **più recente.** Il punto coerente dell'app è dietro l'ultimo punto nel tempo e causa una perdita di dati.
    - **L'ultimo** è un punto di ripristino coerente con l'arresto anomalo del sistema.
    - Con **Latest**, una macchina virtuale esegue il failover fino all'ultimo punto disponibile nel tempo. Se si dispone di un gruppo di replica per la coerenza di più macchine virtuali all'interno di un piano di ripristino, ogni macchina virtuale del gruppo esegue il failover al relativo ultimo punto nel tempo indipendente.
    - Se si usa un punto di ripristino coerente con l'app, ogni macchina virtuale esegue il failover al punto disponibile più recente. Se un piano di ripristino dispone di un gruppo di replica, ogni gruppo esegue il ripristino nel punto di ripristino disponibile comune.
5. Inizia il failover. Site Recovery shuts down the Azure VMs.
6. Al termine del failover, verificare che tutto funzioni come previsto. Verificare che le macchine virtuali di Azure siano arrestate. 
7. Con tutto verificato, fare clic con il pulsante destro del mouse sulla macchina virtuale > **Commit**per completare il processo di failover. Il commit rimuove la macchina virtuale di Azure di cui è stato eseguito il failover. 

> [!NOTE]
> Per le macchine virtuali Windows, Site Recovery disabilita gli strumenti VMware durante il failover. Durante il failback della macchina virtuale Windows, gli strumenti VMware vengono nuovamente abilitati. 




## <a name="reprotect-from-on-premises-to-azure"></a>Abilitare la riprotezione dall'ambiente locale ad Azure

Dopo il commit del failback, le macchine virtuali di Azure vengono eliminate. La macchina virtuale è di nuovo nel sito locale, ma non è protetta. Per avviare nuovamente la replica delle macchine virtuali in Azure, come indicato di seguito:To start replicating VMs to Azure again, as follows:

1. Nell'insieme di > **elementi replicati**selezionare le macchine virtuali con rollback con esito non riuscita e quindi selezionare **Riproteggi**.
2. Specificare il server di elaborazione usato per inviare i dati ad Azure.Specify the process server that's used to send data back to Azure.
3. Selezionare **OK** per avviare il processo di riprotezione.

> [!NOTE]
> Dopo l'avvio di una macchina virtuale locale, la registrazione dell'agente nel server di configurazione richiede fino a 15 minuti. Durante questo intervallo di tempo la riprotezione non riuscirà e sarà visualizzato un messaggio di errore con l'avviso che l'agente non è installato. In questo caso, attendere alcuni minuti e riproteggere.

## <a name="next-steps"></a>Passaggi successivi

Al termine del processo di riprotezione, la macchina virtuale locale esegue la replica in Azure.After the reprotect job finishes, the on-premises VM is replicating to Azure. Se necessario, è possibile [eseguire un altro failover in](site-recovery-failover.md) Azure.As needed, you can run another failover to Azure.

