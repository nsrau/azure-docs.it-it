---
title: Eliminare un insieme di credenziali di Servizi di ripristino di Microsoft AzureDelete a Microsoft Azure Recovery Services vault
description: In questo articolo viene illustrato come rimuovere le dipendenze e quindi eliminare un insieme di credenziali di Servizi di ripristino di Backup di Azure.In this article, learn how to remove dependencies and then delete an Azure Backup Recovery Services vault.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 69fed6c53914ed7aa16b04b5311ec69966734f25
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025136"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Eliminare un insieme di credenziali di Servizi di ripristino di AzureDelete an Azure Backup Recovery Services vault

In questo articolo viene descritto come eliminare un insieme di credenziali di Servizi di ripristino di [Azure.This article](backup-overview.md) describes how to delete an Azure Backup Recovery Services vault. Contiene istruzioni per rimuovere le dipendenze e quindi eliminare un vault.

## <a name="before-you-start"></a>Prima di iniziare

Non è possibile eliminare un insieme di credenziali di Servizi di ripristino con dipendenze, ad esempio server protetti o server di gestione di backup, associati.

- Gli archivi che contengono dati di backup non possono essere eliminati (anche se la protezione è stata interrotta ma mantenuta i dati di backup).

- Se si elimina un vault che contiene dipendenze, viene visualizzato il seguente messaggio:

  ![Eliminare l'errore del vault.](./media/backup-azure-delete-vault/error.png)

- Se si elimina un elemento protetto locale da un portale che contiene dipendenze, viene visualizzato un messaggio di avviso:If you delete an on-premises protected item from a portal that contains dependencies, a warning message appears:

  ![Eliminare l'errore del server protetto.](./media/backup-azure-delete-vault/error-message.jpg)

- Se gli elementi di backup sono in stato eliminato temporaneamente sotto il messaggio di avviso viene visualizzato e sarà necessario attendere fino a quando non vengono eliminati definitivamente. Per ulteriori informazioni, vedere questo [articolo](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud).

   ![Eliminare l'errore del vault.](./media/backup-azure-delete-vault/error-message-soft-delete.png)

- Gli archivi con account di archiviazione registrati non possono essere eliminati. Per informazioni su come annullare la registrazione dell'account, vedere Annullare la registrazione di [un account di archiviazione.](manage-afs-backup.md#unregister-a-storage-account)
  
Per eliminare il vault, scegliere lo scenario che corrisponde alla configurazione e seguire i passaggi consigliati:

Scenario | Procedura per rimuovere le dipendenze per eliminare il vault |
-- | --
Sono protetti file e cartelle locali tramite l'agente di Backup di Azure, che esegue il backup in Azure | Eseguire la procedura descritta in Eliminare elementi di [backup dalla console](#delete-backup-items-from-the-mars-management-console) di gestione MARS
Ho computer locali che sono protetti utilizzando MABS (Server di backup di Microsoft Azure) o DPM (System Center Data Protection Manager) in Azure | Eseguire la procedura descritta in Eliminare gli elementi di backup dalla console di [gestione MABS](#delete-backup-items-from-the-mabs-management-console)
Sono protetti elementi nel cloud (ad esempio, una macchina virtuale laaS o una condivisione file di Azure)I have protected items in the cloud (for example, an laaS virtual machine or an Azure Files share)  | Eseguire la procedura descritta in [Eliminare gli elementi protetti nel cloud](#delete-protected-items-in-the-cloud)
Ho elementi protetti sia in locale che nel cloud | Eseguire i passaggi in tutte le sezioni seguenti, nell'ordine seguente: <br> 1. [Eliminare gli elementi protetti nel cloud](#delete-protected-items-in-the-cloud)<br> 2. [Eliminare gli elementi di backup dalla console](#delete-backup-items-from-the-mars-management-console) di gestione MARS <br> 3. Eliminare gli elementi di backup dalla console di [gestione MABS](#delete-backup-items-from-the-mabs-management-console)
Non ho elementi protetti in locale o cloud; tuttavia, sto ancora ricevendo l'errore di eliminazione Vault | Eseguire la procedura descritta in [Eliminare l'insieme](#delete-the-recovery-services-vault-by-using-azure-resource-manager) di credenziali di Servizi di ripristino usando Azure Resource Manager <br><br> Assicurarsi che non siano presenti account di archiviazione registrati con l'insieme di credenziali. Per informazioni su come annullare la registrazione dell'account, vedere Annullare la registrazione di [un account di archiviazione.](manage-afs-backup.md#unregister-a-storage-account)

## <a name="delete-protected-items-in-the-cloud"></a>Eliminare gli elementi protetti nel cloudDelete protected items in the cloud

Leggere innanzitutto la sezione **[Prima di iniziare](#before-you-start)** per comprendere le dipendenze e il processo di eliminazione dell'insieme di credenziali.

Per interrompere la protezione ed eliminare i dati di backup, attenersi alla seguente procedura:

1. Dal portale passare **all'insieme**di credenziali di Servizi di ripristino , quindi a Elementi di **backup**. Scegliere quindi gli elementi protetti nel cloud, ad esempio Macchine virtuali di Azure, Archiviazione di Azure [servizio File di Azure] o SQL Server in Macchine virtuali di Azure.Then, choose the protected items in the cloud (for example, Azure Virtual Machines, Azure Storage [the Azure Files service] or SQL Server on Azure Virtual Machines).

    ![Selezionare il tipo di backup.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Fare clic con il pulsante destro del mouse per selezionare l'elemento di backup. A seconda che l'elemento di backup sia protetto o meno, nel menu viene visualizzato il riquadro **Interrompi backup** o Elimina dati **di backup.**

    - Se viene visualizzato il riquadro **Interrompi backup,** selezionare **Elimina dati di backup** dal menu a discesa. Immettere il nome dell'elemento di backup (in questo campo viene fatta distinzione tra maiuscole e minuscole), quindi selezionare un motivo dal menu a discesa. Inserisci i tuoi commenti, se ne hai. Selezionare **quindi Interrompi backup**.

        ![Riquadro Interrompi backup.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Se viene visualizzato il riquadro **Elimina dati di** backup, immettere il nome dell'elemento di backup (in questo campo viene fatta distinzione tra maiuscole e minuscole) e quindi selezionare un motivo dal menu a discesa. Inserisci i tuoi commenti, se ne hai. Quindi, selezionare **Elimina**.

         ![Riquadro Elimina dati di backup.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

3. Controllare l'icona ![ **Notifica:** l'icona Notifica.](./media/backup-azure-delete-vault/messages.png) Al termine del processo, il servizio visualizza il seguente messaggio: *Arresto del backup ed eliminazione dei dati*di backup per " Elemento di backup *".* *L'operazione è stata completata correttamente.*
4. Selezionare **Aggiorna** dal menu **Elementi di backup** per assicurarsi che l'elemento di backup sia stato eliminato.

      ![Pagina Elimina elementi di backup.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Eliminare gli elementi protetti in localeDelete protected items on premises

Leggere innanzitutto la sezione **[Prima di iniziare](#before-you-start)** per comprendere le dipendenze e il processo di eliminazione dell'insieme di credenziali.

1. Dal menu del dashboard del vault, selezionare **Infrastruttura di backup**.
2. A seconda dello scenario locale, scegliere una delle opzioni seguenti:

      - Per MARS, selezionare **Server protetti** e quindi Agente di backup di **Azure.** Quindi, selezionare il server che si desidera eliminare.

        ![Per MARS, selezionare il vault per aprire il relativo dashboard.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - Per MABS o DPM, selezionare **Backup Management Servers**. Quindi, selezionare il server che si desidera eliminare.

          ![Per MABS, selezionare il vault per aprire il relativo dashboard.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Viene visualizzato il riquadro **Elimina** con un messaggio di avviso.

     ![Riquadro di eliminazione.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Esaminare il messaggio di avviso e le istruzioni nella casella di controllo di consenso.
    > [!NOTE]
    >- Se il server protetto è sincronizzato con i servizi di Azure e sono presenti elementi di backup, la casella di controllo del consenso visualizzerà il numero di elementi di backup dipendenti e il collegamento per visualizzare gli elementi di backup.
    >- Se il server protetto non è sincronizzato con i servizi di Azure ed esistono elementi di backup, la casella di controllo del consenso visualizzerà solo il numero di elementi di backup.
    >- Se non sono presenti elementi di backup, la casella di controllo del consenso richiederà l'eliminazione.

4. Selezionare la casella di controllo di consenso, quindi scegliere **Elimina**.

5. Controllare **Notification** l'icona ![Notifica](./media/backup-azure-delete-vault/messages.png)eliminare i dati di backup . Al termine dell'operazione, il servizio visualizza il messaggio: Arresto del backup ed eliminazione dei dati di *backup per "Elemento di backup".* *L'operazione è stata completata correttamente.*
6. Selezionare **Aggiorna** dal menu **Elementi di backup** per assicurarsi che l'elemento di backup venga eliminato.

Al termine di questo processo, è possibile eliminare gli elementi di backup dalla console di gestione:After this process finishs, you can delete the backup items from management console:

- [Eliminare gli elementi di backup dalla console di gestione MARS](#delete-backup-items-from-the-mars-management-console)
- [Eliminare gli elementi di backup dalla console di gestione MABS](#delete-backup-items-from-the-mabs-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Eliminare gli elementi di backup dalla console di gestione MARS

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

### <a name="delete-backup-items-from-the-mabs-management-console"></a>Eliminare gli elementi di backup dalla console di gestione MABS

Esistono due metodi che è possibile utilizzare per eliminare gli elementi di backup dalla console di gestione MABS.

#### <a name="method-1"></a>Metodo 1

Per interrompere la protezione ed eliminare i dati di backup, procedere come segue:

1. Aprire la Console amministrazione DPM e quindi selezionare **Protezione dati** sulla barra di spostamento.
2. Nel riquadro informazioni selezionare il membro del gruppo protezione dati che si desidera rimuovere. Fare clic con il pulsante destro del mouse per selezionare l'opzione **Arresta protezione dati dei membri del gruppo.**
3. Nella finestra di dialogo **Arresta protezione** dati selezionare **Elimina dati protetti**e quindi selezionare la casella di controllo Elimina spazio di archiviazione **online.** Selezionare **quindi Arresta protezione**dati .

    ![Selezionare Elimina dati protetti dal riquadro Arresta protezione.](./media/backup-azure-delete-vault/delete-storage-online.png)

    Lo stato del membro protetto diventa *Replica inattiva disponibile.*

4. Fare clic con il pulsante destro del mouse sul gruppo protezione dati inattivo e scegliere **Rimuovi protezione inattiva**.

    ![Rimuovere la protezione inattiva.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Nella finestra **Elimina protezione inattiva** selezionare la casella di controllo **Elimina spazio di archiviazione online** e quindi scegliere **OK**.

    ![Eliminare lo spazio di archiviazione online.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Metodo 2

Aprire la console di **gestione MABS.** In Seleziona metodo di **protezione dati**deselezionare la casella di controllo Si desidera proteggere **online.**

  ![Selezionare il metodo di protezione dati.](./media/backup-azure-delete-vault/data-protection-method.png)

Dopo aver eliminato gli elementi di backup locale, seguire i passaggi successivi dal portale.

## <a name="delete-the-recovery-services-vault"></a>Eliminare l'insieme di credenziali di Servizi di ripristino

1. Dopo aver rimosso tutte le dipendenze, scorrere fino al riquadro **Essentials** nel menu del vault.
2. Verificare che non siano elencati elementi di backup, server di gestione di backup o elementi replicati. Se gli elementi vengono ancora visualizzati nel vault, fare riferimento alla sezione [Prima di iniziare.](#before-you-start)

3. Quando non sono presenti altri elementi nel vault, selezionare **Elimina** nel dashboard del vault.

    ![Selezionare Elimina nel dashboard del vault.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Selezionare **Sì** per verificare che si desidera eliminare il vault. Il vault viene eliminato. Il portale torna al menu **Nuovo** servizio.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>Eliminare l'insieme di credenziali dei servizi di ripristino tramite PowerShellDelete the Recovery Services vault by using PowerShell

Leggere innanzitutto la sezione **[Prima di iniziare](#before-you-start)** per comprendere le dipendenze e il processo di eliminazione dell'insieme di credenziali.

Per interrompere la protezione ed eliminare i dati di backup:

- Se si usa SQL nel backup delle macchine virtuali di Azure e si disabilita la protezione automatica per le istanze SQL, disabilitare prima la protezione automatica.

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

  [Altre informazioni](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection?view=azps-2.6.0) su come disabilitare la protezione per un elemento protetto da Backup di Azure.Learn more on how to disable protection for an Azure Backup-protected item.

- Arrestare la protezione ed eliminare i dati per tutti gli elementi protetti da backup nel cloud (ad es. LAaS VM, Azure File Share ecc.):

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

    [Ulteriori informazioni](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-2.6.0&viewFallbackFrom=azps-2.5.0) su come disabilitare la protezione per un elemento protetto da backup.

- Per i file e le cartelle locali protetti con il backup di Azure Backup Agent (MARS) in Azure, usare il comando di PowerShell seguente per eliminare i dati di cui è stato eseguito il backup da ogni modulo di PowerShell MARS:For on-premises Files and Folders protected using Azure Backup Agent (MARS) backing up to Azure, use the following PowerShell command to delete the backed-up powerShell module:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Pubblicare il seguente messaggio di richiesta:

    *Backup di Microsoft Azure È necessario rimuovere questo criterio di backup? I dati di backup eliminati verranno conservati per 14 giorni. Dopo tale periodo, i dati di backup verranno eliminati definitivamente. <br/> [Y] Sì [A] Sì a tutti [N] No [L] No [S] Sospendi [?] Aiuto (il valore predefinito è "Y"):*

- Per le macchine locali protette tramite MABS (Server di backup di Microsoft Azure) o DPM in Azure (System Center Data Protection Manager), usare il comando seguente per eliminare i dati di cui è stato eseguito il backup in Azure.For on-premises machines protected using MABS (Microsoft Azure Backup Server) or DPM to Azure (System Center Data Protection Manager), use the following command to delete the backed-up data in Azure.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Pubblicare il seguente messaggio di richiesta:

   *Backup di Microsoft Azure* Sei sicuro di voler rimuovere questo criterio di backup? I dati di backup eliminati verranno conservati per 14 giorni. Dopo tale periodo, i dati di backup verranno eliminati definitivamente. <br/>
   [Y] Sì [A] Sì a tutti [N] No [L] No [S] Sospendi [?] Help (il valore predefinito è "Y"):*

Dopo aver eliminato i dati di cui è stato eseguito il backup, annullare la registrazione di tutti i contenitori e i server di gestione locali.

- Per le cartelle locali protette con il backup di Azure Backup Agent (MARS) in Azure:For on-premises Files and Folders protected using Azure Backup Agent (MARS) backing up to Azure:

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

    [Ulteriori informazioni](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) sull'annullamento della registrazione di un Windows Server o di un altro contenitore dall'insieme di credenziali.

- Per i computer locali protetti tramite MABS (Server di backup di Microsoft Azure) o DPM in Azure (System Center Data Protection Manage:

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

    [Ulteriori informazioni](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) sull'annullamento della registrazione di un contenitore di gestione del backup dall'insieme di credenziali.

Dopo l'eliminazione definitiva dei dati di cui è stato eseguito il backup e l'annullamento della registrazione di tutti i contenitori, procedere all'eliminazione del vault.

Per eliminare un insieme di credenziali dei servizi di ripristino:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[Ulteriori informazioni](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) sull'eliminazione di un vault dei servizi di ripristino.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>Eliminare l'insieme di credenziali dei servizi di ripristino tramite l'interfaccia della riga di comando

Leggere innanzitutto la sezione **[Prima di iniziare](#before-you-start)** per comprendere le dipendenze e il processo di eliminazione dell'insieme di credenziali.

> [!NOTE]
> Attualmente, l'interfaccia della riga di comando di Backup di Azure supporta la gestione solo dei backup delle macchine virtuali di Azure, pertanto il comando seguente per eliminare l'insieme di credenziali funziona solo se l'insieme di credenziali contiene backup di macchine virtuali di Azure.Currently, Azure Backup CLI supports managing only Azure VM backups, so the following command to delete the vault works only if the vault contains Azure VM backups. Non è possibile eliminare un insieme di credenziali usando l'interfaccia della riga di comando di Backup di Azure, se l'insieme di credenziali contiene qualsiasi elemento di backup di tipo diverso dalle macchine virtuali di Azure.You cannot delete a vault using Azure Backup CLI, if the vault contains any backup item of type other than Azure VMs.

Per eliminare l'insieme di credenziali dei servizi di ripristino esistente, eseguire le operazioni seguenti:

- Per interrompere la protezione ed eliminare i dati di backup

    ```azurecli
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    Per ulteriori informazioni, vedere questo [articolo](/cli/azure/backup/protection#az-backup-protection-disable).

- Eliminare un insieme di credenziali dei servizi di ripristino esistente:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    Per altre informazioni, vedere questo [articoloFor](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest) more information, see this article

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Eliminare l'insieme di credenziali di Servizi di ripristino tramite Azure Resource ManagerDelete the Recovery Services vault by using Azure Resource Manager

Questa opzione per eliminare l'insieme di credenziali dei servizi di ripristino è consigliata solo se tutte le dipendenze vengono rimosse e viene ancora visualizzato l'errore di eliminazione del *Vault.* Provare uno o tutti i seguenti suggerimenti:

- Dal riquadro **Essentials** del menu dell'insieme di credenziali, verificare che non siano elencati elementi di backup, server di gestione di backup o elementi replicati. Se sono presenti elementi di backup, fare riferimento alla sezione Prima di [iniziare.](#before-you-start)
- Provare a eliminare nuovamente [il vault dal portale.](#delete-the-recovery-services-vault)
- Se tutte le dipendenze vengono rimosse e viene ancora visualizzato l'errore di eliminazione del *Vault,* utilizzare lo strumento ARMClient per eseguire la procedura seguente (dopo la nota).

1. Vai a [chocolatey.org](https://chocolatey.org/) per scaricare e installare Chocolatey. Quindi, installare ARMClient eseguendo il comando seguente:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Accedere all'account Azure e quindi eseguire il comando seguente:Sign in to your Azure account, and then run the following command:

    `ARMClient.exe login [environment name]`

3. Nel portale di Azure raccogliere l'ID sottoscrizione e il nome del gruppo di risorse per l'insieme di credenziali da eliminare.

Per ulteriori informazioni sul comando ARMClient , vedere [README DI ARMClient](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Usare il client Azure Resource Manager per eliminare un insieme di credenziali di Servizi di ripristinoUse the Azure Resource Manager client to delete a Recovery Services vault

1. Eseguire il comando seguente usando l'ID sottoscrizione, il nome del gruppo di risorse e il nome dell'insieme di credenziali. Se non si dispone di dipendenze, l'insieme di credenziali viene eliminato quando si esegue il comando seguente:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```

2. Se l'insieme di credenziali non è vuoto, verrà visualizzato il seguente messaggio di errore: *Impossibile eliminare l'insieme di credenziali in quanto sono presenti risorse esistenti all'interno* di questo insieme di credenziali. Per rimuovere un elemento protetto o un contenitore all'interno di un vault, eseguire il comando seguente:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Nel portale di Azure verificare che l'insieme di credenziali sia stato eliminato.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sugli archivi dei servizi di ripristino](backup-azure-recovery-services-vault-overview.md)<br/>
[Informazioni sul monitoraggio e la gestione degli insiemi di credenziali di Servizi di ripristino](backup-azure-manage-windows-server.md)
