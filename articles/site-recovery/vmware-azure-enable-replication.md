---
title: Abilitare le macchine virtuali VMware per il ripristino di emergenza usando Azure Site Recovery
description: Questo articolo descrive come abilitare la replica di macchine virtuali VMware per il ripristino di emergenza usando il servizio Azure Site Recovery
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 06/28/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 1cc1ee82b45ecab17e4bcfb3a909fc90b33a1545
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954446"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Abilitare la replica per le macchine virtuali VMware in Azure

Questo articolo descrive come abilitare la replica delle macchine virtuali VMware locali in Azure.

## <a name="prerequisites"></a>prerequisiti

Questo articolo presuppone che l'utente abbia:

- [Configurare l'ambiente di origine locale](vmware-azure-set-up-source.md).
- [Configurare l'ambiente di destinazione in Azure](vmware-azure-set-up-target.md).

## <a name="before-you-start"></a>Prima di iniziare
Quando si esegue la replica di macchine virtuali VMware, tenere presenti queste informazioni:

* L'account utente di Azure deve disporre di determinate [autorizzazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) per abilitare la replica di una nuova macchina virtuale in Azure.
* Le VM VMware vengono rilevate ogni 15 minuti. La visualizzazione delle macchine virtuali nella portale di Azure dopo l'individuazione può richiedere più di 15 minuti. Allo stesso modo, l'individuazione può richiedere più di 15 minuti quando si aggiunge un nuovo server vCenter o un host vSphere.
* Possono essere necessari 15 minuti o più per le modifiche dell'ambiente nella macchina virtuale, ad esempio l'installazione degli strumenti VMware, per l'aggiornamento nel portale.
* È possibile controllare l'ora dell'ultima individuazione per le macchine virtuali VMware: vedere il campo **ultimo contatto** nella pagina **server di configurazione** per il server vCenter o l'host vSphere.
* Per aggiungere macchine virtuali per la replica senza attendere l'individuazione pianificata, evidenziare il server di configurazione (ma non fare clic su di esso) e selezionare **Aggiorna**.
* Quando si Abilita la replica, se la macchina virtuale è preparata, il server di elaborazione installa automaticamente il servizio Mobility Azure Site Recovery.

## <a name="enable-replication"></a>Abilitare la replica

Prima di eseguire i passaggi descritti in questa sezione, tenere presenti le seguenti informazioni:
* Azure Site Recovery ora viene replicata direttamente in Managed disks per tutte le nuove repliche. Il server di elaborazione scrive i log di replica in un account di archiviazione della cache nell'area di destinazione. Questi log vengono usati per creare punti di ripristino nei dischi gestiti di replica con convenzione di denominazione asrseeddisk.
* Il supporto di PowerShell per la replica a Managed disks è disponibile a partire dalla [versione 2.0.0 del modulo AZ. RecoveryServices](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) 
* Al momento del failover, il punto di ripristino selezionato viene usato per creare il disco gestito da destinazione.
* Le macchine virtuali configurate in precedenza per la replica negli account di archiviazione di destinazione non sono interessate.
* La replica negli account di archiviazione per una nuova macchina virtuale è disponibile solo tramite un'API REST (Representational State Transfer) e PowerShell. Usare l'API REST di Azure versione 2016-08-10 o 2018-01-10 per la replica negli account di archiviazione.

Per abilitare la replica, attenersi alla procedura seguente:
1. Andare al **passaggio 2: replicare l'applicazione** > **origine**. Dopo aver abilitato la replica per la prima volta, selezionare **+ replica** nell'insieme di credenziali per abilitare la replica per altre macchine virtuali.
2. Nella pagina **Origine** > **Origine** selezionare il server di configurazione.
3. Per **tipo di computer**selezionare **macchine virtuali** o computer **fisici**.
4. In **vCenter/vSphere Hypervisor** selezionare il server vCenter che gestisce l'host di vSphere oppure selezionare l'host. Questa impostazione non è pertinente se si sta eseguendo la replica di computer fisici.
5. Selezionare il server di elaborazione. Se non sono stati creati server di elaborazione aggiuntivi, il server di elaborazione incorporato del server di configurazione sarà disponibile nell'elenco a discesa. Viene indicato lo stato di integrità di ogni server di elaborazione in base ai limiti consigliati e altri parametri. Scegliere un server di elaborazione integro. Non è possibile scegliere un server di elaborazione [critico](vmware-physical-azure-monitor-process-server.md#process-server-alerts). È possibile [individuare e risolvere](vmware-physical-azure-troubleshoot-process-server.md) gli errori **oppure** configurare un [server di elaborazione scale-out](vmware-azure-set-up-process-server-scale.md).
    ![abilitare la finestra di origine della replica](media/vmware-azure-enable-replication/ps-selection.png)

> [!NOTE]
> Da [9,24 versioni](service-updates-how-to.md#links-to-currently-supported-update-rollups)vengono introdotti avvisi aggiuntivi per migliorare gli avvisi di integrità del server di elaborazione. Aggiornare Site Recovery componenti a versioni 9,24 o successive per tutti gli avvisi da generare.

6. Per **destinazione**selezionare la sottoscrizione e il gruppo di risorse in cui si desidera creare le macchine virtuali di cui è stato eseguito il failover. Scegliere il modello di distribuzione che si vuole usare in Azure per le macchine virtuali di cui è stato eseguito il failover.
2. Selezionare la rete di Azure e la subnet a cui si connetteranno le VM di Azure dopo il failover. La rete deve trovarsi nella stessa area dell'insieme di credenziali del servizio Site Recovery.

   Selezionare **Configura ora per le macchine virtuali selezionate** per applicare le impostazioni di rete a tutte le macchine virtuali selezionate per la protezione. Selezionare **Configura in seguito** per selezionare la rete di Azure per ogni macchina virtuale. Se non è disponibile una rete, sarà necessario crearla. Per creare una rete usando Azure Resource Manager, selezionare **Crea nuovo**. Selezionare una subnet, se applicabile, quindi fare clic su **OK**.
   
   ![Abilita finestra destinazione replica](./media/vmware-azure-enable-replication/enable-rep3.png)

1. Per le **macchine virtuali** > **selezionare macchine virtuali**, selezionare ogni macchina virtuale che si desidera replicare. È possibile selezionare solo le macchine virtuali per cui è possibile abilitare la replica. Selezionare **OK**. Se non è possibile visualizzare o selezionare una macchina virtuale specifica, vedere il [computer di origine non è elencato nella portale di Azure](https://aka.ms/doc-plugin-VM-not-showing) per risolvere il problema.

    ![Abilitare la replica selezionare le macchine virtuali finestra](./media/vmware-azure-enable-replication/enable-replication5.png)

1. Per le **proprietà** > **Configura proprietà**, selezionare l'account utilizzato dal server di elaborazione per installare automaticamente il servizio di Site Recovery Mobility nella macchina virtuale. Scegliere anche il tipo di disco gestito di destinazione in base al quale eseguire la replica in base ai modelli di varianza dei dati.
10. Per impostazione predefinita, vengono replicati tutti i dischi di una macchina virtuale di origine. Per escludere i dischi dalla replica, deselezionare la casella di controllo **Includi** per eventuali dischi che non si vuole replicare. Selezionare **OK**. È possibile impostare proprietà aggiuntive in un secondo momento. Altre informazioni sull' [esclusione di dischi](vmware-azure-exclude-disk.md).

    ![Abilita la finestra di dialogo Configura proprietà della replica](./media/vmware-azure-enable-replication/enable-replication6.png)

1. In **impostazioni di replica** > **configurare le impostazioni di replica**, verificare che siano selezionati i criteri di replica corretti. È possibile modificare le impostazioni dei criteri di replica in **impostazioni** > **criteri di replica** > ***nome dei criteri*** > **Modifica impostazioni**. Le modifiche apportate a un criterio sono valide anche per la replica e le nuove macchine virtuali.
1. Abilitare la coerenza tra più **VM** se si desidera raccogliere le macchine virtuali in un gruppo di replica. Specificare un nome per il gruppo e quindi fare clic su **OK**.

    > [!NOTE]
    >    * Le macchine virtuali in un gruppo di replica vengono replicate insieme e hanno condiviso punti di ripristino coerenti con l'arresto anomalo del sistema e coerenti con l'app quando eseguono il failover.
    >    * Raggruppare le macchine virtuali e i server fisici in modo da rispecchiare i carichi di lavoro. L'abilitazione della coerenza su più macchine virtuali può avere un impatto sulle prestazioni del carico di lavoro. Eseguire questa operazione solo se le macchine virtuali eseguono lo stesso carico di lavoro ed è necessaria la coerenza.

    ![Abilita finestra di replica](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Selezionare **Abilita replica**. È possibile tenere traccia dello stato di avanzamento del processo di **Abilitazione della protezione** in **impostazioni** > **processi** > **Site Recovery processi**. Dopo l'esecuzione del processo **Finalizza protezione**, la macchina virtuale è pronta per il failover.

## <a name="view-and-manage-vm-properties"></a>Visualizzare e gestire le proprietà della macchina virtuale

Verificare quindi le proprietà della macchina virtuale di origine. Tenere presente che il nome della VM di Azure deve essere conforme ai [requisiti delle macchine virtuali di Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Passare a **impostazioni** > **elementi replicati**e quindi selezionare la macchina virtuale. La **pagina informazioni** di base Mostra informazioni sulle impostazioni e lo stato della macchina virtuale.
1. In **Proprietà** sono disponibili le informazioni su replica e failover per la VM.
1. In **calcolo e rete** > **proprietà di calcolo**è possibile modificare più proprietà della macchina virtuale. 

    ![Finestra delle proprietà calcolo e rete](./media/vmware-azure-enable-replication/vmproperties.png)

    * Nome della macchina virtuale di Azure: modificare il nome in modo da soddisfare i requisiti di Azure, se necessario.
    * Dimensioni macchina virtuale di destinazione o tipo di macchina virtuale: le dimensioni predefinite della macchina virtuale vengono scelte in base a alcuni parametri che includono il numero di dischi, il numero NIC, il numero di core CPU, la memoria e le dimensioni dei ruoli VM disponibili nell'area di Azure di destinazione Azure Site Recovery seleziona la prima dimensione di macchina virtuale disponibile che soddisfa tutti i criteri. È possibile selezionare una diversa dimensione della macchina virtuale in base alle esigenze in qualsiasi momento prima del failover. Si noti che le dimensioni del disco della macchina virtuale sono basate anche sulle dimensioni del disco di origine e possono essere modificate solo dopo il failover. Per altre informazioni sulle dimensioni dei dischi e sulle tariffe di IOPS, vedere [obiettivi di scalabilità e prestazioni per i dischi delle VM in Windows](../virtual-machines/windows/disk-scalability-targets.md).

    *  Gruppo di risorse: è possibile selezionare un [gruppo di risorse](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines)da cui una macchina virtuale diventa parte di un post-failover. È possibile modificare questa impostazione in qualsiasi momento prima del failover. Dopo il failover, se si esegue la migrazione della macchina virtuale in un gruppo di risorse diverso, le impostazioni di protezione per tale macchina virtuale vengono interrotta.
    * Set di disponibilità: è possibile selezionare un [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) se la macchina virtuale deve far parte di un post-failover. Quando si seleziona un set di disponibilità, tenere presenti le seguenti informazioni:

        * Sono elencati solo i set di disponibilità che appartengono al gruppo di risorse specificato.  
        * Le macchine virtuali che si trovano in reti virtuali diverse non possono far parte dello stesso set di disponibilità.
        * In un set di disponibilità possono essere incluse solo macchine virtuali delle stesse dimensioni.
1. È anche possibile aggiungere informazioni sulla rete di destinazione, la subnet e l'indirizzo IP assegnati alla macchina virtuale di Azure.
2. In **Dischi** è possibile visualizzare il sistema operativo e i dischi dati della VM che verranno replicati.

### <a name="configure-networks-and-ip-addresses"></a>Configurare reti e indirizzi IP

È possibile impostare l'indirizzo IP di destinazione. Se non si specifica un indirizzo, la macchina virtuale di cui è stato eseguito il failover usa DHCP. Se si imposta un indirizzo che non è disponibile al momento del failover, il failover non riesce. Se l'indirizzo è disponibile nella rete di failover di test, è possibile usare lo stesso indirizzo IP di destinazione per il failover di test.

Il numero di schede di rete dipende dalle dimensioni specificate per la macchina virtuale di destinazione, come indicato di seguito:

- Se il numero di schede di rete nella macchina virtuale di origine è minore o uguale al numero di schede consentite per le dimensioni della VM di destinazione, la destinazione avrà lo stesso numero di schede dell'origine.
- Se il numero di schede per la macchina virtuale di origine supera il numero consentito per le dimensioni della VM di destinazione, viene usata la dimensione massima di destinazione. Se, ad esempio, una macchina virtuale di origine dispone di due schede di rete e le dimensioni della macchina virtuale di destinazione ne supportano quattro, la macchina virtuale di destinazione avrà due schede. Se la macchina virtuale di origine dispone di due schede ma le dimensioni di destinazione ne supportano solo una, la macchina virtuale di destinazione ha solo una scheda.
- Se la macchina virtuale ha più schede di rete, si connetteranno tutte alla stessa rete. Inoltre, la prima scheda visualizzata nell'elenco diventa la scheda di rete *predefinita* nella macchina virtuale di Azure. 

### <a name="azure-hybrid-benefit"></a>Vantaggio Azure Hybrid

I clienti di Microsoft Software Assurance possono usare Vantaggio Azure Hybrid per risparmiare sui costi di licenza per i computer Windows Server di cui viene eseguita la migrazione in Azure. Il vantaggio si applica anche al ripristino di emergenza di Azure. Se si è idonei, è possibile assegnare il vantaggio alla macchina virtuale che Site Recovery crea se si verifica un failover. A tale scopo, seguire questi passaggi:
1. Passare alle **Proprietà computer e rete** della macchina virtuale replicata.
2. Risposta quando viene richiesto se si dispone di una licenza di Windows Server che rende idonea per Vantaggio Azure Hybrid.
3. Verificare di disporre di una licenza di Windows Server idonea con Software Assurance che è possibile usare per applicare il vantaggio alla macchina virtuale che verrà creata in fase di failover.
4. Salvare le impostazioni per la macchina virtuale replicata.

Altre informazioni sul [Vantaggio Azure Hybrid](https://aka.ms/azure-hybrid-benefit-pricing).

## <a name="resolve-common-issues"></a>Risolvere i problemi comuni

* Ogni disco deve essere inferiore a 4 TB.
* Il disco del sistema operativo deve essere un disco di base e non un disco dinamico.
* Per le macchine virtuali abilitate per la generazione 2/UEFI, la famiglia di sistemi operativi deve essere Windows e il disco di avvio deve essere inferiore a 300 GB.

## <a name="next-steps"></a>Passaggi successivi

Quando la macchina virtuale raggiunge uno stato protetto, provare a eseguire un [failover](site-recovery-failover.md) per controllare se l'applicazione viene visualizzata in Azure.

* Per disabilitare la replica, vedere come [rimuovere la registrazione e le impostazioni di protezione](site-recovery-manage-registration-and-protection.md).
* Informazioni su come [automatizzare la replica per le macchine virtuali tramite PowerShell](vmware-azure-disaster-recovery-powershell.md).
