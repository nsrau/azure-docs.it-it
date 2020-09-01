---
title: Gestire e monitorare i backup degli agenti MARS
description: Informazioni su come gestire e monitorare i backup degli agenti Servizi di ripristino di Microsoft Azure (MARS) usando il servizio backup di Azure.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: b3b648ca27a407640b42932fe2ed7c32f5109114
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2020
ms.locfileid: "89145570"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Gestire i backup degli agenti Servizi di ripristino di Microsoft Azure (MARS) con il servizio backup di Azure

Questo articolo descrive come gestire i file e le cartelle di cui viene eseguito il backup con l'agente di Servizi di ripristino di Microsoft Azure.

## <a name="modify-a-backup-policy"></a>Modificare un criterio di backup

Quando si modificano i criteri di backup, è possibile aggiungere nuovi elementi, rimuovere gli elementi esistenti dal backup o escludere i file dal backup usando le impostazioni di esclusione.

- **Aggiungi elementi** usare questa opzione solo per aggiungere nuovi elementi per il backup. Per rimuovere gli elementi esistenti, utilizzare l'opzione **Rimuovi elementi** o **impostazioni di esclusione** .  
- **Rimuovi elementi** usare questa opzione per rimuovere gli elementi dal backup.
  - Usare **le impostazioni di esclusione** per rimuovere tutti gli elementi all'interno di un volume anziché **rimuovere gli elementi**.
  - La cancellazione di tutte le selezioni in un volume comporta la conservazione dei backup obsoleti degli elementi in base alle impostazioni di conservazione al momento dell'ultimo backup, senza l'ambito della modifica.
  - Riselezionando questi elementi, viene effettuato un primo backup completo e le nuove modifiche dei criteri non vengono applicate ai backup precedenti.
  - La deselezione di un intero volume mantiene il backup passato senza alcun ambito per la modifica dei criteri di conservazione.
- **Impostazioni di esclusione** usare questa opzione per escludere elementi specifici dal backup.

### <a name="add-new-items-to-existing-policy"></a>Aggiungi nuovi elementi a criteri esistenti

1. In **azioni**selezionare **Pianifica backup**.

    ![Pianificare un backup di Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

2. Nella scheda **selezionare l'elemento dei criteri** selezionare **Modifica pianificazione backup per i file e le cartelle** e selezionare **Avanti**.

    ![Seleziona elementi criterio](./media/backup-azure-manage-mars/select-policy-items.png)

3. Nella scheda **modifica o Interrompi pianificazione backup** selezionare **apportare modifiche a elementi di backup o ora** e selezionare **Avanti**.

    ![Modificare o pianificare il backup](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Nella scheda **selezionare gli elementi da** sottoporre a backup selezionare **Aggiungi elementi** per aggiungere gli elementi di cui si desidera eseguire il backup.

    ![Modificare o pianificare l'aggiunta di elementi di backup](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. Nella finestra **Seleziona elementi** selezionare i file o le cartelle che si desidera aggiungere e selezionare **OK**.

    ![Selezionare gli elementi](./media/backup-azure-manage-mars/select-item.png)

6. Completare i passaggi successivi e selezionare **fine** per completare l'operazione.

### <a name="add-exclusion-rules-to-existing-policy"></a>Aggiungere regole di esclusione a criteri esistenti

È possibile aggiungere regole di esclusione per ignorare i file e le cartelle di cui non si vuole eseguire il backup. Questa operazione può essere eseguita durante la definizione di un nuovo criterio o la modifica di un criterio esistente.

1. Nel riquadro azioni selezionare **Pianifica backup**. Passare a **selezionare gli elementi di cui eseguire il backup** e selezionare **impostazioni di esclusione**.

    ![Impostazioni di esclusione](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. In **impostazioni di esclusione**selezionare **Aggiungi esclusione**.

    ![Aggiungi esclusione](./media/backup-azure-manage-mars/add-exclusion.png)

3. Da **selezionare gli elementi da escludere**, sfogliare i file e le cartelle e selezionare gli elementi che si desidera escludere e selezionare **OK**.

    ![Selezionare gli elementi da escludere](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Per impostazione predefinita, tutte le **sottocartelle** all'interno delle cartelle selezionate sono escluse. È possibile modificare questa impostazione selezionando **Sì** o **No**. È possibile modificare e specificare i tipi di file da escludere come illustrato di seguito:

    ![Selezione tipi sottocartella](./media/backup-azure-manage-mars/subfolders-type.png)

5. Completare i passaggi successivi e selezionare **fine** per completare l'operazione.

### <a name="remove-items-from-existing-policy"></a>Rimuovi elementi dal criterio esistente

1. Nel riquadro azioni selezionare **Pianifica backup**. Passare a **selezionare gli elementi di cui eseguire il backup**. Nell'elenco selezionare i file e le cartelle che si desidera rimuovere dalla pianificazione del backup e selezionare **Rimuovi elementi**.

    ![Selezionare gli elementi da rimuovere](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Quando si rimuove completamente un volume dal criterio, procedere con cautela.  Se è necessario aggiungerlo di nuovo, verrà considerato come un nuovo volume. Il backup pianificato successivo eseguirà un backup iniziale (backup completo) invece del backup incrementale. Se è necessario rimuovere e aggiungere temporaneamente elementi in un secondo momento, è consigliabile usare **le impostazioni di esclusione** anziché **rimuovere gli elementi** per assicurare il backup incrementale anziché il backup completo.

2. Completare i passaggi successivi e selezionare **fine** per completare l'operazione.

## <a name="stop-protecting-files-and-folder-backup"></a>Arrestare la protezione dei file e del backup delle cartelle

Esistono due modi per arrestare la protezione del backup di file e cartelle:

- **Arrestare la protezione e conservare i dati di backup**.
  - Questa opzione consente di arrestare tutti i processi di backup futuri dalla protezione dati.
  - Il servizio backup di Azure continuerà a mantenere tutti i punti di ripristino esistenti.  
  - Sarà possibile ripristinare i dati di cui è stato eseguito il backup per i punti di ripristino non scaduti.
  - Se si decide di riprendere la protezione, è possibile usare l'opzione *riabilita pianificazione backup* . Successivamente, i dati verranno conservati in base ai nuovi criteri di conservazione.
- **Arrestare la protezione ed eliminare i dati di backup**.
  - Questa opzione consente di arrestare tutti i processi di backup futuri per proteggere i dati ed eliminare tutti i punti di ripristino.
  - Si riceverà un messaggio di posta elettronica di avviso per l'eliminazione dei dati di backup con un messaggio che *i dati per questo elemento di backup sono stati eliminati. Questi dati saranno temporaneamente disponibili per 14 giorni, dopo i quali verranno eliminati definitivamente* e l'azione consigliata *riproteggere l'elemento di backup entro 14 giorni per ripristinare i dati.*
  - Per riprendere la protezione, riproteggere entro 14 giorni dall'operazione di eliminazione.

### <a name="stop-protection-and-retain-backup-data"></a>Arrestare la protezione e conservare i dati di backup

1. Aprire la console di gestione di MARS, passare al **riquadro azioni**e **selezionare Pianifica backup**.

    ![Selezione pianificazione backup](./media/backup-azure-manage-mars/mars-actions.png)
1. Nella pagina **Selezione elemento criteri** selezionare **Modifica pianificazione backup per i file e le cartelle** e selezionare **Avanti**.

    ![Selezionare l'elemento criteri](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Nella pagina **modificare o arrestare un backup pianificato** selezionare **Interrompi utilizzando questa pianificazione del backup, ma Mantieni i backup archiviati fino a quando non viene attivata nuovamente una pianificazione**. Quindi selezionare **Avanti**.

    ![Arrestare un backup pianificato.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. In **Sospendi backup pianificato**esaminare le informazioni e selezionare **fine**.

    ![Sospendere un backup pianificato.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. In **modifica stato backup**verificare che la pianificazione della sospensione del backup sia in stato di esito positivo e selezionare **Chiudi** per terminare.

### <a name="stop-protection-and-delete-backup-data"></a>Arrestare la protezione ed eliminare i dati di backup

1. Aprire la console di gestione di MARS, passare al riquadro **azioni** e selezionare **Pianifica backup**.
2. Nella pagina **modificare o arrestare un backup pianificato** selezionare **Interrompi utilizzo di questa pianificazione del backup ed eliminare tutti i backup archiviati**. Quindi selezionare **Avanti**.

    ![Modificare o arrestare un backup pianificato.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Nella pagina **Arresta un backup pianificato** selezionare **fine**.

    ![Arrestare un backup pianificato e selezionare fine](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Viene richiesto di immettere un PIN di sicurezza (Personal Identification Number), che è necessario generare manualmente. A tale scopo, accedere prima al portale di Azure.
5. Passare a impostazioni dell'insieme di credenziali di **servizi di ripristino**  >  **Settings**  >  **Proprietà**.
6. In **pin di sicurezza**selezionare **genera**. Copiare questo PIN. Il PIN è valido solo per cinque minuti.
7. Nella console di gestione incollare il PIN e quindi fare clic su **OK**.

    ![Generare un PIN di sicurezza.](./media/backup-azure-delete-vault/security-pin.png)

8. Nella pagina **modifica stato backup** viene visualizzato il messaggio seguente: i *dati di backup eliminati verranno conservati per 14 giorni. Dopo tale periodo, i dati di backup verranno eliminati definitivamente.*  

    ![Modificare lo stato di avanzamento del backup](./media/backup-azure-delete-vault/deleted-backup-data.png)

Dopo aver eliminato gli elementi di backup locali, seguire i passaggi successivi dal portale.

## <a name="re-enable-protection"></a>Abilitare di nuovo la protezione

Se la protezione è stata interrotta durante la conservazione dei dati e si è deciso di riprendere la protezione, è possibile riattivare la pianificazione del backup utilizzando modifica criteri di backup.

1. In **azioni** selezionare **Pianifica backup**.
1. Selezionare **riabilita pianificazione backup. È anche possibile modificare gli elementi o le ore di backup e fare** clic su **Avanti**.<br>

    ![Riattivare la pianificazione del backup](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. In **Seleziona elementi di cui eseguire il backup**selezionare **Avanti**.

    ![Selezionare gli elementi di cui eseguire il backup](./media/backup-azure-manage-mars/re-enable-next.png)
1. In **specificare la pianificazione del backup**specificare la pianificazione del backup e fare clic su **Avanti**.
1. In **Seleziona criteri di conservazione**specificare durata conservazione e selezionare **Avanti**.
1. Infine, nella schermata di **conferma** esaminare i dettagli dei criteri e selezionare **fine**.

## <a name="re-generate-passphrase"></a>Genera di nuovo passphrase

Viene usata una passphrase per crittografare e decrittografare i dati durante il backup o il ripristino del computer locale o locale usando l'agente MARS da o verso Azure. Se la passphrase è stata persa o dimenticata, è possibile rigenerare la passphrase (purché il computer sia ancora registrato con l'insieme di credenziali di servizi di ripristino e il backup sia configurato) attenendosi alla procedura seguente:

1. Dalla console dell'agente Mars passare al **riquadro azioni**  >  **modifica proprietà** >. Passare quindi alla **scheda crittografia**.<br>
1. Selezionare la casella di controllo **Cambia passphrase** .<br>
1. Immettere una nuova passphrase o selezionare **genera passphrase**.
1. Selezionare **Sfoglia** per salvare la nuova passphrase.

    ![Genera passphrase.](./media/backup-azure-manage-mars/passphrase.png)

1. Selezionare **OK** per applicare le modifiche.  Se la [funzionalità di sicurezza](./backup-azure-security-feature.md#enable-security-features) è abilitata nel portale di Azure per l'insieme di credenziali di servizi di ripristino, verrà richiesto di immettere il pin di sicurezza. Per ricevere il PIN, seguire i passaggi elencati in questo [articolo](./backup-azure-security-feature.md#authentication-to-perform-critical-operations).<br>
1. Incollare il PIN di sicurezza dal portale e selezionare **OK** per applicare le modifiche.<br>

    ![Incollare il PIN di sicurezza](./media/backup-azure-manage-mars/passphrase2.png)
1. Verificare che la passphrase venga salvata in modo sicuro in un percorso alternativo (diverso dalla macchina di origine), preferibilmente nella Azure Key Vault. Tenere traccia di tutte le passphrase se è stato eseguito il backup di più computer con gli agenti MARS.

## <a name="managing-backup-data-for-unavailable-machines"></a>Gestione dei dati di backup per computer non disponibili

Questa sezione illustra uno scenario in cui la macchina virtuale di origine protetta con MARS non è più disponibile perché è stata eliminata, danneggiata, infettata da malware/ransomware o è stata rilasciata.

Per questi computer, il servizio backup di Azure garantisce che il punto di ripristino più recente non scada (ovvero non viene eliminato) in base alle regole di conservazione specificate nei criteri di backup. Pertanto, è possibile ripristinare in modo sicuro il computer.  Si considerino gli scenari seguenti che è possibile eseguire sui dati di cui è stato eseguito il backup:

### <a name="scenario-1-the-source-machine-is-unavailable-and-you-no-longer-need-to-retain-backup-data"></a>Scenario 1: il computer di origine non è disponibile e non è più necessario mantenere i dati di backup

- È possibile eliminare i dati di cui è stato eseguito il backup dal portale di Azure usando la procedura descritta in [questo articolo](backup-azure-delete-vault.md#delete-protected-items-on-premises).

### <a name="scenario-2-the-source-machine-is-unavailable-and-you-need-to-retain-backup-data"></a>Scenario 2: il computer di origine non è disponibile ed è necessario conservare i dati di backup

La gestione dei criteri di backup per MARS viene eseguita tramite la console MARS e non tramite il portale. Se è necessario estendere le impostazioni di conservazione per i punti di ripristino esistenti prima della scadenza, è necessario ripristinare il computer, installare la console di MARS ed estendere il criterio.

- Per ripristinare il computer, seguire questa procedura:
  1. [Ripristinare la macchina virtuale in un computer di destinazione alternativo](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
  1. Ricreare il computer di destinazione con lo stesso nome host del computer di origine
  1. Installare l'agente e ripetere la registrazione nello stesso insieme di credenziali e con la stessa passphrase
  1. Avviare il client MARS per estendere la durata di conservazione in base ai requisiti
- Il computer appena ripristinato, protetto con MARS, continuerà a eseguire i backup.  

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sugli scenari e le limitazioni supportati, vedere la [matrice di supporto per l'agente Mars](./backup-support-matrix-mars-agent.md).
- Altre informazioni sul [comportamento di conservazione dei criteri di backup su richiesta](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior).
- Per domande più frequenti, vedere domande [frequenti sull'agente Mars](backup-azure-file-folder-backup-faq.md).
