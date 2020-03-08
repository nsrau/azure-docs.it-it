---
title: Riproteggere le macchine virtuali VMware in un sito locale con Azure Site Recovery
description: Informazioni su come riproteggere le macchine virtuali VMware dopo il failover in Azure con Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mayg
ms.openlocfilehash: 976888f57269cc9fe6107a38e30d78c73eb5c124
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362918"
---
# <a name="reprotect-from-azure-to-on-premises"></a>Riprotezione da Azure al sito locale

Dopo il [failover](site-recovery-failover.md) di macchine virtuali VMware locali o server fisici in Azure, il primo passaggio dell'esecuzione di un failback nel sito locale consiste nel riapplicare la protezione alle macchine virtuali di Azure create durante il failover. Questo articolo illustra come farlo. 

## <a name="before-you-begin"></a>Prima di iniziare

1. Eseguire la procedura descritta in [questo articolo](vmware-azure-prepare-failback.md) per preparare la riprotezione e il failback, inclusa la configurazione di un server di elaborazione in Azure e un server di destinazione master locale e la configurazione di una VPN da sito a sito o di un peering privato ExpressRoute per il failback.
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
    - Non è possibile eseguire il failback di macchine virtuali VMware in Hyper-V.
    - Macchine virtuali di cui è [stata eseguita la migrazione](migrate-overview.md#what-do-we-mean-by-migration).
    - Una macchina virtuale che è stata spostata in un altro gruppo di risorse.
    - Una macchina virtuale di Azure di replica che è stata eliminata.
    - Una macchina virtuale di Azure di replica non protetta (replica nel sito locale).
10. [Esaminare i tipi di failback](concepts-types-of-failback.md) che è possibile usare, ovvero il ripristino del percorso originale e il ripristino in un percorso alternativo.


## <a name="enable-reprotection"></a>Abilitare la riprotezione

Abilitare la replica. È possibile riproteggere le macchine virtuali specifiche o un piano di ripristino:

- Se si riprotegge un piano di ripristino, è necessario fornire i valori per ogni computer protetto.
- Se le macchine virtuali appartengono a un gruppo di replica per la coerenza tra più macchine virtuali, possono essere riprotette solo usando un piano di ripristino. Le macchine virtuali in un gruppo di replica devono usare lo stesso server di destinazione master

### <a name="before-you-start"></a>Prima di iniziare

- Dopo l'avvio di una macchina virtuale in Azure dopo il failover, l'agente deve eseguire di nuovo la registrazione al server di configurazione (fino a 15 minuti). Durante questo intervallo di tempo non sarà possibile abilitare la riprotezione e un messaggio di errore indica che l'agente non è installato. In tal caso, attendere qualche minuto e quindi riproteggere.
- Se si vuole eseguire il failback della macchina virtuale di Azure in una macchina virtuale locale esistente, montare gli archivi dati della macchina virtuale locale con accesso in lettura/scrittura sull'host ESXi del server di destinazione master.
- Se si vuole eseguire il failback in un percorso alternativo, ad esempio se la macchina virtuale locale non esiste, selezionare l'unità di conservazione e l'archivio dati configurati per il server di destinazione master. Quando si esegue il failback al sito locale, le macchine virtuali VMware nel piano di protezione di failback usano lo stesso archivio dati del server di destinazione master. Viene quindi creata una nuova macchina virtuale in vCenter.

Abilitare la riprotezione come segue:

1. Selezionare **Insieme di credenziali** > **Elementi replicati**. Fare clic con il pulsante destro del mouse sulla macchina virtuale di cui è stato eseguito il failover e scegliere **Riproteggi**. In alternativa, dai pulsanti di comando selezionare la macchina virtuale e quindi selezionare **Riproteggi**.
2. Verificare che sia selezionata la direzione di protezione **Da Azure a locale**.
3. In **Server di destinazione master** e **Server di elaborazione** selezionare il server di destinazione master locale e il server di elaborazione.  
4. Per **Archivio dati**, selezionare l'archivio dati in cui ripristinare i dischi in locale. Questa opzione viene usata quando la macchina virtuale locale viene eliminata e devono essere creati nuovi dischi. Questa opzione viene ignorata se i dischi esistono già. È comunque necessario specificare un valore.
5. Selezionare l'unità di conservazione.
6. I criteri di failback vengono selezionati automaticamente.
7. Selezionare **OK** per avviare la riprotezione.

    ![Finestra di dialogo di riprotezione](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. Un processo inizia a replicare la macchina virtuale di Azure nel sito locale. È possibile monitorare l'avanzamento nella scheda **Processi** .
    - Quando la riprotezione ha esito positivo, la macchina virtuale entra in uno stato protetto.
    - La macchina virtuale locale viene spenta durante la riprotezione, in modo da garantire la coerenza dei dati durante la replica.
    - Non accendere la macchina virtuale locale al termine della riprotezione.
   

## <a name="next-steps"></a>Passaggi successivi

- Se si verificano problemi, vedere l' [articolo sulla risoluzione dei](vmware-azure-troubleshoot-failback-reprotect.md)problemi.
- Una volta protette le macchine virtuali di Azure, è possibile [eseguire un failback](vmware-azure-failback.md). Il failback arresta la macchina virtuale di Azure e avvia la macchina virtuale locale. Prevedere un tempo di inattività per l'applicazione e scegliere di conseguenza un'ora di failback.


