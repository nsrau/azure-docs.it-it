---
title: Eseguire il failback di server fisici/VM VMware da Azure con Azure Site Recovery
description: Informazioni su come eseguire il failback nel sito locale dopo il failover in Azure durante il ripristino di emergenza di macchine virtuali VMware e server fisici in Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: aed015b67aa36e7678b31d7f2f047cb1e77c6a3d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004195"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>Eseguire il failback di macchine virtuali VMware nel sito locale

Questo articolo descrive come eseguire il failback di macchine virtuali di Azure in un sito locale, dopo il [failover](site-recovery-failover.md) di macchine virtuali locali in Azure con [Azure Site Recovery](site-recovery-overview.md). Dopo il failback in locale, si Abilita la replica in modo che le VM locali inizino la replica in Azure.

## <a name="before-you-start"></a>Prima di iniziare

1. Informazioni sul [failback di VMware](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Assicurarsi di aver esaminato e completato i passaggi per [preparare il failback](vmware-azure-prepare-failback.md) e che tutti i componenti necessari siano distribuiti. I componenti includono un server di elaborazione in Azure, un server di destinazione master locale e una connessione VPN da sito a sito (o peering privato ExpressRoute) per il failback.
3. Assicurarsi di aver completato i [requisiti](vmware-azure-reprotect.md#before-you-begin) per la riprotezione e il failback e che sia stata [abilitata la riprotezione](vmware-azure-reprotect.md#enable-reprotection) delle macchine virtuali di Azure, in modo che vengano replicate da Azure al sito locale. Le macchine virtuali devono trovarsi in uno stato replicato per poter eseguire il failback.




## <a name="run-a-failover-to-fail-back"></a>Eseguire un failover per il failback

1. Verificare che le macchine virtuali di Azure siano riprotette e che vengano replicate nel sito locale.
    - Una macchina virtuale richiede almeno un punto di ripristino per eseguire il failback.
    - Se si esegue il failback di un piano di ripristino, tutti i computer del piano devono avere almeno un punto di ripristino.
2. Nell'insieme di credenziali > **Elementi replicati** selezionare la macchina virtuale. Fare clic con il pulsante destro del mouse sulla macchina virtuale > **Failover non pianificato**.
3. In **Conferma failover** verificare che la direzione di failover sia da Azure.
4. Selezionare il punto di ripristino da usare per il failover.
    - Si consiglia di usare il punto di ripristino **più recente**. Il punto coerente con l'app è antecedente al punto di ripristino più recente e causa la perdita di alcuni dati.
    - Il punto **più recente** è un punto di ripristino coerente con l'arresto anomalo del sistema.
    - Con il punto **più recente** viene eseguito il failover di una macchina virtuale al punto di ripristino più recente disponibile. Se è presente un gruppo di replica per la coerenza tra più macchine virtuali in un piano di ripristino, viene effettuato il failover di ogni macchina virtuale del gruppo di replica al punto di ripristino indipendente più recente.
    - Se si usa un punto di ripristino coerente con l'app, viene eseguito il failback di ogni macchina virtuale al punto disponibile più recente. Se un piano di ripristino contiene un gruppo di replica, ogni gruppo di replica viene ripristinato al punto di ripristino disponibile comune.
5. Viene avviato il failover. Site Recovery arresta le macchine virtuali di Azure.
6. Al termine del failover, verificare che tutti gli elementi funzionino come previsto. Verificare che le macchine virtuali di Azure siano state arrestate. 
7. Dopo aver verificato tutti gli elementi, fare clic con il pulsante destro del mouse sulla macchina virtuale > **Commit** per completare il processo di failover. Il commit rimuove la macchina virtuale di Azure di cui è stato eseguito il failover. 

> [!NOTE]
> Per le macchine virtuali Windows Site Recovery disabilita gli strumenti VMware durante il failover. Durante il failback della macchina virtuale Windows, gli strumenti VMware vengono riabilitati. 




## <a name="reprotect-from-on-premises-to-azure"></a>Abilitare la riprotezione dall'ambiente locale ad Azure

Dopo aver eseguito il commit del failback, le macchine virtuali di Azure vengono eliminate. La macchina virtuale si trova nuovamente nel sito locale, ma non è protetta. Per avviare nuovamente la replica delle macchine virtuali in Azure seguire questa procedura:

1. Nell'insieme di credenziali > **Elementi replicati** selezionare macchine virtuali di cui è stato eseguito il failback e quindi selezionare **Riproteggi**.
2. Specificare il server di elaborazione usato per inviare i dati ad Azure.
3. Selezionare **OK** per avviare il processo di riprotezione.

> [!NOTE]
> Dopo l'avvio di una macchina virtuale locale sono necessari fino a 15 minuti prima che l'agente si registri al server di configurazione. Durante questo intervallo di tempo la riprotezione non riuscirà e sarà visualizzato un messaggio di errore con l'avviso che l'agente non è installato. In questo caso, attendere alcuni minuti ed eseguire l'operazione di riprotezione.

## <a name="next-steps"></a>Passaggi successivi

Al termine del processo di riprotezione, la macchina virtuale locale viene replicata in Azure. Se necessario, è possibile [eseguire un altro failover](site-recovery-failover.md) in Azure.

