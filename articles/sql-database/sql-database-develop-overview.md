<properties
	pageTitle="Panoramica dello sviluppo di database SQL | Microsoft Azure"
	description="Informazioni sulle librerie di connettività disponibili e procedure consigliate per applicazioni che si connettono al Database SQL."
	services="sql-database"
	documentationCenter=""
	authors="annemill"
	manager="jhubbard"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="annemill"/>

# Panoramica dello sviluppo di database SQL
Questo articolo esamina le considerazioni di base di cui uno sviluppatore deve tenere conto quando scrive il codice per collegarsi al database SQL di Azure.

## Linguaggio e piattaforma
Sono disponibili esempi di codice per svariati linguaggi di programmazione e piattaforme. È possibile trovare collegamenti agli esempi di codice in:

* Altre informazioni: [Librerie di connessioni per database SQL e SQL Server](sql-database-libraries.md)

## Limiti delle risorse
Il database SQL di Azure gestisce le risorse disponibili per un database usando due meccanismi diversi: la governance delle risorse e l'imposizione di limiti.

* Altre informazioni: [Limiti delle risorse del database SQL di Azure](sql-database-resource-limits.md).

## Sicurezza
Il database SQL di Azure fornisce risorse per limitare l'accesso, proteggere i dati e monitorare le attività in un database SQL.

* Altre informazioni: [Protezione del database SQL](sql-database-security.md)

## Autenticazione
* Il database SQL di Azure supporta utenti e account di accesso per l'autenticazione di SQL Server e utenti e account di accesso per l'[autenticazione di Azure Active Directory](sql-database-aad-authentication.md).
* È necessario specificare un database particolare, invece di usare il database predefinito *master*.
* Non è possibile usare l'istruzione **USE myDatabaseName;** di Transact-SQL sul database SQL per passare a un altro database.
* Altre informazioni: [Protezione del database SQL: gestire l'accesso al database e la sicurezza degli account di accesso](sql-database-manage-logins.md)

## Resilienza
Quando si verifica un errore temporaneo durante la connessione al database SQL, il codice deve ripetere la chiamata. Per la ripetizione dei tentativi si consiglia di usare una logica backoff, in modo da non sovraccaricare il database SQL con più client che ripetono i tentativi contemporaneamente.

* Esempi di codice: per alcuni esempi di codice che illustrano la logica di ripetizione dei tentativi, vedere gli esempi del linguaggio in uso in [Librerie di connessioni per database SQL e SQL Server](sql-database-libraries.md)
* Altre informazioni: [Messaggi di errore per programmi client di Database SQL](sql-database-develop-error-messages.md)

## Gestione delle connessioni
* Nella logica di connessione client sostituire il timeout predefinito affinché sia pari a 30 secondi. Il valore predefinito di 15 secondi è troppo breve per connessioni che dipendono da Internet.
* Se si usa un [pool di connessioni](http://msdn.microsoft.com/library/8xx3tyca.aspx), assicurarsi di chiudere la connessione nel momento in cui il programma non la usa attivamente, né si prepara a riusarla.

## Considerazioni sulla rete
* Nel computer che ospita il programma client, verificare che il firewall consenta le comunicazioni TCP in uscita sulla porta 1433. Altre informazioni: [Configurazione del firewall di un database SQL di Azure](sql-database-configure-firewall-settings.md)
* Se il programma client si connette al database SQL versione 12 mentre il client viene eseguito in una macchina virtuale (VM) di Azure, è necessario aprire determinati intervalli di porte nella macchina virtuale. Altre informazioni: [Porte superiori a 1433 per ADO.NET 4.5 e database SQL versione 12](sql-database-develop-direct-route-ports-adonet-v12.md)
* Le connessioni client al database SQL V12 di Azure talvolta ignorano il proxy e interagiscono direttamente con il database. Le porte diverse da 1433 diventano importanti. Altre informazioni: [Porte superiori a 1433 per ADO.NET 4.5 e database SQL versione 12](sql-database-develop-direct-route-ports-adonet-v12.md)

## Condivisione dei dati con la scalabilità elastica
La scalabilità elastica semplifica il processo di ridimensionamento.

* [Schemi progettuali per applicazioni SaaS multi-tenant con il database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Routing dipendente dei dati](sql-database-elastic-scale-data-dependent-routing.md)
* [Introduzione alla funzionalità di scalabilità elastica del database SQL di Azure (anteprima)](sql-database-elastic-scale-get-started.md)

## Passaggi successivi

Esplorare tutte le [funzionalità del database SQL](https://azure.microsoft.com/services/sql-database/).

<!---HONumber=AcomDC_0817_2016-->