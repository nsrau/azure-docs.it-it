---
title: Risolvere i problemi relativi al backup del database di SQL ServerTroubleshoot SQL Server database backup
description: Informazioni sulla risoluzione dei problemi relativi al backup di database di SQL Server eseguiti su macchine virtuali di Azure con Backup di Azure.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: 8d49adb0ab741903ccb2989cfeb4ceaef2e8a38d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408617"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Risolvere i problemi relativi al backup del database di SQL Server usando Backup di AzureTroubleshoot SQL Server database backup by using Azure Backup

Questo articolo fornisce informazioni sulla risoluzione dei problemi per i database di SQL Server in esecuzione nelle macchine virtuali di Azure.This article provides troubleshooting information for SQL Server databases running on Azure virtual machines.

Per altre informazioni sul processo di backup e sulle limitazioni, vedere Informazioni sul backup di SQL Server nelle macchine virtuali di Azure.For more information about the backup process and limitations, see [About SQL Server backup in Azure VMs](sql-support-matrix.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>Autorizzazioni di SQL Server

Per configurare la protezione per un database di SQL Server in una macchina virtuale, è necessario installare l'estensione **AzureBackupWindowsWorkload** in tale macchina virtuale. Se viene visualizzato l'errore **UserErrorSQLNoSysadminMembership**, significa che l'istanza di SQL Server non dispone delle autorizzazioni di backup necessarie. Per correggere questo errore, seguire la procedura descritta in [Impostare le autorizzazioni della macchina virtuale](backup-azure-sql-database.md#set-vm-permissions).

## <a name="troubleshoot-discover-and-configure-issues"></a>Risolvere i problemi di individuazione e configurazioneTroubleshoot discover and configure issues

Dopo aver creato e configurato un insieme di credenziali di Servizi di ripristino, l'individuazione dei database e la configurazione del backup sono un processo in due passaggi.<br>

![sql](./media/backup-azure-sql-database/sql.png)

Durante la configurazione di backup, se la macchina virtuale SQL e le relative istanze non sono visibili nei database di individuazione nelle macchine virtuali e Configurazione backup (fare riferimento all'immagine precedente) verificare che:During the backup configuration, if the SQL VM and its instances are not visible in the **Discovery DBs in VMs** and **Configure Backup** (refer to above image) ensure that:

### <a name="step-1-discovery-dbs-in-vms"></a>Passaggio 1: Individuazione di database nelle macchine virtualiStep 1: Discovery DMs in VMs

- Se la macchina virtuale non è elencata nell'elenco delle macchine virtuali individuate e non è registrata per il backup SQL in un altro insieme di credenziali, seguire i passaggi di backup di [Discovery SQL Server.](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#discover-sql-server-databases)

### <a name="step-2-configure-backup"></a>Passaggio 2: Configurare il backupStep 2: Configure Backup

- Se l'insieme di credenziali in cui è registrata la macchina virtuale SQL nello stesso insieme di credenziali utilizzato per proteggere i database, seguire i passaggi di configurazione del [backup.](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#configure-backup)

Se la macchina virtuale SQL deve essere registrata nel nuovo insieme di credenziali, è necessario annullarne la registrazione dall'insieme di credenziali precedente.  L'annullamento della registrazione di una macchina virtuale SQL dall'insieme di credenziali richiede che tutte le origini dati protette siano protette e quindi è possibile eliminare i dati di cui è stato eseguito il backup. L'eliminazione dei dati di cui è stato eseguito il backup è un'operazione distruttiva.  Dopo aver esaminato e preso tutte le precauzioni necessarie per annullare la registrazione della macchina virtuale SQL, registrare la stessa macchina virtuale con un nuovo insieme di credenziali e ripetere l'operazione di backup.

## <a name="troubleshoot-backup-and-recovery-issues"></a>Risolvere i problemi di backup e ripristino  

A volte, possono verificarsi errori casuali nelle operazioni di backup e ripristino o tali operazioni potrebbero bloccarsi. Ciò potrebbe essere dovuto a programmi antivirus nella macchina virtuale. Come procedura consigliata, suggeriamo i passaggi seguenti:As a best practice, we suggest the following steps:

1. Escludere le seguenti cartelle dalla scansione antivirus:

    `C:\Program Files\Azure Workload Backup` `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.WorkloadBackup.Edp.AzureBackupWindowsWorkload`

    Sostituire `C:\` con la lettera di *SystemDrive*.

1. Escludere i tre processi seguenti in esecuzione all'interno di una macchina virtuale dall'analisi antivirus:Exclude the following three processes running within a VM from antivirus scanning:

    - IaasWLPluginSvc.exe
    - IaasWorkloadCoordinaorService.exe
    - TriggerExtensionJob.exe

1. SQL offre anche alcune linee guida per lavorare con i programmi antivirus. Vedere [questo articolo](https://support.microsoft.com/help/309422/choosing-antivirus-software-for-computers-that-run-sql-server) per informazioni dettagliate.

## <a name="error-messages"></a>messaggi di errore

### <a name="backup-type-unsupported"></a>Tipo di backup non supportato

| Gravità | Descrizione | Possibili cause | Azione consigliata |
|---|---|---|---|
| Avviso | Le impostazioni correnti per questo database non supportano determinati tipi di backup presenti nei criteri associati. | <li>Solo un'operazione di backup completo del database può essere eseguita sul database master. Non è possibile eseguire il backup differenziale né il backup del log delle transazioni. </li> <li>Qualsiasi database nel modello di recupero con registrazione minima non consente il backup dei log delle transazioni.</li> | Modificare le impostazioni del database in modo che tutti i tipi di backup nei criteri siano supportati. In alternativa, modificare il criterio corrente per includere solo i tipi di backup supportati. In caso contrario, i tipi di backup non supportati verranno ignorati durante il backup pianificato o il processo di backup avrà esito negativo per il backup su richiesta.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il database SQL non supporta il tipo di backup richiesto. | Si verifica quando il modello di recupero del database non consente il tipo di backup richiesto. L'errore può verificarsi nelle situazioni seguenti: <br/><ul><li>Un database che utilizza un modello di recupero semplice non consente il backup del log.</li><li>I backup differenziali e di log non sono consentiti per un database master.</li></ul>Per altre informazioni, vedere la documentazione relativa ai modelli di recupero di [SQL Server.For](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) more detail, see the SQL Server recovery models documentation. | Se il backup del log non riesce per il database nel modello di recupero con registrazione minima, provare una di queste opzioni:<ul><li>Se il database è in modalità di recupero con registrazione minima, disabilitare i backup del log.</li><li>Usare la [documentazione](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) di SQL Server per modificare il modello di recupero del database in full o bulk logged. </li><li> Se non si vuole modificare il modello di recupero ed esistono criteri standard per eseguire il backup più database che non possono essere modificati, ignorare l'errore. I backup completi e differenziali funzioneranno come da pianificazione. Verranno ignorati i backup del log, come previsto in questo caso.</li></ul>Se si tratta di un database master e è stato configurato il backup differenziale o del log, utilizzare uno dei passaggi seguenti:<ul><li>Utilizzare il portale per impostare la pianificazione dei criteri di backup per il database master su full.</li><li>Se esistono criteri standard per eseguire il backup di più database che non possono essere modificati, ignorare l'errore. Il backup completo funzionerà come da pianificazione. Non verranno eseguiti i backup differenziali e del log, come previsto in questo caso.</li></ul> |
| L'operazione è stata annullata perché è già in esecuzione un'operazione in conflitto nello stesso database. | Vedere il [post del blog sulle limitazioni](https://deep.data.blog/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database/) di backup e ripristino eseguite contemporaneamente.| [Utilizzare SQL Server Management Studio (SSMS) per monitorare i processi](manage-monitor-sql-database-backup.md)di backup. Dopo l'esito negativo dell'operazione in conflitto, riavviare l'operazione.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il database SQL non esiste. | Il database è stato eliminato o rinominato. | Controllare se il database è stato eliminato o rinominato accidentalmente.<br/><br/> Se il database è stato eliminato accidentalmente, per continuare con i backup, ripristinare il database nel percorso originale.<br/><br/> Se il database è stato eliminato e non sono necessari backup futuri, nell'insieme di credenziali Servizi di ripristino selezionare **Interrompi backup** con Mantieni dati **di backup** o Elimina dati di **backup**. Per ulteriori informazioni, vedere Gestire e monitorare i database di SQL Server di cui è [stato eseguito il backup.](manage-monitor-sql-database-backup.md)

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| La catena di log è interrotta. | Il backup del database o della macchina virtuale viene eseguito tramite un'altra soluzione di backup, che tronca la catena di log.|<ul><li>Verificare se è in uso un'altra soluzione di backup o uno script. In tal caso, arrestare l'altra soluzione di backup. </li><li>Se il backup è stato un backup del log su richiesta, attivare un backup completo per avviare una nuova catena di log. Per i backup del log pianificati, non è necessaria alcuna azione perché il servizio Backup di Azure attiverà automaticamente un backup completo per risolvere questo problema.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il servizio Backup di Azure non riesce a connettersi all'istanza SQL. | Backup di Azure non è in grado di connettersi all'istanza di SQL Server.Azure Backup can't connect to the SQL Server instance. | Usare i dettagli aggiuntivi nel menu degli errori del portale di Azure per limitare le cause principali. Fare riferimento a [Risolvere i problemi di connessione al motore di database di SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) per correggere l'errore.<br/><ul><li>Se le impostazioni SQL predefinite non consentono connessioni remote, modificare le impostazioni. Per informazioni sulla modifica delle impostazioni, vedere gli articoli seguenti:<ul><li>[MSSQLSERVER_-1](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-1-database-engine-error?view=sql-server-ver15)</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>In caso di problemi di accesso, utilizzare questi collegamenti per risolverli:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Manca il primo backup completo per questa origine dati. | Manca un backup completo per il database. I backup differenziali e dei log sono elementi padre di un backup completo, pertanto assicurarsi di eseguire backup completi prima di attivare backup differenziali o di log. | Attivare un backup completo su richiesta.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Cannot take backup as transaction log for the data source is full (Non è possibile eseguire il backup perché il log delle transazioni per l'origine dati è pieno). | Lo spazio del log delle transazioni del database è pieno. | Per risolvere questo problema, fare riferimento alla [documentazione](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error)di SQL Server . |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Un database con lo stesso nome esiste già nel percorso di destinazione | La destinazione di ripristino di destinazione dispone già di un database con lo stesso nome.  | <ul><li>Modificare il nome del database di destinazione.</li><li>In alternativa, utilizzare l'opzione forza sovrascrittura nella pagina di ripristino.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il ripristino non è riuscito perché non è stato possibile portare offline il database. | Durante l'esecuzione di un ripristino, il database di destinazione deve essere portato offline. Backup di Azure non può portare questi dati offline. | Usare i dettagli aggiuntivi nel menu degli errori del portale di Azure per limitare le cause principali. Per altre informazioni, vedere la [documentazione di SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Cannot find the server certificate with thumbprint on the target (Non è possibile trovare il certificato del server con l'identificazione personale). | Il database master nell'istanza di destinazione non dispone di un'identificazione personale di crittografia valida. | Importare l'identificazione personale del certificato valido usata nell'istanza di origine nell'istanza di destinazione. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il backup del log usato per il ripristino contiene modifiche con registrazione minima delle operazioni bulk. Non è utilizzabile per l'arresto in un punto arbitrario nel tempo in base alle linee guida di SQL. | Quando un database è in modalità di recupero con registrazione minima delle operazioni bulk, i dati tra una transazione con registrazione minima delle operazioni bulk e la transazione di log successiva non possono essere recuperati. | Scegliere un punto nel tempo diverso per il ripristino. [Scopri di più](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server?view=sql-server-ver15).

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Non è possibile soddisfare le preferenze di backup per il gruppo di disponibilità AlwaysOn SQL perché alcuni nodi del gruppo di disponibilità non sono registrati. | I nodi necessari per eseguire i backup non sono registrati o non sono raggiungibili. | <ul><li>Assicurarsi che tutti i nodi necessari per eseguire i backup di questo database siano registrati e integri, quindi ripetere l'operazione.</li><li>Modificare la preferenza di backup per il gruppo di disponibilità AlwaysOn di SQL Server.Change the backup preference for the SQL Server Always On availability group.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| L'istanza di SQL Server è stata arrestata e non è accessibile al servizio Backup di Azure. | La macchina virtuale è stata arrestata. | Verificare che l'istanza di SQL Server sia in esecuzione. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il servizio Backup di Azure usa l'agente guest di macchine virtuali di Azure per l'esecuzione del backup ma l'agente guest non è disponibile nel server di destinazione. | L'agente guest non è abilitato o non è integro. | [Installare l'agente guest di macchine virtuali](../virtual-machines/extensions/agent-windows.md) manualmente. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| La finalità di protezione automatica è stata rimossa o non è più valida. | Quando si abilita la protezione automatica in un'istanza di SQL Server, **configurare** i processi di backup eseguiti per tutti i database in tale istanza. Se si disabilita la protezione automatica mentre i processi sono in esecuzione, i processi **in corso** vengono annullati con questo codice di errore. | Abilitare nuovamente la protezione automatica per proteggere tutti i database rimanenti. |

### <a name="clouddosabsolutelimitreached"></a>CloudDosAbsoluteLimitReached

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
L'operazione è bloccata poiché è stato raggiunto il limite di operazioni consentite in 24 ore. | Una volta raggiunto il limite massimo consentito per un'operazione in un intervallo di 24 ore, viene visualizzato questo errore. <br> Ad esempio: se è stato raggiunto il limite per il numero di processi di backup di configurazione che possono essere attivati al giorno e si tenta di configurare il backup su un nuovo elemento, verrà visualizzato questo errore. | In genere, ritentare l'operazione dopo 24 ore risolve questo problema. Tuttavia, se il problema persiste, è possibile contattare il supporto tecnico Microsoft per assistenza.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedConRetritry

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
Il funzionamento è bloccato poiché l'insieme di credenziali ha raggiunto il limite massimo per tali operazioni consentite in un intervallo di 24 ore. | Una volta raggiunto il limite massimo consentito per un'operazione in un intervallo di 24 ore, viene visualizzato questo errore. Questo errore si verifica in genere quando sono presenti operazioni su larga scala, ad esempio criteri di modifica o protezione automatica. A differenza di CloudDosAbsoluteLimitReached, non c'è molto che puoi fare per risolvere questo stato, infatti, il servizio Backup di Azure ritenterà le operazioni internamente per tutti gli elementi in questione.<br> Ad esempio: se si dispone di un numero elevato di origini dati protette con un criterio e si tenta di modificare tale criterio, verranno attivati i processi di protezione di configurazione per ognuno degli elementi protetti e talvolta può raggiungere il limite massimo consentito per tali operazioni al giorno.| Il servizio Backup di Azure ritenterà automaticamente questa operazione dopo 24 ore.

### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
La macchina virtuale non è in grado di contattare il servizio Backup di Azure a causa di problemi di connettività Internet.The VM is not able to contact Azure Backup service due to internet connectivity issues. | La macchina virtuale richiede la connettività in uscita al servizio Backup di Azure, ad Archiviazione di Azure o ai servizi di Azure Active Directory.The VM needs outbound connectivity to Azure Backup Service, Azure Storage or Azure Active Directory services.| - Se si usa il gruppo di sicurezza di rete per limitare la connettività, è necessario usare il tag del servizio AzureBackup per consentire l'accesso in uscita ai servizi Backup di Azure al servizio di backup di Azure, ad Archiviazione di Azure o ad Azure Active Directory.- If you use NSG to restrict connectivity, then you should use the AzureBackup service tag to allows outbound access to Azure Backup to Azure Backup Service, Azure Storage or Azure Active Directory services. Seguire questi [passaggi](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#allow-access-using-nsg-tags) per concedere l'accesso.<br>- Verificare che il DNS risolva gli endpoint di Azure.- Ensure DNS is resolving Azure endpoints.<br>- Controllare se la macchina virtuale è dietro un servizio di bilanciamento del carico che blocca l'accesso a Internet.- Check if the VM is behind a load balancer blocking internet access. Assegnando l'IP pubblico alle macchine virtuali, l'individuazione funzionerà.<br>- Verificare che non vi siano firewall/antivirus/proxy che bloccano le chiamate ai tre servizi di destinazione precedenti.

## <a name="re-registration-failures"></a>Errori di nuova registrazioneRe-registration failures

Verificare la presenza di uno o più dei seguenti sintomi prima di attivare l'operazione di registrazione di nuovo:

* Tutte le operazioni (ad esempio backup, ripristino e configurazione del backup) hanno esito negativo nella macchina virtuale con uno dei seguenti codici di errore: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionD.**
* Se nell'area **Stato backup** per l'elemento di backup è visualizzato **Non raggiungibile**, escludere tutte le altre cause che potrebbero avere lo stesso stato:

  * Mancanza di autorizzazione per eseguire operazioni relative al backup nella macchina virtuale.
  * Arresto della macchina virtuale, pertanto non è possibile eseguire backup.
  * Problemi di rete.

   ![ri-registrazione della macchina virtuale](./media/backup-azure-sql-database/re-register-vm.png)



* Nel caso di un gruppo di disponibilità AlwaysOnAlways On availability group, i backup hanno iniziato a non riuscire dopo aver modificato la preferenza di backup o dopo un failover.

Questi sintomi possono verificarsi per uno o più dei seguenti motivi:

* Un'estensione è stata eliminata o disinstallata dal portale.
* Un'estensione è stata disinstallata dal **Pannello di controllo** nella macchina virtuale in Disinstalla o modifica **programma**.
* La macchina virtuale è stata ripristinata nel tempo tramite il ripristino sul posto del disco.
* La macchina virtuale è stata arrestata per un periodo prolungato, pertanto la configurazione dell'estensione su di essa è scaduta.
* La macchina virtuale è stata eliminata e un'altra macchina virtuale è stata creata con lo stesso nome e nello stesso gruppo di risorse della macchina virtuale eliminata.
* Uno dei nodi del gruppo di disponibilità non ha ricevuto la configurazione di backup completa. Ciò può verificarsi quando il gruppo di disponibilità viene registrato nell'insieme di credenziali o quando viene aggiunto un nuovo nodo.

Negli scenari precedenti è consigliabile attivare un'operazione di registrazione nella macchina virtuale. Vedere qui per istruzioni su come eseguire questa attività in PowerShell.See [here](https://docs.microsoft.com/azure/backup/backup-azure-sql-automation#enable-backup) for instructions on how to perform this task in PowerShell.

## <a name="size-limit-for-files"></a>Limite di dimensione per i file

La dimensione totale della stringa dei file dipende non solo dal numero di file, ma anche dai loro nomi e percorsi. Per ogni file di database, ottenere il nome del file logico e il percorso fisico. È possibile utilizzare questa query SQL:You can use this SQL query:

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Ora disporli nel seguente formato:

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Ad esempio:

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Se la dimensione della stringa del contenuto supera i 20.000 byte, i file di database vengono archiviati in modo diverso. Durante il ripristino, non sarà possibile impostare il percorso del file di destinazione per il ripristino. The files will be restored to the default SQL path provided by SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Ignorare il percorso del file di ripristino di destinazione predefinito

È possibile sostituire il percorso del file di ripristino di destinazione durante l'operazione di ripristino inserendo un file JSON contenente il mapping del file di database al percorso di ripristino di destinazione. Creare `database_name.json` un file e inserirlo nel percorso *C:*.

Il contenuto del file deve essere nel seguente formato:

```json
[
  {
    "Path": "<Restore_Path>",
    "LogicalName": "<LogicalName>",
    "IsDir": "false"
  },
  {
    "Path": "<Restore_Path>",
    "LogicalName": "LogicalName",
    "IsDir": "false"
  },  
]
```

Ad esempio:

```json
[
  {
   "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
   "LogicalName": "testdb7",
   "IsDir": "false"
  },
  {
    "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
    "LogicalName": "testdb7_log",
    "IsDir": "false"
  },  
]
```

Nel contenuto precedente è possibile ottenere il nome logico del file di database utilizzando la query SQL seguente:

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```

Questo file deve essere posizionato prima di attivare l'operazione di ripristino.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Backup di Azure per le macchine virtuali di SQL Server (anteprima pubblica), vedere Backup di [Azure per macchine virtuali SQL.](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup)
