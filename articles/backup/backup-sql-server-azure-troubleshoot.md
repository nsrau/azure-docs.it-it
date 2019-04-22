---
title: Risoluzione dei problemi di backup dei database di SQL Server con Backup di Azure | Microsoft Docs
description: Informazioni sulla risoluzione dei problemi relativi al backup di database di SQL Server eseguiti su macchine virtuali di Azure con Backup di Azure.
services: backup
author: anuragm
manager: shivamg
ms.service: backup
ms.topic: article
ms.date: 03/13/2019
ms.author: anuragm
ms.openlocfilehash: db204c0e881200f667484daf4348c336f94a0ce7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58916686"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>Risolvere i problemi relativi al backup di SQL Server in Azure

Questo articolo offre informazioni sulla risoluzione dei problemi relativi al backup di macchine virtuali di SQL Server in Azure (anteprima).

## <a name="feature-consideration-and-limitations"></a>Considerazioni e limitazioni della funzionalità

Per visualizzare la considerazione delle funzionalità, vedere l'articolo [backup su SQL Server in macchine virtuali di Azure](backup-azure-sql-database.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>Autorizzazioni di SQL Server

Per configurare la protezione per un database di SQL Server in una macchina virtuale, l'estensione **AzureBackupWindowsWorkload** deve essere installata nella macchina virtuale. Se viene visualizzato l'errore **UserErrorSQLNoSysadminMembership**, significa che l'istanza di SQL Server non ha le autorizzazioni necessarie per il backup. Per correggere l'errore, seguire la procedura in [Impostare le autorizzazioni per le macchine virtuali SQL non del marketplace](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

## <a name="troubleshooting-errors"></a>Risoluzione dei problemi

Usare le informazioni nelle tabelle seguenti per risolvere i problemi e gli errori rilevati durante il backup di SQL Server in Azure.

## <a name="alerts"></a>Avvisi

### <a name="backup-type-unsupported"></a>Tipo di backup non supportato

| Gravità | DESCRIZIONE | Possibili cause | Azione consigliata |
|---|---|---|---|
| Avviso | Impostazioni correnti per questo database non supportano una determinata tipologia di tipi di backup presenti nei criteri associati. | <li>**Master DB**: Solo un'operazione di backup completo del database può essere eseguita sul database master. né **differenziale** backup né transazione **log** è possibile eseguire backup. </li> <li>Qualsiasi database nel **modello di recupero con registrazione minima** non consente l'esecuzione del backup del **log** delle transazioni.</li> | Modificare le impostazioni del database in modo che tutti i tipi di backup nei criteri siano supportati. In alternativa, modificare i criteri correnti in modo da includere solo i tipi di backup supportati. In caso contrario, i tipi di backup non supportati verranno ignorati durante il backup pianificato o il processo di backup avrà esito negativo per backup ad hoc.


## <a name="backup-failures"></a>Errori di backup

Le tabelle seguenti sono organizzate in base al codice di errore.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il database SQL non supporta il tipo di backup richiesto. | Si verifica quando il modello di recupero del database non consente il tipo di backup richiesto. L'errore può verificarsi nelle situazioni seguenti: <br/><ul><li>Un database che usa un modello di recupero con registrazione minima non consente backup del log.</li><li>I backup differenziali e del log non sono consentiti per un database master.</li></ul>Per altri dettagli, vedere la documentazione [Modelli di recupero (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server). | Se il backup del log ha esito negativo per il database nel modello di recupero con registrazione minima, provare una delle opzioni seguenti:<ul><li>Se il database è in modalità di recupero con registrazione minima, disabilitare i backup del log.</li><li>Usare la [documentazione di SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) per informazioni su come modificare il modello di recupero da con registrazione completa a con registrazione minima delle operazioni bulk. </li><li> Se non si vuole modificare il modello di recupero ed esistono criteri standard per eseguire il backup più database che non possono essere modificati, ignorare l'errore. I backup completi e differenziali funzioneranno come da pianificazione. Verranno ignorati i backup del log, come previsto in questo caso.</li></ul>Nel caso di un database master e se è stato configurato il backup differenziale o del log, usare uno dei passaggi seguenti:<ul><li>Usare il portale per modificare la pianificazione dei criteri di backup per il database master, per impostare il backup completo.</li><li>Se esistono criteri standard per eseguire il backup di più database che non possono essere modificati, ignorare l'errore. Il backup completo funzionerà come da pianificazione. Non verranno eseguiti i backup differenziali e del log, come previsto in questo caso.</li></ul> |
| L'operazione è stata annullata perché è già in esecuzione un'operazione in conflitto nello stesso database. | Vedere il [post di blog sulle limitazioni di backup e ripristino](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) eseguiti contemporaneamente.| [Usare SQL Server Management Studio (SSMS) per monitorare i processi di backup.](manage-monitor-sql-database-backup.md) Quando l'operazione in conflitto non riesce, riavviare l'operazione.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il database SQL non esiste. | Il database è stato eliminato o rinominato. | Controllare se il database è stato eliminato o rinominato accidentalmente.<br/><br/> Se il database è stato eliminato accidentalmente, per continuare con i backup, ripristinare il database nel percorso originale.<br/><br/> Se il database è stato eliminato e non sono necessari backup futuri, nell'insieme di credenziali di Servizi di ripristino, fare clic su [stop backup with "Delete/Retain data"](manage-monitor-sql-database-backup.md) (Arresta backup con "Elimina/Mantieni dati").

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| La catena di log è interrotta. | Il backup del database o della macchina virtuale viene eseguito con un'altra soluzione di backup, che tronca la catena di log.|<ul><li>Verificare se è in uso un'altra soluzione di backup o uno script. In tal caso, arrestare l'altra soluzione di backup. </li><li>Se il backup è un backup del log ad hoc, attivare un backup completo per avviare una nuova catena di log. Per i backup del log pianificati, non è richiesto alcun intervento perché il servizio Backup di Azure attiverà automaticamente un backup completo per risolvere questo problema.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il servizio Backup di Azure non riesce a connettersi all'istanza SQL. | Backup di Azure non è in grado di connettersi all'istanza di SQL Server. | Usare i dettagli aggiuntivi nel menu dell'errore nel portale di Azure per risalire alle cause radice. Fare riferimento a [Risolvere i problemi di connessione al motore di database di SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) per correggere l'errore.<br/><ul><li>Se le impostazioni di SQL Server predefinite non consentono le connessioni remote, modificare le impostazioni. Vedere gli articoli seguenti per informazioni su come modificare le impostazioni.<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Se sono presenti problemi di accesso, vedere i collegamenti seguenti per risolvere il problema:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Manca il primo backup completo per questa origine dati. | Manca un backup completo per il database. I backup del log e differenziali fanno riferimento a un backup completo, quindi occorre creare un backup completo prima di attivare backup differenziali o del log. | Attivare un backup completo ad hoc.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Cannot take backup as transaction log for the data source is full (Non è possibile eseguire il backup perché il log delle transazioni per l'origine dati è pieno). | Lo spazio del log delle transazioni del database è pieno. | Per risolvere questo problema, fare riferimento alla [documentazione di SQL Server](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |
| Il database SQL non supporta il tipo di backup richiesto. | Le repliche secondarie di gruppi di disponibilità Always On non supportano backup completi e differenziali. | <ul><li>Se è attivato un backup ad hoc, attivare i backup nel nodo primario.</li><li>Se il backup è stato pianificato da criteri, verificare che il nodo primario sia registrato. Per registrare il nodo [seguire la procedura per individuare un database di SQL Server](backup-sql-server-database-azure-vms.md#discover-sql-server-databases).</li></ul> |

## <a name="restore-failures"></a>Errori di ripristino

I codici di errore seguenti vengono visualizzati in caso di esito negativo dei processi di ripristino.

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Un database con lo stesso nome esiste già nel percorso di destinazione | La destinazione di ripristino di destinazione include già un database con lo stesso nome.  | <ul><li>Modificare il nome del database di destinazione</li><li>In alternativa, usare l'opzione per forzare la sovrascrittura nella pagina di ripristino</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il ripristino non è riuscito perché non è stato possibile portare offline il database. | Il database di destinazione deve essere portato offline durante un ripristino. Backup di Azure non è in grado di portare offline i dati. | Usare i dettagli aggiuntivi nel menu dell'errore nel portale di Azure per risalire alle cause radice. Per altre informazioni, vedere la [documentazione di SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Cannot find the server certificate with thumbprint on the target (Non è possibile trovare il certificato del server con l'identificazione personale). | Il database master nell'istanza di destinazione non ha un'identificazione digitale per la crittografia valida. | Importare l'identificazione personale del certificato valida usata nell'istanza di origine, nell'istanza di destinazione. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il backup del log utilizzato per il ripristino contiene modifiche con registrazione minima delle operazioni bulk. Non è utilizzabile per arrestare in un punto arbitrario nel tempo in base alle linee guida di SQL. | Quando un database è in modalità di ripristino registrati in blocco, non è possibile recuperare i dati tra una transazione di operazioni bulk registrate e il troncamento del log successivo. | Scegliere un altro punto nel tempo per il ripristino. [Altre informazioni](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105))


## <a name="registration-failures"></a>Errori di registrazione

I codici di errore seguenti riguardano gli errori di registrazione.

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Non è possibile soddisfare le preferenze di backup per il gruppo di disponibilità AlwaysOn SQL perché alcuni nodi del gruppo di disponibilità non sono registrati. | I nodi necessari per eseguire i backup non sono registrati o non sono raggiungibili. | <ul><li>Verificare che tutti i nodi necessari per eseguire i backup del database siano registrati e integri, quindi ripetere l'operazione.</li><li>Modificare la preferenza per il backup dei gruppi di disponibilità Always On di SQL Server.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| L'istanza di SQL Server è stata arrestata e non è accessibile al servizio Backup di Azure. | La macchina virtuale è stata arrestata | Verificare che SQL Server sia in esecuzione. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il servizio Backup di Azure usa l'agente guest di macchine virtuali di Azure per l'esecuzione del backup ma l'agente guest non è disponibile nel server di destinazione. | L'agente guest non è abilitato o non è integro | [Installare l'agente guest di macchine virtuali](../virtual-machines/extensions/agent-windows.md) manualmente. |

## <a name="configure-backup-failures"></a>Configurare gli errori di backup

I codici di errore seguenti riguardano gli errori di backup.

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| La finalità di protezione automatica è stata rimossa o non è più valida. | Quando si abilita la protezione automatica in un'istanza SQL, i processi di **configurazione del backup** vengono eseguiti per tutti i database dell'istanza. Se si disabilita la protezione automatica mentre i processi sono in esecuzione, i processi **in corso** vengono annullati con questo codice di errore. | Abilitare di nuovo la protezione automatica per proteggere tutti i database rimanenti. |

## <a name="re-registration-failures"></a>Errori di ri-registrazione

Controllo per uno o più i [sintomi](#symptoms) prima di attivare l'operazione di ripetere la registrazione.

### <a name="symptoms"></a>Sintomi

* Tutte le operazioni, ad esempio backup, ripristino e configurare il backup hanno esito negativo nella macchina virtuale con uno dei codici di errore seguenti: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**
* Il **lo stato di Backup** per il Backup viene visualizzato l'elemento **non è raggiungibile**. Anche se è necessario escludere tutti gli altri motivi per cui possono inoltre comportare lo stesso stato:

  * Mancanza di autorizzazioni per eseguire operazioni correlate di backup nella macchina virtuale  
  * Macchina virtuale è stata arrestata a causa della quale non possono avere luogo i backup
  * Problemi di rete  

    ![Ripetere la registrazione della macchina virtuale](./media/backup-azure-sql-database/re-register-vm.png)

* In caso di gruppo di disponibilità always on, i backup hanno iniziato a bloccarsi dopo aver modificato la preferenza di backup o quando si è verificato un failover

### <a name="causes"></a>Cause
Questi problemi possono verificarsi a causa di uno o più dei motivi seguenti:

  * Estensione è stata eliminata o disinstallata dal portale 
  * Estensione è stata disinstallata dal **Pannello di controllo** della macchina virtuale in **Disinstalla o modifica programma** dell'interfaccia utente
  * Macchina virtuale è stata ripristinata indietro nel tempo tramite il ripristino di dischi in locale
  * Macchina virtuale è stata arrestata per un periodo prolungato a causa della quale la configurazione dell'estensione su di esso è scaduto
  * Macchina virtuale è stata eliminata e un'altra macchina virtuale è stata creata con lo stesso nome e nello stesso gruppo di risorse della macchina virtuale eliminato
  * Uno dei nodi del gruppo di disponibilità non è stata ricevuta la configurazione del backup completata, ciò può verificarsi sia al momento della registrazione di gruppo di disponibilità per l'insieme di credenziali o quando viene aggiunto un nuovo nodo  <br>
    Negli scenari precedenti, è consigliabile attivare registrare di nuovo l'operazione sulla macchina virtuale. Questa opzione è disponibile solo tramite PowerShell e sarà presto disponibile nel portale di Azure anche.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Backup di Azure per macchine virtuali di SQL Server (anteprima pubblica), vedere [Backup di Azure per le macchine virtuali SQL (anteprima pubblica)](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
