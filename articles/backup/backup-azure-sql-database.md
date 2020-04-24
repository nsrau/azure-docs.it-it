---
title: Eseguire un backup dei database SQL Server in Azure
description: Questo articolo illustra come eseguire il backup di SQL Server in Azure. L'articolo spiega inoltre il recupero di SQL Server.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 537257733d7693598fd8007da6ce12c28fbeb02a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408761"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Informazioni sul backup di SQL Server in macchine virtuali di Azure

[Backup di Azure](backup-overview.md) offre una soluzione specializzata basata sul flusso per eseguire il backup SQL Server in esecuzione in macchine virtuali di Azure. Questa soluzione è allineata ai vantaggi del backup con infrastruttura zero, alla conservazione a lungo termine e alla gestione centrale del backup di Azure. Offre inoltre i seguenti vantaggi specifici per SQL Server:

1. Backup con supporto del carico di lavoro che supportano tutti i tipi di backup: completo, differenziale e log
2. 15-min RPO (obiettivo del punto di ripristino) con backup del log frequenti
3. Recupero temporizzato fino a un secondo
4. Backup e ripristino a livello di database singolo

Per visualizzare gli scenari di backup e ripristino attualmente supportati, fare riferimento alla [matrice di supporto](sql-support-matrix.md#scenario-support).

## <a name="backup-process"></a>Processo di backup

Questa soluzione sfrutta le API native SQL per eseguire i backup dei database SQL.

* Dopo aver specificato la macchina virtuale di SQL Server da proteggere e aver eseguito la query per trovare i database al suo interno, il servizio Backup di Azure installerà un'estensione di backup di carichi di lavoro nella macchina virtuale denominata `AzureBackupWindowsWorkload`.
* Questa estensione è costituita da un coordinatore e da un plug-in SQL. Mentre il coordinatore è responsabile di attivare i flussi di lavoro per varie operazioni, come la configurazione del backup, il backup e il ripristino, il plug-in gestisce il flusso di dati effettivo.
* Per individuare i database in questa VM, Backup di Azure crea l'account `NT SERVICE\AzureWLBackupPluginSvc`. Questo account viene usato per il backup e il ripristino e richiede le autorizzazioni sysadmin SQL. L'account `NT SERVICE\AzureWLBackupPluginSvc` è un [account del servizio virtuale](https://docs.microsoft.com/windows/security/identity-protection/access-control/service-accounts#virtual-accounts) e pertanto non richiede alcuna gestione delle password. Backup di Azure sfrutta l'account `NT AUTHORITY\SYSTEM` per l'individuazione o l'interrogazione dei database, di conseguenza questo account deve essere un account di accesso pubblico in SQL. Se la VM SQL Server non è stata creata da Azure Marketplace, è possibile ricevere un errore **UserErrorSQLNoSysadminMembership**. In tal caso [seguire queste istruzioni](#set-vm-permissions).
* Dopo l'attivazione della configurazione della protezione nei database selezionati, il servizio di backup configura il coordinatore con le pianificazioni di backup e altri dettagli sui criteri, che l'estensione memorizza nella cache locale della VM.
* Nell'orario pianificato il coordinatore comunica con il plug-in, che avvia lo streaming dei dati di backup dal server SQL tramite VDI.  
* Il plug-in invia i dati direttamente all'insieme di credenziali dei servizi di ripristino, eliminando così la necessità di una posizione per la gestione temporanea. I dati vengono crittografati e archiviati dal servizio Backup di Azure negli account di archiviazione.
* Al termine del trasferimento dei dati, il coordinatore conferma il commit con il servizio di backup.

  ![Architettura del backup SQL](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare, verificare quanto segue:

1. Assicurarsi che sia in esecuzione un'istanza di SQL Server in Azure. È possibile [creare rapidamente un'istanza di SQL Server](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) nel Marketplace.
2. Vedere le [considerazioni sulla funzionalità](sql-support-matrix.md#feature-consideration-and-limitations) e il [supporto degli scenari](sql-support-matrix.md#scenario-support).
3. [Esaminare le domande comuni](faq-backup-sql-server.md) su questo scenario.

## <a name="set-vm-permissions"></a>Impostare le autorizzazioni della VM

  Quando si esegue l'individuazione in SQL Server, Backup di Azure esegue le operazioni seguenti:

* Aggiunge l'estensione AzureBackupWindowsWorkload.
* Crea un account NT SERVICE\AzureWLBackupPluginSvc per individuare i database nella macchina virtuale. Questo account viene usato per il backup e il ripristino e richiede le autorizzazioni di amministratore di sistema SQL.
* Individua i database in esecuzione in una macchina virtuale. Backup di Azure usa l'account NT AUTHORITY\SYSTEM. Questo account deve essere un account di accesso pubblico in SQL.

Se la VM di SQL Server non è stata creata in Azure Marketplace o se si usa SQL 2008 o 2008 R2, potrebbe venire generato un errore **UserErrorSQLNoSysadminMembership**.

Per concedere le autorizzazioni in caso di **SQL 2008** e **2008 R2** in esecuzione in Windows 2008 R2, vedere [qui](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2).

Per tutte le altre versioni, correggere le autorizzazioni con i passaggi seguenti:

  1. Usare un account con le autorizzazioni sysadmin SQL Server per accedere a SQL Server Management Studio (SSMS). A meno che non siano necessarie autorizzazioni speciali, l'autenticazione di Windows dovrebbe funzionare.
  2. Nella SQL Server aprire la cartella **Security/Logins** .

      ![Aprire la cartella Sicurezza/Account di accesso per vedere gli account](./media/backup-azure-sql-database/security-login-list.png)

  3. Fare clic con il pulsante destro del mouse sulla cartella **accessi** e scegliere **nuovo account di accesso**. In **Account di accesso - Nuovo** selezionare **Cerca**.

      ![Nella finestra di dialogo Account di accesso - Nuovo selezionare Cerca](./media/backup-azure-sql-database/new-login-search.png)

  4. L'account di servizio virtuale di Windows **NT SERVICE\AzureWLBackupPluginSvc** è stato creato durante la fase di registrazione della macchina virtuale e di individuazione SQL. Immettere il nome dell'account come mostrato nella casella **Immettere il nome dell'oggetto da selezionare**. Selezionare **Controlla nomi** per risolvere il nome. Fare clic su **OK**.

      ![Selezionare Controlla nomi per risolvere il nome del servizio sconosciuto](./media/backup-azure-sql-database/check-name.png)

  5. In **Ruoli server** assicurarsi che sia selezionato il ruolo **sysadmin**. Fare clic su **OK**. Le autorizzazioni necessarie a questo punto devono essere presenti.

      ![Verificare che sia selezionato il ruolo del server sysadmin](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Associare ora il database all'insieme di credenziali di Servizi di ripristino. Nell'elenco dei **server protetti** del portale di Azure fare clic con il pulsante destro del mouse sul server in stato di errore e scegliere **Individua di nuovo i database**.

      ![Verificare che il server abbia le autorizzazioni appropriate](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Verificare l'avanzamento nell'area **Notifiche**. Quando i database selezionati sono stati individuati, viene visualizzato un messaggio di conferma.

      ![Messaggio di distribuzione riuscita](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Se sono installate più istanze di SQL Server, è necessario aggiungere l'autorizzazione sysadmin per l'account **NT Service\AzureWLBackupPluginSvc** a tutte le istanze di SQL.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>Concedere le autorizzazioni di amministratore di sistema SQL per SQL 2008 e SQL 2008 R2

Aggiungere gli account di accesso **NT AUTHORITY\SYSTEM** e **NT Service\AzureWLBackupPluginSvc** all'istanza di SQL Server:

1. Passare all'istanza di SQL Server in Esplora oggetti.
2. Passare a Sicurezza -> Account di accesso
3. Fare clic con il pulsante destro del mouse su Account di accesso e scegliere *Nuovo account di accesso*.

    ![Nuovo account di accesso con SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Passare alla scheda Generale e immettere **NT AUTHORITY\SYSTEM** come ID di accesso.

    ![ID di accesso per SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Passare a *Ruoli del server* e scegliere i ruoli *public* e *sysadmin*.

    ![Scelta dei ruoli in SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Passare a *Stato*. Selezionare *Concedi* per Autorizzazione per la connessione al motore di database e selezionare *Abilitato* per Account di accesso.

    ![Concedere le autorizzazioni in SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Fare clic su OK.
8. Ripetere la stessa procedura (passaggi da 1 a 7 precedenti) per aggiungere l'account di accesso NT Service\AzureWLBackupPluginSvc all'istanza di SQL Server. Se l'account di accesso esiste già, assicurarsi che abbia il ruolo del server sysadmin e che in Stato siano selezionate l'opzione Concedi per Autorizzazione per la connessione al motore di database e l'opzione Abilitato per Account di accesso.
9. Dopo aver concesso l'autorizzazione, **riindividuare** i database nel portale: **->** carico di **->** lavoro infrastruttura di backup dell'insieme di credenziali nella macchina virtuale di Azure:

    ![Opzione Individua di nuovo i database nel portale di Azure](media/backup-azure-sql-database/sql-rediscover-dbs.png)

In alternativa, è possibile automatizzare la concessione delle autorizzazioni eseguendo i comandi di PowerShell seguenti in modalità di amministrazione. Per impostazione predefinita, il nome dell'istanza è impostato su MSSQLSERVER. Modificare l'argomento relativo al nome dell'istanza nello script, se necessario:

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string] $InstanceName = "MSSQLSERVER"
)
if ($InstanceName -eq "MSSQLSERVER")
{
    $fullInstance = $env:COMPUTERNAME   # In case it is the default SQL Server Instance
}
else
{
    $fullInstance = $env:COMPUTERNAME + "\" + $InstanceName   # In case of named instance
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT Service\AzureWLBackupPluginSvc', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT AUTHORITY\SYSTEM', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni](backup-sql-server-database-azure-vms.md) sul backup dei database di SQL Server.
* [Informazioni](restore-sql-database-azure-vm.md) sul ripristino dei database SQL Server di cui è stato eseguito il backup.
* [Informazioni](manage-monitor-sql-database-backup.md) sulla gestione dei database SQL Server di cui è stato eseguito il backup.
