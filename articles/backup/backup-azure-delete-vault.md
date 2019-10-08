---
title: Eliminare un Backup di Microsoft Azure insieme di credenziali dei servizi di ripristino
description: Questo articolo descrive come eliminare un Backup di Microsoft Azure insieme di credenziali di servizi di ripristino.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: dacurwin
ms.openlocfilehash: ae8421ca9e3705d697e9638e80fc61f853ff9d28
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028287"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Eliminare un insieme di credenziali di servizi di ripristino di Azure Backup

Questo articolo descrive come eliminare un insieme di credenziali di servizi di ripristino di backup di Microsoft [Azure](backup-overview.md) (Mars). Contiene le istruzioni per rimuovere le dipendenze e quindi eliminare un insieme di credenziali.


## <a name="before-you-start"></a>Prima di iniziare

Non è possibile eliminare un insieme di credenziali di servizi di ripristino con dipendenze, ad esempio server protetti o server di gestione di backup.

- Gli insiemi di credenziali che contengono dati di backup non possono essere eliminati (anche se è stata arrestata la protezione ma sono stati conservati i dati di backup).

- Se si elimina un insieme di credenziali che contiene dipendenze, viene visualizzato il messaggio seguente:

  ![Eliminare l'errore dell'insieme di credenziali.](./media/backup-azure-delete-vault/error.png)

- Se si elimina un elemento protetto locale da un portale che contiene le dipendenze, viene visualizzato un messaggio di avviso:

  ![Eliminare l'errore del server protetto.](./media/backup-azure-delete-vault/error-message.jpg)

- Se gli elementi di backup sono in stato di eliminazione temporanea al di sotto del messaggio di avviso, sarà necessario attendere che vengano eliminati definitivamente. Per altre informazioni, fare riferimento a [questo articolo](https://aka.ms/SoftDeleteCloudWorkloads).

   ![Eliminare l'errore dell'insieme di credenziali.](./media/backup-azure-delete-vault/error-message-soft-delete.png)
  
Per eliminare l'insieme di credenziali, scegliere lo scenario che corrisponde alla configurazione e seguire le procedure consigliate:

Scenario | Procedura per rimuovere le dipendenze per eliminare l'insieme di credenziali |
-- | --
I file e le cartelle locali sono protetti con l'agente di backup di Azure, eseguendo il backup in Azure | Eseguire la procedura descritta in [eliminare gli elementi di backup dalla console di gestione di Mars](#delete-backup-items-from-the-mars-management-console)
Sono presenti computer locali protetti tramite MAB (Backup di Microsoft Azure Server) o DPM (System Center Data Protection Manager) in Azure | Eseguire la procedura descritta in [eliminare gli elementi di backup dalla console di gestione di MAB](#delete-backup-items-from-the-mabs-management-console)
Sono presenti elementi protetti nel cloud (ad esempio, una macchina virtuale laaS o una condivisione File di Azure)  | Eseguire la procedura descritta in [eliminare gli elementi protetti nel cloud](#delete-protected-items-in-the-cloud)
Sono presenti elementi protetti sia in locale che nel cloud | Eseguire i passaggi in tutte le sezioni seguenti, nell'ordine seguente: <br> 1. [Eliminare gli elementi protetti nel cloud](#delete-protected-items-in-the-cloud)<br> 2. [Eliminare gli elementi di backup dalla console di gestione di MARS](#delete-backup-items-from-the-mars-management-console) <br> 3. [Eliminare gli elementi di backup dalla console di gestione di MAB](#delete-backup-items-from-the-mabs-management-console)
Non sono presenti elementi protetti in locale o nel cloud; Tuttavia, ricevo ancora l'errore di eliminazione dell'insieme di credenziali | Eseguire la procedura descritta in [eliminare l'insieme di credenziali di servizi di ripristino usando Azure Resource Manager](#delete-the-recovery-services-vault-by-using-azure-resource-manager)


## <a name="delete-protected-items-in-the-cloud"></a>Eliminare gli elementi protetti nel cloud

Prima di tutto, leggere la sezione **[prima di iniziare](#before-you-start)** per comprendere le dipendenze e il processo di eliminazione dell'insieme di credenziali.

Per arrestare la protezione ed eliminare i dati di backup, seguire questa procedura:

1. Dal portale passare a insieme di credenziali di **servizi di ripristino**, quindi passare a **elementi di backup**. Scegliere quindi gli elementi protetti nel cloud (ad esempio, macchine virtuali di Azure, archiviazione di Azure [il servizio File di Azure] o SQL Server in macchine virtuali di Azure).

    ![Selezionare il tipo di backup.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Fare clic con il pulsante destro del mouse per selezionare l'elemento di backup. A seconda del fatto che l'elemento di backup sia protetto o meno, il menu Visualizza il riquadro **Interrompi backup** o **Elimina dati di backup** .

    - Se viene visualizzato il riquadro **Interrompi backup** , scegliere **Elimina dati di backup** dal menu a discesa. Immettere il nome dell'elemento di backup (questo campo fa distinzione tra maiuscole e minuscole) e quindi selezionare un motivo dal menu a discesa. Immettere i commenti, se disponibili. Selezionare quindi **Interrompi backup**.

        ![Riquadro Interrompi backup.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Se viene visualizzato il riquadro **Elimina dati di backup** , immettere il nome dell'elemento di backup (questo campo fa distinzione tra maiuscole e minuscole) e quindi selezionare un motivo dal menu a discesa. Immettere i commenti, se disponibili. Quindi, selezionare **Elimina**. 

         ![Riquadro Elimina dati di backup.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Controllare l'icona di **notifica** : icona di notifica ![The. ](./media/backup-azure-delete-vault/messages.png) Al termine del processo, il servizio Visualizza il messaggio seguente: *Interruzione del backup ed eliminazione dei dati di backup per "* Elemento di backup *"* . *L'operazione è stata completata*.
6. Selezionare **Aggiorna** dal menu **elementi di backup** per assicurarsi che l'elemento di backup sia stato eliminato.

      ![Pagina Elimina elementi di backup.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Elimina gli elementi protetti in locale

Prima di tutto, leggere la sezione **[prima di iniziare](#before-you-start)** per comprendere le dipendenze e il processo di eliminazione dell'insieme di credenziali.

1. Dal menu del dashboard dell'insieme di credenziali selezionare **infrastruttura di backup**.
2. A seconda dello scenario locale, scegliere una delle opzioni seguenti:

      - Per MARS selezionare **server protetti** e quindi **agente di backup di Azure**. Selezionare quindi il server che si desidera eliminare. 

        ![Per MARS selezionare l'insieme di credenziali per aprire il relativo dashboard.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - Per MAB o DPM, selezionare **server di gestione di backup**. Selezionare quindi il server che si desidera eliminare. 


          ![Per MAB selezionare l'insieme di credenziali per aprire il relativo dashboard.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Il riquadro **Elimina** viene visualizzato con un messaggio di avviso.

     ![Riquadro Elimina.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Esaminare il messaggio di avviso e le istruzioni riportate nella casella di controllo del consenso.
    > [!NOTE]
    >- Se il server protetto viene sincronizzato con i servizi di Azure e gli elementi di backup sono presenti, la casella di controllo del consenso visualizzerà il numero di elementi di backup dipendenti e il collegamento per visualizzare gli elementi di backup.
    >- Se il server protetto non è sincronizzato con i servizi di Azure e sono presenti elementi di backup, nella casella di controllo del consenso verrà visualizzato solo il numero di elementi di backup.
    >- Se non sono presenti elementi di backup, la casella di controllo del consenso richiede l'eliminazione.

4. Selezionare la casella di controllo consenso, quindi selezionare **Elimina**.


5. Selezionare l'icona di **notifica** ![delete backup data @ no__t-2. Al termine dell'operazione, il servizio Visualizza il messaggio: *Interruzione del backup ed eliminazione dei dati di backup per "elemento di backup".* *L'operazione è stata completata*.
6. Selezionare **Aggiorna** dal menu **elementi di backup** per assicurarsi che l'elemento di backup venga eliminato.

Al termine di questo processo, è possibile eliminare gli elementi di backup dalla console di gestione:
    
   - [Eliminare gli elementi di backup dalla console di gestione di MARS](#delete-backup-items-from-the-mars-management-console)
    - [Eliminare gli elementi di backup dalla console di gestione di MAB](#delete-backup-items-from-the-mabs-management-console)


### <a name="delete-backup-items-from-the-mars-management-console"></a>Eliminare gli elementi di backup dalla console di gestione di MARS

1. Aprire la console di gestione di MARS, passare al riquadro **azioni** e selezionare **Pianifica backup**.
2. Nella pagina **modificare o arrestare un backup pianificato** selezionare **Interrompi utilizzo di questa pianificazione del backup ed eliminare tutti i backup archiviati**. Quindi selezionare **Avanti**.

    ![Modificare o arrestare un backup pianificato.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Nella pagina **Arresta un backup pianificato** selezionare **fine**.

    ![Arrestare un backup pianificato.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Viene richiesto di immettere un PIN di sicurezza (Personal Identification Number), che è necessario generare manualmente. A tale scopo, accedere prima al portale di Azure.
5. Passare a insieme di credenziali di **servizi di ripristino** > **Impostazioni** > **proprietà**.
6. In **pin di sicurezza**selezionare **genera**. Copiare questo PIN. Il PIN è valido solo per cinque minuti.
7. Nella console di gestione incollare il PIN e quindi fare clic su **OK**.

    ![Generare un PIN di sicurezza.](./media/backup-azure-delete-vault/security-pin.png)

8. Nella pagina **modifica stato backup** viene visualizzato il messaggio seguente: i dati di backup *Deleted verranno conservati per 14 giorni. Dopo tale periodo, i dati di backup verranno eliminati definitivamente.*  

    ![Eliminare l'infrastruttura di backup.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Dopo aver eliminato gli elementi di backup locali, seguire i passaggi successivi dal portale.

### <a name="delete-backup-items-from-the-mabs-management-console"></a>Eliminare gli elementi di backup dalla console di gestione di MAB

Esistono due metodi che è possibile usare per eliminare gli elementi di backup dalla console di gestione di MAB.

#### <a name="method-1"></a>Metodo 1
Per arrestare la protezione ed eliminare i dati di backup, seguire questa procedura:

1.  Aprire il Console amministrazione DPM e quindi selezionare **protezione** sulla barra di spostamento.
2.  Nel riquadro informazioni selezionare il membro del gruppo protezione dati che si desidera rimuovere. Fare clic con il pulsante destro del mouse per selezionare l'opzione **Arresta protezione dei membri del gruppo** .
3.  Nella finestra di dialogo **Arresta protezione** dati selezionare **Elimina dati protetti**, quindi selezionare la casella di controllo **Elimina archiviazione online** . Quindi selezionare **Arresta protezione**.

    ![Selezionare Elimina dati protetti dal riquadro Arresta protezione.](./media/backup-azure-delete-vault/delete-storage-online.png)

    Lo stato del membro protetto passa alla *replica inattiva disponibile*.

4. Fare clic con il pulsante destro del mouse sul gruppo protezione dati inattivo e selezionare **Rimuovi protezione dati inattiva**.

    ![Rimuovere la protezione dati inattiva.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Nella finestra **Elimina protezione dati inattiva** selezionare la casella di controllo **Elimina archiviazione online** , quindi selezionare **OK**.

    ![Elimina lo spazio di archiviazione online.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Metodo 2
Aprire la console di **gestione di MAB** . In **Seleziona metodo protezione dati**deselezionare la casella di controllo voglio la **protezione online** .

  ![Selezionare il metodo di protezione dati.](./media/backup-azure-delete-vault/data-protection-method.png)

Dopo aver eliminato gli elementi di backup locali, seguire i passaggi successivi dal portale.


## <a name="delete-the-recovery-services-vault"></a>Eliminare l'insieme di credenziali di Servizi di ripristino

1. Quando tutte le dipendenze sono state rimosse, scorrere fino al riquadro **Essentials** nel menu dell'insieme di credenziali.
2. Verificare che non siano presenti elementi di backup, server di gestione di backup o elementi replicati. Se gli elementi vengono ancora visualizzati nell'insieme di credenziali, fare riferimento alla sezione [prima di iniziare](#before-you-start) .

3. Quando nell'insieme di credenziali non sono presenti altri elementi, selezionare **Elimina** nel dashboard dell'insieme di credenziali.

    ![Selezionare Elimina nel dashboard dell'insieme di credenziali.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Selezionare **Sì** per verificare che si vuole eliminare l'insieme di credenziali. L'insieme di credenziali viene eliminato. Il portale torna al menu **nuovo** del servizio.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>Eliminare l'insieme di credenziali di servizi di ripristino tramite PowerShell

Prima di tutto, leggere la sezione **[prima di iniziare](#before-you-start)** per comprendere le dipendenze e il processo di eliminazione dell'insieme di credenziali.

Per arrestare la protezione ed eliminare i dati di backup:

- Se si usa SQL nel backup di macchine virtuali di Azure e si Abilita la protezione automatica per le istanze di SQL, disabilitare prima la protezione automatica.

    ```PowerShell
        Disable-AzRecoveryServicesBackupAutoProtection 
           [-InputItem] <ProtectableItemBase> 
           [-BackupManagementType] <BackupManagementType> 
           [-WorkloadType] <WorkloadType> 
           [-PassThru] 
           [-VaultId <String>] 
           [-DefaultProfile <IAzureContextContainer>] 
           [-WhatIf] 
           [-Confirm] 
           [<CommonParameters>] 
    ```

  [Scopri di più](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection?view=azps-2.6.0) su come disabilitare la protezione per un elemento protetto da backup di Azure 

- Arrestare la protezione ed eliminare i dati per tutti gli elementi protetti da backup nel cloud (ad esempio, VM laaS, condivisione file di Azure e così via):

    ```PowerShell
       Disable-AzRecoveryServicesBackupProtection 
       [-Item] <ItemBase> 
       [-RemoveRecoveryPoints] 
       [-Force] 
       [-VaultId <String>] 
       [-DefaultProfile <IAzureContextContainer>] 
       [-WhatIf] 
       [-Confirm] 
       [<CommonParameters>] 
    ```
    [Altre informazioni](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-2.6.0&viewFallbackFrom=azps-2.5.0) about Disabilita la protezione per un elemento protetto da backup. 

- Per i file e le cartelle locali protetti con l'agente di backup di Azure (MARS) che esegue il backup in Azure, usare il comando di PowerShell seguente per eliminare i dati di cui è stato eseguito il backup da ogni modulo di PowerShell MARS:

    ```
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Pubblicare il messaggio seguente:
     
    @no__t backup di Azure 0Microsoft rimuovere il criterio di backup? I dati di backup eliminati verranno conservati per 14 giorni. Dopo tale periodo, i dati di backup verranno eliminati definitivamente. <br/> [Y] Sì [A] Sì a tutti [N] no [L] no a tutti [S] Sospendi [?] Guida (il valore predefinito è "Y"): *


- Per i computer locali protetti con MAB (Backup di Microsoft Azure Server) o DPM in Azure (System Center Data Protection Manager), usare il comando seguente per eliminare i dati di cui è stato eseguito il backup in Azure.

    ```
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin> 
    ```

    Pubblicare il messaggio seguente: 
         
   @no__t backup di Azure 0Microsoft rimuovere il criterio di backup? I dati di backup eliminati verranno conservati per 14 giorni. Dopo tale periodo, i dati di backup verranno eliminati definitivamente. <br/> [Y] Sì [A] Sì a tutti [N] no [L] no a tutti [S] Sospendi [?] Guida (il valore predefinito è "Y"): *

Dopo aver eliminato i dati di cui è stato eseguito il backup, annullare la registrazione di tutti i contenitori locali e i server di gestione. 

- Per i file e le cartelle locali protetti con l'agente di backup di Azure (MARS) che esegue il backup in Azure:

    ```PowerShell
    Unregister-AzRecoveryServicesBackupContainer 
              [-Container] <ContainerBase> 
              [-PassThru] 
              [-VaultId <String>] 
              [-DefaultProfile <IAzureContextContainer>] 
              [-WhatIf] 
              [-Confirm] 
              [<CommonParameters>] 
    ```
    [Altre](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) informazioni su come annullare la registrazione di un server Windows o di un altro contenitore dall'insieme di credenziali. 

- Per i computer locali protetti con MAB (Backup di Microsoft Azure Server) o DPM in Azure (System Center Data Protection Manage:

    ```PowerShell
        Unregister-AzRecoveryServicesBackupManagementServer
          [-AzureRmBackupManagementServer] <BackupEngineBase>
          [-PassThru]
          [-VaultId <String>]
          [-DefaultProfile <IAzureContextContainer>]
          [-WhatIf]
          [-Confirm]
          [<CommonParameters>]
    ```

    [Altre](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) informazioni su come annullare la registrazione di un contenitore di gestione di backup dall'insieme di credenziali.

Dopo aver eliminato definitivamente i dati sottoposti a backup e aver annullato la registrazione di tutti i contenitori, procedere con l'eliminazione dell'insieme di credenziali. 

Per eliminare un insieme di credenziali dei servizi di ripristino: 

   ```PowerShell
       Remove-AzRecoveryServicesVault 
      -Vault <ARSVault> 
      [-DefaultProfile <IAzureContextContainer>] 
      [-WhatIf] 
      [-Confirm] 
      [<CommonParameters>]        
   ```

[Altre](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) informazioni sull'eliminazione di un insieme di credenziali di servizi di ripristino. 

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>Eliminare l'insieme di credenziali di servizi di ripristino tramite CLI

Prima di tutto, leggere la sezione **[prima di iniziare](#before-you-start)** per comprendere le dipendenze e il processo di eliminazione dell'insieme di credenziali.

> [!NOTE]
> Attualmente, l'interfaccia della riga di comando di backup di Azure supporta la gestione solo dei backup di VM di Azure, quindi il comando seguente per eliminare l'insieme di credenziali funziona solo se l'insieme di credenziali contiene backup di macchine virtuali Non è possibile eliminare un insieme di credenziali usando l'interfaccia della riga di comando di backup di Azure se l'insieme di credenziali contiene un elemento di backup di tipo diverso dalle macchine virtuali 

Per eliminare l'insieme di credenziali di servizi di ripristino esistente, seguire questa procedura: 

- Per arrestare la protezione ed eliminare i dati di backup 

    ```CLI
    az backup protection disable --container-name 
                             --item-name 
                             [--delete-backup-data {false, true}] 
                             [--ids] 
                             [--resource-group] 
                             [--subscription] 
                             [--vault-name] 
                             [--yes] 
    ```

    Per ulteriori informazioni, fare riferimento a questo [articolo](/cli/azure/backup/protection#az-backup-protection-disable). 

- Eliminare un insieme di credenziali di servizi di ripristino esistente: 

    ```CLI
    az backup vault delete [--force] 
                       [--ids] 
                       [--name] 
                       [--resource-group] 
                       [--subscription] 
                       [--yes] 
    ```

    Per ulteriori informazioni, fare riferimento a questo [articolo](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest) . 

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Eliminare l'insieme di credenziali di servizi di ripristino usando Azure Resource Manager

Questa opzione per eliminare l'insieme di credenziali di servizi di ripristino è consigliata solo se tutte le dipendenze vengono rimosse e si sta ancora ricevendo l' *errore di eliminazione*dell'insieme di credenziali. Provare uno o tutti i suggerimenti seguenti:

- Dal riquadro **Essentials** nel menu dell'insieme di credenziali verificare che non siano presenti elementi di backup, server di gestione di backup o elementi replicati. Se sono presenti elementi di backup, vedere la sezione [prima di iniziare](#before-you-start) .
- Provare [di nuovo a eliminare l'insieme di credenziali dal portale](#delete-the-recovery-services-vault) .
- Se tutte le dipendenze vengono rimosse e si sta ancora ricevendo l' *errore di eliminazione*dell'insieme di credenziali, usare lo strumento ARMClient per eseguire i passaggi seguenti (dopo la nota).

1. Passare a [chocolatey.org](https://chocolatey.org/) per scaricare e installare cioccolato. Quindi, installare ARMClient eseguendo il comando seguente:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Accedere al proprio account di Azure e quindi eseguire il comando seguente:

    `ARMClient.exe login [environment name]`

3. Nella portale di Azure raccogliere l'ID sottoscrizione e il nome del gruppo di risorse per l'insieme di credenziali che si vuole eliminare.

Per ulteriori informazioni sul comando ARMClient, vedere il [file Leggimi di ARMClient](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Usare il client di Azure Resource Manager per eliminare un insieme di credenziali di servizi di ripristino

1. Eseguire il comando seguente usando l'ID sottoscrizione, il nome del gruppo di risorse e il nome dell'insieme di credenziali. Se non sono presenti dipendenze, l'insieme di credenziali viene eliminato quando si esegue il comando seguente:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Se l'insieme di credenziali non è vuoto, verrà visualizzato il messaggio di errore seguente: *Non è possibile eliminare l'insieme di credenziali perché sono presenti risorse esistenti all'interno di questo insieme di credenziali.* Per rimuovere un elemento o un contenitore protetto in un insieme di credenziali, eseguire il comando seguente:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Nel portale di Azure assicurarsi che l'insieme di credenziali venga eliminato.


## <a name="next-steps"></a>Passaggi successivi

[Informazioni sugli insiemi di credenziali dei servizi di ripristino](backup-azure-recovery-services-vault-overview.md)<br/>
[Informazioni sul monitoraggio e la gestione degli insiemi di credenziali dei servizi di ripristino](backup-azure-manage-windows-server.md)
