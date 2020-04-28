---
title: Configurare il failover e il failback per i server fisici con Site Recovery
description: Informazioni su come eseguire il failover di server fisici in Azure ed eseguire il failback nel sito locale per il ripristino di emergenza con Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2019
ms.author: raynew
ms.openlocfilehash: ea5893f45962d67f4b6f3e9a261c65aa0ec926bf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75497864"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Failover e failback dei server fisici replicati in Azure

Questa esercitazione descrive come eseguire il failover dei server fisici locali che eseguono la replica in Azure con [Azure Site Recovery](site-recovery-overview.md). Dopo il failover, si esegue il failback da Azure al sito locale quando è disponibile.

## <a name="before-you-start"></a>Prima di iniziare

- [Informazioni sul processo](failover-failback-overview.md) di failover in ripristino di emergenza.
- Se si desidera eseguire il failover di più computer [, vedere la pagina](recovery-plan-overview.md) relativa alla raccolta di macchine virtuali in un piano di ripristino.
- Prima di eseguire un failover completo, eseguire un' [esercitazione sul ripristino di emergenza](site-recovery-test-failover-to-azure.md) per verificare che tutto funzioni come previsto.
- Seguire [queste istruzioni](site-recovery-failover.md#prepare-to-connect-after-failover) per preparare la connessione alle macchine virtuali di Azure dopo il failover.



## <a name="run-a-failover"></a>Eseguire un failover

### <a name="verify-server-properties"></a>Verificare le proprietà del server

Verificare le proprietà del server e assicurarsi che sia conforme ai [requisiti di Azure](vmware-physical-azure-support-matrix.md#replicated-machines) per le VM di Azure.

1. In **Elementi protetti** fare clic su **Elementi replicati** e selezionare il computer.
2. Nel riquadro **Replicated item** (Elemento replicato) è possibile vedere un riepilogo relativo a informazioni sulla macchina, stato integrità e ultimi punti di ripristino disponibili. Fare clic su **Proprietà** per visualizzare altri dettagli.
3. In **calcolo e rete**è possibile modificare il nome di Azure, il gruppo di risorse, le dimensioni di destinazione, il [set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md)e le impostazioni del disco gestito
4. È possibile visualizzare e modificare le impostazioni di rete, tra cui la rete/subnet in cui si troverà la macchina virtuale di Azure dopo il failover e l'indirizzo IP che le verrà assegnato.
5. In **Disks** (Dischi) è possibile vedere le informazioni sul sistema operativo e sui dischi dati della macchina.

### <a name="fail-over-to-azure"></a>Failover in Azure

1. In **Impostazioni** > **Elementi replicati** fare clic su computer > **Failover**.
2. In **Failover** selezionare un **Punto di ripristino** in cui eseguire il failover. È possibile usare una delle opzioni seguenti.
   - **Più recente**: questa opzione elabora prima tutti i dati inviati a Site Recovery. Offre il valore RPO (Recovery Point Objective) più basso perché la macchina virtuale di Azure creata dopo il failover contiene tutti i dati che sono stati replicati in Site Recovery all'attivazione del failover.
   - **Latest processed** (Elaborato più recente): questa opzione consente di eseguire il failover della macchina nel punto di recupero più recente elaborato da Site Recovery. Offre un RTO (Recovery Time Objective) basso poiché non viene impiegato tempo per elaborare dati non elaborati.
   - **Latest app-consistent** (Coerente con l'app più recente): questa opzione esegue il failover della macchina nel punto di recupero coerente con l'app più recente elaborato da Site Recovery.
   - **Personalizzato**: specificare un punto di ripristino.

3. Selezionare **Shut down machine before beginning failover** (Arrestare la macchina prima di iniziare il failover) se si vuole provare ad arrestare la macchina di origine tramite Site Recovery prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover.
4. Se la connessione alla macchina virtuale di Azure è stata preparata, connettersi per convalidarla dopo il failover.
5. Dopo la verifica, **eseguire il commit** del failover. In questo modo tutti i punti di ripristino disponibili verranno eliminati.

> [!WARNING]
> Non annullare un failover in corso. Prima di iniziare il failover, la replica della macchina si arresta. Se si annulla il failover questo si arresta, ma la macchina non eseguirà nuovamente la replica.
> Per i server fisici, il completamento dell'elaborazione di un failover aggiuntivo può richiedere da 8 a 10 minuti circa.

## <a name="automate-actions-during-failover"></a>Automatizzare le azioni durante il failover

Potrebbe essere necessario automatizzare le azioni durante il failover. A tale scopo, è possibile usare gli script o manuali operativi di automazione di Azure nei piani di ripristino.

- [Informazioni sulla creazione](site-recovery-create-recovery-plans.md) e la personalizzazione dei piani di ripristino, inclusa l'aggiunta di script.
- [Informazioni](site-recovery-runbook-automation.md) su come aggiungere manuali operativi di automazione di Azure ai piani di ripristino.

## <a name="configure-settings-after-failover"></a>Configura impostazioni dopo il failover

Dopo il failover è necessario [configurare le impostazioni di Azure](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover) per la connessione alle macchine virtuali di Azure replicate. Inoltre, configurare gli indirizzi IP [interni e pubblici](site-recovery-failover.md#set-up-ip-addressing) .

## <a name="prepare-for-reprotection-and-failback"></a>Preparare la riprotezione e il failback

Dopo il failover in Azure, è necessario riproteggere le macchine virtuali di Azure eseguendone la replica nel sito locale. Quindi, dopo la replica, è possibile eseguire il failback in locale eseguendo un failover da Azure al sito locale.

1. I server fisici replicati in Azure con Site Recovery possono eseguire il failback solo come VM VMware. È quindi necessaria un'infrastruttura VMware per eseguire il failback. Eseguire la procedura descritta in [questo articolo](vmware-azure-prepare-failback.md) per preparare la riprotezione e il failback, inclusa la configurazione di un server di elaborazione in Azure e un server di destinazione master locale e la configurazione di una VPN da sito a sito o di un peering privato ExpressRoute per il failback.
2. Verificare che il server di configurazione locale sia in esecuzione e connesso ad Azure. Durante il failover in Azure, il sito locale potrebbe non essere accessibile e il server di configurazione potrebbe non essere disponibile o arrestarsi. Durante il failback, la macchina virtuale deve esistere nel database del server di configurazione. In caso contrario, il failback ha esito negativo.
3. Eliminare gli snapshot nel server di destinazione master locale. Se sono presenti snapshot, la riprotezione non funzionerà.  Gli snapshot nella macchina virtuale vengono uniti automaticamente durante un processo di riprotezione.
4. Per la riprotezione delle VM raccolte in un gruppo di replica per la coerenza tra più macchine virtuali, assicurarsi che tutti abbiano lo stesso sistema operativo (Windows o Linux) e assicurarsi che il server di destinazione master distribuito abbia lo stesso tipo di sistema operativo. Tutte le macchine virtuali in un gruppo di replica devono usare lo stesso server di destinazione master.
5. Aprire [le porte necessarie per il](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) failback.
6. Verificare che il server vCenter sia connesso prima del failback. In caso contrario, la disconnessione dei dischi e il ricollegamento alla macchina virtuale non riescono.
7. Se un server vCenter gestisce le macchine virtuali a cui si eseguirà il failback, assicurarsi di disporre delle autorizzazioni necessarie. Se si esegue l'individuazione di vCenter con utente di sola lettura e la protezione delle macchine virtuali, la protezione funziona e il failover viene eseguito. Tuttavia, durante la riprotezione, il failover non riesce perché gli archivi dati non possono essere individuati e non sono elencati durante la riprotezione. Per risolvere il problema, è possibile aggiornare le credenziali di vCenter con un [account o autorizzazioni appropriate](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery), quindi ripetere il processo. 
8. Se è stato usato un modello per creare le macchine virtuali, assicurarsi che ogni macchina virtuale disponga del proprio UUID per i dischi. Se l'UUID della macchina virtuale locale è in conflitto con l'UUID del server di destinazione master perché entrambi sono stati creati dallo stesso modello, la riprotezione non riesce. Eseguire la distribuzione da un modello diverso.
9. Se si esegue il failback in un server vCenter alternativo, assicurarsi che vengano individuati il nuovo server vCenter e il server di destinazione master. In genere, se non sono gli archivi dati non sono accessibili o non sono visibili nella **riprotezione**.
10. Verificare i seguenti scenari in cui non è possibile eseguire il failback:
    - Se si usa l'edizione gratuita ESXi 5,5 o vSphere 6 hypervisor. Eseguire l'aggiornamento a una versione diversa.
    - Se si dispone di un server fisico Windows Server 2008 R2 SP1.
    - Macchine virtuali di cui è [stata eseguita la migrazione](migrate-overview.md#what-do-we-mean-by-migration).
    - Una macchina virtuale che è stata spostata in un altro gruppo di risorse.
    - Una macchina virtuale di Azure di replica che è stata eliminata.
    - Una macchina virtuale di Azure di replica non protetta (replica nel sito locale).
10. [Esaminare i tipi di failback](concepts-types-of-failback.md) che è possibile usare, ovvero il ripristino del percorso originale e il ripristino in un percorso alternativo.


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>Riproteggere le macchine virtuali di Azure in un percorso alternativo

Questa procedura presuppone che la macchina virtuale locale non sia disponibile.

1. Nell'insieme di credenziali > **Impostazioni** > **elementi replicati**fare clic con il pulsante destro del mouse sul computer di cui è stato eseguito il failover > **riproteggere**.
2. In **Riproteggi** verificare che **Da Azure a locale** sia selezionato.
3. Specificare il server di destinazione master locale e il server di elaborazione.
4. In **Archivio dati** selezionare l'archivio dati del server di destinazione master in cui ripristinare i dischi in locale.
       - Usare questa opzione se la macchina virtuale locale è stata eliminata o non esiste ed è necessario creare nuovi dischi.
       - Questa impostazione viene ignorata se i dischi esistono già, ma è necessario specificare un valore.
5. Selezionare l'unità di conservazione del server di destinazione master. I criteri di failback vengono selezionati automaticamente.
6. Fare clic su **OK** per avviare la riprotezione. Un processo inizia a replicare la macchina virtuale di Azure nel sito locale. È possibile monitorare l'avanzamento nella scheda **Processi** .

> [!NOTE]
> Se si desidera ripristinare la macchina virtuale di Azure in una macchina virtuale locale esistente montare l'archivio dati della macchina virtuale locale con accesso in lettura/scrittura sull'host ESXi del server di destinazione master.


## <a name="fail-back-from-azure"></a>Effettuare il failback da Azure

Eseguire il failover come descritto di seguito:

1. Nella pagina **Elementi replicati** fare clic con il pulsante destro del mouse sul computer > **Failover non pianificato**.
2. In **Conferma failover** verificare che la direzione di failover sia da Azure.
3. Selezionare il punto di ripristino che si desidera utilizzare per il failover.
    - Si consiglia di usare il punto di ripristino **più recente** . Il punto coerente con l'app è alla base dell'ultimo punto nel tempo e causa la perdita di dati.
    - Il **più recente** è un punto di ripristino coerente con l'arresto anomalo.
    - Quando viene eseguito il failover, Site Recovery arresta le macchine virtuali di Azure e avvia la macchina virtuale locale. L'operazione comporta un tempo di inattività. Scegliere pertanto un orario appropriato.
4. Fare clic con il pulsante destro del mouse sul computer e quindi su **Esegui commit**. Verrà attivato un processo che rimuove le macchine virtuali di Azure.
5. Verificare che le macchine virtuali di Azure siano state arrestate come previsto.


## <a name="reprotect-on-premises-machines-to-azure"></a>Riproteggere i computer locali in Azure

I dati dovrebbero ora essere tornati nel sito locale, ma non vengono replicati in Azure. È possibile avviare nuovamente la replica in Azure seguendo questa procedura:

1. Nell'insieme di credenziali > **Impostazioni** >**Elementi replicati** selezionare le macchine virtuali di cui è stato eseguito il failback e fare clic su **Riproteggi**.
2. Selezionare il server di elaborazione usato per inviare i dati replicati in Azure e fare clic su **OK**.


## <a name="next-steps"></a>Passaggi successivi

Al termine del processo di riprotezione, la macchina virtuale locale viene replicata in Azure. Se necessario, è possibile [eseguire un altro failover](site-recovery-failover.md) in Azure.
