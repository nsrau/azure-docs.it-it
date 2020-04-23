---
title: Gestire e monitorare i backup dell'agente MARS
description: Informazioni su come gestire e monitorare i backup dell'agente di Microsoft Azure Recovery Services (MARS) usando il servizio Backup di Azure.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: a88ec4dc9283114e06eed424172dbb958850c2e9
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025102"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Gestire i backup dell'agente di Microsoft Azure Recovery Services (MARS) tramite il servizio Backup di AzureManage Microsoft Azure Recovery Services (MARS) Agent backups by using the Azure Backup service

In questo articolo viene descritto come gestire i file e le cartelle di cui viene eseguito il backup con l'agente dei servizi di ripristino di Microsoft Azure.This article describes how to manage files and folders that are backed up with the Microsoft Azure Recovery Services Agent.

## <a name="modify-a-backup-policy"></a>Modificare un criterio di backup

Quando si modificano i criteri di backup, è possibile aggiungere nuovi elementi, rimuovere elementi esistenti dal backup o escludere i file dal backup utilizzando Impostazioni di esclusione.

- **Aggiungi elementi** utilizzare questa opzione solo per aggiungere nuovi elementi di cui eseguire il backup. Per rimuovere elementi esistenti, utilizzare l'opzione **Rimuovi elementi** o Impostazioni **di esclusione.**  
- **Rimuovi elementi** utilizzare questa opzione per rimuovere gli elementi dal backup.
  - Utilizzare **Impostazioni di esclusione** per rimuovere tutti gli elementi all'interno di un volume anziché **Rimuovi elementi**.
  - La cancellazione di tutte le selezioni in un volume fa sì che i vecchi backup degli elementi, da conservare in base alle impostazioni di conservazione al momento dell'ultimo backup, senza l'ambito di modifica.
  - Se si riselezionano questi elementi, viene eseguito un primo backup completo e le nuove modifiche dei criteri non vengono applicate ai backup precedenti.
  - La deselezione dell'intero volume consente di mantenere il backup passato senza alcun ambito per la modifica dei criteri di conservazione.
- Le impostazioni di **esclusione** utilizzano questa opzione per escludere elementi specifici dal backup.

### <a name="add-new-items-to-existing-policy"></a>Aggiungere nuovi elementi ai criteri esistenti

1. In **Azioni**fare clic su **Pianifica backup**.

    ![Pianificare un backup di Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

2. Nella scheda **Seleziona elemento criterio** selezionare Modifica pianificazione backup per i file e le **cartelle** e fare clic su **Avanti**.

    ![Seleziona elementi criterio](./media/backup-azure-manage-mars/select-policy-items.png)

3. Nella scheda **Modifica o interrompi backup** di pianificazione selezionare Apporta modifiche agli elementi o agli orari di **backup** e fare clic su **Avanti**.

    ![Modificare o pianificare il backup](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Nella scheda **Seleziona elementi da eseguire** il backup fare clic su Aggiungi **elementi** per aggiungere gli elementi di cui si desidera eseguire il backup.

    ![Modificare o pianificare elementi aggiuntivi di backup](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. Nella finestra **Seleziona elementi** selezionare i vocaboli o le cartelle che si desidera aggiungere e fare clic su **OK**.

    ![Selezionare gli elementi](./media/backup-azure-manage-mars/select-item.png)

6. Completare i passaggi successivi e fare clic su **Fine** per completare l'operazione.

### <a name="add-exclusion-rules-to-existing-policy"></a>Aggiungere regole di esclusione ai criteri esistenti

È possibile aggiungere regole di esclusione per ignorare i file e le cartelle di cui non si desidera eseguire il backup. È possibile eseguire questa operazione durante la definizione di un nuovo criterio o la modifica di un criterio esistente.

1. Nel riquadro Azioni fare clic su **Pianifica backup**. Passare a **Selezionare gli elementi da eseguire il backup** e fare clic su Impostazioni di **esclusione**.

    ![Selezionare gli elementi](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. In **Impostazioni di esclusione**fare clic su **Aggiungi esclusione**.

    ![Selezionare gli elementi](./media/backup-azure-manage-mars/add-exclusion.png)

3. Da **Seleziona elementi da escludere**, sfogliare i file e le cartelle e selezionare gli elementi che si desidera escludere e fare clic su **OK**.

    ![Selezionare gli elementi](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Per impostazione predefinita, tutte le **sottocartelle** all'interno delle cartelle selezionate sono escluse. È possibile modificare questa impostazione selezionando **Sì** o **No**. È possibile modificare e specificare i tipi di file da escludere come illustrato di seguito:

    ![Selezionare gli elementi](./media/backup-azure-manage-mars/subfolders-type.png)

5. Completare i passaggi successivi e fare clic su **Fine** per completare l'operazione.

### <a name="remove-items-from-existing-policy"></a>Rimuovere elementi dai criteri esistenti

1. Nel riquadro Azioni fare clic su **Pianifica backup**. Passare a **Selezionare gli elementi da Backup**. Nell'elenco selezionare i file e le cartelle che si desidera rimuovere dalla pianificazione del backup e fare clic su **Rimuovi elementi**.

    ![Selezionare gli elementi](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Procedere con cautela quando si rimuove completamente un volume dal criterio.  Se è necessario aggiungerlo di nuovo, verrà considerato come un nuovo volume. Il successivo backup pianificato eseguirà un backup iniziale (backup completo) anziché un backup incrementale. Se è necessario rimuovere temporaneamente e aggiungere elementi in un secondo momento, è consigliabile utilizzare **Impostazioni esclusioni** anziché **Rimuovi elementi** per garantire il backup incrementale anziché il backup completo.

2. Completare i passaggi successivi e fare clic su **Fine** per completare l'operazione.

## <a name="stop-protecting-files-and-folder-backup"></a>Interrompere la protezione del backup di file e cartelle

Esistono due modi per interrompere la protezione del backup di file e cartelle:

- **Arrestare la protezione e conservare i dati**di backup .
  - Questa opzione interromperà la protezione di tutti i processi di backup futuri.
  - Il servizio Backup di Azure continuerà a mantenere tutti i punti di ripristino esistenti.  
  - Sarà possibile ripristinare i dati di cui è stato eseguito il backup per i punti di ripristino non scaduti.
  - Se si decide di riprendere la protezione, è possibile utilizzare l'opzione *Riattiva pianificazione backup.* Dopo di che, i dati verrebbero conservati in base al nuovo criterio di conservazione.
- **Interrompere la protezione ed eliminare i dati di backup**.
  - Questa opzione impedirà a tutti i processi di backup futuri di proteggere i dati ed eliminare tutti i punti di ripristino.
  - Riceverai un'e-mail di avviso dati di backup con un messaggio *I tuoi dati per questo elemento di backup sono stati eliminati. Questi dati saranno temporaneamente disponibili per 14 giorni, dopo di che verranno eliminati definitivamente* e l'azione consigliata *Riproteggi l'elemento Backup entro 14 giorni per recuperare i dati.*
  - Per riprendere la protezione, riproteggere entro 14 giorni dall'operazione di eliminazione.

### <a name="stop-protection-and-retain-backup-data"></a>Arrestare la protezione e conservare i dati di backup

1. Aprire la console di gestione MARS, passare al **riquadro Azioni**e **selezionare Pianifica backup**.

    ![Modificare o arrestare un backup pianificato.](./media/backup-azure-manage-mars/mars-actions.png)
1. Nella pagina **Seleziona elemento criterio** selezionare Modifica pianificazione di backup per i file e le **cartelle** e fare clic su **Avanti**.

    ![Modificare o arrestare un backup pianificato.](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Nella pagina **Modifica o interrompi backup pianificato** selezionare Interrompi utilizzando questa pianificazione di **backup, ma mantenere i backup archiviati fino a quando non viene nuovamente attivata una pianificazione.** Quindi selezionare **Avanti**.

    ![Modificare o arrestare un backup pianificato.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. In **Sospendi backup pianificato** esaminare le informazioni e fare clic su **Fine**.

    ![Modificare o arrestare un backup pianificato.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. In **Modifica processo di backup** verificare che la pausa di backup della pianificazione sia in stato di esito positivo e fare clic su **Chiudi** per terminare.

### <a name="stop-protection-and-delete-backup-data"></a>Interrompere la protezione ed eliminare i dati di backup

1. Aprire la console di gestione MARS, passare al riquadro **Azioni** e selezionare **Pianifica backup**.
2. Nella pagina **Modifica o interrompi backup pianificato** selezionare Interrompi utilizzando questa pianificazione di backup ed eliminare tutti i backup **archiviati.** Quindi selezionare **Avanti**.

    ![Modificare o arrestare un backup pianificato.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Nella pagina **Interrompi backup pianificato** selezionare **Fine**.

    ![Interrompere un backup pianificato.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Viene richiesto di immettere un PIN di sicurezza (numero di identificazione personale), che è necessario generare manualmente. A tale scopo, accedere innanzitutto al portale di Azure.To do this, first sign in to the Azure portal.
5. Passare a**Proprietà****impostazioni** >  **dell'insieme di credenziali** > di Servizi di ripristino .
6. In **PIN di sicurezza**selezionare **Genera**. Copiare questo PIN. Il PIN è valido solo per cinque minuti.
7. Nella console di gestione incollare il PIN e quindi scegliere **OK**.

    ![Generare un PIN di sicurezza.](./media/backup-azure-delete-vault/security-pin.png)

8. Nella pagina **Modifica stato** di backup viene visualizzato il messaggio seguente: I dati di backup *eliminati verranno conservati per 14 giorni. Dopo tale periodo,* i dati di backup verranno eliminati definitivamente.  

    ![Eliminare l'infrastruttura di backup.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Dopo aver eliminato gli elementi di backup locale, seguire i passaggi successivi dal portale.

## <a name="re-enable-protection"></a>Riattivare la protezione

Se la protezione è stata interrotta durante la conservazione dei dati e si è deciso di riprendere la protezione, è possibile riattivare la pianificazione del backup utilizzando i criteri di modifica del backup.

1. In **Azioni** selezionare **Pianifica backup**.
1. Selezionare **Riabilita pianificazione backup. È inoltre possibile modificare gli elementi o gli orari** di backup e fare clic su **Avanti**.<br>

    ![Eliminare l'infrastruttura di backup.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. In **Seleziona elementi di cui eseguire il backup**fare clic su **Avanti**.

    ![Eliminare l'infrastruttura di backup.](./media/backup-azure-manage-mars/re-enable-next.png)
1. In **Specifica pianificazione backup**specificare la pianificazione del backup e fare clic su **Avanti**.
1. In **Seleziona criterio di conservazione**specificare la durata della conservazione e fare clic su **Avanti**.
1. Infine, nella schermata **Conferma,** esaminare i dettagli dei criteri e fare clic su **Fine**.

## <a name="re-generate-passphrase"></a>Rigenerare la passphrase

Una passphrase viene usata per crittografare e decrittografare i dati durante il backup o il ripristino del computer locale o locale usando l'agente MARS da o verso Azure.A passphrase is used to encrypt and decrypt data while backing up or restoring your on-premises or local machine using the MARS agent to or from Azure. Se la passphrase è stata persa o dimenticata, è possibile rigenerare la passphrase (a condizione che il computer sia ancora registrato nel Vault dei servizi di ripristino e il backup sia configurato) attenendosi alla seguente procedura:

- Dalla console dell'agente MARS, passare a **Actions Pane** > **Change properties** >. Quindi vai alla **scheda Crittografia**.<br>
- Selezionare la casella di controllo **Cambia passphrase.**<br>
- Immettere una nuova passphrase o fare clic su **Genera passphrase**.
- Fare clic su **Sfoglia** per salvare la nuova passphrase.

    ![Generare la passphrase.](./media/backup-azure-manage-mars/passphrase.png)
- Fai clic su **OK** per applicare le modifiche.  Se la funzionalità di [sicurezza](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#enable-security-features) è abilitata nel portale di Azure per l'insieme di credenziali dei servizi di ripristino, verrà richiesto di immettere il PIN di sicurezza. Per ricevere il PIN, attenersi alla procedura descritta in questo [articolo](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#authentication-to-perform-critical-operations).<br>
- Incollare il PIN di sicurezza dal portale e fare clic **su OK** per applicare le modifiche.<br>

    ![Generare la passphrase.](./media/backup-azure-manage-mars/passphrase2.png)
- Assicurarsi che la passphrase venga salvata in modo sicuro in un percorso alternativo (diverso dal computer di origine), preferibilmente nell'insieme di credenziali delle chiavi di Azure.Ensure that the passphrase is securely saved in an alternate location (other than the source machine), preferibilmente in the Azure Key Vault. Tenere traccia di tutte le passphrase se si dispone di più computer di cui viene eseguito il backup con gli agenti MARS.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sugli scenari e le limitazioni supportati, fare riferimento alla [matrice di supporto per l'agente MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent).
- Ulteriori informazioni sul comportamento di conservazione dei criteri di [backup su richiesta](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior).
