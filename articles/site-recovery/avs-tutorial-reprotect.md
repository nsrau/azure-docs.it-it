---
title: Riproteggere le macchine virtuali di Azure in un cloud privato della soluzione Azure VMware con Azure Site Recovery
description: Informazioni su come riproteggere le macchine virtuali della soluzione Azure VMware dopo il failover in Azure con Azure Site Recovery.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 80ff2f3f3d5fdcf61770889dcdaaf075941b90ff
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91814239"
---
# <a name="reprotect-from-azure-to-azure-vmware-solution-private-cloud"></a>Riproteggere da Azure in un cloud privato della soluzione Azure VMware

Dopo il [failover](avs-tutorial-failover.md) di macchine virtuali della soluzione Azure VMware in Azure, il primo passaggio dell'esecuzione di un failback nel cloud privato della soluzione Azure VMware consiste nel riproteggere le macchine virtuali di Azure create durante il failover. Questo articolo illustra come farlo. 

## <a name="before-you-begin"></a>Prima di iniziare

1. Seguire la procedura descritta in [questo articolo](vmware-azure-prepare-failback.md) per preparare la riprotezione e il failback, inclusa la configurazione di un server di elaborazione in Azure e di un server di destinazione master nel cloud privato della soluzione Azure VMware, oltre alla configurazione di una VPN da sito a sito o di un peering privato ExpressRoute per il failback.
2. Assicurarsi che il server di configurazione nel cloud privato della soluzione Azure VMware sia in esecuzione e connesso ad Azure. Durante il failback, la macchina virtuale deve trovarsi nel database del server di configurazione. In caso contrario, il failback ha esito negativo.
3. Eliminare eventuali snapshot nel server di destinazione master nel cloud privato della soluzione Azure VMware. Se sono presenti snapshot, la riprotezione non funzionerà.  Durante il processo di riprotezione, gli snapshot nella macchina virtuale vengono uniti automaticamente.
4. Se è necessario riproteggere macchine virtuali raccolte in un gruppo di replica per la coerenza tra più macchine virtuali, assicurarsi che tutte abbiano lo stesso sistema operativo (Windows o Linux) e assicurarsi che il server di destinazione master distribuito abbia lo stesso tipo di sistema operativo. Tutte le macchine virtuali in un gruppo di replica devono usare lo stesso server di destinazione master.
5. Aprire [le porte necessarie](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) per il failback.
6. Assicurarsi che il server vCenter sia connesso prima del failback. In caso contrario, la disconnessione dei dischi e il ricollegamento alla macchina virtuale non riescono.
7. Se le macchine virtuali in cui si vuole eseguire il failback sono gestite da un server vCenter, assicurarsi di avere le autorizzazioni necessarie. Se si esegue l'individuazione di vCenter con utente di sola lettura e la protezione delle macchine virtuali, la protezione funziona e il failover viene eseguito. Tuttavia, durante la riprotezione, il failover ha esito negativo perché non è possibile individuare gli archivi dati che non vengono quindi elencati. Per risolvere questo problema, è possibile aggiornare le credenziali di vCenter con un [account/autorizzazioni appropriati](avs-tutorial-prepare-avs.md#prepare-an-account-for-automatic-discovery) e quindi ripetere il processo. 
8. Se è stato usato un modello per creare le macchine virtuali, assicurarsi che ogni macchina virtuale abbia un UUID univoco per i dischi. Se l'UUID della macchina virtuale della soluzione Azure VMware è in conflitto con quello del server di destinazione master perché entrambi sono stati creati a partire dallo stesso modello, la riprotezione non riesce. Eseguire la distribuzione da un modello diverso.
9. Se si esegue il failback in un server vCenter alternativo, assicurarsi che il nuovo server vCenter e il server di destinazione master vengano rilevati. In genere, se non vengono rilevati, gli archivi dati non sono accessibili o non sono visibili in **Riproteggi**.
10. Verificare gli scenari seguenti in cui non è possibile eseguire il failback:
    - Se si usa l'edizione gratuita ESXi 5.5 o vSphere 6 Hypervisor. Eseguire l'aggiornamento a una versione diversa.
    - Se si dispone di un server fisico Windows Server 2008 R2 SP1.
    - Non è possibile eseguire il failback di macchine virtuali VMware in Hyper-V.
    - Macchine virtuali di cui è stata eseguita la migrazione.
    - Una macchina virtuale che è stata spostata in un altro gruppo di risorse.
    - Una macchina virtuale di Azure di replica che è stata eliminata.
    - Una macchina virtuale di Azure di replica che non è stata protetta.
10. [Esaminare i tipi di failback](concepts-types-of-failback.md) che è possibile usare: il ripristino nel percorso originale e il ripristino in un percorso alternativo.


## <a name="enable-reprotection"></a>Abilitare la riprotezione

Abilitare la replica. È possibile riproteggere macchine virtuali specifiche o un piano di ripristino:

- Se si riprotegge un piano di ripristino, è necessario specificare i valori per ogni macchina protetta.
- Se le macchine virtuali appartengono a un gruppo di replica per la coerenza tra più macchine virtuali, possono essere riprotette solo usando un piano di ripristino. Le macchine virtuali in un gruppo di replica devono usare lo stesso server di destinazione master.

>[!NOTE]
>La quantità di dati inviati da Azure all'origine precedente durante la riprotezione può essere compresa tra 0 byte e la somma delle dimensioni dei dischi di tutte le macchine protette e non può essere calcolata.

### <a name="before-you-start"></a>Prima di iniziare

- Dopo l'avvio di una macchina virtuale in Azure dopo il failover, registrare di nuovo l'agente nel server di configurazione può richiedere fino a 15 minuti. Durante questo intervallo di tempo non sarà possibile abilitare la riprotezione e un messaggio di errore indica che l'agente non è installato. In questo caso, attendere alcuni minuti e quindi eseguire l'operazione di riprotezione.
- Se si vuole eseguire il failback della macchina virtuale di Azure in una macchina virtuale della soluzione Azure VMware esistente, montare gli archivi dati della macchina virtuale con accesso in lettura/scrittura nell'host ESXi del server di destinazione master.
- Se si vuole eseguire il ripristino in un percorso alternativo, ad esempio se la macchina virtuale della soluzione Azure VMware non esiste, selezionare l'unità di conservazione e l'archivio dati configurati per il server di destinazione master. Quando si esegue il failback nel cloud privato della soluzione Azure VMware, le macchine virtuali nel piano di protezione di failback usano lo stesso archivio dati del server di destinazione master. Una nuova macchina virtuale viene quindi creata in vCenter.

Abilitare la riprotezione come segue:

1. Selezionare **Insieme di credenziali** > **Elementi replicati**. Fare clic con il pulsante destro del mouse sulla macchina virtuale di cui è stato eseguito il failover e scegliere **Riproteggi**. In alternativa, dai pulsanti di comando selezionare la macchina virtuale e quindi selezionare **Riproteggi**.
2. Verificare che sia selezionata la direzione di protezione **Da Azure a locale**.
3. In **Server di destinazione master** e **Server di elaborazione** selezionare il server di destinazione master locale e il server di elaborazione.  
4. Per **Archivio dati** selezionare l'archivio dati in cui ripristinare i dischi nella soluzione Azure VMware. Questa opzione viene usata quando la macchina virtuale della soluzione Azure VMware viene eliminata ed è necessario creare nuovi dischi. Questa opzione viene ignorata se i dischi esistono già. È comunque necessario specificare un valore.
5. Selezionare l'unità di conservazione.
6. I criteri di failback vengono selezionati automaticamente.
7. Selezionare **OK** per avviare la riprotezione.

    ![Finestra di dialogo di riprotezione](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. Un processo inizia a replicare la macchina virtuale di Azure nel cloud privato della soluzione Azure VMware. È possibile monitorare l'avanzamento nella scheda **Processi** .
    - Al termine del processo di riprotezione, la macchina virtuale acquisisce uno stato protetto.
    - La macchina virtuale della soluzione Azure VMware viene disattivata durante la riprotezione. in modo da garantire la coerenza dei dati durante la replica.
    - Non attivare la macchina virtuale della soluzione Azure VMware al termine della riprotezione.
   

## <a name="next-steps"></a>Passaggi successivi

- Se si verificano problemi, vedere l'[articolo sulla risoluzione dei problemi](vmware-azure-troubleshoot-failback-reprotect.md).
- Una volta protette le macchine virtuali di Azure, è possibile [eseguire un failback](avs-tutorial-failback.md). Il failback arresta la macchina virtuale di Azure e avvia la macchina virtuale della soluzione Azure VMware. Prevedere un tempo di inattività per l'applicazione e scegliere di conseguenza una data/ora per il failback.


