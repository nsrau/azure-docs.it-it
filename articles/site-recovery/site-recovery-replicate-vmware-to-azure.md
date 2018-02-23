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
ms.date: 09/29/2017
ms.author: asgang
ms.openlocfilehash: 028aa0f23c3a7c98c4801d9e306c5dcfa35aab80
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2017
---
# <a name="replicate-applications-running-on-vmware-virtual-machines-to-azure"></a>Eseguire la replica di applicazioni in esecuzione su macchine virtuali VMware in Azure



Questo articolo descrive come configurare la replica di macchine virtuali (VM) in esecuzione su VMware in Azure.
## <a name="prerequisites"></a>prerequisiti

Questo articolo presuppone che l'utente abbia:

1.  [Configurato l'ambiente di origine locale](site-recovery-set-up-vmware-to-azure.md).
2.  [Configurato l'ambiente di destinazione in Azure](site-recovery-prepare-target-vmware-to-azure.md).


## <a name="enable-replication"></a>Abilitare la replica
### <a name="before-you-start"></a>Prima di iniziare
Quando si esegue la replica di macchine virtuali VMware:

* L'account utente di Azure deve disporre di determinate [autorizzazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) per abilitare la replica di una nuova macchina virtuale in Azure.
* Le VM VMware vengono rilevate ogni 15 minuti. Possono essere necessari più di 15 minuti perché vengano visualizzate nel portale di Azure dopo l'individuazione. Allo stesso modo, l'individuazione può richiedere anche più di 15 minuti quando si aggiunge un nuovo host vSphere o un server vCenter.
* Anche per le modifiche dell'ambiente nella macchina virtuale, ad esempio l'installazione degli strumenti VMware, possono essere necessari oltre 15 minuti per l'aggiornamento nel portale.
* È possibile controllare l'ora dell'ultima individuazione di VM VMware nel campo **Ora ultimo contatto** relativo al server vCenter o all'host vSphere della pagina **Server di configurazione**.
* Per aggiungere computer per la replica senza attendere l'individuazione pianificata, evidenziare il server di configurazione, senza selezionarlo, e scegliere il pulsante **Aggiorna** .
* Quando si abilita la replica, se il computer è preparato, il server di elaborazione installa automaticamente il servizio Mobility nel computer.


### <a name="enable-replication-as-follows"></a>Per abilitare la replica, procedere come descritto di seguito

1. Fare clic su **Passaggio 2: Eseguire la replica dell'applicazione** > **Origine**. Dopo avere abilitato la replica per la prima volta, è necessario fare clic su **+Replica** nell'insieme di credenziali per abilitare la replica per altre macchine.
2. Nella pagina **Origine** > **Origine** selezionare il server di configurazione.
3. In **Tipo di computer** selezionare **Macchine virtuali** o **Computer fisici**.
4. In **vCenter/vSphere Hypervisor** selezionare il server vCenter che gestisce l'host di vSphere oppure selezionare l'host. Questa impostazione non si applica se si esegue la replica di computer fisici.
5. Selezionare il server di elaborazione. Questo sarà il nome del server di configurazione se ancora non sono stati creati server di elaborazione aggiuntivi. Fare quindi clic su **OK**.

    ![Abilitare l'origine della replica](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. In **Destinazione** selezionare la sottoscrizione e il gruppo di risorse in cui si vuole creare il failover delle macchine virtuali. Scegliere il modello di distribuzione che si vuole usare in Azure (classica o con Azure Resource Manager) per il failover delle macchine virtuali.

7. Selezionare l'account di archiviazione di Azure da usare per la replica dei dati. 

    > [!NOTE]

    >   * È possibile selezionare un account di archiviazione Standard o Premium. Se si seleziona un account Premium, si dovrà specificare un account di archiviazione Standard aggiuntivo per i log di replica in corso. L'account deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.
    >   * Per usare un account di archiviazione diverso, è possibile [crearne uno](../storage/common/storage-create-storage-account.md). Per creare un account di archiviazione con Resource Manager, fare clic su **Crea nuovo**. Per creare un account di archiviazione con il modello di distribuzione classica, usare il portale di Azure.

8. Selezionare la rete di Azure e la subnet a cui dovranno connettersi le macchine virtuali di Azure attivate dopo il failover. La rete deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino. Scegliere **Configurare ora per le macchine virtuali selezionate** per applicare le impostazioni di rete a tutti i computer selezionati per la protezione. Scegliere **Configurare in seguito** per selezionare la rete di Azure per ogni computer. Se non è disponibile una rete, sarà necessario [crearla](#set-up-an-azure-network). Per creare una rete con Resource Manager, fare clic su **Crea nuovo**. Per creare una rete con il modello di distribuzione classica, usare il [portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selezionare una subnet, se applicabile, e fare clic su **OK**.

    ![Abilitare le impostazioni delle destinazione di replica](./media/site-recovery-vmware-to-azure/enable-rep3.png)
9. In **Macchine virtuali** > **Seleziona macchine virtuali** selezionare tutte le macchine virtuali da replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Fare quindi clic su **OK**.

    ![Abilitare la replica delle macchine virtuali](./media/site-recovery-vmware-to-azure/enable-replication5.png)
10. In **Proprietà** > **Configura proprietà** selezionare l'account che verrà usato dal server di elaborazione per installare automaticamente il servizio Mobility nel computer.  
11. Per impostazione predefinita, vengono replicati tutti i dischi. Per escludere i dischi dalla replica, fare clic su **Tutti i dischi** e cancellare eventuali dischi di cui non si vuole eseguire la replica.  Fare quindi clic su **OK**. È possibile impostare proprietà aggiuntive in un secondo momento. [Altre informazioni](site-recovery-exclude-disk.md) sull'esclusione di dischi.

    ![Abilitare le proprietà di configurazione della replica](./media/site-recovery-vmware-to-azure/enable-replication6.png)

12. In **Impostazioni della replica** > **Configura impostazioni di replica** verificare che siano selezionati i criteri di replica corretti. È possibile modificare le impostazioni dei criteri di replica in **Impostazioni** > **Criteri di replica** > (nome dei criteri) > **Modifica impostazioni**. Le modifiche apportate ai criteri saranno applicate anche ai computer nuovi e in fase di replica.
13. Abilitare la **Coerenza tra più VM** per raccogliere le macchine in un gruppo di replica. Specificare il nome del gruppo e fare clic su **OK**. 

    > [!NOTE]

    >    * Le macchine virtuali in un gruppo di replica vengono replicate insieme e hanno punti di ripristino condivisi coerenti con l'arresto anomalo del sistema e coerenti con l'app quando si esegue il failover.
    >    * Raggruppare le macchine virtuali e i server fisici in modo da rispecchiare i carichi di lavoro. L'abilitazione della coerenza su più macchine virtuali può avere un impatto sulle prestazioni del carico di lavoro. Usarla solo se i computer eseguono lo stesso carico di lavoro ed è necessaria la coerenza.

    ![Abilitare la replica](./media/site-recovery-vmware-to-azure/enable-replication7.png)
14. Fare clic su **Abilita la replica**. È possibile tenere traccia dello stato del processo **Abilita protezione** in **Impostazioni** > **Processi** > **Processi di Site Recovery**. Dopo l'esecuzione del processo **Finalizza protezione** la macchina virtuale è pronta per il failover.

> [!NOTE]
> Se il computer è pronto per l'installazione push, il componente servizio Mobility verrà installato quando la protezione viene abilitata. Una volta installato il componente nel computer, un processo di protezione viene avviato e non riesce. Dopo l'errore, è necessario riavviare manualmente tutti i computer. Dopo il riavvio, il processo di protezione viene avviato nuovamente e viene eseguita la replica iniziale.
>
>

## <a name="view-and-manage-vm-properties"></a>Visualizzare e gestire le proprietà della macchina virtuale

Successivamente, verificare le proprietà del computer di origine. Tenere presente che il nome della VM di Azure deve essere conforme ai [requisiti delle macchine virtuali di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. Fare clic su **Impostazioni** > **Elementi replicati** e selezionare il computer. La pagina **Informazioni di base** visualizza informazioni sulle impostazioni e sullo stato dei computer.
2. In **Proprietà** sono disponibili le informazioni su replica e failover per la VM.
3. In **Calcolo e rete** > **Proprietà di calcolo** è possibile specificare le dimensioni di destinazione e il nome della VM di Azure. Se necessario, modificare il nome in modo che sia conforme ai requisiti di Azure.

    ![Proprietà di Calcolo e rete](./media/site-recovery-vmware-to-azure/vmproperties.png)

4.  È possibile selezionare un [gruppo di risorse](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines) di cui il computer diventerà parte dopo il failover. È possibile modificare questa impostazione in qualsiasi momento prima del failover. Dopo il failover, se si esegue la migrazione del computer a un gruppo di risorse diverso, le impostazioni di protezione di quel computer non potranno essere applicate.
5. Se il computer deve far parte di un [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) dopo il failover, è possibile selezionarne uno. Quando si seleziona un set di disponibilità, tenere presente quanto segue:

    * Verranno elencati solo i set di disponibilità che appartengono al gruppo di risorse specificato.  
    * Computer con reti virtuali diverse non possono far parte dello stesso set di disponibilità.
    * In un set di disponibilità possono essere incluse solo macchine virtuali delle stesse dimensioni.
5. È anche possibile visualizzare e aggiungere le informazioni sulla rete di destinazione, la subnet e l'indirizzo IP assegnati alla macchina virtuale di Azure.
6. In **Dischi** è possibile visualizzare il sistema operativo e i dischi dati della VM che verranno replicati.

### <a name="network-adapters-and-ip-addressing"></a>Schede di rete e indirizzi IP

- È possibile impostare l'indirizzo IP di destinazione. Se non si specifica un indirizzo, il computer di cui è stato eseguito il failover usa DHCP. Se si imposta un indirizzo che non è disponibile al momento del failover, il failover non riesce. Se l'indirizzo è disponibile nella rete di failover di test, è possibile usare lo stesso indirizzo IP di destinazione per il failover di test.
- Il numero di schede di rete dipende dalle dimensioni specificate per la macchina virtuale di destinazione, come illustrato di seguito:
    - Se il numero di schede di rete nella macchina di origine è minore o uguale al numero di schede consentite per la macchina di destinazione, la destinazione avrà lo stesso numero di schede dell'origine.
    - Se il numero di schede per la macchina virtuale di origine supera il numero consentito per le dimensioni di destinazione, verrà usata la dimensione di destinazione massima.
    Ad esempio, se una macchina di origine ha due schede di rete e le dimensioni della macchina di destinazione ne supportano quattro, la macchina di destinazione avrà due schede. Se la macchina di origine ha due schede, ma le dimensioni di destinazione supportate ne consentono solo una, la macchina di destinazione avrà una sola scheda.
    - Se la macchina virtuale ha più schede di rete, si connetteranno tutte alla stessa rete. Inoltre, la prima nell'elenco diventa la scheda di rete *predefinita* nella macchina virtuale di Azure.

### <a name="azure-hybrid-use-benefit"></a>Vantaggio Azure Hybrid Use

I clienti di Microsoft Software Assurance possono usare il Vantaggio Azure Hybrid Use per risparmiare sui costi di licenza per i computer Windows Server di cui viene eseguita la migrazione ad Azure o usare Azure per il ripristino di emergenza. Se si ha diritto a usare il Vantaggio Azure Hybrid Use, è possibile specificare che questo vantaggio dovrà essere assegnato alla macchina virtuale creata da Azure Site Recovery in Azure in caso di failover. A tale scopo, effettuare l'operazione seguente:
- Passare alla sezione delle proprietà Calcolo e rete della macchina virtuale replicata.
- Rispondere alla domanda che chiede se si ha una licenza di Windows Server in virtù della quale si è idonei per il Vantaggio Azure Hybrid Use.
- Selezionare la casella di controllo per confermare che si ha una licenza di Windows Server con Software Assurance idonea che consente di applicare il Vantaggio Hybrid Use nel computer che verrà creato con il failover.
- Salvare le impostazioni per il computer replicato.

Altre informazioni sul [Vantaggio Azure Hybrid Use](https://aka.ms/azure-hybrid-use-benefit-pricing).

## <a name="common-issues"></a>Problemi comuni

* Le dimensioni di ogni disco devono essere inferiori a 1 TB.
* Il disco del sistema operativo deve essere un disco di base e non un disco dinamico.
* Per le macchine virtuali di seconda generazione/abilitate per l'interfaccia UEFI, la famiglia del sistema operativo deve essere Windows e le dimensioni del disco di avvio devono essere minori di 300 GB.

## <a name="next-steps"></a>Passaggi successivi

Quando la protezione è completata e il computer si trova in uno stato protetto, si può provare a effettuare un [failover](site-recovery-failover.md) per controllare se l'applicazione risulta disponibile in Azure o meno.

Se si desidera disabilitare la protezione, vedere come [rimuovere la registrazione e le impostazioni di protezione](site-recovery-manage-registration-and-protection.md).
