---
title: Eseguire il backup dello stato del sistema Windows in Azure
description: Informazioni su come eseguire il backup dello stato del sistema dei computer Windows Server in Azure.
ms.topic: conceptual
ms.date: 05/23/2018
ms.openlocfilehash: 1b3573d757d2f7b1ffec9ae718aa791488960f3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91332891"
---
# <a name="back-up-windows-system-state-to-azure"></a>Eseguire il backup dello stato del sistema Windows in Azure

Questo articolo illustra come eseguire il backup dello stato del sistema Windows Server in Azure. Il suo scopo è quello di illustrare le nozioni di base.

Per altre informazioni su Backup di Azure, vedere questa [panoramica](backup-overview.md).

Se non è disponibile una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) che consente di accedere a qualsiasi servizio di Azure.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy-for-the-vault"></a>Impostare la ridondanza di archiviazione dell'insieme di credenziali

Quando si crea un insieme di credenziali dei Servizi di ripristino, assicurarsi che la ridondanza di archiviazione sia configurata in base alle proprie esigenze.

1. Dal riquadro insiemi di credenziali dei **servizi di ripristino** selezionare il nuovo insieme di credenziali.

    ![Selezionare il nuovo insieme di credenziali dall'elenco corrispondente per Servizi di ripristino](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Quando si seleziona l'insieme di credenziali, il riquadro dell'insieme di credenziali di **servizi di ripristino** si restringe e il riquadro Impostazioni (*che ha il nome dell'insieme di credenziali nella parte superiore*) e il riquadro dei dettagli dell'insieme di credenziali sono aperti.

    ![Visualizzare la configurazione dell'archiviazione per il nuovo insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. Nel riquadro impostazioni del nuovo insieme di credenziali usare la diapositiva verticale per scorrere verso il basso fino alla sezione Gestisci e selezionare **infrastruttura di backup**.
    Verrà visualizzato il riquadro infrastruttura di backup.
3. Nel riquadro infrastruttura di backup selezionare **configurazione backup** per aprire il riquadro **configurazione backup** .

    ![Impostare la configurazione dell'archiviazione per il nuovo insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Scegliere l'opzione di replica di archiviazione appropriata per l'insieme di credenziali.

    ![Opzioni di configurazione dell'archiviazione](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration-for-vault.png)

    Per impostazione predefinita, l'insieme di credenziali prevede l'archiviazione con ridondanza geografica. Se si usa Azure come endpoint di archiviazione di backup primario, continuare a usare l'opzione **Con ridondanza geografica**. Se non si usa Azure come endpoint di archiviazione di backup primario, scegliere l'opzione **Con ridondanza locale**, che riduce i costi di archiviazione di Azure. Per altre informazioni sulle opzioni di archiviazione con ridondanza [geografica](../storage/common/storage-redundancy.md#geo-redundant-storage)e con ridondanza [locale](../storage/common/storage-redundancy.md#locally-redundant-storage) e con [ridondanza della zona](../storage/common/storage-redundancy.md#zone-redundant-storage) , vedere la [Panoramica della ridondanza di archiviazione](../storage/common/storage-redundancy.md)

Dopo aver creato un insieme di credenziali, configurarlo per il backup dello stato del sistema Windows.

## <a name="configure-the-vault"></a>Configurare l'insieme di credenziali

1. Nel riquadro dell'insieme di credenziali di servizi di ripristino (per l'insieme di credenziali appena creato), nella sezione Introduzione selezionare **backup**, quindi nel riquadro **Introduzione con backup** selezionare **obiettivo del backup**.

    ![Apri impostazioni backup](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    Verrà visualizzato il riquadro **obiettivo del backup** .

    ![Apri riquadro obiettivo di backup](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. Scegliere **Locale** dal menu a discesa **Posizione di esecuzione del carico di lavoro**.

    Si sceglie **locale** perché il computer Windows Server o Windows è un computer fisico che non si trova in Azure.

3. Scegliere lo **stato del sistema**dal menu **di cui si desidera eseguire il backup?** e selezionare **OK**.

    ![Configurazione di file e cartelle](./media/backup-azure-system-state/backup-goal-system-state.png)

    Dopo aver selezionato **OK**, viene visualizzato un segno di spunta accanto a **obiettivo di backup**e viene aperto il riquadro **prepara infrastruttura** .

    ![Preparare l'infrastruttura dopo aver configurato l'obiettivo del backup](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. Nel riquadro **prepara infrastruttura** selezionare **Scarica agente per Windows Server o client Windows**.

    ![Preparare l'infrastruttura](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Se si usa Windows Server Essential, scegliere di scaricare l'agente per Windows Server Essential. Un menu a comparsa chiederà di eseguire o salvare MARSAgentInstaller.exe.

    ![Finestra di dialogo di MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. Nel menu a comparsa Scarica selezionare **Salva**.

    Per impostazione predefinita, il file **MARSagentinstaller.exe** viene salvato nella cartella Downloads. Al termine del programma di installazione verrà visualizzato un messaggio popup che chiede se si vuole eseguire il programma di installazione o aprire la cartella.

    ![Programma di installazione MARS completato](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    Non è ancora necessario installare l'agente. È possibile installare l'agente dopo aver scaricato le credenziali dell'insieme di credenziali.

6. Nel riquadro **prepara infrastruttura** selezionare **Scarica**.

    ![Scaricare le credenziali dell'insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    Le credenziali dell'insieme di credenziali vengono scaricate nella cartella **Downloads** . Al termine del download delle credenziali dell'insieme di credenziali verrà visualizzato un messaggio popup che chiede se si desidera aprire o salvare le credenziali. Selezionare **Salva**. Se si seleziona accidentalmente **Apri**, consentire alla finestra di dialogo che tenta di aprire le credenziali dell'insieme di credenziali di non riuscire. Non sarà possibile aprire le credenziali dell'insieme di credenziali. Continuare con il passaggio successivo. Le credenziali dell'insieme di credenziali si trovano nella cartella **Downloads** .

    ![Il download delle credenziali dell'insieme di credenziali è terminato](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)
   > [!NOTE]
   > Le credenziali dell'insieme di credenziali devono essere salvate solo in un percorso locale nel server Windows in cui si intende usare l'agente.
   >

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>Installare e registrare l'agente

> [!NOTE]
> L'abilitazione del backup tramite il portale di Azure non è disponibile. Usare l'agente di Servizi di ripristino di Microsoft Azure per eseguire il backup dello stato del sistema Windows Server.
>

1. Cercare e fare doppio clic sul file **MARSagentinstaller.exe** nella cartella Downloads o nella cartella in cui è stato salvato.

    Il programma di installazione visualizzerà una serie di messaggi durante l'estrazione, l'installazione e la registrazione dell'agente di Servizi di ripristino.

    ![Eseguire il programma di installazione dell'agente di Servizi di ripristino](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Completare l'Installazione guidata di Agente servizi di ripristino di Microsoft Azure. Per completare la procedura guidata, è necessario:

   * Scegliere un percorso per la cartella di installazione e della cache.
   * Fornire le informazioni sul server proxy se si usa un server proxy per connettersi a Internet.
   * Se si usa un proxy autenticato, immettere il nome utente e la password.
   * Fornire le credenziali dell'insieme di credenziali scaricate.
   * Salvare la passphrase di crittografia in un luogo sicuro.

     > [!NOTE]
     > Se la passphrase viene persa o dimenticata, Microsoft non può contribuire a recuperare i dati di backup. Salvare il file in una posizione sicura. È necessario ripristinare un backup.
     >
     >

L'agente ora è installato e il computer è registrato nell'insieme di credenziali. Ora è possibile configurare e pianificare il backup.

## <a name="back-up-windows-server-system-state"></a>Eseguire un backup dello stato del sistema Windows Server

Il backup iniziale comprende due attività:

* Pianificare il backup
* Eseguire il backup dello stato del sistema per la prima volta

Per completare il backup iniziale, usare l'agente di Servizi di ripristino di Microsoft Azure.

> [!NOTE]
> È possibile eseguire il backup dello stato del sistema in Windows Server 2008 R2 tramite Windows Server 2016. Il backup dello stato del sistema non è supportato negli SKU client. Lo stato del sistema non viene visualizzato come opzione per i client Windows o per i computer Windows Server 2008 SP2.
>
>

### <a name="to-schedule-the-backup-job"></a>Per pianificare il processo di backup

1. Aprire l'agente di Servizi di ripristino di Microsoft Azure. È possibile trovarlo se si cerca **Backup di Microsoft Azure**nel computer.

    ![Avviare l'agente di Servizi di ripristino di Azure](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. Nell'agente di servizi di ripristino selezionare **Pianifica backup**.

    ![Pianificare un backup di Windows Server](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. Nella pagina attività **iniziali** della pianificazione guidata backup selezionare **Avanti**.

4. Nella pagina **Seleziona elementi per il backup** selezionare **Aggiungi elementi**.

5. Selezionare **stato del sistema** , quindi fare clic su **OK**.

6. Selezionare **Avanti**.

7. Nelle pagine successive, selezionare la frequenza e i criteri di conservazione del backup per i backup dello stato del sistema.

8. Nella pagina Conferma esaminare le informazioni e quindi fare clic su **fine**.

9. Dopo aver creato la pianificazione del backup tramite la procedura guidata, fare clic su **Chiudi**.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>Eseguire il backup dello stato del sistema Windows Server per la prima volta

1. Verificare che non siano presenti aggiornamenti in sospeso di Windows Server che richiedono un riavvio.

2. Nell'agente di servizi di ripristino selezionare **Esegui backup** per completare il seeding iniziale sulla rete.

    ![Backup di Windows Server ora](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. Selezionare **stato del sistema** nella schermata **Selezione elemento di backup** visualizzata e selezionare **Avanti**.

4. Nella pagina Conferma riesaminare le impostazioni che l'Esecuzione guidata backup userà per il backup del computer. Selezionare quindi **Backup**.

5. Fare clic su **Chiudi** per chiudere la procedura guidata. Se si chiude la procedura guidata prima che venga completato il processo di backup, l'esecuzione guidata proseguirà in background.
    > [!NOTE]
    > L'agente MARS viene attivato `SFC /verifyonly` come parte delle verifiche preliminari prima di ogni backup dello stato del sistema. In questo modo si garantisce che i file di cui è stato eseguito il backup come parte dello stato del sistema abbiano le versioni corrette corrispondenti alla versione di Windows. Altre informazioni su controllo file di sistema (SFC) in [questo articolo](/windows-server/administration/windows-commands/sfc).
    >

Al termine del backup iniziale, nella console Backup comparirà lo stato **Processo completato** .

  ![Completamento infrarossi](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Domande?

Per eventuali domande, [inviare commenti e suggerimenti](https://feedback.azure.com/forums/258995-azure-backup).

## <a name="next-steps"></a>Passaggi successivi

* Sono disponibili altre informazioni sul [backup di computer Windows](backup-windows-with-mars-agent.md).
* Dopo aver eseguito il backup dello stato del sistema Windows Server, è possibile [gestire l'insieme di credenziali e i server](backup-azure-manage-windows-server.md).
* Se è necessario ripristinare un backup, usare questo articolo per [ripristinare i file in un computer Windows](backup-azure-restore-windows-server.md).
