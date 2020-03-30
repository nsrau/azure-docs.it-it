---
title: Abilitare le macchine virtuali VMware per il ripristino di emergenza usando Azure Site RecoveryEnable VMware VMs for disaster recovery using Azure Site Recovery
description: In questo articolo viene descritto come abilitare la replica delle macchine virtuali VMware per il ripristino di emergenza tramite il servizio Azure Site Recovery
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 06/28/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 10b3e572ec61d1eff342f24a6a5a7bcba6276983
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257316"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Abilitare la replica per le macchine virtuali VMware in Azure

Questo articolo descrive come abilitare la replica delle macchine virtuali VMware locali in Azure.

## <a name="resolve-common-issues"></a>Risolvere i problemi comuni

* Ogni disco deve essere inferiore a 4 TB.
* Il disco del sistema operativo deve essere un disco di base, non un disco dinamico.
* Per le macchine virtuali abilitate per la generazione 2/UEFI, la famiglia di sistemi operativi deve essere Windows e il disco di avvio deve essere inferiore a 300 GB.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente abbia:

- [Configurare l'ambiente](vmware-azure-set-up-source.md)di origine locale.
- [Configurare l'ambiente di destinazione in Azure.](vmware-azure-set-up-target.md)
- [Verificare i requisiti e i prerequisiti](vmware-physical-azure-support-matrix.md) prima di iniziare. Le cose importanti da notare includono:
    - [Sistemi operativi supportati](vmware-physical-azure-support-matrix.md#replicated-machines) per i computer replicati.
    - [Supporto di archiviazione/disco.](vmware-physical-azure-support-matrix.md#storage)
    - [Requisiti di Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements) con cui le macchine locali devono essere conformi.


## <a name="before-you-start"></a>Prima di iniziare
Quando si replicano le macchine virtuali VMware, tenere presente queste informazioni:When you're replicating VMware virtual machines, keep this information in mind:

* L'account utente di Azure deve disporre di determinate [autorizzazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) per abilitare la replica di una nuova macchina virtuale in Azure.
* Le VM VMware vengono rilevate ogni 15 minuti. La visualizzazione delle macchine virtuali nel portale di Azure dopo l'individuazione può richiedere 15 minuti o più. Analogamente, l'individuazione può richiedere 15 minuti o più quando si aggiunge un nuovo server vCenter o un nuovo host vSphere.
* L'aggiornamento nel portale delle modifiche all'ambiente nella macchina virtuale, ad esempio l'installazione degli strumenti VMware, può richiedere 15 minuti o più.
* È possibile controllare l'ora dell'ultimo individuazione per le macchine virtuali VMware: vedere il campo **Ultimo contatto nella** pagina **Server** di configurazione per l'host server/vSphere di vCenter.
* Per aggiungere macchine virtuali per la replica senza attendere l'individuazione pianificata, evidenziare il server di configurazione (ma non fare clic su di esso) e selezionare **Aggiorna**.
* Quando si abilita la replica, se la macchina virtuale è preparata, il server di elaborazione installa automaticamente il servizio Mobilità di Azure Site Recovery su di esso.

## <a name="enable-replication"></a>Abilitare la replica

Prima di eseguire i passaggi descritti in questa sezione, tenere presente le informazioni seguenti:
* Azure Site Recovery ora replica direttamente i dischi gestiti per tutte le nuove repliche. Il server di elaborazione scrive i log di replica in un account di archiviazione della cache nell'area di destinazione. Questi log vengono utilizzati per creare punti di ripristino nei dischi gestiti di replica con convenzione di denominazione asrseeddisk.These logs are used to create recovery points in replica managed disks that have naming convention of asrseeddisk.
* Il supporto di Powershell per la replica nei dischi gestiti è disponibile dal [modulo Az.RecoveryServices versione 2.0.0 e versioni successive](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) 
* Al momento del failover, il punto di ripristino selezionato viene utilizzato per creare il disco gestito di destinazione.
* Le macchine virtuali configurate in precedenza per la replica in account di archiviazione di destinazione non sono interessate.
* La replica negli account di archiviazione per una nuova macchina virtuale è disponibile solo tramite un'API REST (Representational State Transfer) e Powershell.Replication to storage accounts for a new virtual machine is only available via a Representational State Transfer (REST) API and Powershell. Usare la versione dell'API REST di Azure 2016-08-10 o 2018-01-10 per la replica negli account di archiviazione.

Attenersi alla seguente procedura per abilitare la replica:
1. Andare al **Passaggio 2: Replicare l'origine dell'applicazione** > **Source**. Dopo aver abilitato la replica per la prima volta, selezionare **Replica** nell'insieme di credenziali per abilitare la replica per altre macchine virtuali.
2. Nella pagina **Origine** > **Origine** selezionare il server di configurazione.
3. Per **Tipo di computer**, selezionare Macchine **virtuali** o Macchine **fisiche**.
4. In **vCenter/vSphere Hypervisor** selezionare il server vCenter che gestisce l'host di vSphere oppure selezionare l'host. Questa impostazione non è rilevante se si replicano computer fisici.
5. Selezionare il server di elaborazione. Se non vengono creati altri server di elaborazione, il server di elaborazione integrato del server di configurazione sarà disponibile nell'elenco a discesa. Viene indicato lo stato di integrità di ogni server di elaborazione in base ai limiti consigliati e altri parametri. Scegliere un server di elaborazione integro. Non è possibile scegliere un server di elaborazione [critico](vmware-physical-azure-monitor-process-server.md#process-server-alerts). È possibile [individuare e risolvere](vmware-physical-azure-troubleshoot-process-server.md) gli errori **oppure** configurare un [server di elaborazione scale-out](vmware-azure-set-up-process-server-scale.md).
    ![Finestra Abilita origine replica](media/vmware-azure-enable-replication/ps-selection.png)

> [!NOTE]
> A partire dalle [versioni 9.24,](service-updates-how-to.md#links-to-currently-supported-update-rollups)vengono introdotti avvisi aggiuntivi per migliorare gli avvisi di integrità del server di elaborazione. Aggiornare i componenti di Site Recovery alle versioni 9.24 o successive per la generazione di tutti gli avvisi.

6. In **Destinazione**selezionare la sottoscrizione e il gruppo di risorse in cui si desidera creare le macchine virtuali di cui è stato eseguito il failover. Scegliere il modello di distribuzione che si vuole usare in Azure per le macchine virtuali di cui è stato eseguito il failover.
2. Selezionare la rete e la subnet di Azure a cui si connetteranno le macchine virtuali di Azure dopo il failover. La rete deve trovarsi nella stessa area dell'insieme di credenziali del servizio Site Recovery.The network must be in the same region as the Site Recovery service vault.

   Selezionare **Configura ora per i computer selezionati** per applicare l'impostazione di rete a tutte le macchine virtuali selezionate per la protezione. Selezionare **Configura in un secondo momento** per selezionare la rete di Azure per ogni macchina virtuale. Se non è disponibile una rete, sarà necessario crearla. Per creare una rete tramite Azure Resource Manager, selezionare **Crea nuova**. Selezionare una subnet, se applicabile, quindi scegliere **OK**.
   
   ![Finestra Abilita destinazione replica](./media/vmware-azure-enable-replication/enable-rep3.png)

1. Per **Macchine** > virtuali**Selezionare macchine virtuali**selezionare ogni macchina virtuale che si desidera replicare. È possibile selezionare solo macchine virtuali per le quali è possibile abilitare la replica. Quindi selezionare **OK**. Se non è possibile visualizzare o selezionare una macchina virtuale specifica, vedere Macchina di [origine non elencata nel portale](https://aka.ms/doc-plugin-VM-not-showing) di Azure per risolvere il problema.

    ![Finestra Seleziona replica seleziona macchine virtuali](./media/vmware-azure-enable-replication/enable-replication5.png)

1. In **Proprietà** > **Configurare le proprietà**selezionare l'account utilizzato dal server di elaborazione per installare automaticamente il servizio per dispositivi mobili di Site Recovery nella macchina virtuale. Scegliere inoltre il tipo di disco gestito di destinazione in cui eseguire la replica in base ai modelli di varianza dei dati.
10. Per impostazione predefinita, tutti i dischi di una macchina virtuale di origine vengono replicati. Per escludere dischi dalla replica, **deselezionare** la casella di controllo Includi per i dischi che non si desidera replicare. Quindi selezionare **OK**. È possibile impostare proprietà aggiuntive in un secondo momento. Ulteriori informazioni [sull'esclusione dei dischi](vmware-azure-exclude-disk.md).

    ![Finestra delle proprietà di abilitazione della configurazione della replica](./media/vmware-azure-enable-replication/enable-replication6.png)

1. In **Impostazioni** > di replica**Configurare le impostazioni di replica**verificare che sia selezionato il criterio di replica corretto. È possibile modificare le impostazioni dei criteri di replica in **Criteri** > **di replica** > ***nome*** > **criteri Modifica impostazioni**. Le modifiche applicate a un criterio si applicano anche alla replica e alle nuove macchine virtuali.
1. Abilitare la **coerenza multi-VM** se si desidera raccogliere macchine virtuali in un gruppo di replica. Specificare un nome per il gruppo e quindi selezionare **OK**.

    > [!NOTE]
    >    * Le macchine virtuali in un gruppo di replica vengono replicate insieme e dispongono di punti di ripristino coerenti con l'arresto anomalo e coerenti con le app quando eseguono il failover.
    >    * Raggruppare le macchine virtuali e i server fisici in modo da rispecchiare i carichi di lavoro. L'abilitazione della coerenza su più macchine virtuali può avere un impatto sulle prestazioni del carico di lavoro. Eseguire questa operazione solo se le macchine virtuali eseguono lo stesso carico di lavoro ed è necessaria la coerenza.

    ![Abilita finestra di replica](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Selezionare **Abilita replica**. È possibile tenere traccia dello stato di avanzamento del processo Abilita **protezione** in Processi di ripristino**Jobs** > **siti** **di Impostazioni** > . Dopo l'esecuzione del processo **Finalize Protection,** la macchina virtuale è pronta per il failover.

## <a name="view-and-manage-vm-properties"></a>Visualizzare e gestire le proprietà della macchina virtuale

Verificare quindi le proprietà della macchina virtuale di origine. Tenere presente che il nome della VM di Azure deve essere conforme ai [requisiti delle macchine virtuali di Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Passare a **Impostazioni** > **elementi replicati**e quindi selezionare la macchina virtuale. La pagina **Essentials** mostra informazioni sulle impostazioni e lo stato della macchina virtuale.
1. In **Proprietà** sono disponibili le informazioni su replica e failover per la VM.
1. Nelle proprietà **Calcolo e** > **Calcolo**di rete è possibile modificare più proprietà della macchina virtuale. 

    ![Finestra delle proprietà di calcolo e di rete](./media/vmware-azure-enable-replication/vmproperties.png)

    * Nome della macchina virtuale di Azure: modificare il nome per soddisfare i requisiti di Azure, se necessario.
    * Dimensioni della macchina virtuale di destinazione o tipo di macchina virtuale: la dimensione predefinita della macchina virtuale viene scelta in base a pochi parametri che includono il numero di dischi, il numero di NIC, il numero di core della CPU, la memoria e le dimensioni dei ruoli VM disponibili nell'area di Azure di destinazione. Azure Site Recovery sceglie la prima dimensione di macchina virtuale disponibile che soddisfa tutti i criteri. È possibile selezionare una dimensione di macchina virtuale diversa in base alle proprie esigenze in qualsiasi momento prima del failover. Si noti che le dimensioni del disco della macchina virtuale si basano anche sulle dimensioni del disco di origine e possono essere modificate solo dopo il failover. Altre informazioni sulle dimensioni dei dischi e sulle velocità di IOPS in Obiettivi di [scalabilità e prestazioni per i dischi di macchine virtuali in Windows](../virtual-machines/windows/disk-scalability-targets.md).

    *  Gruppo di risorse: è possibile selezionare un gruppo di [risorse](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines)da cui una macchina virtuale diventa parte di un post failover. È possibile modificare questa impostazione in qualsiasi momento prima del failover. Dopo il failover, se si esegue la migrazione della macchina virtuale in un gruppo di risorse diverso, le impostazioni di protezione per tale macchina virtuale si interrompono.
    * Set di disponibilità: è possibile selezionare un set di [disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) se la macchina virtuale deve far parte di un post failover. Quando si seleziona un set di disponibilità, tenere presenti le informazioni seguenti:When you select an availability set, keep the following information in mind:

        * Vengono elencati solo i set di disponibilità che appartengono al gruppo di risorse specificato.  
        * Le macchine virtuali che si trovano in reti virtuali diverse non possono far parte dello stesso set di disponibilità.
        * In un set di disponibilità possono essere incluse solo macchine virtuali delle stesse dimensioni.
1. È anche possibile aggiungere informazioni sulla rete di destinazione, la subnet e l'indirizzo IP assegnati alla macchina virtuale di Azure.You can also add information about the target network, subnet, and IP address that's assigned to the Azure VM.
2. In **Dischi**è possibile visualizzare il sistema operativo e i dischi dati nella macchina virtuale che verrà replicata.

### <a name="configure-networks-and-ip-addresses"></a>Configurare reti e indirizzi IP

È possibile impostare l'indirizzo IP di destinazione. Se non si specifica un indirizzo, la macchina virtuale di failover utilizza DHCP. Se si imposta un indirizzo che non è disponibile al momento del failover, il failover non riesce. Se l'indirizzo è disponibile nella rete di failover di test, è possibile usare lo stesso indirizzo IP di destinazione per il failover di test.

Il numero di schede di rete è determinato dalle dimensioni specificate per la macchina virtuale di destinazione, come indicato di seguito:The number of network adapters is dictated by the size that you specify for the target virtual machine, as follows:

- Se il numero di schede di rete nella macchina virtuale di origine è minore o uguale al numero di schede consentite per le dimensioni della macchina virtuale di destinazione, la destinazione ha lo stesso numero di schede dell'origine.
- Se il numero di schede per la macchina virtuale di origine supera il numero consentito per le dimensioni della macchina virtuale di destinazione, viene utilizzato il valore massimo delle dimensioni di destinazione. Ad esempio, se una macchina virtuale di origine dispone di due schede di rete e le dimensioni della macchina virtuale di destinazione ne supportano quattro, la macchina virtuale di destinazione dispone di due schede. Se la macchina virtuale di origine ha due adattatori ma la dimensione di destinazione ne supporta solo uno, la macchina virtuale di destinazione ha un solo adattatore.
- Se la macchina virtuale ha più schede di rete, si connetteranno tutte alla stessa rete. Inoltre, la prima scheda visualizzata nell'elenco diventa la scheda di rete predefinita nella macchina virtuale di Azure.Also, the first adapter that's shown in the list becomes the *default* network adapter in the Azure virtual machine. 

### <a name="azure-hybrid-benefit"></a>Vantaggio Azure Hybrid

Microsoft Software Assurance customers can use Azure Hybrid Benefit to save on licensing costs for Windows Server computers that are migrated to Azure. Il vantaggio si applica anche al ripristino di emergenza di Azure.The benefit also applies to Azure disaster recovery. Se si è idonei, è possibile assegnare il vantaggio alla macchina virtuale creata da Site Recovery in caso di failover. A tale scopo, seguire questi passaggi:
1. Passare alle **proprietà Computer e Rete** della macchina virtuale replicata.
2. Rispondere quando viene chiesto se si dispone di una licenza di Windows Server che rende idoneo per il vantaggio Azure ibrido.
3. Verificare di disporre di una licenza di Windows Server idonea con Software Assurance che è possibile utilizzare per applicare il vantaggio alla macchina virtuale che verrà creata al momento del failover.
4. Salvare le impostazioni per la macchina virtuale replicata.

Altre informazioni sul [Vantaggio Azure Hybrid](https://aka.ms/azure-hybrid-benefit-pricing).



## <a name="next-steps"></a>Passaggi successivi

Quando la macchina virtuale raggiunge uno stato protetto, provare un failover per verificare se l'applicazione viene visualizzata in Azure.After the virtual machine reaches a protected state, try a [failover](site-recovery-failover.md) to check whether your application appears in Azure.

* Per disabilitare la replica, vedere come [rimuovere la registrazione e le impostazioni di protezione](site-recovery-manage-registration-and-protection.md).
* Informazioni su come [automatizzare la replica per le macchine virtuali tramite Powershell](vmware-azure-disaster-recovery-powershell.md).
