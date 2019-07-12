---
title: Risolvere i problemi di backup del database di SQL Server con Backup di Azure | Microsoft Docs
description: Informazioni sulla risoluzione dei problemi relativi al backup di database di SQL Server eseguiti su macchine virtuali di Azure con Backup di Azure.
services: backup
author: anuragm
manager: sivan
ms.service: backup
ms.topic: article
ms.date: 06/18/2019
ms.author: anuragm
ms.openlocfilehash: b2822a3c7dfa23065f2cbd35ef4e506efae026f2
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704855"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Risolvere i problemi di backup del database di SQL Server con Backup di Azure

Questo articolo fornisce informazioni sulla risoluzione dei problemi per i database di SQL Server in esecuzione in macchine virtuali di Azure.

Per altre informazioni sul processo di backup e le limitazioni, vedere [backup su SQL Server in macchine virtuali di Azure](backup-azure-sql-database.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>Autorizzazioni di SQL Server

Per configurare la protezione per un database di SQL Server in una macchina virtuale, è necessario installare il **AzureBackupWindowsWorkload** estensione su tale macchina virtuale. Se viene visualizzato l'errore **UserErrorSQLNoSysadminMembership**, significa che l'istanza di SQL Server non ha le autorizzazioni necessarie di backup. Per correggere questo errore, seguire i passaggi descritti in [le autorizzazioni di macchine Virtuali del Set](backup-azure-sql-database.md#set-vm-permissions).

## <a name="error-messages"></a>messaggi di errore

### <a name="backup-type-unsupported"></a>Tipo di backup non supportato

| severity | DESCRIZIONE | Possibili cause | Azione consigliata |
|---|---|---|---|
| Avviso | Impostazioni correnti per questo database non supportano alcuni tipi di backup presente nei criteri associati. | <li>Solo un'operazione di backup completo del database può essere eseguita nel database master. È possibile un backup differenziale né backup del log delle transazioni. </li> <li>Qualsiasi database nel modello di recupero con registrazione minima non consente il backup dei log delle transazioni.</li> | Modificare le impostazioni del database in modo che tutti i tipi di backup nei criteri siano supportati. In alternativa, modificare i criteri correnti in modo da includere solo i tipi di backup supportati. In caso contrario, i tipi di backup non supportati verranno ignorati durante il backup pianificato o il processo di backup avrà esito negativo per backup ad hoc.


### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il database SQL non supporta il tipo di backup richiesto. | Si verifica quando il modello di recupero del database non consente il tipo di backup richiesto. L'errore può verificarsi nelle situazioni seguenti: <br/><ul><li>Un database che utilizza un modello di recupero con registrazione minima non consente backup del log.</li><li>I backup differenziali e del log non sono consentiti per un database master.</li></ul>Per altre informazioni, vedere la [modelli di recupero di SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) documentazione. | Se il backup del log ha esito negativo per il database nel modello di recupero con registrazione minima, provare una delle opzioni seguenti:<ul><li>Se il database è in modalità di recupero con registrazione minima, disabilitare i backup del log.</li><li>Usare la [documentazione di SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) per modificare il modello di recupero del database su completo o bulk registrate. </li><li> Se non si vuole modificare il modello di recupero ed esistono criteri standard per eseguire il backup più database che non possono essere modificati, ignorare l'errore. I backup completi e differenziali funzioneranno come da pianificazione. Verranno ignorati i backup del log, come previsto in questo caso.</li></ul>Se si tratta di un database master e aver configurato i backup differenziali o backup del log, utilizzare uno dei passaggi seguenti:<ul><li>Usare il portale per modificare la pianificazione dei criteri di backup per il database master, a full.</li><li>Se esistono criteri standard per eseguire il backup di più database che non possono essere modificati, ignorare l'errore. Il backup completo funzionerà come da pianificazione. Non verranno eseguiti i backup differenziali e del log, come previsto in questo caso.</li></ul> |
| L'operazione è stata annullata perché è già in esecuzione un'operazione in conflitto nello stesso database. | Vedere le [post di blog sulle limitazioni di backup e ripristino](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) eseguite simultaneamente.| [Usare SQL Server Management Studio (SSMS) per monitorare i processi di backup](manage-monitor-sql-database-backup.md). Dopo l'operazione in conflitto non riesce, riavviare l'operazione.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il database SQL non esiste. | Il database è stato eliminato o rinominato. | Controllare se il database è stato eliminato o rinominato accidentalmente.<br/><br/> Se il database è stato eliminato accidentalmente, per continuare con i backup, ripristinare il database nel percorso originale.<br/><br/> Se il database è stato eliminato e non devono selezionare i backup futuri, quindi nell'insieme di credenziali di servizi di ripristino **Interrompi backup** con **conserva i dati di Backup** oppure **Elimina dati di Backup**. Per altre informazioni, vedere [gestire e monitorare i database di SQL Server sottoposta a backup](manage-monitor-sql-database-backup.md).

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| La catena di log è interrotta. | Il database o la macchina virtuale è il backup tramite un'altra soluzione di backup, che tronca la catena di log.|<ul><li>Verificare se è in uso un'altra soluzione di backup o uno script. In tal caso, arrestare l'altra soluzione di backup. </li><li>Se il backup è un backup del log ad hoc, attivare un backup completo per avviare una nuova catena di log. Per i backup del log pianificati, è necessaria alcuna azione perché il servizio Backup di Azure attiva automaticamente un backup completo per risolvere questo problema.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il servizio Backup di Azure non riesce a connettersi all'istanza SQL. | Backup di Azure non è possibile connettersi all'istanza di SQL Server. | Usare i dettagli aggiuntivi dal menu di errore del portale di Azure per restringere le cause radice. Fare riferimento a [Risolvere i problemi di connessione al motore di database di SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) per correggere l'errore.<br/><ul><li>Se le impostazioni di SQL predefinita non consentono le connessioni remote, modificare le impostazioni. Vedere gli articoli seguenti per informazioni su come modificare le impostazioni:<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Se sono presenti problemi di accesso, usare i collegamenti seguenti per risolvere il problema:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Manca il primo backup completo per questa origine dati. | Manca un backup completo per il database. Log e i backup differenziali sono elementi padre di un backup completo, pertanto assicurarsi di eseguire backup completi prima di attivare backup differenziali o backup del log. | Attivare un backup completo ad hoc.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Cannot take backup as transaction log for the data source is full (Non è possibile eseguire il backup perché il log delle transazioni per l'origine dati è pieno). | Lo spazio del log delle transazioni del database è pieno. | Per risolvere questo problema, vedere la [documentazione di SQL Server](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Un database con lo stesso nome esiste già nel percorso di destinazione | La destinazione di ripristino di destinazione esiste già un database con lo stesso nome.  | <ul><li>Modificare il nome di database di destinazione.</li><li>In alternativa, usare l'opzione di sovrascrittura force nella pagina di ripristino.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il ripristino non è riuscito perché non è stato possibile portare offline il database. | Mentre si sta eseguendo un ripristino, il database di destinazione deve essere portato offline. Backup di Azure non è possibile portare offline i dati. | Usare i dettagli aggiuntivi dal menu di errore del portale di Azure per restringere le cause radice. Per altre informazioni, vedere la [documentazione di SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Cannot find the server certificate with thumbprint on the target (Non è possibile trovare il certificato del server con l'identificazione personale). | Il database master sull'istanza di destinazione non ha un'identificazione personale di crittografia valido. | Importare l'identificazione personale del certificato valido utilizzato nell'istanza di origine, all'istanza di destinazione. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il backup del log usato per il ripristino contiene modifiche con registrazione minima delle operazioni bulk. Non è utilizzabile per l'arresto in un punto arbitrario nel tempo in base alle linee guida di SQL. | Quando un database è in modalità di recupero con registrazione minima delle operazioni bulk, non è possibile recuperare i dati tra una transazione con registrazione minima delle operazioni bulk e il troncamento del log successivo. | Scegliere un altro punto nel tempo per il ripristino. [Altre informazioni](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105))


### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Non è possibile soddisfare le preferenze di backup per il gruppo di disponibilità AlwaysOn SQL perché alcuni nodi del gruppo di disponibilità non sono registrati. | I nodi necessari per eseguire i backup non sono registrati o non sono raggiungibili. | <ul><li>Assicurarsi che tutti i nodi necessari per eseguire il backup dei database vengono registrati e integri e quindi ripetere l'operazione.</li><li>Modificare le preferenze di backup per il gruppo di disponibilità SQL Server Always On.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| L'istanza di SQL Server è stata arrestata e non è accessibile al servizio Backup di Azure. | La macchina virtuale viene arrestata. | Assicurarsi che l'istanza di SQL Server sia in esecuzione. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il servizio Backup di Azure usa l'agente guest di macchine virtuali di Azure per l'esecuzione del backup ma l'agente guest non è disponibile nel server di destinazione. | L'agente guest non è abilitata o non è integro. | [Installare l'agente guest di macchine virtuali](../virtual-machines/extensions/agent-windows.md) manualmente. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| La finalità di protezione automatica è stata rimossa o non è più valida. | Quando si abilita la protezione automatica in un'istanza di SQL Server, **configurare il Backup** processi vengono eseguiti per tutti i database in tale istanza. Se si disabilita la protezione automatica mentre i processi sono in esecuzione, i processi **in corso** vengono annullati con questo codice di errore. | Abilitare la protezione automatica ancora una volta proteggere tutti i database rimanenti. |

## <a name="re-registration-failures"></a>Errori di ri-registrazione

Cercare uno o più dei sintomi seguenti prima di attivare l'operazione di ripetere la registrazione:

* Tutte le operazioni (ad esempio backup, ripristino e configurare il backup) hanno esito negativo nella macchina virtuale con uno dei codici di errore seguenti: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**.
* Il **lo stato del Backup** viene visualizzata l'area dell'elemento di backup **non è raggiungibile**. Escludere tutte le altre cause che potrebbe essere lo stesso stato:

  * Mancanza di autorizzazioni per eseguire operazioni correlate al backup della macchina virtuale  
  * Arresto della macchina virtuale, in modo che non possono avere luogo i backup
  * Problemi di rete  

  ![Stato "Non raggiungibile" nella nuova registrazione di una macchina virtuale](./media/backup-azure-sql-database/re-register-vm.png)

* Nel caso di un gruppo di disponibilità Always On, i backup riescono dopo aver modificato la preferenza di backup o dopo un failover.

Questi problemi possono verificarsi per uno o più dei motivi seguenti:

* Un'estensione è stata eliminata o disinstallata dal portale. 
* Un'estensione è stata disinstallata dal **Pannello di controllo** nella macchina virtuale sotto **Disinstalla o modifica programma**.
* La macchina virtuale è stata ripristinata indietro nel tempo tramite il ripristino del disco sul posto.
* La macchina virtuale è stata arrestata per un periodo prolungato, in modo che la configurazione dell'estensione su di esso è scaduto.
* È stata eliminata la macchina virtuale e un'altra macchina virtuale è stata creata con lo stesso nome e nello stesso gruppo di risorse della macchina virtuale eliminata.
* Uno dei nodi del gruppo di disponibilità non è stata ricevuta la configurazione del backup completata. Questa situazione può verificarsi quando il gruppo di disponibilità viene registrato nell'insieme di credenziali o quando viene aggiunto un nuovo nodo.

Negli scenari precedenti, è consigliabile che si attiva un'operazione di ripetere la registrazione della macchina virtuale. Per il momento, questa opzione è disponibile solo tramite PowerShell.

## <a name="size-limit-for-files"></a>Dimensione massima per i file

La dimensione della stringa totale dei file dipende non solo sul numero di file, ma anche sui relativi nomi e percorsi. Per ogni file di database, recuperare il nome di file logico e percorso fisico. È possibile usare la query SQL seguente:

```
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

A questo punto disporle nel formato seguente:

```
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Di seguito è riportato un esempio:

```
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Se le dimensioni del contenuto della stringa superano i 20.000 byte, i file di database vengono archiviati in modo diverso. Durante il ripristino, sarà possibile impostare il percorso del file di destinazione per il ripristino. I file verranno ripristinati nel percorso di SQL predefiniti forniti da SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Sostituire il percorso del file predefinito destinazione ripristino

È possibile sostituire il percorso del file di destinazione ripristino durante l'operazione di ripristino, inserendo un file JSON che contiene il mapping del file di database nel percorso di ripristino di destinazione. Creare un `database_name.json` file e inserirlo nella posizione *C:\Program Files\Azure del carico di lavoro Backup\bin\plugins\SQL*.

Il contenuto del file deve essere nel formato seguente:
```
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

Di seguito è riportato un esempio:

```
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

Il contenuto precedente, è possibile ottenere il nome logico del file di database usando query SQL seguente:

```
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```


Questo file deve essere inserito prima di attivare l'operazione di ripristino.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Backup di Azure per macchine virtuali di SQL Server (anteprima pubblica), vedere [Backup di Azure per macchine virtuali SQL](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
