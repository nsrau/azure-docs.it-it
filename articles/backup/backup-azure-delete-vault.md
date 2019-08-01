---
title: Eliminare un insieme di credenziali di servizi di ripristino in Azure
description: Viene descritto come eliminare un insieme di credenziali di servizi di ripristino.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: dacurwin
ms.openlocfilehash: 34484c309cb186aabec519e54269fefae316165e
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639904"
---
# <a name="delete-a-recovery-services-vault"></a>Eliminare un insieme di credenziali dei servizi di ripristino

Questo articolo descrive come eliminare un insieme di credenziali di servizi di ripristino di [backup di Azure](backup-overview.md) . Contiene le istruzioni per rimuovere le dipendenze e quindi eliminare un insieme di credenziali.


## <a name="before-you-start"></a>Prima di iniziare

Non è possibile eliminare un insieme di credenziali di servizi di ripristino con dipendenze quali server protetti o server di gestione di backup associati all'insieme di credenziali.

- Non è possibile eliminare l'insieme di credenziali contenente i dati di backup, anche se la protezione è stata interrotta ma sono stati conservati i dati di backup.

- Se si elimina un insieme di credenziali che contiene dipendenze, verrà visualizzato l'errore seguente:

  ![Errore di eliminazione dell'insieme di credenziali](./media/backup-azure-delete-vault/error.png)

- Se si elimina un elemento protetto locale (MARS, MAB o DPM in Azure) dal portale che contiene le dipendenze, viene visualizzato un messaggio di avviso:

  ![Errore di eliminazione del server protetto](./media/backup-azure-delete-vault/error-message.jpg)

  
Per eliminare normalmente l'insieme di credenziali, scegliere lo scenario che corrisponde alla configurazione e seguire le procedure consigliate:

Scenario | Procedura per rimuovere le dipendenze per eliminare l'insieme di credenziali |
-- | --
Sono presenti file e cartelle locali protetti con l'agente di backup di Azure (MARS) che esegue il backup in Azure | Eseguire la procedura descritta in eliminare i dati di backup e gli elementi [di backup per l'agente Mars](#delete-backup-items-from-mars-management-console)
Sono presenti computer locali protetti con MAB (Server Backup di Microsoft Azure) o DPM in Azure (System Center Data Protection Manager) | Eseguire la procedura descritta in eliminare i dati di backup e gli elementi [di backup per l'agente MAB](#delete-backup-items-from-mabs-management-console)
Sono presenti elementi protetti nel cloud (ad esempio, VM laaS, condivisione file di Azure e così via)  | Eseguire la procedura descritta in eliminare i dati di backup e gli elementi di backup [per gli elementi protetti nel cloud](#delete-protected-items-in-cloud)
Sono presenti elementi protetti sia in locale che nel cloud | Eseguire la procedura descritta in eliminare i dati di backup e gli elementi di backup nella sequenza seguente: <br> - [Per gli elementi protetti nel cloud](#delete-protected-items-in-cloud)<br> - [Per l'agente MARS](#delete-backup-items-from-mars-management-console) <br> - [Per l'agente di MAB](#delete-backup-items-from-mabs-management-console)
Non sono presenti elementi protetti in locale o nel cloud; Tuttavia, ricevo ancora l'errore di eliminazione dell'insieme di credenziali | Eseguire la procedura descritta in eliminare l'insieme di credenziali di [servizi di ripristino usando Azure Resource Manager client](#delete-the-recovery-services-vault-using-azure-resource-manager-client)


## <a name="delete-protected-items-in-cloud"></a>Elimina elementi protetti nel cloud

Prima di continuare, leggere **[questa](#before-you-start)** sezione per comprendere le dipendenze e il processo di eliminazione dell'insieme di credenziali.

Per arrestare la protezione ed eliminare i dati di backup, eseguire le operazioni seguenti:

1. Dal portale >**gli elementi di backup** dell'insieme di credenziali di **Servizi** > di ripristino scegliere gli elementi protetti nel cloud (ad esempio, computer AzureVirtual, archiviazione di Azure (file di Azure), VM SQL di Azure e così via).

    ![Selezionare il tipo di backup](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Fare clic con il pulsante destro del mouse sull'elemento di backup, a seconda che l'elemento di backup sia protetto o meno, nel menu verrà visualizzato **Interrompi backup** o **Elimina dati di backup**.

    - Per **Interrompi backup**selezionare **Elimina dati di backup** dall'elenco a discesa. Immettere il **nome** dell'elemento di backup (con distinzione tra maiuscole e minuscole), selezionare un **motivo**, immettere **Commenti**e fare clic su **Interrompi backup**.

        ![Selezionare il tipo di backup](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Per **Elimina dati di backup**, immettere il nome dell'elemento di backup (con distinzione tra maiuscole e minuscole), selezionare un **motivo**, immettere **Commenti**e fare clic su **Elimina**. 

         ![Elimina dati di backup](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Controllare la **notifica** ![Elimina dati](./media/backup-azure-delete-vault/messages.png)di backup. Al termine, il servizio visualizzerà il messaggio: **Interruzione del backup ed eliminazione dei dati di backup per "*elemento di backup*"** . **L'operazione è stata completata**.
6. Fare clic su **Aggiorna** nel menu **elementi di backup** per verificare se l'elemento di backup è stato rimosso.

      ![Elimina dati di backup](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Eliminare gli elementi protetti in locale

Prima di continuare, leggere **[questa](#before-you-start)** sezione per comprendere le dipendenze e il processo di eliminazione dell'insieme di credenziali.

1. Dal menu del dashboard dell'insieme di credenziali fare clic su **infrastruttura di backup**.
2. A seconda dello scenario locale, scegliere l'opzione seguente:

      - Per **Azure Backup Agent**scegliere **Server** > protetti**Azure Backup Agent** e selezionare il server che si vuole eliminare. 

        ![Selezionare l'insieme di credenziali per aprire il relativo dashboard](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - Per **server di backup di Azure**/**DPM**, scegliere **backup server di gestione**. Selezionare il server che si desidera eliminare. 


          ![Selezionare l'insieme di credenziali per aprire il dashboard](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Viene visualizzato il pannello **Elimina** con il messaggio di avviso.

     ![Elimina dati di backup](./media/backup-azure-delete-vault/delete-protected-server.png)

     Esaminare il messaggio di avviso e le istruzioni fornite nella casella di controllo del consenso.
    
    > [!NOTE]
    >- Se il server protetto è sincronizzato con il servizio di Azure e gli elementi di backup esistono, nella casella di controllo del consenso verrà visualizzato il numero di elementi di backup dipendenti e il collegamento per visualizzare gli elementi di backup.
    >- Se il server protetto non è sincronizzato con il servizio di Azure e gli elementi di backup esistono, nella casella di controllo del consenso verrà visualizzato il numero di elementi di backup.
    >- Se gli elementi di backup non esistono, la casella di controllo del consenso richiederà l'eliminazione.

4. Selezionare la casella di controllo consenso e fare clic su **Elimina**.




5. Controllare la **notifica** ![Elimina dati](./media/backup-azure-delete-vault/messages.png)di backup. Al termine, il servizio visualizzerà il messaggio: **Interruzione del backup ed eliminazione dei dati di backup per "*elemento di backup*"** . **L'operazione è stata completata**.
6. Fare clic su **Aggiorna** nel menu **elementi di backup** per verificare se l'elemento di backup è stato rimosso.

È ora possibile procedere all'eliminazione degli elementi di backup dalla console di gestione:
    
   - [Elementi protetti con MARS](#delete-backup-items-from-mars-management-console)
    - [Elementi protetti con MAB](#delete-backup-items-from-mabs-management-console)


### <a name="delete-backup-items-from-mars-management-console"></a>Eliminare gli elementi di backup dalla console di gestione MARS

Per eliminare gli elementi di backup dalla console di gestione MARS

- Avviare la console di gestione di MARS, passare al riquadro **azioni** e scegliere **Pianifica backup**.
- Per **modificare o arrestare una** procedura guidata di backup pianificato, scegliere l'opzione **Interrompi utilizzo di questa pianificazione del backup ed eliminare tutti i backup archiviati** e fare clic su **Avanti**.

    ![Modifica o interrompi un backup pianificato](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- Da **Arresta una** procedura guidata di backup pianificato, fare clic su **fine**.

    ![Arrestare un backup pianificato](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- Viene richiesto di immettere un pin di sicurezza. Per generare il PIN, seguire questa procedura:
  - Accedere al portale di Azure.
  - Passare a **Insiemi di credenziali dei servizi di ripristino** > **Impostazioni** > **Proprietà**.
  - Fare clic su **Genera** in **PIN di sicurezza**. Copiare questo PIN. (Questo PIN è valido solo per cinque minuti)
- Nella console di gestione (app client) incollare il PIN e fare clic su **OK**.

  ![Pin di sicurezza](./media/backup-azure-delete-vault/security-pin.png)

- Nella procedura guidata **modifica stato backup** verranno visualizzati *i dati di backup eliminati per 14 giorni. Dopo tale periodo, i dati di backup verranno eliminati definitivamente.*  

    ![Elimina infrastruttura di backup](./media/backup-azure-delete-vault/deleted-backup-data.png)

Ora che gli elementi di backup sono stati eliminati dall'ambiente locale, completare i passaggi successivi dal portale.

### <a name="delete-backup-items-from-mabs-management-console"></a>Eliminare gli elementi di backup dalla console di gestione di MAB

Per eliminare gli elementi di backup dalla console di gestione di MAB

**Metodo 1** Per arrestare la protezione ed eliminare i dati di backup, seguire questa procedura:

1.  In Console amministrazione DPM fare clic su **protezione** dati sulla barra di spostamento.
2.  Nel riquadro informazioni selezionare il membro del gruppo protezione dati che si desidera rimuovere. Fare clic con il pulsante destro del mouse per scegliere **Arresta protezione dati dell'opzione membri del gruppo** .
3.  Nella finestra di dialogo **Arresta protezione** selezionare la casella di controllo **Elimina dati** > protetti**Elimina archiviazione online** , quindi fare clic su **Arresta protezione**.

    ![Elimina archiviazione online](./media/backup-azure-delete-vault/delete-storage-online.png)

Lo stato del membro protetto viene ora modificato in **replica inattiva disponibile**.

4. Fare clic con il pulsante destro del mouse sul gruppo protezione dati inattivo e selezionare **Rimuovi protezione**dati inattiva.

    ![Rimuovi protezione dati inattiva](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Nella finestra **Elimina protezione** dati inattiva selezionare **Elimina archiviazione online** e fare clic su **OK**.

    ![Rimuovi le repliche su disco e online](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**Metodo 2** Avviare la console di **gestione di MAB** . Nella sezione **selezionare il metodo di protezione dei dati** deselezionare la **protezione online**.

  ![Selezionare il metodo di protezione dati](./media/backup-azure-delete-vault/data-protection-method.png)

Ora che gli elementi di backup sono stati eliminati dall'ambiente locale, completare i passaggi successivi dal portale.


## <a name="delete-the-recovery-services-vault"></a>Eliminare l'insieme di credenziali di Servizi di ripristino

1. Quando tutte le dipendenze sono state rimosse, scorrere fino al riquadro **Essentials** nel menu dell'insieme di credenziali.
2. Verificare che non siano presenti **elementi di backup**, **server di gestione di backup**o **elementi replicati** . Se gli elementi vengono ancora visualizzati nell'insieme di credenziali, vedere la sezione [prima di iniziare](#before-you-start) .

3. Dopo che tutti gli elementi nell'insieme di credenziali sono stati eliminati, nel dashboard dell'insieme di credenziali fare clic su **Elimina**.

    ![Elimina dati di backup](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Per confermare l'eliminazione dell'insieme di credenziali, fare clic su **Sì**. L'insieme di credenziali viene eliminato e il portale torna al menu **Nuovo** del servizio.

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Eliminare l'insieme di credenziali di servizi di ripristino usando Azure Resource Manager client

Questa opzione per eliminare l'insieme di credenziali di servizi di ripristino è consigliata solo quando vengono rimosse tutte le dipendenze e si riceve ancora l' *errore di eliminazione*dell'insieme di credenziali.

- Dal riquadro **Essentials** nel menu dell'insieme di credenziali verificare che non siano presenti **elementi di backup**, **server di gestione di backup**o **elementi replicati** . Se sono presenti elementi di backup, vedere la sezione [prima di iniziare](#before-you-start) .
- Riprovare [a eliminare l'insieme di credenziali dal portale](#delete-the-recovery-services-vault).
- Se tutte le dipendenze vengono rimosse e si riceve ancora l' *errore di eliminazione* dell'insieme di credenziali, usare lo strumento ARMClient per eseguire i passaggi indicati di seguito.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Installare cioccolato da [qui](https://chocolatey.org/) e per installare ARMClient eseguire il comando seguente:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Accedere al proprio account di Azure ed eseguire questo comando:

    `ARMClient.exe login [environment name]`

3. Nella portale di Azure raccogliere l'ID sottoscrizione e il nome del gruppo di risorse per l'insieme di credenziali che si vuole eliminare.

Per ulteriori informazioni sul comando ARMClient, fare riferimento a questo [documento](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Usare Azure Resource Manager client per eliminare l'insieme di credenziali di servizi di ripristino

1. Eseguire il comando seguente usando l'ID sottoscrizione, il nome del gruppo di risorse e il nome dell'insieme di credenziali. Quando si esegue il comando, l'insieme di credenziali viene eliminato se non sono presenti dipendenze.

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Se l'insieme di credenziali non è vuoto, verrà visualizzato l'errore "non è possibile eliminare l'insieme di credenziali perché sono presenti risorse esistenti in questo insieme di credenziali". Per rimuovere un contenitore o elementi protetti in un insieme di credenziali, eseguire le operazioni seguenti:

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Nel portale di Azure verificare che l'insieme di credenziali sia stato eliminato.


## <a name="next-steps"></a>Passaggi successivi

Informazioni [su](backup-azure-recovery-services-vault-overview.md) Insiemi di credenziali dei servizi di ripristino.<br/>
Informazioni su come monitorare e gestire gli insiemi di credenziali dei servizi [di](backup-azure-manage-windows-server.md) ripristino.
