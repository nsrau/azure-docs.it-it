---
title: Abilitare la replica di macchine virtuali VMware per il ripristino di emergenza in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come abilitare le macchine virtuali VMware per la replica per il ripristino di emergenza in Azure usando Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 4/18/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: ba55afbd62bbbc2290d1daaebf77becc249c1d8b
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004738"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Abilitare la replica per le macchine virtuali VMware in Azure

Questo articolo descrive come abilitare la replica delle macchine virtuali VMware locali in Azure.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente abbia:

- [Configurare l'ambiente di origine in locale](vmware-azure-set-up-source.md).
- [Configurare l'ambiente di destinazione in Azure](vmware-azure-set-up-target.md).

## <a name="before-you-start"></a>Prima di iniziare
Quando si esegue la replica di macchine virtuali VMware, tenere presente queste informazioni:

* L'account utente di Azure deve disporre di determinate [autorizzazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) per abilitare la replica di una nuova macchina virtuale in Azure.
* Le VM VMware vengono rilevate ogni 15 minuti. Potrebbero essere necessari 15 minuti o più lungo per le macchine virtuali visualizzate nel portale di Azure dopo l'individuazione. Analogamente, individuazione può richiedere 15 minuti o più tempo quando si aggiunge un nuovo host vSphere o server vCenter.
* Potrebbero essere necessari 15 minuti o più lungo per le modifiche dell'ambiente nella macchina virtuale (ad esempio installazione degli strumenti VMware) da aggiornare nel portale.
* È possibile controllare l'ora individuati last per le macchine virtuali VMware: Vedere le **ultimo contatto alla** campo il **server di configurazione** pagina per il server vCenter/host vSphere.
* Per aggiungere le macchine virtuali per la replica senza attendere l'individuazione pianificata, evidenziare il server di configurazione, ma senza selezionarlo e selezionare **Aggiorna**.
* Quando si abilita la replica, se la macchina virtuale è pronto, il server di elaborazione installa automaticamente il servizio Azure Site Recovery Mobility su di esso.

## <a name="enable-replication"></a>Abilitare la replica

Prima di seguire i passaggi descritti in questa sezione, tenere presente quanto segue:
* Azure Site Recovery replica ora direttamente al servizio managed disks per tutte le nuove repliche. Il server di elaborazione scrive i log di replica in un account di archiviazione della cache nell'area di destinazione. Questi log vengono utilizzati per creare i punti di ripristino in dischi gestiti di replica.
* Al momento del failover, il punto di ripristino selezionato viene usato per creare il disco gestito di destinazione.
* Le macchine virtuali configurati in precedenza per la replica in account di archiviazione di destinazione non sono interessate.
* La replica in account di archiviazione per una nuova macchina virtuale è disponibile solo tramite un Representational State Transfer (REST) API e Powershell. Usare l'API REST di Azure versione 2016-08-10 o 2018-01-10 per la replica in account di archiviazione.

1. Passare a **passaggio 2: Eseguire la replica dell'applicazione** > **Origine**. Dopo aver abilitato la replica per la prima volta, selezionare **+ replica** nell'insieme di credenziali per abilitare la replica per le macchine virtuali aggiuntive.
1. Nella pagina **Origine** > **Origine** selezionare il server di configurazione.
1. Per la **tipo di computer**, selezionare **macchine virtuali** oppure **macchine fisiche**.
1. In **vCenter/vSphere Hypervisor** selezionare il server vCenter che gestisce l'host di vSphere oppure selezionare l'host. Questa impostazione non è rilevante se si esegue la replica di computer fisici.
1. Selezionare il server di elaborazione, che sarà il server di configurazione se sono state create server di elaborazione aggiuntivi. Selezionare **OK**.

    ![Abilitare la finestra di origine di replica](./media/vmware-azure-enable-replication/enable-replication2.png)

1. Per la **destinazione**, selezionare il sottoscrizione e gruppo di risorse in cui si desidera creare le macchine virtuali sottoposte a failover. Scegliere il modello di distribuzione che si desidera usare per le macchine virtuali sottoposte a failover in Azure.

1. Selezionare la rete di Azure e la subnet a cui si connetteranno le VM di Azure dopo il failover. La rete deve trovarsi nella stessa area dell'insieme di credenziali del servizio Site Recovery.

   Selezionare **configurare ora per le macchine virtuali selezionate** per applicare le impostazioni di rete per tutte le macchine virtuali che si seleziona per la protezione. Selezionare **configurare successivamente** per selezionare la rete di Azure per ogni macchina virtuale. Se non è disponibile una rete, sarà necessario crearla. Per creare una rete con Azure Resource Manager, selezionare **Crea nuovo**. Selezionare una subnet, se applicabile e quindi **OK**.
   
   ![Abilitare la finestra di destinazione di replica](./media/vmware-azure-enable-replication/enable-rep3.png)

1. Per la **macchine virtuali** > **selezionare le macchine virtuali**, selezionare ogni macchina virtuale che si vuole replicare. È possibile selezionare solo le macchine virtuali per cui è possibile abilitare la replica. Selezionare **OK**. Se non è possibile vedere o seleziona una determinata macchina virtuale, vedere [macchina virtuale di origine non è elencato nel portale di Azure](https://aka.ms/doc-plugin-VM-not-showing) per risolvere il problema.

    ![Abilita finestra selezionare macchine virtuali di replica](./media/vmware-azure-enable-replication/enable-replication5.png)

1. Per la **delle proprietà** > **configurare proprietà**, selezionare l'account che usa il server di elaborazione per installare automaticamente il servizio Mobility di Site Recovery nella macchina virtuale. Inoltre, scegliere il tipo di disco gestito nella destinazione per la replica in base ai dati di modelli di varianza.
10. Per impostazione predefinita, tutti i dischi di una macchina virtuale di origine vengono replicati. Per escludere dischi dalla replica, deselezionare il **inclusione** casella di controllo per tutti i dischi che non si vuole replicare. Selezionare **OK**. È possibile impostare proprietà aggiuntive in un secondo momento. Altre informazioni sulle [esclusione di dischi](vmware-azure-exclude-disk.md).

    ![Abilitare la replica configurare la finestra proprietà](./media/vmware-azure-enable-replication/enable-replication6.png)

1. Alla **le impostazioni di replica** > **Configura le impostazioni di replica**, verificare che sia selezionato il criterio di replica corretto. È possibile modificare le impostazioni di Criteri replica **le impostazioni** > **i criteri di replica** > ***nome del criterio***  >  **Modificare le impostazioni**. Le modifiche che applicano a un criterio si applicano anche a replica e nuovi di macchine virtuali.
1. Abilitare **coerenza tra più VM** se si desidera raccogliere le macchine virtuali in un gruppo di replica. Specificare un nome per il gruppo e quindi selezionare **OK**.

    > [!NOTE]
    >    * Le macchine virtuali in un gruppo di replica vengono replicati insieme e hanno condiviso i punti di ripristino coerenti con l'arresto anomalo del sistema e coerenti con l'app quando si esegue il failover.
    >    * Raggruppare le macchine virtuali e i server fisici in modo da rispecchiare i carichi di lavoro. L'abilitazione della coerenza su più macchine virtuali può avere un impatto sulle prestazioni del carico di lavoro. Questa operazione solo se le macchine virtuali eseguono stesso carico di lavoro ed necessaria la coerenza.

    ![Abilitare la finestra di replica](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Selezionare **Abilita replica**. È possibile monitorare lo stato del **Abilita protezione** del processo nella **impostazioni** > **processi** > **processi di Site Recovery**. Dopo l'esecuzione del processo **Finalizza protezione**, la macchina virtuale è pronta per il failover.

## <a name="view-and-manage-vm-properties"></a>Visualizzare e gestire le proprietà della macchina virtuale

Successivamente, verificare le proprietà della macchina virtuale di origine. Tenere presente che il nome della VM di Azure deve essere conforme ai [requisiti delle macchine virtuali di Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Passare a **le impostazioni** > **elementi replicati**e quindi selezionare la macchina virtuale. Il **Essentials** pagina Mostra le informazioni sulle impostazioni e sullo stato della macchina virtuale.
1. In **Proprietà** sono disponibili le informazioni su replica e failover per la VM.
1. Nelle **calcolo e rete** > **proprietà di calcolo**, è possibile modificare più proprietà della macchina virtuale. 

    ![Finestra delle proprietà di calcolo e rete](./media/vmware-azure-enable-replication/vmproperties.png)

    * Nome della macchina virtuale Azure: Modificare il nome per soddisfare i requisiti di Azure, se necessario.
    * Dimensioni della macchina virtuale di destinazione o tipo di macchina virtuale: La dimensione di macchina virtuale predefinita viene scelto in base a alcuni parametri che includono il numero di dischi, numero di NIC, numero di core CPU, memoria e le dimensioni dei ruoli di macchina virtuale disponibile nell'area di Azure di destinazione. Azure Site Recovery sceglie le prime dimensioni disponibili della macchina virtuale che soddisfa tutti i criteri. È possibile selezionare una dimensione VM diversa in base alle esigenze in qualsiasi momento prima del failover. Si noti che le dimensioni di disco della macchina virtuale anche si basa su dimensioni del disco di origine e può essere modificato dopo il failover. Altre informazioni sulle dimensioni dei dischi e le frequenze di IOPS [obiettivi di scalabilità e prestazioni per i dischi di macchina virtuale in Windows](../virtual-machines/windows/disk-scalability-targets.md).

    *  Gruppo di risorse: È possibile selezionare una [gruppo di risorse](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines), da cui una macchina virtuale diventa una parte dopo il failover. È possibile modificare questa impostazione in qualsiasi momento prima del failover. Dopo il failover, se si esegue la migrazione della macchina virtuale a un gruppo di risorse diverso, le impostazioni di protezione per la macchina virtuale interrotta.
    * Set di disponibilità: È possibile selezionare un [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) se la macchina virtuale deve essere una parte dopo il failover. Quando si seleziona un set di disponibilità, tenere presente le informazioni seguenti:

        * Vengono elencati solo i set di disponibilità che appartengono al gruppo di risorse specificato.  
        * Le macchine virtuali che si trovano in reti virtuali diverse non possono essere una parte dello stesso set di disponibilità.
        * In un set di disponibilità possono essere incluse solo macchine virtuali delle stesse dimensioni.
1. È anche possibile aggiungere informazioni sulla rete di destinazione, subnet e indirizzo IP assegnato alla macchina virtuale di Azure.
2. In **Dischi** è possibile visualizzare il sistema operativo e i dischi dati della VM che verranno replicati.

### <a name="configure-networks-and-ip-addresses"></a>Configurare reti e indirizzi IP

È possibile impostare l'indirizzo IP di destinazione. Se non si specifica un indirizzo, la macchina virtuale sottoposta a failover Usa DHCP. Se si imposta un indirizzo che non è disponibile al momento del failover, il failover non riesce. Se l'indirizzo è disponibile nella rete di failover di test, è possibile usare lo stesso indirizzo IP di destinazione per il failover di test.

Il numero di schede di rete dipende dalle dimensioni specificato per la macchina virtuale di destinazione, come indicato di seguito:

- Se il numero di schede di rete nella macchina virtuale di origine è minore o uguale al numero di schede consentite per le dimensioni della macchina virtuale di destinazione, la destinazione avrà lo stesso numero di schede dell'origine.
- Se il numero di schede per la macchina virtuale di origine supera il numero consentito per le dimensioni della macchina virtuale di destinazione, viene usata la dimensione massima di destinazione. Ad esempio, se una macchina virtuale di origine ha due schede di rete e le dimensioni della macchina virtuale di destinazione ne supportano quattro, la macchina virtuale di destinazione dispone di due schede. Se la VM di origine ha due schede ma le dimensioni di destinazione supportano solo una, la macchina virtuale di destinazione con una sola scheda.
- Se la macchina virtuale ha più schede di rete, si connetteranno tutte alla stessa rete. Inoltre, la prima scheda che viene visualizzata nell'elenco diventa la *predefinito* scheda di rete nella macchina virtuale di Azure. 

### <a name="azure-hybrid-benefit"></a>Vantaggio Azure Hybrid

I clienti Microsoft Software Assurance possono usare il vantaggio Azure Hybrid per risparmiare sui costi di licenza per computer Windows Server in cui viene eseguita la migrazione ad Azure. Il vantaggio vale anche per il ripristino di emergenza in Azure. Se sei idoneo, è possibile assegnare il vantaggio per la macchina virtuale che Site Recovery crea se si verifica un failover. A tale scopo, seguire questi passaggi:
1. Andare alla **delle proprietà di Computer e della rete** della macchina virtuale replicata.
2. Rispondere quando viene richiesto se si ha una licenza di Windows Server che ti rende idonei al vantaggio Azure Hybrid.
3. Verificare di avere una licenza di Windows Server idonea con Software Assurance che è possibile usare per applicare il vantaggio alla VM che verrà creata al momento del failover.
4. Salvare le impostazioni per la macchina virtuale replicata.

Altre informazioni sul [Vantaggio Azure Hybrid](https://aka.ms/azure-hybrid-benefit-pricing).

## <a name="resolve-common-issues"></a>Risolvere i problemi comuni

* Ogni disco devono essere inferiore a 4 TB.
* Il disco del sistema operativo deve essere un disco di base, non un disco dinamico.
* Per le macchine virtuali abilitate per 2/UEFI generazione, la famiglia del sistema operativo deve essere Windows e il disco di avvio deve essere inferiore a 300 GB.

## <a name="next-steps"></a>Passaggi successivi

Dopo che la macchina virtuale raggiunge uno stato protetto, provare una [failover](site-recovery-failover.md) per verificare se l'applicazione viene visualizzata in Azure.

* Per disabilitare la replica, vedere come [rimuovere la registrazione e le impostazioni di protezione](site-recovery-manage-registration-and-protection.md).
* Informazioni su come [automatizzare la replica per le macchine virtuali usando Powershell](vmware-azure-disaster-recovery-powershell.md).
