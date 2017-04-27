---
title: Replicare applicazioni (da VMware in Azure) | Microsoft Docs
description: Questo articolo descrive come configurare la replica di macchine virtuali in esecuzione su VMware in Azure.
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 2/17/2017
ms.author: asgang
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 06ac75a40ed1dc97046836388bb7938dabd2b9ac
ms.lasthandoff: 04/12/2017


---


# <a name="replicate-applications"></a>Replicare applicazioni


Questo articolo descrive come configurare la replica di macchine virtuali in esecuzione su VMware in Azure.
## <a name="prerequisites"></a>Prerequisiti

Nell'articolo si presuppone che le seguenti procedure siano già state eseguite

1.  [Configurare l'ambiente di origine locale](site-recovery-set-up-vmware-to-azure.md)
2.  [Configurare l'ambiente di destinazione in Azure](site-recovery-prepare-target-vmware-to-azure.md).


## <a name="enable-replication"></a>Abilitare la replica
#### <a name="before-you-start"></a>Prima di iniziare
Se si esegue la replica di macchine virtuali VMware, tenere presente quanto segue:

* Le VM VMware vengono rilevate ogni 15 minuti. Possono essere necessari più di 15 minuti perché vengano visualizzate nel portale dopo l'individuazione. Allo stesso modo, l'individuazione può richiedere anche più di 15 minuti quando si aggiunge un nuovo host vSphere o un server vCenter.
* Anche per le modifiche dell'ambiente nella macchina virtuale, ad esempio l'installazione degli strumenti VMware, possono essere necessari oltre 15 minuti per l'aggiornamento nel portale.
* È possibile controllare l'ora dell'ultima individuazione di VM VMware nel campo **Ora ultimo contatto** relativo al server vCenter o all'host vSphere del pannello **Server di configurazione**.
* Per aggiungere computer per la replica senza attendere l'individuazione pianificata, evidenziare il server di configurazione, senza selezionarlo, e scegliere il pulsante **Aggiorna** .
* Quando si abilita la replica, se il computer è preparato, il server di elaborazione installa automaticamente il servizio Mobility nel computer.


**Per abilitare la replica, procedere come descritto di seguito**.

1. Fare clic su **Passaggio 2: Eseguire la replica dell'applicazione** > **Origine**. Dopo avere abilitato la replica per la prima volta, è necessario fare clic su **+Replica** nell'insieme di credenziali per abilitare la replica per altre macchine.
2. Nel pannello **Origine** > **Origine** selezionare il server di configurazione.
3. In **Tipo di computer** selezionare **Macchine virtuali** o **Computer fisici**.
4. In **vCenter/vSphere Hypervisor** selezionare il server vCenter che gestisce l'host di vSphere oppure selezionare l'host. Questa impostazione non si applica se si esegue la replica di computer fisici.
5. Selezionare il server di elaborazione. Se ancora non sono stati creati server di elaborazione aggiuntivi, questo sarà il nome del server di configurazione. Fare quindi clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. In **Destinazione** selezionare la sottoscrizione e il gruppo di risorse in cui si vuole creare il failover delle macchine virtuali. Scegliere il modello di distribuzione che si vuole usare in Azure (classica o con Resource Manager) per il failover delle macchine virtuali.


7. Selezionare l'account di archiviazione di Azure da usare per la replica dei dati. Si noti che:

   * È possibile selezionare un account di archiviazione Standard o Premium. Se si seleziona un account Premium, si dovrà specificare un account di archiviazione Standard aggiuntivo per i log di replica in corso. L'account deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.
   * Se si vuole usare un account di archiviazione diverso rispetto da quello disponibile, è possibile creare un *collegamento segnaposto con Resource Manager, come illustrato nella Guida introduttiva*. Per creare un account di archiviazione con Resource Manager, fare clic su **Crea nuovo**. Per creare un account di archiviazione con il modello di distribuzione classica, usare il [portale di Azure](../storage/storage-create-storage-account-classic-portal.md).


8. Selezionare la rete di Azure e la subnet a cui dovranno connettersi le macchine virtuali di Azure attivate dopo il failover. La rete deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino. Scegliere **Configurare ora per le macchine virtuali selezionate** per applicare le impostazioni di rete a tutti i computer selezionati per la protezione. Scegliere **Configurare in seguito** per selezionare la rete di Azure per ogni computer. Se non è disponibile una rete, sarà necessario [crearla](#set-up-an-azure-network). Per creare una rete con Resource Manager, fare clic su **Crea nuovo**. Per creare una rete con il modello di distribuzione classica, usare il [portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selezionare una subnet, se applicabile. Fare quindi clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/enable-rep3.png)
9. In **Macchine virtuali** > **Seleziona macchine virtuali** fare clic per selezionare tutte le macchine virtuali da replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Fare quindi clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/enable-replication5.png)
10. In **Proprietà** > **Configura proprietà**selezionare l'account che verrà usato dal server di elaborazione per installare automaticamente il servizio Mobility nel computer. Per impostazione predefinita, vengono replicati tutti i dischi. Fare clic su **Tutti i dischi** e deselezionare i dischi da non replicare. Fare quindi clic su **OK**. È possibile impostare proprietà aggiuntive in un secondo momento.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/enable-replication6.png)


> [!NOTE]
> Per impostazione predefinita, vengono replicati tutti i dischi in un computer. È possibile [escludere dischi dalla replica](site-recovery-exclude-disk.md). Ad esempio, è possibile evitare di replicare i dischi con dati temporanei o dati che vengono aggiornati ogni volta che un computer o un'applicazione viene riavviata, come pagefile.sys o tempdb di SQL Server.
>



11. In **Impostazioni della replica** > **Configura impostazioni di replica** verificare che siano selezionati i criteri di replica corretti. È possibile modificare le impostazioni dei criteri di replica in **Impostazioni** > **Criteri di replica** > nome dei criteri > **Modifica impostazioni**. Le modifiche apportate ai criteri saranno applicate ai computer nuovi e in fase di replica.
12. Abilitare la **Coerenza tra più VM** per raccogliere le macchine in un gruppo di replica e specificare un nome per il gruppo. Fare quindi clic su **OK**. Si noti che:

    * Le macchine virtuali in un gruppo di replica vengono replicate insieme hanno punti di ripristino condivisi coerenti con l'arresto anomalo del sistema e coerenti con l'app quando si esegue il failover.
    * È consigliabile raggruppare le macchine virtuali e i server fisici in modo da rispecchiare i carichi di lavoro. L'abilitazione della coerenza di più macchine virtuali può influire sulle prestazioni del carico di lavoro e deve essere utilizzata solo se i computer eseguono stesso carico di lavoro ed è necessaria la coerenza.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/enable-replication7.png)
13. Fare clic su **Abilita la replica**. È possibile tenere traccia dello stato del processo **Abilita protezione** in **Impostazioni** > **Processi** > **Processi di Site Recovery**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.

> [!NOTE]
> Se il computer è pronto per l'installazione push, il componente servizio Mobility verrà installato quando la protezione viene abilitata. Una volta installato il componente nel computer, un processo di protezione viene avviato e non riesce. Dopo l'errore, è necessario riavviare manualmente tutti i computer. Dopo il riavvio, il processo di protezione viene avviato nuovamente e viene eseguita la replica iniziale.
>
>

## <a name="view-and-manage-vm-properties"></a>Visualizzare e gestire le proprietà della macchina virtuale
È consigliabile verificare le proprietà del computer di origine. Tenere presente che il nome della VM di Azure deve essere conforme ai [requisiti delle macchine virtuali di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. Fare clic su **Impostazioni** > **Elementi replicati** e selezionare il computer. Il pannello **Informazioni di base** visualizza informazioni sulle impostazioni e sullo stato dei computer.
2. In **Proprietà** sono disponibili le informazioni su replica e failover per la VM.
3. In **Calcolo e rete** > **Proprietà di calcolo** è possibile specificare le dimensioni di destinazione e il nome della VM di Azure. Se necessario, modificare il nome in modo che sia conforme ai requisiti di Azure.
![Abilitare la replica](./media/site-recovery-vmware-to-azure/VMProperties_AVSET.png)

*Gruppo di risorse*
   
  * È possibile selezionare un [gruppo di risorse](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines) il cui computer andrà a fare parte del post-failover. Questa impostazione può essere modificata in qualsiasi momento prima del failover. 
  
> [!NOTE]
> Dopo il failover, se si esegue la migrazione del computer a un gruppo di risorse diverso, le impostazioni di protezione di un computer vengono interrotte.
 
*Set di disponibilità*

È possibile selezionare un [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) se il computer deve essere una parte di un post-failover. Mentre si seleziona il set di disponibilità, tenere presente che:

* Verranno elencati solo i set di disponibilità che appartengono al gruppo di risorse specificato  
* I computer con reti virtuali diverse non possono fare parte dello stesso set di disponibilità 
* Solo le macchine virtuali della stessa dimensione possono fare parte della stesso set di disponibilità 

*Proprietà di rete*

È anche possibile visualizzare e aggiungere le informazioni sulla rete di destinazione, la subnet e l'indirizzo IP che verranno assegnati alla macchina virtuale di Azure. Tenere presente quanto segue:

   * È possibile impostare l'indirizzo IP di destinazione. Se non si specifica un indirizzo, il computer di cui è stato eseguito il failover usa DHCP. Se si imposta un indirizzo che non è disponibile al momento del failover, il failover non riesce. Se l'indirizzo è disponibile nella rete di failover di test, è possibile usare lo stesso indirizzo IP di destinazione per il failover di test.
   * Il numero di schede di rete dipende dalle dimensioni specificate per la macchina virtuale di destinazione, come illustrato di seguito:

     * Se il numero di schede di rete nella macchina di origine è minore o uguale al numero di schede consentite per la macchina di destinazione, la destinazione avrà lo stesso numero di schede dell’origine.
     * Se il numero di schede per la macchina virtuale di origine supera il numero consentito per le dimensioni di destinazione, verrà utilizzata la dimensione di destinazione massima.
     * Ad esempio, se una macchina di origine dispone di due schede di rete e le dimensioni della macchina di destinazione ne supportano quattro, la macchina di destinazione avrà due schede. Se la macchina di origine dispone di due schede ma le dimensioni di destinazione supportate ne consentono solo una, la macchina di destinazione avrà una sola scheda.     
   * Se la macchina virtuale ha più schede di rete, si connetteranno tutte alla stessa rete.
   * Se la macchina virtuale ha più schede di rete, la prima nell'elenco diventa la scheda di rete *predefinita* nella macchina virtuale di Azure.

4. In **Dischi** è possibile visualizzare il sistema operativo e i dischi dati della VM che verranno replicati.


## <a name="common-issues"></a>Problemi comuni

* Le dimensioni di ogni disco devono essere inferiori a 1 TB.
* Il disco del sistema operativo deve essere un disco di base e non un disco dinamico
* Per le macchine virtuali di seconda generazione/abilitate per l'interfaccia UEFI, la famiglia del sistema operativo deve essere Windows e le dimensioni del disco di avvio devono essere minori di 300 GB

## <a name="next-steps"></a>Passaggi successivi

Una volta completata la protezione, è possibile provare il test di [failover](site-recovery-failover.md) per controllare se l'applicazione viene visualizzata o meno in Azure.

Se si desidera disabilitare la protezione, controllare come [rimuovere la registrazione e le impostazioni di protezione](site-recovery-manage-registration-and-protection.md)

