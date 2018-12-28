---
title: Guida alla risoluzione dei problemi di Backup di Azure per le macchine virtuali di SQL Server | Microsoft Docs
description: Informazioni sulla risoluzione dei problemi per il backup di macchine virtuali di SQL Server in Azure.
services: backup
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/19/2018
ms.author: anuragm
ms.custom: ''
ms.openlocfilehash: 89344b6e06dbc62fe56c0aebc30a049aebf5c097
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53339519"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>Risolvere i problemi relativi al backup di SQL Server in Azure

Questo articolo offre informazioni sulla risoluzione dei problemi relativi al backup di macchine virtuali di SQL Server in Azure (anteprima).

## <a name="public-preview-limitations"></a>Limiti dell'anteprima pubblica

Per visualizzare i limiti dell'anteprima pubblica, vedere l'articolo [Eseguire il backup del database di SQL Server in Azure](backup-azure-sql-database.md#public-preview-limitations).

## <a name="sql-server-permissions"></a>Autorizzazioni di SQL Server

Per configurare la protezione per un database di SQL Server in una macchina virtuale, l'estensione **AzureBackupWindowsWorkload** deve essere installata nella macchina virtuale. Se viene visualizzato l'errore **UserErrorSQLNoSysadminMembership**, significa che l'istanza di SQL Server non ha le autorizzazioni necessarie per il backup. Per correggere l'errore, seguire la procedura in [Impostare le autorizzazioni per le macchine virtuali SQL non del marketplace](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms).

## <a name="troubleshooting-errors"></a>Risoluzione dei problemi

Usare le informazioni nelle tabelle seguenti per risolvere i problemi e gli errori rilevati durante il backup di SQL Server in Azure.

## <a name="backup-failures"></a>Errori di backup

Le tabelle seguenti sono organizzate in base al codice di errore.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il database SQL non supporta il tipo di backup richiesto. | Si verifica quando il modello di recupero del database non consente il tipo di backup richiesto. L'errore può verificarsi nelle situazioni seguenti: <br/><ul><li>Un database che usa un modello di recupero con registrazione minima non consente backup del log.</li><li>I backup differenziali e del log non sono consentiti per un database master.</li></ul>Per altri dettagli, vedere la documentazione [Modelli di recupero (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server). | Se il backup del log ha esito negativo per il database nel modello di recupero con registrazione minima, provare una delle opzioni seguenti:<ul><li>Se il database è in modalità di recupero con registrazione minima, disabilitare i backup del log.</li><li>Usare la [documentazione di SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) per informazioni su come modificare il modello di recupero da con registrazione completa a con registrazione minima delle operazioni bulk. </li><li> Se non si vuole modificare il modello di recupero ed esistono criteri standard per eseguire il backup più database che non possono essere modificati, ignorare l'errore. I backup completi e differenziali funzioneranno come da pianificazione. Verranno ignorati i backup del log, come previsto in questo caso.</li></ul>Nel caso di un database master e se è stato configurato il backup differenziale o del log, usare uno dei passaggi seguenti:<ul><li>Usare il portale per modificare la pianificazione dei criteri di backup per il database master, per impostare il backup completo.</li><li>Se esistono criteri standard per eseguire il backup di più database che non possono essere modificati, ignorare l'errore. Il backup completo funzionerà come da pianificazione. Non verranno eseguiti i backup differenziali e del log, come previsto in questo caso.</li></ul> |
| L'operazione è stata annullata perché è già in esecuzione un'operazione in conflitto nello stesso database. | Vedere il [post di blog sulle limitazioni di backup e ripristino](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) eseguiti contemporaneamente.| [Usare SQL Server Management Studio (SSMS) per monitorare i processi di backup.](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms) Quando l'operazione in conflitto non riesce, riavviare l'operazione.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il database SQL non esiste. | Il database è stato eliminato o rinominato. | <ul><li>Controllare se il database è stato eliminato o rinominato accidentalmente.</li><li>Se il database è stato eliminato accidentalmente, per continuare con i backup, ripristinare il database nel percorso originale.</li><li>Se il database è stato eliminato e non sono necessari backup futuri, nell'insieme di credenziali di Servizi di ripristino, fare clic su [stop backup with "Delete/Retain data"](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms) (Arresta backup con "Elimina/Mantieni dati").</li>|

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| La catena di log è interrotta. | Il backup del database o della macchina virtuale viene eseguito con un'altra soluzione di backup, che tronca la catena di log.|<ul><li>Verificare se è in uso un'altra soluzione di backup o uno script. In tal caso, arrestare l'altra soluzione di backup. </li><li>Se il backup era un backup del log ad hoc, attivare un backup completo per avviare una nuova catena di log. Per i backup del log pianificati, non è richiesto alcun intervento perché il servizio Backup di Azure attiverà automaticamente un backup completo per risolvere questo problema.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Il servizio Backup di Azure non riesce a connettersi all'istanza SQL. | Backup di Azure non è in grado di connettersi all'istanza di SQL Server. | Usare i dettagli aggiuntivi nel menu dell'errore nel portale di Azure per risalire alle cause radice. Fare riferimento a [Risolvere i problemi di connessione al motore di database di SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) per correggere l'errore.<br/><ul><li>Se le impostazioni di SQL Server predefinite non consentono le connessioni remote, modificare le impostazioni. Vedere i collegamenti di seguito per modificare le impostazioni.<ul><li>[https://msdn.microsoft.com/library/bb326495.aspx](https://msdn.microsoft.com/library/bb326495.aspx)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Se sono presenti problemi di accesso, vedere i collegamenti seguenti per risolvere il problema:<ul><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Manca il primo backup completo per questa origine dati. | Manca un backup completo per il database. I backup del log e differenziali fanno riferimento a un backup completo, quindi occorre creare un backup completo prima di attivare backup differenziali o del log. | Attivare un backup completo ad hoc.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Messaggio di errore | Possibili cause | Azione consigliata |
|---|---|---|
| Cannot take backup as transaction log for the data source is full (Non è possibile eseguire il backup perché il log delle transazioni per l'origine dati è pieno). | Lo spazio del log delle transazioni del database è pieno. | Per risolvere questo problema, fare riferimento alla [documentazione di SQL Server](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |
| Il database SQL non supporta il tipo di backup richiesto. | Le repliche secondarie di gruppi di disponibilità Always On non supportano backup completi e differenziali. | <ul><li>Se è stato attivato un backup ad hoc, attivare i backup nel nodo primario.</li><li>Se il backup è stato pianificato da criteri, verificare che il nodo primario sia registrato. Per registrare il nodo [seguire la procedura per individuare un database di SQL Server](backup-azure-sql-database.md#discover-sql-server-databases).</li></ul> |

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

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Backup di Azure per macchine virtuali di SQL Server (anteprima pubblica), vedere [Backup di Azure per le macchine virtuali SQL (anteprima pubblica)](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
