---
title: Risolvere i problemi di SQL Server backup del database tramite backup di Azure | Microsoft Docs
description: Informazioni sulla risoluzione dei problemi relativi al backup di database di SQL Server eseguiti su macchine virtuali di Azure con Backup di Azure.
ms.reviewer: anuragm
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: article
ms.date: 06/18/2019
ms.author: dacurwin
ms.openlocfilehash: 323470adfe56ee20fe0fb64aeba38b6af4330351
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827591"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Risolvere i problemi di SQL Server backup del database con backup di Azure

Questo articolo fornisce informazioni sulla risoluzione dei problemi per SQL Server database in esecuzione in macchine virtuali di Azure.

Per altre informazioni sul processo di backup e sulle limitazioni, vedere [informazioni su SQL Server backup in macchine virtuali di Azure](backup-azure-sql-database.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>Autorizzazioni di SQL Server

Per configurare la protezione per un database di SQL Server in una macchina virtuale, è necessario installare l'estensione **AzureBackupWindowsWorkload** in tale macchina virtuale. Se si riceve l'errore **UserErrorSQLNoSysadminMembership**, significa che l'istanza di SQL Server non dispone delle autorizzazioni necessarie per il backup. Per correggere l'errore, seguire la procedura descritta in [impostare le autorizzazioni della macchina virtuale](backup-azure-sql-database.md#set-vm-permissions).

## <a name="error-messages"></a>messaggi di errore

### <a name="backup-type-unsupported"></a>Tipo di backup non supportato

| severity | DESCRIZIONE | Possibili cause | Azione consigliata |
|---|---|---|---|
| Avviso | Le impostazioni correnti per questo database non supportano determinati tipi di backup presenti nei criteri associati. | <li>Sul database master è possibile eseguire solo un'operazione di backup completo del database. Non è possibile eseguire il backup differenziale o il backup del log delle transazioni. </li> <li>Qualsiasi database nel modello di recupero con registrazione minima non consente il backup dei log delle transazioni.</li> | Modificare le impostazioni del database in modo che tutti i tipi di backup nei criteri siano supportati. In alternativa, modificare i criteri correnti in modo da includere solo i tipi di backup supportati. In caso contrario, i tipi di backup non supportati verranno ignorati durante il backup pianificato oppure il processo di backup non riuscirà per il backup ad hoc.


### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il database SQL non supporta il tipo di backup richiesto. | Si verifica quando il modello di recupero del database non consente il tipo di backup richiesto. L'errore può verificarsi nelle situazioni seguenti: <br/><ul><li>Un database che utilizza un modello di recupero con registrazione minima non consente il backup del log.</li><li>I backup differenziali e del log non sono consentiti per un database master.</li></ul>Per informazioni dettagliate, vedere la documentazione relativa ai [modelli di recupero SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) . | Se il backup del log non riesce per il database nel modello di recupero con registrazione minima, provare una delle opzioni seguenti:<ul><li>Se il database è in modalità di recupero con registrazione minima, disabilitare i backup del log.</li><li>Utilizzare la [documentazione di SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) per modificare il modello di recupero del database in completo o con registrazione minima delle operazioni bulk. </li><li> Se non si vuole modificare il modello di recupero ed esistono criteri standard per eseguire il backup più database che non possono essere modificati, ignorare l'errore. I backup completi e differenziali funzioneranno come da pianificazione. Verranno ignorati i backup del log, come previsto in questo caso.</li></ul>Se si tratta di un database master ed è stato configurato il backup differenziale o del log, usare uno dei passaggi seguenti:<ul><li>Usare il portale per modificare la pianificazione dei criteri di backup per il database master in full.</li><li>Se esistono criteri standard per eseguire il backup di più database che non possono essere modificati, ignorare l'errore. Il backup completo funzionerà come da pianificazione. Non verranno eseguiti i backup differenziali e del log, come previsto in questo caso.</li></ul> |
| L'operazione è stata annullata perché è già in esecuzione un'operazione in conflitto nello stesso database. | Vedere il [post di Blog sulle limitazioni di backup e ripristino](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) eseguite simultaneamente.| [Usare SQL Server Management Studio (SSMS) per monitorare i processi di backup](manage-monitor-sql-database-backup.md). Quando l'operazione in conflitto ha esito negativo, riavviare l'operazione.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il database SQL non esiste. | Il database è stato eliminato o rinominato. | Controllare se il database è stato eliminato o rinominato accidentalmente.<br/><br/> Se il database è stato eliminato accidentalmente, per continuare con i backup, ripristinare il database nel percorso originale.<br/><br/> Se il database è stato eliminato e non sono necessari backup futuri, nell'insieme di credenziali di servizi di ripristino selezionare **Interrompi backup** con **Mantieni dati di backup** o **Elimina dati di backup**. Per ulteriori informazioni, vedere [gestire e monitorare i database SQL Server](manage-monitor-sql-database-backup.md)sottoposti a backup.

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| La catena di log è interrotta. | Viene eseguito il backup del database o della macchina virtuale tramite un'altra soluzione di backup, che tronca la catena di log.|<ul><li>Verificare se è in uso un'altra soluzione di backup o uno script. In tal caso, arrestare l'altra soluzione di backup. </li><li>Se il backup era un backup del log ad hoc, attivare un backup completo per avviare una nuova catena di log. Per i backup del log pianificati, non è necessaria alcuna azione perché il servizio backup di Azure attiverà automaticamente un backup completo per risolvere il problema.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il servizio Backup di Azure non riesce a connettersi all'istanza SQL. | Backup di Azure non è in grado di connettersi all'istanza di SQL Server. | Per restringere le cause principali, utilizzare i dettagli aggiuntivi disponibili nel menu portale di Azure Error. Fare riferimento a [Risolvere i problemi di connessione al motore di database di SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) per correggere l'errore.<br/><ul><li>Se le impostazioni SQL predefinite non consentono le connessioni remote, modificare le impostazioni. Per informazioni sulla modifica delle impostazioni, vedere gli articoli seguenti:<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Se si verificano problemi di accesso, usare i collegamenti seguenti per correggerli:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Manca il primo backup completo per questa origine dati. | Manca un backup completo per il database. I backup di log e differenziali sono elementi padre di un backup completo, quindi assicurarsi di eseguire backup completi prima di attivare backup differenziali o del log. | Attivare un backup completo ad hoc.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Cannot take backup as transaction log for the data source is full (Non è possibile eseguire il backup perché il log delle transazioni per l'origine dati è pieno). | Lo spazio del log delle transazioni del database è pieno. | Per risolvere il problema, fare riferimento alla [documentazione di SQL Server](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Un database con lo stesso nome esiste già nel percorso di destinazione | Per la destinazione di ripristino di destinazione è già presente un database con lo stesso nome.  | <ul><li>Modificare il nome del database di destinazione.</li><li>In alternativa, usare l'opzione forza sovrascrittura nella pagina Ripristina.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il ripristino non è riuscito perché non è stato possibile portare offline il database. | Quando si esegue un ripristino, il database di destinazione deve essere portato offline. Backup di Azure non è in grado di portare offline questi dati. | Per restringere le cause principali, utilizzare i dettagli aggiuntivi disponibili nel menu portale di Azure Error. Per ulteriori informazioni, vedere la [documentazione SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Cannot find the server certificate with thumbprint on the target (Non è possibile trovare il certificato del server con l'identificazione personale). | Il database master nell'istanza di destinazione non dispone di un'identificazione personale di crittografia valida. | Importare nell'istanza di destinazione l'identificazione personale del certificato valida utilizzata nell'istanza di di origine. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il backup del log usato per il ripristino contiene modifiche con registrazione minima delle operazioni bulk. Non è utilizzabile per l'arresto in un punto arbitrario nel tempo in base alle linee guida di SQL. | Quando un database è in modalità di recupero con registrazione minima delle operazioni bulk, i dati tra una transazione con registrazione minima delle operazioni bulk e la transazione di log successiva non possono essere recuperati. | Scegliere un momento diverso per il ripristino. [Altre informazioni](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105))


### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Non è possibile soddisfare le preferenze di backup per il gruppo di disponibilità AlwaysOn SQL perché alcuni nodi del gruppo di disponibilità non sono registrati. | I nodi necessari per eseguire i backup non sono registrati o non sono raggiungibili. | <ul><li>Verificare che tutti i nodi necessari per eseguire i backup del database siano registrati e integri, quindi ripetere l'operazione.</li><li>Modificare le preferenze di backup per il gruppo di disponibilità SQL Server Always On.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| L'istanza di SQL Server è stata arrestata e non è accessibile al servizio Backup di Azure. | La macchina virtuale viene arrestata. | Verificare che l'istanza di SQL Server sia in esecuzione. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il servizio Backup di Azure usa l'agente guest di macchine virtuali di Azure per l'esecuzione del backup ma l'agente guest non è disponibile nel server di destinazione. | L'agente guest non è abilitato o non è integro. | [Installare l'agente guest di macchine virtuali](../virtual-machines/extensions/agent-windows.md) manualmente. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| La finalità di protezione automatica è stata rimossa o non è più valida. | Quando si Abilita la protezione automatica in un'istanza di SQL Server, configurare i processi di **backup** eseguiti per tutti i database in tale istanza. Se si disabilita la protezione automatica mentre i processi sono in esecuzione, i processi **in corso** vengono annullati con questo codice di errore. | Abilitare di nuovo la protezione automatica per proteggere tutti i database rimanenti. |

### <a name="clouddosabsolutelimitreached"></a>CloudDosAbsoluteLimitReached

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
L'operazione è bloccata perché è stato raggiunto il limite per il numero di operazioni consentite nelle 24 ore. | Quando è stato raggiunto il limite massimo consentito per un'operazione in un intervallo di 24 ore, viene visualizzato questo errore. <br> Ad esempio:  Se è stato raggiunto il limite per il numero di processi di backup di configurazione che possono essere attivati al giorno e si tenta di configurare il backup su un nuovo elemento, verrà visualizzato questo errore. | In genere, la ripetizione dell'operazione dopo 24 ore risolve questo problema. Tuttavia, se il problema persiste, è possibile contattare il supporto tecnico Microsoft per assistenza.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedWithRetry

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
L'operazione è bloccata perché l'insieme di credenziali ha raggiunto il limite massimo per le operazioni consentite in un intervallo di 24 ore. | Quando è stato raggiunto il limite massimo consentito per un'operazione in un intervallo di 24 ore, viene visualizzato questo errore. Questo errore si verifica in genere in caso di operazioni su larga scala, ad esempio la modifica dei criteri o la protezione automatica. A differenza di quanto accade per CloudDosAbsoluteLimitReached, non è possibile risolvere questo stato in realtà, il servizio backup di Azure tenterà di ritentare le operazioni internamente per tutti gli elementi in questione.<br> Ad esempio:  Se si dispone di un numero elevato di origini dati protette con un criterio e si tenta di modificare tale criterio, verrà attivata la configurazione dei processi di protezione per ogni elemento protetto e talvolta potrebbe verificarsi il limite massimo consentito per tali operazioni al giorno.| Il servizio backup di Azure tenterà automaticamente di ripetere l'operazione dopo 24 ore. 


## <a name="re-registration-failures"></a>Errori di ripetizione della registrazione

Prima di attivare l'operazione di ripetizione della registrazione, verificare la presenza di uno o più dei seguenti sintomi:

* Tutte le operazioni, ad esempio il backup, il ripristino e la configurazione del backup, hanno esito negativo nella macchina virtuale con uno dei codici di errore seguenti: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**.
* L'area **stato backup** per l'elemento di backup **non è raggiungibile**. Escludere tutte le altre cause che possono generare lo stesso stato:

  * Mancanza di autorizzazioni per eseguire operazioni relative al backup nella macchina virtuale  
  * Arrestare la macchina virtuale, quindi non è possibile eseguire i backup
  * Problemi di rete  

  ![Stato "non raggiungibile" nella ripetizione della registrazione di una macchina virtuale](./media/backup-azure-sql-database/re-register-vm.png)

* Nel caso di un gruppo di disponibilità di Always On, i backup hanno avuto esito negativo dopo aver modificato la preferenza di backup o dopo un failover.

Questi sintomi possono verificarsi per uno o più dei seguenti motivi:

* Un'estensione è stata eliminata o disinstallata dal portale. 
* Un'estensione è stata disinstallata dal **Pannello di controllo** nella macchina virtuale in **Disinstalla o modifica programma**.
* La macchina virtuale è stata ripristinata di nuovo nel tempo tramite il ripristino del disco sul posto.
* La macchina virtuale è stata arrestata per un periodo prolungato, quindi la configurazione dell'estensione è scaduta.
* La macchina virtuale è stata eliminata e un'altra VM è stata creata con lo stesso nome e nello stesso gruppo di risorse della macchina virtuale eliminata.
* Uno dei nodi del gruppo di disponibilità non ha ricevuto la configurazione del backup completo. Questo problema può verificarsi quando il gruppo di disponibilità viene registrato nell'insieme di credenziali o quando viene aggiunto un nuovo nodo.

Negli scenari precedenti è consigliabile attivare un'operazione di ripetizione della registrazione nella macchina virtuale. Per il momento, questa opzione è disponibile solo tramite PowerShell.

## <a name="size-limit-for-files"></a>Limite dimensioni per i file

Le dimensioni totali della stringa dei file dipendono non solo dal numero di file, ma anche dai rispettivi nomi e percorsi. Per ogni file di database, ottenere il nome e il percorso fisico del file logico. È possibile usare questa query SQL:

```
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Ora è possibile disporli nel formato seguente:

```
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Di seguito è riportato un esempio:

```
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Se le dimensioni della stringa del contenuto superano 20.000 byte, i file di database vengono archiviati in modo diverso. Durante il ripristino, non sarà possibile impostare il percorso del file di destinazione per il ripristino. I file verranno ripristinati nel percorso SQL predefinito fornito da SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Esegui override del percorso predefinito del file di ripristino di destinazione

È possibile eseguire l'override del percorso del file di ripristino di destinazione durante l'operazione di ripristino inserendo un file JSON che contiene il mapping del file di database al percorso di ripristino di destinazione. Creare un `database_name.json` file e inserirlo nel percorso *C:\Program Files\Azure carico di lavoro Backup\bin\plugins\SQL*.

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

Nel contenuto precedente è possibile ottenere il nome logico del file di database usando la query SQL seguente:

```
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```


Questo file deve essere inserito prima di attivare l'operazione di ripristino.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su backup di Azure per le macchine virtuali SQL Server (anteprima pubblica), vedere [backup di Azure per macchine virtuali SQL](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
