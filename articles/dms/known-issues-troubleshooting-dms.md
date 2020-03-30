---
title: Problemi comuni - Servizio di migrazione del database di AzureCommon issues - Azure Database Migration Service
description: Informazioni su come risolvere i problemi noti/errori comuni associati all'uso del servizio Migrazione del database di Azure.Learn about how to troubleshoot common known issues/errors associated with using Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: c5d2ad481124f5ae048d010cdf632ee661bbd6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649108"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Risolvere gli errori e i problemi comuni del servizio di migrazione del database di AzureTroubleshoot common Azure Database Migration Service issues and errors

Questo articolo descrive alcuni problemi comuni ed errori che gli utenti del Servizio migrazione di database di Azure possono incontrare. L'articolo include anche informazioni su come risolvere questi problemi ed errori.

## <a name="migration-activity-in-queued-state"></a>Attività di migrazione in stato in codaMigration activity in queued state

Quando si creano nuove attività in un progetto di servizio Migrazione database di Azure, le attività rimangono in uno stato in coda.

| Causa         | Risoluzione |
| ------------- | ------------- |
| Questo problema si verifica quando l'istanza del servizio migrazione del database di Azure ha raggiunto la capacità massima per le attività in corso che eseguono contemporaneamente. Ogni nuova attività viene accodata fino a quando la capacità non diventa disponibile. | Convalidare che l'istanza del servizio di migrazione dei dati abbia attività in esecuzione tra i progetti. È possibile continuare a creare nuove attività che vengono aggiunte automaticamente alla coda per l'esecuzione. Non appena una delle attività in esecuzione esistenti viene completata, l'attività in coda successiva inizia l'esecuzione e lo stato passa automaticamente allo stato in esecuzione. Non è necessario eseguire alcuna azione aggiuntiva per avviare la migrazione dell'attività in coda.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Numero massimo di database selezionati per la migrazione

L'errore seguente si verifica quando si crea un'attività per un progetto di migrazione del database per lo spostamento nel database SQL di Azure o in un'istanza gestita del database SQL di Azure:The following error occurs when creating an activity for a database migration project for moving to Azure SQL Database or an Azure SQL Database managed instance:

* **Errore:** errore di convalida delle impostazioni di migrazione", "errorDetail": "Più del numero massimo di oggetti '4' di 'Database' è stato selezionato per la migrazione."

| Causa         | Risoluzione |
| ------------- | ------------- |
| Questo errore viene visualizzato quando sono stati selezionati più di quattro database per una singola attività di migrazione. Attualmente, ogni attività di migrazione è limitata a quattro database. | Selezionare al solo numero di database per ogni attività di migrazione. Se è necessario eseguire la migrazione di più di quattro database in parallelo, eseguire il provisioning di un'altra istanza del servizio Migrazione del database di Azure.If you need to migrate more than four databases in parallel, provision another instance of Azure Database Migration Service. Attualmente, ogni sottoscrizione supporta fino a due istanze del servizio migrazione di database di Azure.Currently, each subscription supports up to two Azure Database Migration Service instances.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Errori per la migrazione di MySQL ad Azure MySQL con errori di ripristinoErrors for MySQL migration to Azure MySQL with recovery failures

Quando si esegue la migrazione da MySQL al database di Azure per MySQL usando il servizio di migrazione del database di Azure, l'attività di migrazione ha esito negativo con il seguente errore:

* **Errore:** errore di migrazione del database: l'attività 'TaskID' è stata sospesa a causa di [n] errori di ripristino successivi.

| Causa         | Risoluzione |
| ------------- | ------------- |
| Questo errore può verificarsi quando all'utente che esegue la migrazione mancano il ruolo ReplicationAdmin e/o i privilegi di REPLICATION CLIENT, REPLICATION REPLICA e SUPER (versioni precedenti a MySQL 5.6.6).<br><br><br><br><br><br><br><br><br><br><br><br><br> | Assicurarsi che [i privilegi prerequisiti](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) per l'account utente siano configurati in modo accurato nel database di Azure per l'istanza MySQL. Ad esempio, è possibile seguire i passaggi seguenti per creare un utente denominato "migrateuser" con privilegi necessari:For example, the following steps can be followed to create a user named 'migrateuser' with required privileges:<br>1. CREATE USER MIGRATEUSER@'%' IDENTIFICATO DA 'SEGRETo'; <br>2. Concedere a tutti i privilegi per db_name. ripetere questo passaggio per concedere l'accesso su più database <br>3. Concedere slave di replica su *.* a 'migrateuser''%' identificato da 'secret';<br>4. Concedere il client di replica su *.* a 'migrateuser''%' identificato da 'secret';<br>5. Scaricare i privilegi; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Errore durante il tentativo di arrestare il servizio di migrazione del database di AzureError when attempting to stop Azure Database Migration Service

Viene visualizzato il seguente errore quando si arresta l'istanza del servizio di migrazione del database di Azure:

* **Errore:** Impossibile arrestare il servizio. Errore: {'error':{'code':'InvalidRequest','message':'Una o più attività sono attualmente in esecuzione. Per arrestare il servizio, attendere il completamento delle attività o arrestarle manualmente e riprovare.

| Causa         | Risoluzione |
| ------------- | ------------- |
| Questo errore viene visualizzato quando l'istanza del servizio che si sta tentando di arrestare include attività ancora in esecuzione o presenti nei progetti di migrazione. <br><br><br><br><br><br> | Verificare che nell'istanza del servizio Migrazione del database di Azure non siano in esecuzione attività che si sta tentando di arrestare. È inoltre possibile eliminare le attività o i progetti prima di tentare di arrestare il servizio. I passaggi seguenti illustrano come rimuovere i progetti per pulire l'istanza del servizio di migrazione eliminando tutte le attività in esecuzione:The following steps illustrate how to remove projects to clean up the migration service instance by deleting all running tasks:<br>1. Installare-Modulo -Name AzureRM.DataMigration <br>2. Accesso-AzureRmAccount <br>3. Selezionare-AzureRmSubscription -SubscriptionName "\<subName>" <br> 4. Remove-AzureRmDataMigrationProject \<-Name projectName> \<-ResourceGroupName \<rgName> -ServiceName serviceName> -DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Errore durante il tentativo di avviare il servizio di migrazione del database di AzureError when attempting to start Azure Database Migration Service

Viene visualizzato il seguente errore quando si avvia l'istanza del servizio di migrazione del database di Azure:

* **Errore:** Impossibile avviare il servizio. Errore: ''errorDetail':'Impossibile avviare il servizio, contattare il supporto tecnico Microsoft''

| Causa         | Risoluzione |
| ------------- | ------------- |
| Questo errore viene visualizzato quando l'istanza precedente non è riuscita internamente. Questo errore si verifica raramente e il team di progettazione ne è a conoscenza. <br> | Eliminare l'istanza del servizio che non è possibile avviare e quindi eseguire il provisioning di una nuova per sostituirla. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Errore durante il ripristino del database durante la migrazione di SQL all'istanza gestita del database SQL di AzureError restoring database while migrating SQL to Azure SQL DB managed instance

Quando si esegue una migrazione online da SQL Server a un'istanza gestita del database SQL di Azure, il cutover ha esito negativo con errore seguente:When you perform an online migration from SQL Server to an Azure SQL Database managed instance, the cutover fails with following error:

* **Errore:** operazione di ripristino non riuscita per l'operazione ID 'operationId'. Codice 'AuthorizationFailed', il messaggio 'Il client 'clientId' con ID oggetto 'objectId' non dispone dell'autorizzazione per eseguire l'azione 'Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read' nell'ambito '/subscriptions/subscriptionId'.'.

| Causa         | Risoluzione    |
| ------------- | ------------- |
| Questo errore indica che l'entità applicazione utilizzata per la migrazione online da SQL Server a un'istanza gestita del database SQL di Azure non dispone dell'autorizzazione di collaborazione per la sottoscrizione. Alcune chiamate API con istanza gestita richiedono questa autorizzazione per la sottoscrizione per l'operazione di ripristino. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Utilizzare `Get-AzureADServicePrincipal` il cmdlet `-ObjectId` PowerShell con available nel messaggio di errore per elencare il nome visualizzato dell'ID applicazione in uso.<br><br> Convalidare le autorizzazioni per questa applicazione e assicurarsi che disponga del [ruolo di collaboratore](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) a livello di sottoscrizione. <br><br> Il team di progettazione del servizio di migrazione del database di Azure sta lavorando per limitare l'accesso richiesto dal ruolo di collaborazione corrente nella sottoscrizione. Se si dispone di un requisito aziendale che non consente l'uso del ruolo contribute, contattare il supporto di Azure per ulteriore assistenza. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Errore durante l'eliminazione della scheda di interfaccia di rete associata al servizio di migrazione del database di AzureError when deleting NIC associated with Azure Database Migration Service

Quando si tenta di eliminare una scheda di interfaccia di rete associata al servizio di migrazione del Database di Azure, il tentativo di eliminazione ha esito negativo con questo errore:When you try to delete a Network Interface Card associated with Azure Database Migration Service, the deletion attempt fails with this error:

* **Errore:** impossibile eliminare la scheda di interfaccia di rete associata al servizio Di migrazione del database di Azure a causa del servizio DMS che utilizza la scheda di interfaccia di rete

| Causa         | Risoluzione    |
| ------------- | ------------- |
| Questo problema si verifica quando l'istanza del servizio di migrazione del database di Azure potrebbe essere ancora presente e utilizzare la scheda di interfaccia di rete. <br><br><br><br><br><br><br><br> | Per eliminare questa scheda di interfaccia di rete, eliminare l'istanza del servizio DMS che elimina automaticamente la scheda di interfaccia di rete utilizzata dal servizio.<br><br> **Importante:** verificare che l'istanza del servizio di migrazione del database di Azure da eliminare non abbia attività in esecuzione.<br><br> Dopo l'eliminazione di tutti i progetti e le attività associati all'istanza del servizio di migrazione del database di Azure, è possibile eliminare l'istanza del servizio. La scheda di interfaccia di rete utilizzata dall'istanza del servizio viene pulita automaticamente come parte dell'eliminazione del servizio. |

## <a name="connection-error-when-using-expressroute"></a>Errore di connessione quando si usa ExpressRoute

Quando si cerca di connettersi all'origine nella procedura guidata del progetto del servizio Migrazione del database di Azure, la connessione ha esito negativo dopo un timeout prolungato se l'origine usa ExpressRoute per la connettività.

| Causa         | Risoluzione    |
| ------------- | ------------- |
| Quando si usa [ExpressRoute,](https://azure.microsoft.com/services/expressroute/)il servizio di migrazione del database di Azure richiede il provisioning di tre endpoint di servizio nella subnet della rete virtuale associata al servizio:When using ExpressRoute , Azure Database Migration Service [requires](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) provisioning three service endpoints on the Virtual Network subnet associated with the service:<br> -- Endpoint del bus di servizio<br> -- Endpoint di archiviazione<br> -- Endpoint del database di destinazione (ad esempio endpoint SQL, endpoint Cosmos DB)<br><br><br><br><br> | [Abilitare](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) gli endpoint di servizio necessari per la connettività ExpressRoute tra il servizio di migrazione del database di Azure.Enable the required service endpoints for ExpressRoute connectivity between source and Azure Database Migration Service. <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>Errore di timeout di attesa del blocco durante la migrazione di un database MySQL al database di Azure per MySQLLock wait timeout error when migrating a MySQL database to Azure DB for MySQL

Quando si esegue la migrazione di un database MySQL a un database di Azure per l'istanza MySQL tramite il servizio di migrazione del database di Azure, la migrazione non riesce con il seguente errore di timeout di attesa di blocco:When you migrate a MySQL database to an Azure Database for MySQL instance via Azure Database Migration Service, the migration fails with following lock wait timeout:

* **Errore**: Errore di migrazione del database - Impossibile caricare il file - Impossibile avviare il processo di caricamento per il file 'n' RetCode: SQL_ERROR SqlState: HY000 NativeError: 1205 Messaggio: [MySQL][Driver ODBC][mysqld] Timeout di attesa blocco superato; provare a riavviare la transazione

| Causa         | Risoluzione    |
| ------------- | ------------- |
| Questo errore si verifica quando la migrazione non riesce a causa del timeout di attesa del blocco durante la migrazione. | Valutare la possibilità di aumentare il valore del parametro del server **'innodb_lock_wait_timeout'.** Il valore massimo consentito è 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Errore durante la connessione all'origine di SQL Server quando si utilizza la porta dinamica o l'istanza denominata

Quando si tenta di connettere il servizio di migrazione del database di Azure all'origine di SQL Server in esecuzione su un'istanza denominata o una porta dinamica, la connessione non riesce con questo errore:When you try to connect Azure Database Migration Service to SQL Server source that runs on either named instance or a dynamic port, the connection fails with this error:

* **Errore:**-1 - Connessione SQL non riuscita. Si è verificato un errore di rete o specifico dell'istanza mentre veniva stabilita la connessione a SQL Server. Il server non è stato trovato o non è accessibile. Verificare che il nome dell'istanza sia corretto e che il server sia configurato in modo da consentire connessioni remote. (provider: interfacce di rete SQL, errore: 26 - Errore nell'individuazione del server/dell'istanza specificata)

| Causa         | Risoluzione    |
| ------------- | ------------- |
| Questo problema si verifica quando l'istanza di SQL Server di origine che il servizio di migrazione del database di Azure tenta di connettersi a una porta dinamica o utilizza un'istanza denominata. Il servizio SQL Server Browser è in ascolto della porta UDP 1434 per le connessioni in ingresso a un'istanza denominata o quando si utilizza una porta dinamica. La porta dinamica può cambiare ogni volta che il servizio SQL Server viene riavviato. È possibile controllare la porta dinamica assegnata a un'istanza tramite la configurazione di rete in Gestione configurazione SQL ServerSQL Server .<br><br><br> |Verificare che il servizio di migrazione del database di Azure possa connettersi al servizio SQL Server Browser di origine sulla porta UDP 1434 e l'istanza di SQL Server tramite la porta TCP assegnata in modo dinamico, se applicabile. |

## <a name="additional-known-issues"></a>Ulteriori problemi noti

* [Problemi noti/limitazioni di migrazione con le migrazioni online al database SQL di AzureKnown issues/migration limitations with online migrations to Azure SQL Database](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Problemi noti/limitazioni di migrazione con le migrazioni online al database di Azure per MySQLKnown issues/migration limitations with online migrations to Azure Database for MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Problemi noti/limitazioni di migrazione con le migrazioni online al database di Azure per PostgreSQLKnown issues/migration limitations with online migrations to Azure Database for PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Passaggi successivi

* Vedere l'articolo [Azure Database Migration Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Vedere l'articolo Come configurare i parametri del server nel database di [Azure per MySQL tramite il portale](https://docs.microsoft.com/azure/mysql/howto-server-parameters)di Azure.
* Vedere l'articolo [Panoramica dei prerequisiti per l'utilizzo del servizio Migrazione del database di Azure.](https://docs.microsoft.com/azure/dms/pre-reqs)
* Vedere le [domande frequenti sull'utilizzo](https://docs.microsoft.com/azure/dms/faq)del servizio di migrazione del database di Azure .
