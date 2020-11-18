---
title: 'Esercitazione: Configurare il ripristino di emergenza per le macchine virtuali Windows con Azure Site Recovery'
description: Informazioni su come abilitare il ripristino di emergenza per le macchine virtuali Windows in un'area di Azure diversa usando il servizio Azure Site Recovery.
author: rayne-wiselman
ms.service: virtual-machines-windows
ms.subservice: recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 53cc0b820bd2ffb9fc28b37f44bb71a7b9d3cd30
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379934"
---
# <a name="tutorial-enable-disaster-recovery-for-windows-vms"></a>Esercitazione: Abilitare il ripristino di emergenza per le macchine virtuali Windows

Questa esercitazione illustra come configurare il ripristino di emergenza per le macchine virtuali di Azure che eseguono Windows. In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Abilitare il ripristino di emergenza per una macchina virtuale Windows
> * Eseguire un'esercitazione sul ripristino di emergenza
> * Arrestare la replica della macchina virtuale dopo l'esercitazione

Quando si abilita la replica per una macchina virtuale, l'estensione del servizio di mobilità di Site Recovery viene installata nella macchina virtuale e registrata con [Azure Site Recovery](../../site-recovery/site-recovery-overview.md). Durante la replica, le scritture su disco della macchina virtuale vengono inviate a un account di archiviazione della cache nell'area di origine. I dati vengono inviati da questo account all'area di destinazione e vengono generati i punti di ripristino dai dati.  Quando si esegue il failover di una macchina virtuale durante il ripristino di emergenza, viene usato un punto di ripristino per ripristinare la macchina virtuale nell'area di destinazione.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

1. Verificare che la sottoscrizione di Azure in uso consenta la creazione di una macchina virtuale nell'area di destinazione. Se è appena stato creato l'account Azure gratuito, si è amministratori della propria sottoscrizione e si hanno le autorizzazioni necessarie.
2. Se non si è l'amministratore della sottoscrizione, chiedere all'amministratore di provvedere all'assegnazione delle autorizzazioni seguenti:
    - Il ruolo predefinito Collaboratore Macchina virtuale oppure le autorizzazioni specifiche per:
        - Creare una macchina virtuale nella rete virtuale selezionata.
        - Scrivere in un account di archiviazione di Azure.
        - Scrivere su un disco gestito di Azure.
    - Il ruolo predefinito Collaboratore di Site Recovery per gestire le operazioni di Site Recovery nell'insieme di credenziali. 
3. È consigliabile usare una macchina virtuale Windows che esegue Windows Server 2012 o versione successiva. Ai fini di questa esercitazione, il disco della macchina virtuale non deve essere crittografato.
4. Se le connessioni in uscita della macchina virtuale usano un proxy basato su URL, assicurarsi che possa accedere a questi URL. L'uso di un proxy autenticato non è supportato.

    **Nome** | **Cloud pubblico** | **Cloud per enti pubblici** | **Informazioni dettagliate**
    --- | --- | --- | ---
    Archiviazione | `*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`| Scrittura dei dati dalla macchina virtuale nell'account di archiviazione della cache all'area di origine. 
    Azure AD  | `login.microsoftonline.com` | `login.microsoftonline.us`| Autorizzazione e autenticazione per gli URL del servizio Site Recovery. 
    Replica | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`  |Comunicazione della macchina virtuale con il servizio Site Recovery. 
    Bus di servizio | `*.servicebus.windows.net` | `*.servicebus.usgovcloudapi.net` | La macchina virtuale esegue la scrittura dei dati di monitoraggio e diagnostica in Site Recovery. 

4. Se si usano i gruppi di sicurezza di rete per limitare il traffico di rete per le macchine virtuali, creare regole dei gruppi di sicurezza di rete che consentano la connettività in uscita (HTTPS 443) per la macchina virtuale usando questi tag del servizio (gruppi di indirizzi IP). Provare prima le regole in un gruppo di sicurezza di rete di test.

    **Tag** | **Consentito** 
    --- | --- 
    Tag Storage | Consente la scrittura di dati dalla macchina virtuale nell'account di archiviazione della cache.
    Tag Azure AD | Consente l'accesso a tutti gli indirizzi IP corrispondenti ad Azure AD.
    Tag EventsHub | Consente l'accesso al monitoraggio di Site Recovery.
    Tag AzureSiteRecovery | Consente l'accesso al servizio Site Recovery in qualsiasi area.
    GuestAndHybridManagement | Usare questo tag se si vuole aggiornare automaticamente l'agente di mobilità di Site Recovery in esecuzione nelle macchine virtuali abilitate per la replica.
5.  Nelle macchine virtuali Windows installare gli aggiornamenti di Windows più recenti per assicurarsi che le macchine virtuali dispongano dei certificati radice più recenti.
 
## <a name="enable-disaster-recovery"></a>Abilitare il ripristino di emergenza

1. Nel portale di Azure aprire la pagina delle proprietà della macchina virtuale.
2. In **Operazioni** selezionare **Ripristino di emergenza**.
3. In **Informazioni di base** > **Area di destinazione** selezionare l'area in cui eseguire la replica della macchina virtuale. Le aree di origine e di destinazione devono risiedere nello stesso tenant di Azure Active Directory.
4. Fare clic su **Rivedi e avvia replica**.

    :::image type="content" source="./media/tutorial-disaster-recovery/disaster-recovery.png" alt-text="Abilitare la replica nella pagina delle proprietà della macchina virtuale per il ripristino di emergenza.":::

5. In **Rivedi e avvia replica** verificare le impostazioni:

    - **Impostazioni di destinazione**. Per impostazione predefinita, Site Recovery esegue il mirroring delle impostazioni di origine per creare le risorse di destinazione.
    - **Impostazioni di archiviazione-Account di archiviazione della cache**. Site Recovery usa un account di archiviazione nell'area di origine. Le modifiche alla macchina virtuale di origine vengono memorizzate nella cache di questo account, prima di essere replicate nell'area di destinazione.
    - **Impostazioni di archiviazione-Disco di replica**. Per impostazione predefinita, Site Recovery crea dischi gestiti di replica nell'area di destinazione per il mirroring dei dischi gestiti della macchina virtuale di origine con lo stesso tipo di archiviazione (Standard o Premium).
    - **Impostazioni di replica**. Mostra i dettagli dell'insieme di credenziali e indica che i punti di ripristino creati da Site Recovery vengono conservati per 24 ore.
    - **Impostazioni estensione**. Indica che Site Recovery gestisce gli aggiornamenti all'estensione Servizio di mobilità di Site Recovery installata nelle macchine virtuali di cui si esegue la replica. L'account di Automazione di Azure indicato gestisce il processo di aggiornamento.

    :::image type="content" source="./media/tutorial-disaster-recovery/settings-summary.png" alt-text="Pagina che mostra il riepilogo delle impostazioni di destinazione e di replica.":::

2. Selezionare **Avvia replica**. La distribuzione viene avviata e Site Recovery inizia a creare risorse di destinazione. È possibile monitorare lo stato di avanzamento nelle notifiche.

    :::image type="content" source="./media/tutorial-disaster-recovery/notifications.png" alt-text="Notifica per lo stato di avanzamento della replica.":::

## <a name="check-vm-status"></a>Controllare lo stato della macchina virtuale

Al termine del processo di replica, è possibile controllare lo stato della replica della macchina virtuale.

1. Apertura della pagina delle proprietà della macchina virtuale.
2. In **Operazioni** selezionare **Ripristino di emergenza**.
3. Espandere la sezione **Informazioni di base** per esaminare le impostazioni predefinite relative all'insieme di credenziali, ai criteri di replica e alle impostazioni di destinazione.
4. In **Integrità e stato** vengono mostrate le informazioni sullo stato di replica per la macchina virtuale, la versione dell'agente, l'idoneità per il failover e i punti di ripristino più recenti. 

    :::image type="content" source="./media/tutorial-disaster-recovery/essentials.png" alt-text="Visualizzazione Informazioni di base per il ripristino di emergenza della macchina virtuale.":::

5. In **Visualizzazione dell'infrastruttura** è possibile ottenere una panoramica visiva delle macchine virtuali di origine e di destinazione, dei dischi gestiti e dell'account di archiviazione della cache.

    :::image type="content" source="./media/tutorial-disaster-recovery/infrastructure.png" alt-text="Mappa visiva dell'infrastruttura per il ripristino di emergenza della macchina virtuale.":::


## <a name="run-a-drill"></a>Eseguire un'esercitazione

Eseguire un'esercitazione per verificare che il ripristino di emergenza funzioni come previsto. Quando si esegue un failover di test, viene creata una copia della macchina virtuale, senza alcun impatto sulla replica in corso o sull'ambiente di produzione. 

1. Nella pagina del ripristino di emergenza della macchina virtuale selezionare **Failover di test**.
2. In **Failover di test** lasciare l'impostazione predefinita **Elaborato più recente (RPO basso**) per il punto di ripristino.

   Questa opzione fornisce l'obiettivo del punto di ripristino (RPO) più basso e, in genere, la creazione più rapida della macchina virtuale di destinazione. Prima vengono elaborati tutti i dati inviati al servizio Site Recovery per creare un punto di ripristino per ogni macchina virtuale e quindi viene eseguito il failover in tale punto di ripristino. Per questo punto di ripristino, tutti i dati vengono replicati in Site Recovery da quando è stato attivato il failover.

3. Selezionare la rete virtuale in cui risiederà la macchina virtuale dopo il failover. 

     :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-settings.png" alt-text="Pagina per impostare le opzioni del failover di test.":::

4. Viene avviato il processo di failover di test. È possibile monitorare lo stato di avanzamento nelle notifiche.

    :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-notification.png" alt-text="Notifiche del failover di test."::: 
    
   Al termine del failover di test, la macchina virtuale si trova nello stato *Pulizia del failover di test in sospeso* nella pagina **Informazioni di base**. 



## <a name="clean-up-resources"></a>Pulire le risorse

La macchina virtuale viene pulita automaticamente da Site Recovery dopo l'esercitazione.

1. Per avviare la pulizia automatica, selezionare **Pulisci failover di test**.

    :::image type="content" source="./media/tutorial-disaster-recovery/start-cleanup.png" alt-text="Avviare la pulizia nella pagina Informazioni di base."::: 

2. In **Pulizia del failover di test** digitare le eventuali note da registrare per il failover e quindi selezionare **Il test è stato completato. Eliminare la macchina virtuale del failover di test**. Selezionare **OK**.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test.png" alt-text="Pagina per registrare le note ed eliminare la macchina virtuale di test."::: 

7. Viene avviato il processo di eliminazione. È possibile monitorare lo stato di avanzamento nelle notifiche.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test-notification.png" alt-text="Notifiche per il monitoraggio dell'eliminazione della macchina virtuale di test."::: 

### <a name="stop-replicating-the-vm"></a>Arrestare la replica della macchina virtuale

Dopo aver completato un'esercitazione sul ripristino di emergenza, è consigliabile continuare per provare a eseguire un failover completo. Se non si vuole eseguire un failover completo, è possibile disabilitare la replica. Vengono eseguite le operazioni seguenti:

- Rimuove la macchina virtuale dall'elenco delle macchine virtuali replicate di Site Recovery.
- La fatturazione di Site Recovery per la macchina virtuale viene arrestata.
- Viene eseguita la pulizia automatica delle impostazioni della replica di origine.

Per arrestare la replica, seguire questa procedura:

1. Nella pagina del ripristino di emergenza della macchina virtuale selezionare **Disabilita replica**.
2. In **Disabilita replica** selezionare i motivi per cui si vuole disabilitare la replica. Selezionare **OK**.

    :::image type="content" source="./media/tutorial-disaster-recovery/disable-replication.png" alt-text="Pagina per disabilitare la replica e specificare un motivo."::: 


L'estensione Site Recovery installata nella macchina virtuale durante la replica non viene rimossa automaticamente. Se si disabilita la replica per la macchina virtuale e non si vuole replicarla di nuovo in un secondo momento, è possibile rimuovere manualmente l'estensione Site Recovery, come indicato di seguito: 

1. Passare alla macchina virtuale > **Impostazioni** > **Estensioni**.
2. Nella pagina **Estensioni** selezionare ogni voce *Microsoft.Azure.RecoveryServices* per Linux.
3. Nella pagina delle proprietà per l'estensione selezionare **Disinstalla**.

   :::image type="content" source="./media/tutorial-disaster-recovery/uninstall-extension.png" alt-text="Pagina per disinstallare l'estensione macchina virtuale Site Recovery.":::



## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato il ripristino di emergenza per una macchina virtuale di Azure ed è stata eseguita un'esercitazione per il ripristino di emergenza. A questo punto, è possibile eseguire un failover completo per la macchina virtuale.

> [!div class="nextstepaction"]
> [Effettuare il failover di una macchina virtuale in un'altra area](../../site-recovery/azure-to-azure-tutorial-dr-drill.md)
