---
title: Failover e failback di macchine virtuali VMware e server fisici replicati in Azure con Site Recovery | Microsoft Docs
description: Informazioni su come eseguire il failover di macchine virtuali VMware e server fisici in Azure e il failback nel sito locale con Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/11/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 7e586e7e3ec8c16dcd215dbc11251d1b9fe928e1
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457067"
---
# <a name="fail-over-and-fail-back-vmware-vms-and-physical-servers-replicated-to-azure"></a>Failover e failback di macchine virtuali VMware e server fisici replicati in Azure

Questa esercitazione descrive come eseguire il failover di una macchina virtuale VMware in Azure. Dopo il failover è possibile eseguire il failback nel sito locale quando disponibile. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Controllare le proprietà della macchina virtuale VMware per verificare la conformità ai requisiti di Azure
> * Eseguire un failover in Azure
> * Creare un server di elaborazione e un server di destinazione master per il failback
> * Riproteggere le macchine virtuali di Azure nel sito locale
> * Eseguire il failover da Azure nel sito locale
> * Riproteggere le macchine virtuali locali per avviare nuovamente la replica in Azure

>[!NOTE]
>Le esercitazioni sono progettate per illustrare il percorso di distribuzione più semplice per uno scenario. Quando possibile, vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per informazioni dettagliate sui passaggi del failover di test, leggere la [guida alle procedure](site-recovery-failover.md).

Questa è la quinta esercitazione di una serie. In questa esercitazione si presuppone che siano già state completate le attività delle esercitazioni precedenti.

1. [Preparare Azure](tutorial-prepare-azure.md)
2. [Preparare istanze di VMware locali](vmware-azure-tutorial-prepare-on-premises.md)
3. [Configurare il ripristino di emergenza](vmware-azure-tutorial.md)
4. [Eseguire un'esercitazione sul ripristino di emergenza](tutorial-dr-drill-azure.md)
5. Oltre ai passaggi precedenti, è utile [esaminare l'architettura](vmware-azure-architecture.md) per lo scenario di ripristino di emergenza.

## <a name="failover-and-failback"></a>Failover e failback

Per il failover e il failback sono previste quattro fasi:

1. **Failover in Azure**: eseguire il failover dei computer dal sito locale in Azure.
2. **Riprotezione delle macchine virtuali di Azure**: riproteggere le macchine virtuali di Azure in modo da avviarne la replica nelle macchine virtuali VMware locali. La macchina virtuale locale viene spenta durante la riprotezione, in modo da garantire la coerenza dei dati durante la replica.
3. **Failover nel sito locale**: eseguire un failover per il failback da Azure.
4. **Riprotezione delle macchine virtuali locali**: dopo il failback dei dati, riproteggere le macchine virtuali locali in cui è stato eseguito il failback in modo da avviarne la replica in Azure.

## <a name="verify-vm-properties"></a>Verificare le proprietà della macchina virtuale

Verificare le proprietà della macchina virtuale e assicurarsi che sia conforme ai [requisiti di Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. In **Elementi protetti** fare clic su **Elementi replicati** > macchina virtuale.

2. Nel riquadro **Elemento replicato** è possibile vedere un riepilogo relativo a informazioni sulla macchina virtuale, stato integrità e ultimi punti di ripristino disponibili. Fare clic su **Proprietà** per visualizzare altri dettagli.

3. In **Calcolo e rete** è possibile modificare il nome Azure, il gruppo di risorse, le dimensioni di destinazione, il [set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md) e le [impostazioni del disco gestito](#managed-disk-considerations)

4. È possibile visualizzare e modificare le impostazioni di rete, tra cui la rete/subnet in cui si troverà la macchina virtuale di Azure dopo il failover e l'indirizzo IP che le verrà assegnato.

5. In **Dischi** è possibile vedere le informazioni sul sistema operativo e sui dischi dati della VM.

## <a name="run-a-failover-to-azure"></a>Eseguire un failover in Azure

1. In **Impostazioni** > **Elementi replicati**, fare clic sulla macchina virtuale > **Failover**.

2. In **Failover** selezionare un **Punto di ripristino** in cui eseguire il failover. È possibile usare una delle opzioni seguenti.
   - **Più recente**: questa opzione elabora prima tutti i dati inviati a Site Recovery. Offre il valore RPO (Recovery Point Objective) più basso perché la macchina virtuale di Azure creata dopo il failover contiene tutti i dati che sono stati replicati in Site Recovery all'attivazione del failover.
   - **Elaborato più recente**: questa opzione consente di eseguire il failover della macchina virtuale nel punto di ripristino più recente elaborato da Site Recovery. Offre un RTO (Recovery Time Objective) basso poiché non viene impiegato tempo per elaborare dati non elaborati.
   - **Coerente con l'app più recente**: questa opzione esegue il failover della macchina virtuale nel punto di ripristino coerente con l'app più recente elaborato da Site Recovery.
   - **Personalizzato**: specificare un punto di ripristino.

3. Selezionare **Arrestare la macchina virtuale prima di iniziare il failover** per provare ad arrestare le macchine virtuali di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover.

In alcuni scenari il failover richiede un'ulteriore elaborazione il cui completamento richiede da 8 a 10 minuti. Si potrebbero notare **tempi più lunghi per il failover di test** per le macchine virtuali VMware che usano il servizio Mobility di una versione precedente alla 9.8, i server fisici, le macchine virtuali VMware Linux, le macchine virtuali Hyper-V protette come server fisici, le macchine virtuali VMware che non hanno il servizio DHCP abilitato e le macchine virtuali VMware che non hanno i driver di avvio seguenti: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> **Non annullare un failover in corso**: prima dell'avvio del failover, la replica della macchina virtuale viene arrestata.
> Se si annulla un failover in corso, il failover viene arrestato ma non viene eseguita di nuovo la replica della macchina virtuale.

## <a name="connect-to-failed-over-virtual-machine-in-azure"></a>Stabilire la connessione alla macchina virtuale in cui è stato eseguito il failover

1. Per connettersi alle macchine virtuali di Azure con RDP/SSH dopo il failover, seguire i requisiti riepilogati nella tabella [qui](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. Dopo il failover, passare alla macchina virtuale e convalidarla [connettendosi](../virtual-machines/windows/connect-logon.md) a essa.
3. Dopo la convalida, fare clic su **Esegui commit** per finalizzare il punto di recupero della macchina virtuale dopo il failover. Dopo il commit, tutti gli altri punti di recupero disponibili vengono eliminati. In questo modo, viene completata l'attività di failover.

>[!TIP]
> L'opzione **Modifica punto di ripristino** aiuta nella scelta di un punto di recupero diverso dopo il failover se non si è soddisfatti della macchina virtuale scelta per il failover. Dopo il **commit**, questa opzione non sarà disponibile.

Seguire i passaggi descritti [qui](site-recovery-failover-to-azure-troubleshoot.md) per risolvere i problemi di connettività dopo il failover.

## <a name="preparing-for-reprotection-of-azure-vm"></a>Preparazione per la riprotezione della macchina virtuale di Azure

- È possibile usare il server di elaborazione locale (server di elaborazione predefinito) installato automaticamente nel server di configurazione durante la configurazione **se si ha una connessione Azure ExpressRoute**.

> [!IMPORTANT]
> Se si dispone di una connessione VPN tra l'ambiente locale e Azure, è necessario configurare una VM di Azure come server di elaborazione per la riprotezione e il failback. Per configurare un server di elaborazione in Azure, seguire le istruzioni fornite in [questo articolo](vmware-azure-set-up-process-server-azure.md).

Per ulteriori informazioni sui prerequisiti per la riprotezione e il failback fare riferimento a questa [sezione](vmware-azure-reprotect.md##before-you-begin). 

### <a name="configure-the-master-target-server"></a>Configurare il server di destinazione master

Il server di destinazione master riceve e gestisce i dati di replica durante il failback da Azure. Per impostazione predefinita, è disponibile nel server di configurazione locale. In questa esercitazione si usa il server di destinazione master predefinito.

>[!NOTE]
>La protezione di una macchina virtuale basata su Linux richiede la creazione di un server di destinazione master separato. [Fare clic qui](vmware-azure-install-linux-master-target.md) per altre informazioni.

Se la macchina virtuale si trova in un **host ESXi gestito da un server vCenter**, è necessario che il server di destinazione master abbia accesso all'archivio dati della macchina virtuale (VMDK) per scrivere i dati replicati nei dischi della macchina virtuale. Verificare che l'archivio dati della macchina virtuale sia montato nell'host del server di destinazione master con accesso in lettura/scrittura.

Se la macchina virtuale si trova in un **host ESXi non gestito da un server vCenter**, il servizio Site Recovery crea una nuova macchina virtuale durante la riprotezione. La macchina virtuale viene creata nell'host ESX in cui si crea il server di destinazione master.
Il disco rigido della macchina virtuale deve trovarsi in un archivio dati accessibile dall'host in cui è in esecuzione il server di destinazione master.

Se la macchina virtuale **non usa vCenter**, è necessario completare l'individuazione dell'host in cui è in esecuzione il server di destinazione master prima di riproteggere la macchina. Questa indicazione è valida anche per il failback dei server fisici. Un'altra opzione, in presenza della macchina virtuale locale, consiste nell'eliminarla prima di eseguire il failback. Il failback crea quindi una nuova macchina virtuale nello stesso host dell'host ESX di destinazione master. Quando si esegue il failback in una posizione alternativa, i dati vengono ripristinati nello stesso archivio dati e nello stesso host ESX usati dal server di destinazione master locale.

Non è possibile usare Storage vMotion nel server di destinazione master. Se lo si usa, il failback non funzionerà perché i dischi non risulteranno disponibili. Escludere i server di destinazione master dall'elenco vMotion.

>[!Warning]
>Se invece si usa un server di destinazione master diverso, il server non potrà fornire una temporizzazione comune.

## <a name="reprotect-azure-vms"></a>Riproteggere le macchine virtuali di Azure

La riprotezione della macchina virtuale di Azure comporta la replica dei dati nella macchina virtuale locale. Questo passaggio è obbligatorio prima di eseguire il failover da Azure alla macchina virtuale locale. Attenersi alle istruzioni seguenti per eseguire la riprotezione.

1. In **Impostazioni** > **Elementi replicati**, fare clic con il pulsante destro del mouse sulla macchina virtuale in cui è stato eseguito il failover > **Riproteggi**.
2. In **Riproteggi** verificare che **Da Azure a locale** sia selezionato.
3. Specificare il server di destinazione master locale e il server di elaborazione.
4. In **Archivio dati** selezionare l'archivio dati del server di destinazione master in cui ripristinare i dischi in locale. Se la macchina virtuale è stata eliminata, vengono creati nuovi dischi in questo archivio dati. Questa impostazione viene ignorata se i dischi esistono già, ma è necessario specificare un valore.
5. Selezionare l'unità di conservazione del server di destinazione master. I criteri di failback vengono selezionati automaticamente.
6. Fare clic su **OK** per avviare la riprotezione. Avrà inizio un processo di replica della macchina virtuale da Azure al sito locale. È possibile monitorare l'avanzamento nella scheda **Processi**.
7. Quando lo stato della macchina virtuale in **Elementi replicati** cambia in **Protetto**, la macchina è pronta per il failover in locale.

> [!NOTE]
> È possibile ripristinare la macchina virtuale di Azure in una macchina virtuale locale esistente o in un percorso alternativo. Per altre informazioni, vedere [questo articolo](concepts-types-of-failback.md).

## <a name="run-a-failover-from-azure-to-on-premises"></a>Eseguire un failover da Azure nel sito locale

Per eseguire la replica locale si usano criteri di failback. Questi criteri sono stati creati automaticamente al momento della creazione dei criteri di replica per la replica in Azure:

- I criteri vengono automaticamente associati al server di configurazione.
- I criteri non possono essere modificati.
- I valori dei criteri sono:
    - Soglia RPO: 15 minuti
    - Conservazione del punto di ripristino: 24 ore
    - Frequenza snapshot coerenti con l'app: 60 minuti

Eseguire il failover come descritto di seguito:

1. Nella pagina **Elementi replicati** fare clic con il pulsante destro del mouse sulla macchina > **Failover**.
2. In **Conferma failover** verificare che la direzione di failover sia da Azure.
    ![Direzione failover](media/vmware-azure-tutorial-failover-failback/failover-direction.PNG)
3. Selezionare il punto di ripristino da usare per il failover. Il punto di ripristino coerente con l'applicazione si verifica prima del punto più recente nel tempo e provoca perdita di dati.

    >[!WARNING]
    >Quando viene eseguito il failover, Site Recovery arresta le macchine virtuali di Azure e avvia la macchina virtuale locale. L'operazione comporta un tempo di inattività. Scegliere pertanto un orario appropriato.

4. È possibile monitorare lo stato di avanzamento del processo in **Insieme di credenziali di Servizi di ripristino** > **Monitoraggio e report** > **Processi di Site Recovery**.
5. Dopo il completamento del failover, fare clic con il pulsante destro del mouse sulla macchina virtuale e scegliere **Esegui commit**. Verrà attivato un processo che rimuove le macchine virtuali di Azure.
6. Verificare che le macchine virtuali di Azure siano state arrestate come previsto.

## <a name="reprotect-on-premises-machines-to-azure"></a>Riproteggere i computer locali in Azure

I dati dovrebbero ora essere tornati nel sito locale, ma non vengono replicati in Azure. È possibile avviare nuovamente la replica in Azure seguendo questa procedura:

1. Nell'insieme di credenziali delle chiavi > **Elementi protetti** >**Elementi replicati**, selezionare la macchina virtuale di cui è stato eseguito il failback e fare clic su **Riproteggi**.
2. Selezionare il server di elaborazione da usare per inviare i dati replicati in Azure e fare clic su **OK**.

Al termine della riprotezione, la macchina virtuale viene replicata in Azure e sarà possibile eseguire un failover in base alle esigenze.
