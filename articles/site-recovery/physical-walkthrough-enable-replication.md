---
title: Abilitare la replica dei server fisici in Azure con Azure Site Recovery | Microsoft Docs
description: Vengono riepilogati i passaggi necessari per abilitare la replica dei server fisici in Azure usando il servizio Azure Site Recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 519c5559-7032-4954-b8b5-f24f5242a954
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 42f35c53eec06a346281fd90c97aecfd2269307d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="step-10-enable-replication-for-physical-servers-to-azure"></a>Passaggio 10: Abilitare la replica dei server fisici in Azure


Questo articolo illustra come abilitare la replica dei server fisici Windows/Linux locali in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md) nel portale di Azure.

Inserire commenti e domande nella parte inferiore di questo articolo oppure nel [forum sui servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Prima di iniziare

- Nei server deve essere installato il [componente servizio Mobility](physical-walkthrough-install-mobility.md).
- Se una VM è pronta per l'installazione push, il server di elaborazione installa automaticamente il servizio Mobility quando si abilita la replica.
- Quando si abilita un computer per la replica, l'account utente di Azure necessita di [autorizzazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) specifiche per assicurarsi di poter usare Archiviazione di Azure e creare VM di Azure.
- Quando si aggiungono o si modificano gli indirizzi IP per i server, possono essere necessari 15 minuti o più prima che le modifiche abbiano effetto e vengano visualizzate nel portale.


## <a name="exclude-disks-from-replication"></a>Escludere dischi dalla replica

Per impostazione predefinita, vengono replicati tutti i dischi in un computer. È possibile escludere dischi dalla replica. Ad esempio, è possibile evitare di replicare i dischi con dati temporanei o dati che vengono aggiornati ogni volta che un computer o un'applicazione viene riavviata, come pagefile.sys o tempdb di SQL Server. [Altre informazioni](site-recovery-exclude-disk.md)

## <a name="replicate-servers"></a>Replicare i server

1. Fare clic su **Passaggio 2: Eseguire la replica dell'applicazione** > **Origine**.
2. In **Origine** selezionare il server di configurazione.
3. In **Tipo di computer** selezionare **Computer fisici**.
4. Selezionare il server di elaborazione. Se non sono stati creati server di elaborazione aggiuntivi, questo sarà il server di configurazione. Fare quindi clic su **OK**.
5. In **Destinazione** selezionare la sottoscrizione e il gruppo di risorse in cui si vogliono creare le VM di cui viene effettuato il failover. Scegliere il modello di distribuzione (classica o Resource Manager) da usare in Azure per le VM di cui viene effettuato il failover.
6. Selezionare l'account di archiviazione di Azure da usare per la replica dei dati. Se non si vuole usare un account già configurato, è possibile crearne uno nuovo.
7. Selezionare la **rete** e la **subnet** di Azure a cui dovranno connettersi le VM di Azure dopo il failover. Scegliere **Configurare ora per le macchine virtuali selezionate** per applicare le impostazioni di rete a tutti i computer selezionati per la protezione. Scegliere **Configurare in seguito** per selezionare la rete di Azure per ogni computer. Se non si vuole usare una rete esistente, è possibile crearne una.

    ![Abilitare la replica](./media/physical-walkthrough-enable-replication/targetsettings.png)

8. In **Computer fisici**, fare clic su **+Computer fisici** e inserire il **nome** e l'**indirizzo IP**. Scegliere il sistema operativo del computer da replicare. È necessario attendere alcuni minuti prima che i computer vengano individuati e visualizzati nell'elenco.
9. In **Proprietà** > **Configura proprietà**selezionare l'account che verrà usato dal server di elaborazione per installare automaticamente il servizio Mobility nel computer.
10. Per impostazione predefinita, vengono replicati tutti i dischi. Fare clic su **Tutti i dischi** e deselezionare i dischi da non replicare. Fare quindi clic su **OK**. È possibile impostare proprietà aggiuntive delle VM in un secondo momento.

    ![Abilitare la replica](./media/physical-walkthrough-enable-replication/enable-replication6.png)
11. In **Impostazioni della replica** > **Configura impostazioni di replica** verificare che siano selezionati i criteri di replica corretti. Se si modificano i criteri, le modifiche verranno applicate al computer di replica e ai nuovi computer.
12. Abilitare la **Coerenza tra più VM** per raccogliere le macchine in un gruppo di replica e specificare un nome per il gruppo. Fare quindi clic su **OK**. Si noti che:

    * I computer in gruppi di replica vengono replicati insieme e hanno punti di ripristino condivisi coerenti con l'arresto anomalo del sistema e coerenti con l'app in caso di failover.
    * È consigliabile raggruppare i server fisici in modo da rispecchiare i carichi di lavoro. L'abilitazione della coerenza tra più VM può influire sulle prestazioni del carico di lavoro e deve essere usata solo se i computer eseguono lo stesso carico di lavoro ed è necessaria la coerenza.

13. Fare clic su **Abilita la replica**. È possibile tenere traccia dello stato del processo **Abilita protezione** in **Impostazioni** > **Processi** > **Processi di Site Recovery**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.

## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 11: Eseguire un failover di test](physical-walkthrough-test-failover.md)
