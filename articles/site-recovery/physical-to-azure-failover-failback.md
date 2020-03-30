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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75497864"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Failover e failback dei server fisici replicati in Azure

Questa esercitazione descrive come eseguire il failover dei server fisici locali che eseguono la replica in Azure con [Azure Site Recovery.](site-recovery-overview.md) Dopo aver eseguito il failover, si esegue il failback da Azure al sito locale quando è disponibile.

## <a name="before-you-start"></a>Prima di iniziare

- [Informazioni](failover-failback-overview.md) sul processo di failover nel ripristino di emergenza.
- Se si desidera eseguire il failover su più computer, [imparare](recovery-plan-overview.md) a riunire le macchine in un piano di ripristino.
- Prima di eseguire un failover completo, eseguire [un'esercitazione](site-recovery-test-failover-to-azure.md) sul ripristino di emergenza per assicurarsi che tutto funzioni come previsto.
- Seguire [queste istruzioni](site-recovery-failover.md#prepare-to-connect-after-failover) per prepararsi a connettersi alle macchine virtuali di Azure dopo il failover.



## <a name="run-a-failover"></a>Eseguire un failover

### <a name="verify-server-properties"></a>Verificare le proprietà del server

Verificare le proprietà del server e assicurarsi che sia conforme ai [requisiti di Azure](vmware-physical-azure-support-matrix.md#replicated-machines) per le VM di Azure.

1. In **Elementi protetti** fare clic su **Elementi replicati** e selezionare il computer.
2. Nel riquadro **Replicated item** (Elemento replicato) è possibile vedere un riepilogo relativo a informazioni sulla macchina, stato integrità e ultimi punti di ripristino disponibili. Fare clic su **Proprietà** per visualizzare altri dettagli.
3. In **Calcolo e rete**è possibile modificare il nome di Azure, il gruppo di risorse, le dimensioni di destinazione, il set di [disponibilità](../virtual-machines/windows/tutorial-availability-sets.md)e le impostazioni del disco gestito
4. È possibile visualizzare e modificare le impostazioni di rete, tra cui la rete/subnet in cui si troverà la macchina virtuale di Azure dopo il failover e l'indirizzo IP che le verrà assegnato.
5. In **Disks** (Dischi) è possibile vedere le informazioni sul sistema operativo e sui dischi dati della macchina.

### <a name="fail-over-to-azure"></a>Failover in Azure

1. In **Impostazioni** > **elementi replicati** fare clic sul computer > **Failover**.
2. In **Failover** selezionare un **Punto di ripristino** in cui eseguire il failover. È possibile usare una delle opzioni seguenti.
   - **Più recente**: questa opzione elabora prima tutti i dati inviati a Site Recovery. Offre il valore RPO (Recovery Point Objective) più basso perché la macchina virtuale di Azure creata dopo il failover contiene tutti i dati che sono stati replicati in Site Recovery all'attivazione del failover.
   - **Latest processed** (Elaborato più recente): questa opzione consente di eseguire il failover della macchina nel punto di recupero più recente elaborato da Site Recovery. Offre un RTO (Recovery Time Objective) basso poiché non viene impiegato tempo per elaborare dati non elaborati.
   - **Latest app-consistent** (Coerente con l'app più recente): questa opzione esegue il failover della macchina nel punto di recupero coerente con l'app più recente elaborato da Site Recovery.
   - **Personalizzato**: specificare un punto di ripristino.

3. Selezionare **Shut down machine before beginning failover** (Arrestare la macchina prima di iniziare il failover) se si vuole provare ad arrestare la macchina di origine tramite Site Recovery prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. È possibile seguire l'avanzamento del failover nella pagina **Processi.You** can follow the failover progress on the Jobs page.
4. Se la connessione alla macchina virtuale di Azure è stata preparata, connettersi per convalidarla dopo il failover.
5. Dopo la verifica, **eseguire il commit** del failover. In questo modo tutti i punti di ripristino disponibili verranno eliminati.

> [!WARNING]
> Non annullare un failover in corso. Prima di iniziare il failover, la replica della macchina si arresta. Se si annulla il failover questo si arresta, ma la macchina non eseguirà nuovamente la replica.
> Per i server fisici, il completamento dell'elaborazione di un failover aggiuntivo può richiedere da 8 a 10 minuti circa.

## <a name="automate-actions-during-failover"></a>Automatizzare le azioni durante il failoverAutomate actions during failover

È possibile automatizzare le azioni durante il failover. A tale scopo, è possibile usare script o runbook di automazione di Azure nei piani di ripristino.

- [Informazioni](site-recovery-create-recovery-plans.md) sulla creazione e la personalizzazione di piani di ripristino, inclusa l'aggiunta di script.
- [Informazioni sull'aggiunta](site-recovery-runbook-automation.md) di runbook di Automazione di Azure ai piani di ripristino.

## <a name="configure-settings-after-failover"></a>Configurare le impostazioni dopo il failover

Dopo il failover è necessario [configurare le impostazioni](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover) di Azure per connettersi alle macchine virtuali di Azure replicate. Inoltre, impostare l'indirizzamento IP [interno e pubblico.](site-recovery-failover.md#set-up-ip-addressing)

## <a name="prepare-for-reprotection-and-failback"></a>Preparare la riprotezione e il failback

Dopo il failover in Azure, è possibile riproteggere le macchine virtuali di Azure replicandole nel sito locale. Dopo la replica, è quindi possibile eseguirne il failback in locale eseguendo un failover da Azure al sito locale.

1. I server fisici replicati in Azure con Site Recovery possono eseguire il failback solo come VM VMware. È quindi necessaria un'infrastruttura VMware per eseguire il failback. Seguire i passaggi descritti in [questo articolo](vmware-azure-prepare-failback.md) per preparare la riprotezione e il failback, inclusa la configurazione di un server di elaborazione in Azure e di un server di destinazione master locale e la configurazione di una VPN da sito a sito o di un peering privato ExpressRoute per il failback.
2. Assicurarsi che il server di configurazione locale sia in esecuzione e connesso ad Azure.Make sure that the on-premises configuration server is running and connected to Azure. Durante il failover in Azure, il sito locale potrebbe non essere accessibile e il server di configurazione potrebbe non essere disponibile o arrestarsi. Durante il failback, la macchina virtuale deve esistere nel database del server di configurazione. In caso contrario, il failback ha esito negativo.
3. Eliminare eventuali snapshot nel server di destinazione master locale. La riprotezione non funzionerà se sono presenti snapshot.  Gli snapshot nella macchina virtuale vengono uniti automaticamente durante un processo di riprotezione.
4. Se si proteggono le macchine virtuali raccolte in un gruppo di replica per la coerenza di più macchine virtuali, assicurarsi che tutte abbiano lo stesso sistema operativo (Windows o Linux) e assicurarsi che il server di destinazione master distribuito abbia lo stesso tipo di sistema operativo. Tutte le macchine virtuali in un gruppo di replica devono utilizzare lo stesso server di destinazione master.
5. Aprire [le porte necessarie](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) per il failback.
6. Assicurarsi che il server vCenter sia connesso prima del failback. In caso contrario, la disconnessione dei dischi e il ricollegamento alla macchina virtuale non riescono.
7. Se un server vCenter gestisce le macchine virtuali in cui verrà eseguito il failback, assicurarsi di disporre delle autorizzazioni necessarie. Se si esegue l'individuazione di vCenter con utente di sola lettura e la protezione delle macchine virtuali, la protezione funziona e il failover viene eseguito. Tuttavia, durante la riprotezione, il failover non riesce perché non è possibile individuare gli archivi dati e non sono elencati durante la riprotezione. Per risolvere questo problema, è possibile aggiornare le credenziali di vCenter con un [account/autorizzazioni appropriato](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)e quindi ritentare il processo. 
8. Se è stato usato un modello per creare le macchine virtuali, verificare che ogni macchina virtuale disponga di un proprio UUID per i dischi. Se l'UUID della macchina virtuale locale si scontra con l'UUID del server di destinazione master perché entrambi sono stati creati dallo stesso modello, la riprotezione non riesce. Eseguire la distribuzione da un modello diverso.
9. Se si esegue il failback a un server vCenter alternativo, assicurarsi che siano individuati il nuovo server vCenter e il server di destinazione master. In genere, se non sono i datastore non sono accessibili o non sono visibili in **Reprotect**.
10. Verificare gli scenari seguenti in cui non è possibile eseguire il failback:
    - Se si utilizza l'edizione gratuita ESXi 5.5 o l'edizione gratuita di vSphere 6 Hypervisor. Eseguire l'aggiornamento a una versione diversa.
    - Se si dispone di un server fisico di Windows Server 2008 R2 SP1.
    - Macchine virtuali [di cui è stata eseguita la migrazione.](migrate-overview.md#what-do-we-mean-by-migration)
    - Una macchina virtuale che è stata spostata in un altro gruppo di risorse.
    - Una macchina virtuale di Azure di replica che è stata eliminata.
    - Una macchina virtuale di Azure di replica non protetta (replica al sito locale).
10. [Esaminare i tipi di failback](concepts-types-of-failback.md) che è possibile utilizzare, ovvero il ripristino della posizione originale e il ripristino della posizione alternativa.


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>Riproteggere le macchine virtuali di Azure in un percorso alternativoReprotect Azure VMs to an alternate location

Questa procedura presuppone che la macchina virtuale locale non sia disponibile.

1. Nell'insieme di credenziali > **Impostazioni** > **elementi replicati**, fare clic con il pulsante destro del mouse sul computer di cui è stato eseguito il failover > **riproteggi**.
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
3.Selezionare il punto di ripristino che si desidera utilizzare per il failover.
    - È consigliabile utilizzare il punto di ripristino **più recente.** Il punto coerente dell'app è dietro l'ultimo punto nel tempo e causa una perdita di dati.
    - **L'ultimo** è un punto di ripristino coerente con l'arresto anomalo del sistema.
    - Quando viene eseguito il failover, Site Recovery arresta le macchine virtuali di Azure e avvia la macchina virtuale locale. L'operazione comporta un tempo di inattività. Scegliere pertanto un orario appropriato.
4. Fare clic con il pulsante destro del mouse sul computer e quindi su **Esegui commit**. Verrà attivato un processo che rimuove le macchine virtuali di Azure.
5. Verificare che le macchine virtuali di Azure siano state arrestate come previsto.


## <a name="reprotect-on-premises-machines-to-azure"></a>Riproteggere i computer locali in Azure

I dati dovrebbero ora essere tornati nel sito locale, ma non vengono replicati in Azure. È possibile avviare nuovamente la replica in Azure seguendo questa procedura:

1. Nell'insieme di credenziali > **Impostazioni** >**Elementi replicati** selezionare le macchine virtuali di cui è stato eseguito il failback e fare clic su **Riproteggi**.
2. Selezionare il server di elaborazione usato per inviare i dati replicati in Azure e fare clic su **OK**.


## <a name="next-steps"></a>Passaggi successivi

Al termine del processo di riprotezione, la macchina virtuale locale esegue la replica in Azure.After the reprotect job finishes, the on-premises VM is replicating to Azure. Se necessario, è possibile [eseguire un altro failover in](site-recovery-failover.md) Azure.As needed, you can run another failover to Azure.
