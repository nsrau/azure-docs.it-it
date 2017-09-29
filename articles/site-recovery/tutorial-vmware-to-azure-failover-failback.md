---
title: Failover e failback di macchine virtuali VMware e server fisici replicati in Azure con Site Recovery | Microsoft Docs
description: Informazioni su come eseguire il failover di macchine virtuali VMware e server fisici in Azure e il failback nel sito locale con Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 45f1c61189b338d38681c8e93be01953da65913f
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

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

Questa è la quinta esercitazione di una serie. In questa esercitazione si presuppone che siano già state completate le attività delle esercitazioni precedenti.

1. [Preparare Azure](tutorial-prepare-azure.md)
2. [Preparare istanze di VMware locali](tutorial-prepare-on-premises-vmware.md)
3. [Configurare il ripristino di emergenza](tutorial-vmware-to-azure.md)
4. [Eseguire un'esercitazione sul ripristino di emergenza](tutorial-dr-drill-azure.md)

## <a name="preparing-for-failover-and-failback"></a>Preparazione per il failover e il failback

Verificare che nella macchina virtuale non ci siano snapshot. La macchina virtuale locale viene spenta durante la riprotezione,
in modo da garantire la coerenza dei dati durante la replica. Non accendere la macchina virtuale al termine della riprotezione. Usare lo stesso server di destinazione master per riproteggere un gruppo di replica. Se invece si usa un server di destinazione master diverso, il server non potrà fornire una temporizzazione comune.

L'operazione di failover e failback comprende quattro fasi:

1. **Failover in Azure**: eseguire il failover dei computer dal sito locale in Azure.
2. **Riprotezione delle macchine virtuali di Azure**: riproteggere le macchine virtuali di Azure in modo da avviarne la replica nelle macchine virtuali VMware locali.
3. **Failover nel sito locale**: eseguire un failover per il failback da Azure.
4. **Riprotezione delle macchine virtuali locali**: dopo il failback dei dati, riproteggere le macchine virtuali locali in cui è stato eseguito il failback in modo da avviarne la replica in Azure.

## <a name="verify-vm-properties"></a>Verificare le proprietà della macchina virtuale

Verificare le proprietà della macchina virtuale e assicurarsi che sia conforme ai [requisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. In **Elementi protetti** fare clic su **Elementi replicati** > macchina virtuale.

2. Nel riquadro **Elemento replicato** è possibile vedere un riepilogo relativo a informazioni sulla macchina virtuale, stato integrità e ultimi punti di ripristino disponibili. Fare clic su **Proprietà** per visualizzare altri dettagli.

3. In **Calcolo e rete** è possibile modificare il nome Azure, il gruppo di risorse, le dimensioni di destinazione, il [set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md) e le [impostazioni del disco gestito](#managed-disk-considerations)

4. È possibile visualizzare e modificare le impostazioni di rete, tra cui la rete/subnet in cui si troverà la macchina virtuale di Azure dopo il failover e l'indirizzo IP che le verrà assegnato.

5. In **Dischi** è possibile vedere le informazioni sul sistema operativo e sui dischi dati della VM.

## <a name="run-a-failover-to-azure"></a>Eseguire un failover in Azure

1. In **Impostazioni** > **Elementi replicati** fare clic sulla macchina virtuale > **Failover**.

2. In **Failover** selezionare un **Punto di ripristino** in cui eseguire il failover. È possibile usare una delle opzioni seguenti.
   - **Più recente** (predefinita): questa opzione elabora prima tutti i dati inviati a Site Recovery. Offre il valore RPO (Recovery Point Objective) più basso perché la macchina virtuale di Azure creata dopo il failover contiene tutti i dati che sono stati replicati in Site Recovery all'attivazione del failover.
   - **Elaborato più recente**: questa opzione consente di eseguire il failover della macchina virtuale nel punto di ripristino più recente elaborato da Site Recovery. Offre un RTO (Recovery Time Objective) basso poiché non viene impiegato tempo per elaborare dati non elaborati.
   - **Coerente con l'app più recente**: questa opzione esegue il failover della macchina virtuale nel punto di ripristino coerente con l'app più recente elaborato da Site Recovery.
   - **Personalizzato**: specificare un punto di ripristino.

3. Selezionare **Arrestare la macchina virtuale prima di iniziare il failover** per provare ad arrestare le macchine virtuali di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover.

4. Se la connessione alla macchina virtuale di Azure è stata preparata, connettersi per convalidarla dopo il failover.

5. Dopo la verifica, **eseguire il commit** del failover. In questo modo tutti i punti di ripristino disponibili verranno eliminati.

> [!WARNING]
> **Non annullare un failover in corso**: prima dell'avvio del failover, la replica della macchina virtuale viene arrestata.
> Se si annulla un failover in corso, il failover viene arrestato ma non viene eseguita di nuovo la replica della macchina virtuale.

In alcuni scenari il failover richiede un'ulteriore elaborazione il cui completamento richiede da 8 a 10 minuti. L'esecuzione del failover di test potrebbe richiedere più tempo per server fisici, computer Linux VMware, macchine virtuali VMware per cui non è abilitato il servizio DHCP e macchine virtuali VMware che non hanno i driver di avvio seguenti: storvsc, vmbus, storflt, intelide, atapi.

## <a name="create-a-process-server-in-azure"></a>Creare un server di elaborazione in Azure

Il server di elaborazione riceve i dati dalla macchina virtuale di Azure e li invia al sito locale. È necessaria una rete a bassa latenza tra il server di elaborazione e la macchina virtuale protetta.

- A scopo di test, se si ha una connessione Azure ExpressRoute è possibile usare il server di elaborazione locale che viene installato automaticamente nel server di configurazione.
- Se si ha una connessione VPN o si esegue il failback in un ambiente di produzione è necessario configurare una macchina virtuale di Azure come server di elaborazione basato su Azure per il failback.
- Per configurare un server di elaborazione in Azure, seguire le istruzioni fornite in [questo articolo](site-recovery-vmware-setup-azure-ps-resource-manager.md).

## <a name="configure-the-master-target-server"></a>Configurare il server di destinazione master

Per impostazione predefinita, il server di destinazione master viene eseguito nel server di configurazione locale. Per questa esercitazione verrà usato il master predefinito. Il server di destinazione master riceve i dati di failback.

Se la macchina virtuale si trova in un host ESXi gestito da un server vCenter è necessario che il server di destinazione master abbia accesso all'archivio dati della macchina virtuale (VMDK) per poter scrivere i dati replicati nei dischi della macchina virtuale. Verificare che l'archivio dati della macchina virtuale sia montato nell'host del server di destinazione master con accesso in lettura/scrittura.

Se la macchina virtuale si trova in un ESXi non gestito da un server vCenter, il servizio Site Recovery crea una nuova macchina virtuale durante la riprotezione. La macchina virtuale viene creata nell'host ESX in cui si crea il server di destinazione master.
Il disco rigido della macchina virtuale deve trovarsi in un archivio dati accessibile dall'host in cui viene eseguito il server di destinazione master.

Se la macchina virtuale non usa vCenter è necessario completare l'individuazione dell'host in cui viene eseguito il server di destinazione master prima di riproteggerla. Questa indicazione è valida anche per il failback dei server fisici. Un'altra opzione, in presenza della macchina virtuale locale, consiste nell'eliminarla prima di eseguire il failback. Il failback crea quindi una nuova macchina virtuale nello stesso host dell'host ESX di destinazione master. Quando si esegue il failback in una posizione alternativa, i dati vengono ripristinati nello stesso archivio dati e nello stesso host ESX usati dal server di destinazione master locale.

Non è possibile usare Storage vMotion nel server di destinazione master. Se lo si usa, il failback non funzionerà perché i dischi non risulteranno disponibili. Escludere i server di destinazione master dall'elenco vMotion.

## <a name="reprotect-azure-vms"></a>Riproteggere le macchine virtuali di Azure

Questa procedura presuppone che la macchina virtuale locale non sia disponibile e che la riprotezione venga eseguita in un percorso alternativo.

1. In **Impostazioni** > **Elementi replicati** fare clic con il pulsante destro del mouse sulla macchina virtuale di cui è stato eseguito il failover > **Riproteggi**.
2. In **Riproteggi** verificare che **Da Azure a locale** sia selezionato.
3. Specificare il server di destinazione master locale e il server di elaborazione.

4. In **Archivio dati** selezionare l'archivio dati del server di destinazione master in cui ripristinare i dischi in locale. Usare questa opzione quando la macchina virtuale locale è stata eliminata ed è necessario creare nuovi dischi. Questa impostazione viene ignorata se i dischi esistono già, ma è necessario specificare un valore.
5. Selezionare l'unità di conservazione del server di destinazione master. I criteri di failback vengono selezionati automaticamente.
6. Fare clic su **OK** per avviare la riprotezione. Avrà inizio un processo di replica della macchina virtuale da Azure al sito locale. È possibile monitorare l'avanzamento nella scheda **Processi** .

> [!NOTE]
> Se si desidera ripristinare la macchina virtuale di Azure in una macchina virtuale locale esistente montare l'archivio dati della macchina virtuale locale con accesso in lettura/scrittura sull'host ESXi del server di destinazione master.


## <a name="run-a-failover-from-azure-to-on-premises"></a>Eseguire un failover da Azure nel sito locale

Per eseguire la replica locale si usano criteri di failback. Questi criteri sono stati creati automaticamente al momento della creazione dei criteri di replica per la replica in Azure:

- I criteri vengono automaticamente associati al server di configurazione.
- I criteri non possono essere modificati.
- I valori dei criteri sono:
    - Soglia RPO: 15 minuti
    - Conservazione del punto di ripristino: 24 ore
    - Frequenza snapshot coerenti con l'app: 60 minuti

Eseguire il failover come descritto di seguito:

1. Nella pagina **Elementi replicati** fare clic con il pulsante destro del mouse sul computer > **Failover non pianificato**.
2. In **Conferma failover** verificare che la direzione di failover sia da Azure.

3. Selezionare il punto di ripristino da usare per il failover. Il punto di ripristino coerente con l'applicazione si verifica prima del punto più recente nel tempo e provoca perdita di dati. Quando viene eseguito il failover, Site Recovery arresta le macchine virtuali di Azure e avvia la macchina virtuale locale. L'operazione comporta un tempo di inattività. Scegliere pertanto un orario appropriato.
4. Fare clic con il pulsante destro del mouse sul computer e quindi su **Esegui commit**. Verrà attivato un processo che rimuove le macchine virtuali di Azure.
5. Verificare che le macchine virtuali di Azure siano state arrestate come previsto.


## <a name="reprotect-on-premises-machines-to-azure"></a>Riproteggere i computer locali in Azure

I dati dovrebbero ora essere tornati nel sito locale, ma non vengono replicati in Azure. È possibile avviare nuovamente la replica in Azure seguendo questa procedura:

1. Nell'insieme di credenziali > **Impostazioni** >**Elementi replicati** selezionare le macchine virtuali di cui è stato eseguito il failback e fare clic su **Riproteggi**.
2. Selezionare il server di elaborazione usato per inviare i dati replicati in Azure e fare clic su **OK**.

Al termine della riprotezione, la macchina virtuale viene replicata in Azure e sarà possibile eseguire un failover in base alle esigenze.

