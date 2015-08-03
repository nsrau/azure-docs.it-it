<properties 
	pageTitle="Gestire i database SQL di Azure mediante il portale di gestione di Azure" 
	description="Informazioni su come utilizzare il portale di gestione di Azure per gestire un database relazionale nel cloud tramite il portale di gestione di Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="NA" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA" 
	ms.date="04/14/2015" 
	ms.author="sstein"/>


# Gestione dei database SQL di Azure tramite il portale di gestione di Azure

Il [portale di gestione di Azure][Management Portal] consente di creare, monitorare e gestire server e database SQL di Azure. In questo articolo verranno evidenziate le operazioni di database che possono essere eseguite tramite il portale di gestione. Ulteriori informazioni sugli altri strumenti di gestione database SQL di Azure sono disponibili [qui][AzureDb management overview].

>[AZURE.NOTE]Se non si ha familiarità con il portale di gestione di Azure, in questo [tour video viene fornita una rapida panoramica][Azure Portal Tour] dei concetti e delle caratteristiche generali.

![Panoramica dei database](./media/sql-database-manage-portal/sqldatabase_annotated.png)

## 1. Azioni di gestione del database
![Azioni di gestione del database](./media/sql-database-manage-portal/sqldatabase_actions.png)

Il portale di gestione di Azure fornisce un set di azioni di database comuni accessibile nella parte superiore del pannello di un database. È possibile ripristinare un database a un momento precedente, aprire un database in Visual Studio, copiare un database in un nuovo server ed esportare il database a un account di archiviazione di Azure.

## 2. Monitoraggio del database
![Monitoraggio del database](./media/sql-database-manage-portal/sqldatabase_monitoring.png)

Per impostazione predefinita, i database SQL di Azure dispongono di grafici di monitoraggio per unità di velocità effettiva database (DTU), dimensioni del database e stato della connessione. Questi grafici di monitoraggio possono essere personalizzati ed estesi per indicare la percentuale di CPU, la percentuale di IO dei dati, i deadlock, la percentuale di IO del log o persino la percentuale di richieste bloccate dal firewall. Ulteriori informazioni su come personalizzare i grafici di monitoraggio sono disponibili [qui][Azure part monitoring].

Inoltre, è possibile configurare regole di avviso per monitorare una metrica specificata e avvisare un amministratore designato e un co-amministratore quando vengono raggiunte le soglie predefinite. Ulteriori informazioni su come configurare regole di avviso nel portale di gestione di Azure sono disponibili [qui][Azure part monitoring].

## 3. Protezione del database e controllo
![Protezione del database](./media/sql-database-manage-portal/sqldatabase_security.png)

I database SQL di Azure possono essere configurati per tenere traccia di tutti gli eventi di database e scriverli in un log di controllo nell'account di archiviazione di Azure. Tale funzionalità consente di mantenere la conformità alle normative, comprendere l’attività del database e ottenere informazioni su eventuali discrepanze che potrebbero indicare problemi aziendali o sospette violazioni della sicurezza. Ulteriori informazioni sul controllo dei database SQL di Azure sono reperibili [qui][AzureDb Auditing]

I database SQL di Azure possono essere configurati anche per mascherare i dati sensibili agli utenti che non dispongono di privilegi. Ulteriori informazioni sulla funzionalità di mascheramento dinamico dei dati dei database SQL di Azure sono reperibili [qui][AzureDb datamasking]

## 4. Replica geografica
![Replica geografica](./media/sql-database-manage-portal/sqldatabase_georeplication.png)

I database SQL di Azure possono essere configurati per replicare in modo asincrono le transazioni sottoposte a commit in un database secondario. La parte della replica geografica del portale di gestione consente di selezionare l'area di Azure in cui si desidera far risiedere il database secondario. Ulteriori informazioni sulla replica geografica di database in Azure sono reperibili [qui][Database geo-replication]

##Risorse aggiuntive
* [Introduzione al database SQL][]   
* [Gestione del database SQL di Azure tramite SQL Server Management Studio][]   
* [Monitoraggio del database SQL tramite le viste a gestione dinamica][]   
* [Riferimento a Transact-SQL Reference (Database SQL)][]
  
  [Azure Portal Tour]: https://go.microsoft.com/fwlink/?LinkID=522341
  [Management Portal]: https://portal.azure.com
  [Azure part monitoring]: ../documentdb-monitor-accounts.md
  [AzureDb management overview]: http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/
  [Introduzione al database SQL]: http://azure.microsoft.com/services/sql-database
  [Database geo-replication]: http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/
  [Gestione del database SQL di Azure tramite SQL Server Management Studio]: sql-database-manage-azure-ssms.md
  [Monitoraggio del database SQL tramite le viste a gestione dinamica]: http://msdn.microsoft.com/library/windowsazure/ff394114.aspx
  [Riferimento a Transact-SQL Reference (Database SQL)]: http://msdn.microsoft.com/library/bb510741(v=sql.120).aspx
  [AzureDb Auditing]: http://azure.microsoft.com/documentation/articles/sql-database-auditing-get-started/
  [AzureDb datamasking]: http://azure.microsoft.com/documentation/articles/sql-database-dynamic-data-masking-get-started/

 
 

<!---HONumber=July15_HO4-->