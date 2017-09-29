---
title: Failover e failback di macchine virtuali Hyper-V replicate in Azure con Site Recovery | Microsoft Docs
description: Informazioni su come eseguire il failover di macchine virtuali Hyper-V in Azure e il failback nel sito locale con Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72065d01-ed0f-430e-b117-a5885cecd1db
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/15/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e1cc21661450a983c25b24fe2a6228e26ceecec6
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Failover e failback di macchine virtuali Hyper-V replicate in Azure

Il servizio [Azure Site Recovery](site-recovery-overview.md) gestisce e controlla la replica, il failover e il failback dei computer locali e delle macchine virtuali di Azure.

Questa esercitazione descrive come eseguire il failover di una macchina virtuale Hyper-V in Azure. Dopo il failover è possibile eseguire il failback nel sito locale quando disponibile. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Eseguire il failover di macchine virtuali Hyper-V dal sito locale in Azure
> * Eseguire il failback da Azure nel sito locale e avviare di nuovo la replica in Azure

## <a name="overview"></a>Panoramica
L'operazione di failover e failback comprende due fasi:

1. **Failover in Azure**: eseguire il failover dei computer dal sito locale in Azure.
2. **Failback da Azure nel sito locale**: eseguire un failover pianificato da Azure nel sito locale. Dopo il failover pianificato è possibile abilitare la replica inversa per avviare di nuovo la replica dal sito locale in Azure. 


## <a name="fail-over-to-azure"></a>Failover in Azure

### <a name="failover-prerequisites"></a>Prerequisiti di failover

Per completare il failover:

- Assicurarsi di aver completato una [esercitazione sul ripristino di emergenza](tutorial-dr-drill-azure.md) per verificare che tutto funzioni come previsto.
- Preparare facoltativamente la connessione alle macchine virtuali di Azure prima del failover.
- Verificare le proprietà della macchina virtuale prima di eseguire il failover.

#### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparare la connessione alle macchine virtuali di Azure dopo il failover

Per connettersi alle macchine virtuali di Azure con RDP dopo il failover, seguire questa procedura:

##### <a name="azure-vms-running-windows"></a>Macchine virtuali di Azure che eseguono Windows

1. Per accedere alle macchine virtuali di Azure tramite Internet, abilitare RDP nella macchina virtuale locale prima del failover. Assicurarsi che le regole TCP e UDP siano aggiunte per il profilo **Pubblico** e che RDP sia consentito in **Windows Firewall** > **App consentite** per tutti i profili.
2. Per accedere tramite VPN da sito a sito, abilitare RDP nel computer locale. RDP deve essere consentito in **Windows Firewall** -> **App e funzionalità consentite** per le reti di **dominio e private**. Verificare che il criterio SAN del sistema operativo sia impostato su **OnlineAll**. [Altre informazioni](https://support.microsoft.com/kb/3031135). Quando si attiva un failover, nella macchina virtuale non devono essere presenti aggiornamenti di Windows in sospeso. Se sono presenti aggiornamenti in sospeso, non sarà possibile accedere alla macchina virtuale fino al completamento dell'aggiornamento. 
3. Dopo il failover nella macchina virtuale Windows di Azure selezionare **Diagnostica di avvio** per visualizzare uno screenshot della macchina virtuale. Se non è possibile connettersi, controllare che la macchina virtuale sia in esecuzione e rivedere i [suggerimenti per la risoluzione dei problemi](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


##### <a name="azure-vms-running-linux"></a>Macchine virtuali di Azure che eseguono Linux

1. Nel computer locale prima del failover verificare che il servizio Secure Shell sia impostato per essere avviato automaticamente all'avvio del sistema. Controllare che le regole del firewall consentano una connessione SSH.
2. Nella macchina virtuale di Azure dopo il failover autorizzare le connessioni in entrata nella porta SSH per le regole del gruppo di sicurezza di rete nella macchina virtuale in cui è stato eseguito il failover e per la subnet di Azure a cui è connessa.  [Aggiungere un indirizzo IP pubblico](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine) per la macchina virtuale. È possibile selezionare **Diagnostica di avvio** per visualizzare uno screenshot della macchina virtuale.


#### <a name="verify-vm-properties"></a>Verificare le proprietà della macchina virtuale

Verificare le proprietà della macchina virtuale e assicurarsi che sia conforme ai [requisiti di Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. In **Elementi protetti** fare clic su **Elementi replicati** > macchina virtuale.
2. Nel riquadro **Elemento replicato** è possibile vedere un riepilogo relativo a informazioni sulla macchina virtuale, stato integrità e ultimi punti di ripristino disponibili. Fare clic su **Proprietà** per visualizzare altri dettagli.
3. In **Calcolo e rete** è possibile modificare il nome Azure, il gruppo di risorse, le dimensioni di destinazione, il [set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md) e le [impostazioni del disco gestito](#managed-disk-considerations)
4. È possibile visualizzare e modificare le impostazioni di rete, tra cui la rete/subnet in cui si troverà la macchina virtuale di Azure dopo il failover e l'indirizzo IP che le verrà assegnato.
5. In **Dischi** è possibile vedere le informazioni sul sistema operativo e sui dischi dati della VM.


### <a name="run-a-failover-from-on-premises-to-azure"></a>Eseguire un failover dal sito locale in Azure

Per le macchine virtuali Hyper-V è possibile eseguire un failover regolare o pianificato

- Usare un failover regolare per interruzioni impreviste. Quando si esegue questo tipo di failover, Site Recovery crea una macchina virtuale di Azure e la accende. Il failover viene eseguito su un punto di ripristino specifico. Può verificarsi una perdita di dati, a seconda del punto di ripristino usato.
- È possibile usare un failover pianificato per le attività di manutenzione o durante un'interruzione prevista. Questa opzione offre una perdita di dati pari a zero. Quando viene attivato un failover pianificato, le macchine virtuali di origine vengono arrestate. I dati non sincronizzati vengono sincronizzati e il failover viene attivato.

Questa procedura descrive come eseguire un failover regolare.


1. In **Impostazioni** > **Elementi replicati** fare clic sulla macchina virtuale > **Failover**.
2. In **Failover** selezionare un **Punto di ripristino** in cui eseguire il failover. È possibile usare una delle opzioni seguenti.
    - **Più recente** (predefinita): questa opzione elabora prima tutti i dati inviati a Site Recovery. Offre il valore RPO (Recovery Point Objective) più basso perché la macchina virtuale di Azure creata dopo il failover contiene tutti i dati che sono stati replicati in Site Recovery all'attivazione del failover.
    - **Elaborato più recente**: questa opzione consente di eseguire il failover della macchina virtuale nel punto di ripristino più recente elaborato da Site Recovery. Offre un RTO (Recovery Time Objective) basso poiché non viene impiegato tempo per elaborare dati non elaborati.
    - **Coerente con l'app più recente**: questa opzione esegue il failover della macchina virtuale nel punto di ripristino coerente con l'app più recente elaborato da Site Recovery. 
    - **Personalizzato**: specificare un punto di ripristino.
3. Se si esegue il failover in Azure di macchine virtuali Hyper-V presenti nei cloud System Center VMM e la crittografia dei dati è abilitata per il cloud, in **Chiave di crittografia** selezionare il certificato emesso quando è stata abilitata la crittografia dei dati durante l'installazione del provider nel server VMM.
4. Selezionare **Arrestare la macchina prima di iniziare il failover** se si vuole provare ad arrestare le macchine virtuali di origine tramite Site Recovery prima di attivare il failover. Prima di attivare il failover, Site Recovery tenterà anche di sincronizzare i dati locali non ancora inviati ad Azure. Si noti che il failover continuerà anche se l'arresto non riesce. Nella pagina **Processi** è possibile seguire lo stato del failover.
5. Se la connessione alla macchina virtuale di Azure è stata preparata, connettersi per convalidarla dopo il failover.
6. Dopo la verifica, **eseguire il commit** del failover. In questo modo tutti i punti di ripristino disponibili verranno eliminati.

> [!WARNING]
> **Non annullare un failover in corso**: prima dell'avvio del failover, la replica della macchina virtuale viene arrestata. Se si annulla un failover in corso, il failover viene arrestato ma non viene eseguita di nuovo la replica della macchina virtuale.  


## <a name="fail-back-from-azure-to-on-premises"></a>Eseguire il failback da Azure nel sito locale

### <a name="prerequisites-for-failback"></a>Prerequisiti per il failback

Per completare il failback, verificare che il server VMM o il server Hyper-V del sito primario sia connesso a Site Recovery.


### <a name="run-a-failback-and-reprotect-on-premises-vms"></a>Eseguire un failback e riproteggere le macchine virtuali locali

Eseguire il failover da Azure nel sito locale e avviare la replica delle macchine virtuali dal sito locale in Azure.

1. In **Impostazioni** > **Elementi replicati** fare clic sulla macchina virtuale > **Failover pianificato**.
2. In **Conferma failover pianificato** verificare la direzione di failover (da Azure) e selezionare il percorso di origine e di destinazione. 
3. In **Sincronizzazione dati** specificare la modalità di sincronizzazione:
    - **Sincronizza i dati prima del failover (sincronizza solo modifiche differenziali)**: questa opzione riduce al minimo i tempi di inattività della macchina virtuale poiché la sincronizza senza arrestarla. Vengono eseguite le operazioni seguenti:
        1. Crea uno snapshot della macchina virtuale di Azure e lo copia nell'host Hyper-V locale. La macchina virtuale rimane in esecuzione in Azure.
        2. Arresta la macchina virtuale di Azure in modo che non possano essere apportate nuove modifiche. Il set finale di modifiche differenziali viene trasferito al server locale e la macchina virtuale locale viene avviata.
    - **Sincronizza i dati durante il failover (download completo)**: usare questa opzione se Azure è in esecuzione da molto tempo. Questa opzione è più veloce poiché sono previste molteplici modifiche del disco e non viene speso tempo in calcoli del checksum. Questa opzione esegue il download del disco. È utile anche quando la macchina virtuale locale è stata eliminata.
4. Se si esegue il failover in Azure di macchine virtuali Hyper-V presenti nei cloud System Center VMM e la crittografia dei dati è abilitata per il cloud, in **Chiave di crittografia** selezionare il certificato emesso quando è stata abilitata la crittografia dei dati durante l'installazione del provider nel server VMM.
5. Avviare il failover. Nella scheda **Processi** è possibile monitorare l’avanzamento del failover.
6. Se si è scelto di sincronizzare i dati prima del failover, dopo la sincronizzazione iniziale dei dati, quando si è pronti ad arrestare le macchine virtuali di Azure, fare clic su **Processi** > nome del processo di failover pianificato > **Completa failover**. La macchina virtuale di Azure viene arrestata, le modifiche più recenti vengono trasferite in locale e la macchina virtuale locale viene avviata.
7. Accedere alla macchina virtuale locale per verificare che sia disponibile come previsto.
8. La macchina virtuale locale è ora in uno stato di commit in sospeso. Fare clic su **Esegui commit** per eseguire il commit del failover. Le macchine virtuali di Azure e i relativi dischi verranno eliminati e la macchina virtuale locale verrà preparata per la replica inversa.
9. Per avviare la replica della macchina virtuale locale in Azure, abilitare **Replica inversa**.


> [!NOTE]
> La replica inversa replica solo le modifiche apportate dopo che la macchina virtuale di Azure è stata spenta e vengono inviate solo le modifiche differenziali.


