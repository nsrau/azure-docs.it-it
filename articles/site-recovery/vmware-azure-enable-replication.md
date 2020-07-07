---
title: Abilitare le macchine virtuali VMware per il ripristino di emergenza usando Azure Site Recovery
description: Questo articolo descrive come abilitare la replica di macchine virtuali VMware per il ripristino di emergenza usando il servizio Azure Site Recovery
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 04/01/2020
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 6547bcf2061213cd01550367171d432900693ea5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80584137"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Abilitare la replica per le macchine virtuali VMware in Azure

Questo articolo descrive come abilitare la replica di macchine virtuali VMware locali (VM) in Azure.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che il sistema soddisfi i criteri seguenti:

- [Configurare l'ambiente di origine locale](vmware-azure-set-up-source.md).
- [Configurare l'ambiente di destinazione in Azure](vmware-azure-set-up-target.md).
- [Verificare i requisiti e i prerequisiti](vmware-physical-azure-support-matrix.md) prima di iniziare. Gli aspetti importanti da tenere presenti sono:
  - [Sistemi operativi supportati](vmware-physical-azure-support-matrix.md#replicated-machines) per i computer replicati.
  - Supporto di [archiviazione/dischi](vmware-physical-azure-support-matrix.md#storage) .
  - [Requisiti di Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements) con cui i computer locali devono essere conformi.

### <a name="resolve-common-issues"></a>Risolvere i problemi comuni

- Ogni disco deve essere inferiore a 4 TB.
- Il disco del sistema operativo deve essere un disco di base e non un disco dinamico.
- Per le macchine virtuali abilitate per UEFI di seconda generazione, la famiglia di sistemi operativi deve essere Windows e il disco di avvio deve essere inferiore a 300 GB.

## <a name="before-you-start"></a>Prima di iniziare

Quando si esegue la replica di macchine virtuali VMware, tenere presenti le seguenti informazioni:

- L'account utente di Azure deve disporre di determinate [autorizzazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) per abilitare la replica di una nuova macchina virtuale in Azure.
- Le VM VMware vengono rilevate ogni 15 minuti. Potrebbero essere necessari 15 minuti o più per visualizzare le macchine virtuali nel portale di Azure dopo l'individuazione. Quando si aggiunge un nuovo server vCenter o un host vSphere, l'individuazione può richiedere più di 15 minuti.
- Potrebbero essere necessari 15 minuti o più per aggiornare le modifiche dell'ambiente nella macchina virtuale nel portale. Ad esempio, l'installazione degli strumenti VMware.
- È possibile controllare l'ora dell'ultima individuazione per le macchine virtuali VMware: vedere il campo **ultimo contatto** nella pagina **server di configurazione** per il server vCenter o l'host vSphere.
- Per aggiungere macchine virtuali per la replica senza attendere l'individuazione pianificata, evidenziare il server di configurazione (ma non fare clic su di esso) e selezionare **Aggiorna**.
- Quando si Abilita la replica, se la macchina virtuale è preparata, il server di elaborazione installa automaticamente il servizio Mobility Azure Site Recovery nella macchina virtuale.

## <a name="enable-replication"></a>Abilitare la replica

Prima di eseguire i passaggi descritti in questa sezione, esaminare le seguenti informazioni:

- Azure Site Recovery ora viene replicata direttamente in Managed disks per tutte le nuove repliche. Il server di elaborazione scrive i log di replica in un account di archiviazione della cache nell'area di destinazione. Questi log vengono usati per creare punti di ripristino nei dischi gestiti di replica con convenzione di denominazione `asrseeddisk` .
- Il supporto di PowerShell per la replica a Managed disks è disponibile a partire dalla [versione 2.0.0 del modulo AZ. RecoveryServices](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview)
- Al momento del failover, il punto di ripristino selezionato viene usato per creare il disco gestito da destinazione.
- Le macchine virtuali configurate in precedenza per la replica negli account di archiviazione di destinazione non sono interessate.
- La replica negli account di archiviazione per una nuova macchina virtuale è disponibile solo tramite un'API REST (Representational State Transfer) e PowerShell. Usare l'API REST di Azure versione 2016-08-10 o 2018-01-10 per la replica negli account di archiviazione.

Per abilitare la replica, attenersi alla seguente procedura:

1. Andare al **passaggio 2: replicare l'origine dell'applicazione**  >  **Source**. Dopo aver abilitato la replica per la prima volta, selezionare **+ replica** nell'insieme di credenziali per abilitare la replica per altre macchine virtuali.
1. Nella pagina **Origine** > **Origine** selezionare il server di configurazione.
1. Per **tipo di computer**selezionare **macchine virtuali** o computer **fisici**.
1. In **vCenter/vSphere Hypervisor** selezionare il server vCenter che gestisce l'host di vSphere oppure selezionare l'host. Questa impostazione non è pertinente se si sta eseguendo la replica di computer fisici.
1. Selezionare il server di elaborazione. Se non sono stati creati server di elaborazione aggiuntivi, il server di elaborazione incorporato del server di configurazione sarà disponibile nel menu a discesa. Lo stato di integrità di ogni server di elaborazione è indicato in base ai limiti consigliati e ad altri parametri. Scegliere un server di elaborazione integro. Non è possibile scegliere un server di elaborazione [critico](vmware-physical-azure-monitor-process-server.md#process-server-alerts) . È possibile [individuare e risolvere](vmware-physical-azure-troubleshoot-process-server.md) gli errori **oppure** configurare un [server di elaborazione scale-out](vmware-azure-set-up-process-server-scale.md).

   :::image type="content" source="./media/vmware-azure-enable-replication/ps-selection.png" alt-text="Abilita finestra di origine della replica":::

   > [!NOTE]
   > A partire dalla [versione 9,24](site-recovery-whats-new.md), vengono introdotti avvisi aggiuntivi per migliorare gli avvisi di integrità del server di elaborazione. Aggiornare i componenti Site Recovery alla versione 9,24 o successiva per tutti gli avvisi da generare.

1. Per **destinazione**selezionare la sottoscrizione e il gruppo di risorse in cui si desidera creare le macchine virtuali di cui è stato eseguito il failover. Scegliere il modello di distribuzione che si vuole usare in Azure per le macchine virtuali di cui è stato eseguito il failover.
1. Selezionare la rete di Azure e la subnet a cui si connetteranno le VM di Azure dopo il failover. La rete deve trovarsi nella stessa area dell'insieme di credenziali del servizio Site Recovery.

   Selezionare **Configura ora per le macchine virtuali selezionate** per applicare le impostazioni di rete a tutte le macchine virtuali selezionate per la protezione. Selezionare **Configura in seguito** per selezionare la rete di Azure per ogni macchina virtuale. Se non è disponibile una rete, sarà necessario crearla. Per creare una rete usando Azure Resource Manager, selezionare **Crea nuovo**. Selezionare una subnet, se applicabile, quindi fare clic su **OK**.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-rep3.png" alt-text="Abilita finestra destinazione replica":::

1. Per le **macchine virtuali**  >  **selezionare macchine virtuali**, selezionare ogni macchina virtuale da replicare. È possibile selezionare solo le macchine virtuali per cui è possibile abilitare la replica. Selezionare **OK**. Se non è possibile visualizzare o selezionare una macchina virtuale specifica, vedere il [computer di origine non è elencato nella portale di Azure](vmware-azure-troubleshoot-replication.md#step-3-troubleshoot-source-machines-that-arent-available-for-replication) per risolvere il problema.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication5.png" alt-text="Abilitare la replica selezionare le macchine virtuali finestra":::

1. Per **Proprietà**  >  **Configura proprietà**selezionare l'account usato dal server di elaborazione per installare automaticamente il servizio Mobility Site Recovery nella macchina virtuale. Scegliere anche il tipo di disco gestito di destinazione da usare per la replica in base ai modelli di varianza dei dati.
1. Per impostazione predefinita, vengono replicati tutti i dischi di una macchina virtuale di origine. Per escludere i dischi dalla replica, deselezionare la casella di controllo **Includi** per eventuali dischi che non si vuole replicare. Selezionare **OK**. È possibile impostare proprietà aggiuntive in un secondo momento. [Altre informazioni](vmware-azure-exclude-disk.md) sull'esclusione di dischi.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication6.png" alt-text="Abilita la finestra di dialogo Configura proprietà della replica":::

1. In **impostazioni di replica**  >  **configurare le impostazioni di replica**, verificare che siano selezionati i criteri di replica corretti. È possibile modificare le impostazioni dei criteri di replica in **Impostazioni**criteri di  >  **replica**criteri  >  _nome_  >  **Modifica impostazioni**. Le modifiche applicate a un criterio sono valide anche per la replica e le nuove macchine virtuali.
1. Se si desidera raccogliere le macchine virtuali in un gruppo di replica, abilitare la coerenza tra più **VM**. Specificare un nome per il gruppo e quindi fare clic su **OK**.

   > [!NOTE]
   > - Le macchine virtuali in un gruppo di replica vengono replicate insieme e hanno condiviso punti di ripristino coerenti con l'arresto anomalo del sistema e coerenti con l'app quando eseguono il failover.
   > - Raggruppare le macchine virtuali e i server fisici in modo da rispecchiare i carichi di lavoro. L'abilitazione della coerenza su più macchine virtuali può avere un impatto sulle prestazioni del carico di lavoro. Eseguire questa operazione solo se le macchine virtuali eseguono lo stesso carico di lavoro ed è necessaria la coerenza.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication7.png" alt-text="Abilita finestra di replica":::

1. Selezionare **Abilita replica**. È possibile tenere traccia dello stato di avanzamento del processo di **Abilitazione della protezione** in **Impostazioni**  >  **processi**  >  **Site Recovery processi**. Dopo l'esecuzione del processo **finalizza protezione** , la macchina virtuale è pronta per il failover.

## <a name="view-and-manage-vm-properties"></a>Visualizzare e gestire le proprietà della macchina virtuale

Verificare quindi le proprietà della macchina virtuale di origine. Tenere presente che il nome della VM di Azure deve essere conforme ai [requisiti delle macchine virtuali di Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Passare a **Impostazioni**  >  **elementi replicati**e quindi selezionare la macchina virtuale. La **pagina informazioni** di base Mostra informazioni sulle impostazioni e lo stato della macchina virtuale.
1. In **Proprietà** sono disponibili le informazioni su replica e failover per la VM.
1. In **calcolo e rete**  >  **proprietà di calcolo**è possibile modificare più proprietà della macchina virtuale.

   :::image type="content" source="./media/vmware-azure-enable-replication/vmproperties.png" alt-text="Finestra delle proprietà calcolo e rete":::

   - **Nome della macchina virtuale di Azure**: modificare il nome in modo da soddisfare i requisiti di Azure, se necessario.
   - **Dimensioni macchina virtuale di destinazione o tipo di macchina virtuale**: le dimensioni predefinite della macchina virtuale vengono scelte in base ai parametri che includono il numero di dischi, il numero di nic, il numero di core CPU, la memoria e le dimensioni del ruolo VM disponibili nell'area di Azure di destinazione Azure Site Recovery seleziona la prima dimensione di macchina virtuale disponibile che soddisfa tutti i criteri. È possibile selezionare una diversa dimensione della macchina virtuale in base alle esigenze in qualsiasi momento prima del failover. Anche le dimensioni del disco della macchina virtuale sono basate sulle dimensioni del disco di origine e possono essere modificate solo dopo il failover. Per altre informazioni sulle dimensioni dei dischi e sulle tariffe di IOPS, vedere [obiettivi di scalabilità e prestazioni per i dischi delle VM in Windows](/azure/virtual-machines/windows/disk-scalability-targets).
   - **Gruppo di risorse**: è possibile selezionare un [gruppo di risorse](/azure/azure-resource-manager/management/overview#resource-groups)da cui una macchina virtuale diventa parte di un post-failover. È possibile modificare questa impostazione in qualsiasi momento prima del failover. Dopo il failover, se si esegue la migrazione della macchina virtuale in un gruppo di risorse diverso, le impostazioni di protezione per tale macchina virtuale vengono interrotta.
   - **Set di disponibilità**: è possibile selezionare un [set di disponibilità](/azure/virtual-machines/windows/tutorial-availability-sets) se la macchina virtuale deve far parte di un post-failover. Quando si seleziona un set di disponibilità, tenere presenti le seguenti informazioni:
     - Sono elencati solo i set di disponibilità che appartengono al gruppo di risorse specificato.
     - Le macchine virtuali che si trovano in reti virtuali diverse non possono far parte dello stesso set di disponibilità.
     - In un set di disponibilità possono essere incluse solo macchine virtuali delle stesse dimensioni.

1. È anche possibile aggiungere informazioni sulla rete di destinazione, la subnet e l'indirizzo IP assegnati alla macchina virtuale di Azure.
1. In **dischi**è possibile visualizzare il sistema operativo e i dischi dati nella macchina virtuale che verrà replicata.

### <a name="configure-networks-and-ip-addresses"></a>Configurare reti e indirizzi IP

È possibile impostare l'indirizzo IP di destinazione:

- Se non si specifica un indirizzo, la macchina virtuale di cui è stato eseguito il failover usa DHCP.
- Se si imposta un indirizzo che non è disponibile al momento del failover, il failover non riesce.
- Se l'indirizzo è disponibile nella rete di failover di test, è possibile usare lo stesso indirizzo IP di destinazione per il failover di test.

Il numero di schede di rete dipende dalle dimensioni specificate per la macchina virtuale di destinazione, come indicato di seguito:

- Se il numero di schede di rete nella macchina virtuale di origine è minore o uguale al numero di schede consentite per le dimensioni della VM di destinazione, la destinazione avrà lo stesso numero di schede dell'origine.
- Se il numero di schede per la macchina virtuale di origine supera il numero consentito per le dimensioni della VM di destinazione, viene usata la dimensione massima di destinazione. Se, ad esempio, una macchina virtuale di origine dispone di due schede di rete e le dimensioni della macchina virtuale di destinazione ne supportano quattro, la macchina virtuale di destinazione avrà due schede. Se la macchina virtuale di origine dispone di due schede ma le dimensioni di destinazione ne supportano solo una, la macchina virtuale di destinazione ha solo una scheda.
- Se la macchina virtuale ha più schede di rete, si connetteranno tutte alla stessa rete. Inoltre, la prima scheda visualizzata nell'elenco diventa la scheda di rete predefinita nella macchina virtuale di Azure.

### <a name="azure-hybrid-benefit"></a>Vantaggio Azure Hybrid

I clienti di Microsoft Software Assurance possono usare Vantaggio Azure Hybrid per risparmiare sui costi di licenza per i computer Windows Server di cui viene eseguita la migrazione in Azure. Il vantaggio si applica anche al ripristino di emergenza di Azure. Se si è idonei, è possibile assegnare il vantaggio alla macchina virtuale che Site Recovery crea se si verifica un failover.

1. Passare alle **Proprietà computer e rete** della macchina virtuale replicata.
1. Risposta quando viene richiesto se si dispone di una licenza di Windows Server che rende idonea per Vantaggio Azure Hybrid.
1. Verificare di disporre di una licenza di Windows Server idonea con Software Assurance che è possibile usare per applicare il vantaggio alla macchina virtuale che verrà creata in fase di failover.
1. Salvare le impostazioni per la macchina virtuale replicata.

[Altre](https://azure.microsoft.com/pricing/hybrid-benefit/) informazioni su vantaggio Azure Hybrid.

## <a name="next-steps"></a>Passaggi successivi

Quando la macchina virtuale raggiunge uno stato protetto, provare a eseguire un [failover](site-recovery-failover.md) per controllare se l'applicazione viene visualizzata in Azure.

- [Altre](site-recovery-manage-registration-and-protection.md) informazioni su come pulire le impostazioni di registrazione e protezione per disabilitare la replica.
- [Altre](vmware-azure-disaster-recovery-powershell.md) informazioni su come automatizzare la replica per le macchine virtuali tramite PowerShell.
