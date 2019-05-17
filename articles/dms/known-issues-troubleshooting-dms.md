---
title: Articolo sulla risoluzione dei problemi più comuni problemi/errori associati a usare servizio migrazione del Database di Azure noti | Microsoft Docs
description: Informazioni su come risolvere i noti problemi/errori comuni associati con servizio migrazione del Database di Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: dc8ba315d08f3a130ff0adf91afc90f545baf4e4
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604428"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Risolvere i problemi di migrazione di Database di Azure e gli errori comuni

Questo articolo descrive alcuni problemi comuni e gli errori che possono riscontrare gli utenti di servizio migrazione del Database. L'articolo include anche informazioni su come risolvere questi problemi ed errori.

## <a name="migration-activity-in-queued-state"></a>Attività di migrazione nello stato in coda

Quando si crea nuove attività in un progetto di servizio migrazione del Database, le attività rimangono nello stato in coda.

| Causa         | Risoluzione |
| ------------- | ------------- |
| Questo problema si verifica quando l'istanza del servizio migrazione del Database ha raggiunto la capacità massima per le attività in corso che eseguire contemporaneamente. Qualsiasi nuova attività viene accodata fino a quando non diventa disponibile la capacità. | Convalidare il servizio di migrazione dati istanza dispone di attività in esecuzione in progetti. È possibile continuare a creare nuove attività che vengono aggiunti automaticamente alla coda per l'esecuzione. Non appena una delle attività in esecuzione esistente completa, l'attività in coda successiva viene avviata l'esecuzione e lo stato passa allo stato di esecuzione automaticamente. Non è necessario intraprendere alcuna azione aggiuntiva per avviare la migrazione di attività in coda.<br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Numero massimo di database selezionati per la migrazione

Il seguente errore si verifica quando l'istanza gestita di creazione di un'attività per un progetto di migrazione di database per lo spostamento di Database SQL di Azure o un Database SQL di Azure:

* **Errore**: Errore di convalida delle impostazioni di migrazione","errorDetail":"gli oggetti numero max più di '4' di 'Database' è stata selezionata per la migrazione".

| Causa         | Risoluzione |
| ------------- | ------------- |
| Dopo aver selezionato più di quattro database per un'attività di migrazione single, viene visualizzato questo errore. Al momento, ogni attività di migrazione è limitata a quattro database. | Selezionare un massimo di quattro database per ogni attività di migrazione. Se è necessario eseguire la migrazione di più di quattro database in parallelo, eseguire il provisioning di un'altra istanza del servizio migrazione del Database. Attualmente, ogni sottoscrizione supporta fino a due istanze del servizio migrazione del Database.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Errori per la migrazione di MySQL da MySQL di Azure con errori di ripristino

Quando esegue la migrazione da MySQL a Database di Azure per MySQL usando servizio migrazione del Database, l'attività di migrazione non riesce con l'errore seguente:

* **Errore**: Errore di migrazione di database - attività 'TaskID' è stata sospesa a causa di errori di ripristino successive [n].

| Causa         | Risoluzione |
| ------------- | ------------- |
| Questo errore può verificarsi quando l'utente che esegue la migrazione non è presente il ruolo ReplicationAdmin e/o i privilegi di REPLICATION CLIENT, REPLICA di replica e con privilegi elevati (versioni precedenti a MySQL 5.6.6).<br> <br><br><br> <br> <br> <br> <br> <br> <br> | Assicurarsi che il [privilegi prerequisiti](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) per l'utente account configurati in modo accurato nel Database di Azure per l'istanza di MySQL. Ad esempio, i passaggi seguenti possono essere seguiti per creare un utente denominato 'migrateuser' con i privilegi necessari:<br>1. CREATE USER migrateuser@'%' identificato da 'secret'; <br>2. Concedere tutti i privilegi per db_name.* per 'migrateuser'@'%' identificato da 'secret'; Ripetere questo passaggio per concedere l'accesso a più database <br>3. Slave di replica GRANT su *.* per 'migrateuser'@'%' identificato da 'secret';<br>4. Client di replica GRANT in *.* per 'migrateuser'@'%' identificato da 'secret';<br>5. Scaricamento privilegi. |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Errore durante il tentativo di arrestare servizio migrazione del Database di Azure

Viene visualizzato l'errore seguente quando si arresta l'istanza del servizio migrazione del Database:

* **Errore**: Impossibile arrestare servizio. Errore: {"errore": {'code': 'InvalidRequest', '% s':' una o più attività sono attualmente in esecuzione. Per arrestare il servizio, attendere che le attività completate o arrestare tali attività manualmente e ripetere l'operazione.'}}

| Causa         | Risoluzione |
| ------------- | ------------- |
| Questo errore viene visualizzato quando si tenta di arrestare l'istanza del servizio include attività che sono ancora in esecuzione o presenti nei progetti di migrazione. <br><br><br><br><br><br> | Assicurarsi che non sono presenti attività in esecuzione nell'istanza del servizio migrazione del Database di Azure che si desidera arrestare. È anche possibile eliminare le attività o i progetti prima di tentare di arrestare il servizio. I passaggi seguenti illustrano come rimuovere i progetti per pulire l'istanza del servizio migrazione eliminando tutte le attività in esecuzione:<br>1. Install-Module-nome azurerm. DataMigration <br>2. Login-AzureRmAccount <br>3. Select-AzureRmSubscription -SubscriptionName "<subName>" <br> 4. Remove-AzureRmDataMigrationProject -Name <projectName> -ResourceGroupName <rgName> -ServiceName <serviceName> -DeleteRunningTask |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Errore durante il ripristino di database, mentre l'istanza gestita di SQL la migrazione al database SQL di Azure

Quando si esegue una migrazione online di SQL Server a un'istanza gestita di Database SQL di Azure, il trasferimento non riesce con l'errore seguente:

* **Errore**: Operazione di ripristino non riuscito per l'Id 'operationId' operazione. Codice 'AuthorizationFailed', messaggio ' client 'clientId' con id oggetto 'objectId' non è autorizzato a eseguire l'azione 'Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read' sull'ambito ' /subscriptions/ subscriptionId'. '.

| Causa         | Risoluzione    |
| ------------- | ------------- |
| Questo errore indica che l'entità applicazione in uso per la migrazione online di SQL Server a un'istanza gestita di Database SQL di Azure non hanno l'autorizzazione di collaborazione per la sottoscrizione. Alcune chiamate API con istanza gestita al momento richiedono tale autorizzazione per sottoscrizione per l'operazione di ripristino. <br><br><br><br><br><br><br><br><br><br> | Usare la `Get-AzureADServicePrincipal` cmdlet di PowerShell con `-ObjectId` disponibile dal messaggio di errore per elencare il nome visualizzato dell'ID dell'applicazione in uso.<br><br> Convalidare le autorizzazioni a questa applicazione e assicurarsi che disponga i [ruolo di collaboratore](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) a livello di sottoscrizione. <br><br> Azure Database Migration Service Team di progettazione di funzioni per limitare il richiesto l'accesso dalla posizione corrente di contribuire con ruolo nella sottoscrizione. Se si dispone di un requisito di business che non consente l'uso di collaborazione di ruolo, per ulteriore assistenza, contattare il supporto di Azure. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Errore durante l'eliminazione di interfaccia di rete associato con servizio migrazione del Database di Azure

Quando si prova a eliminare una scheda di interfaccia di rete associato con servizio migrazione del Database, il tentativo di eliminazione ha esito negativo con errore:

* **Errore**: Non è possibile eliminare l'interfaccia di rete associata al servizio migrazione del Database di Azure a causa del servizio migrazione del database che usano l'interfaccia di rete

| Causa         | Risoluzione    |
| ------------- | ------------- |
| Questo problema si verifica quando l'istanza del servizio migrazione del Database potrebbe essere ancora presente e l'utilizzo di interfaccia di rete. <br><br><br><br><br><br> | Per eliminare questa interfaccia di rete, eliminare l'istanza del servizio migrazione del database che elimina automaticamente l'interfaccia di rete utilizzato dal servizio.<br><br> **Importante**: Assicurarsi che l'istanza del servizio migrazione del Database in corso l'eliminazione è associata alcuna attività in esecuzione.<br><br> Dopo l'eliminazione di tutti i progetti e attività associate all'istanza del servizio migrazione del Database, è possibile eliminare l'istanza del servizio. L'interfaccia di rete utilizzato dall'istanza del servizio viene pulita automaticamente durante l'eliminazione del servizio. |

## <a name="connection-error-when-using-expressroute"></a>Errore di connessione quando si usa ExpressRoute

Quando si prova a connettersi all'origine Creazione guidata progetto di servizio migrazione del Database di Azure, la connessione ha esito negativo dopo il timeout prolungati se origine Usa ExpressRoute per la connettività.

| Causa         | Risoluzione    |
| ------------- | ------------- |
| Quando si usa [ExpressRoute](https://azure.microsoft.com/services/expressroute/), servizio migrazione del Database di Azure [richiede](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) tre endpoint di servizio nella subnet della rete virtuale associata al servizio di provisioning:<br> : Endpoint del Bus di servizio<br> : Endpoint di archiviazione<br> : Endpoint di database (ad esempio endpoint SQL, l'endpoint di Cosmos DB) di destinazione<br><br><br><br> | [Abilitare](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) gli endpoint di servizio necessari per la connettività di ExpressRoute tra origine e di servizio migrazione del Database. <br><br><br><br><br><br><br><br> |

## <a name="timeout-error-when-migrating-a-mysql-database-to-azure-mysql"></a>Errore di timeout durante la migrazione di un database MySQL da MySQL di Azure

Quando si esegue la migrazione di un database MySQL a un Database di Azure per l'istanza di MySQL tramite servizio migrazione del Database, la migrazione non riesce con errore di timeout del seguente:

* **Errore**: Errore - migrazione del database non è stato possibile caricare il file - Impossibile avviare il processo di caricamento per il file ' n' RetCode: SQL_ERROR SqlState: NativeError HY000: Messaggio 1205: [MySQL] [Driver ODBC] [mysqld] timeout superato; di attesa blocco Provare a riavviare delle transazioni

| Causa         | Risoluzione    |
| ------------- | ------------- |
| Questo errore si verifica quando la migrazione ha esito negativo a causa del timeout di attesa di blocco durante la migrazione.<br><br> | Provare ad aumentare il valore di parametro del server **'innodb_lock_wait_timeout'**. Il valore massimo consentito è 1073741824. |

## <a name="additional-known-issues"></a>Altri problemi noti

* [Limitazioni di problemi noti/migrazione con le migrazioni online al Database SQL di Azure](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Limitazioni di migrazione/problemi noti con migrazioni online al Database di Azure per MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Limitazioni di migrazione/problemi noti con migrazioni online al Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Passaggi successivi

* Visualizzare l'articolo [PowerShell per Azure Database Migration Service](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Visualizzare l'articolo [come configurare i parametri del server nel Database di Azure per MySQL usando il portale di Azure](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Visualizzare l'articolo [panoramica dei prerequisiti per usare servizio migrazione del Database](https://docs.microsoft.com/azure/dms/pre-reqs).
* Vedere le [domande frequenti sull'uso di Azure Database Migration Service](https://docs.microsoft.com/azure/dms/faq).
