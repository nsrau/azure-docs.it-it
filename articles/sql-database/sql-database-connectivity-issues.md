<properties 
	pageTitle="Problemi di connettività del database SQL di Azure" 
	description="Identificazione e determinazione degli errori di connessione al database SQL." 
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
	ms.date="07/24/2015" 
	ms.author="sstein"/>


# Problemi di connessione al database SQL

Questo articolo fornisce una panoramica sull'uso di vari strumenti per la risoluzione dei problemi di connessione al database SQL di Azure.


## Come determinare se il problema di connettività è specifico di una singola applicazione o se semplicemente non è possibile connettersi al database?

Per verificare la connettività al database, usare SQL Server Management Studio o SQLCMD.EXE.

- Per istruzioni sulla connessione al database SQL con SQL Server Management Studio (SSMS), vedere [Come connettersi a un database SQL di Azure con SQL Server Management Studio](sql-database-connect-to-database.md).
- Per istruzioni sulla connessione al database SQL con SQLCMD, vedere [Procedura: Connettersi al database SQL di Azure tramite sqlcmd](https://msdn.microsoft.com/library/azure/ee336280.aspx).



## L'applicazione prova a connettersi al database SQL in esecuzione in Azure (ad esempio, l'applicazione non riesce a connettersi al database di un servizio cloud o di un'app Web)?

Verificare che la regola del firewall che consente l'esecuzione di tutti i servizi di Azure sia abilitata per il server/database.

- Per informazioni sulle regole del firewall e l'abilitazione delle connessioni da Azure, vedere [Firewall di database SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure).



## L'applicazione prova a connettersi al database SQL da una rete privata?

Verificare che le regole del firewall che consentono l'accesso da una o più reti specifiche siano abilitate per il server/database.

- Per informazioni generali sulle regole del firewall, vedere [Firewall di database SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).
- Per istruzioni sull'impostazione di regole del firewall, vedere [Procedura: Configurare le impostazioni del firewall (database SQL di Azure)](https://msdn.microsoft.com/library/azure/jj553530.aspx).


## Se le regole del firewall sono configurate correttamente, provare la procedura dettagliata per la [risoluzione dei problemi di connettività del database SQL di Microsoft Azure](https://support2.microsoft.com/common/survey.aspx?scid=sw;en;3844&showpage=1).

Questo articolo del supporto tecnico fornisce informazioni per i seguenti problemi di connettività del database SQL:

- Non è possibile aprire la connessione al server a causa delle impostazioni del firewall 
- Il server non è stato trovato o non era accessibile 
- Non è possibile accedere al server 
- Si sono verificati errori di timeout della connessione 
- Si sono verificati errori temporanei 
- La connessione è stata terminata a causa del raggiungimento di un limite definito dal sistema 
- Si sono verificati errori di connessione da SQL Server Management Studio (SSMS) 


## Informazioni aggiuntive

- Per altre informazioni sulla connessione al database SQL, vedere [Linee guida per la connessione al database SQL di Azure a livello di codice](https://msdn.microsoft.com/library/azure/ee336282.aspx).   

- Informazioni dettagliate su errori di connessione specifici sono disponibili nella sezione **Errori temporanei, errori di perdita della connessione** dell'argomento [Messaggi di errore per programmi client del database SQL](sql-database-develop-error-messages.md#bkmk_connection_errors).

- Per accedere ai dati degli eventi di connessione, è possibile eseguire query mediante la visualizzazione [**sys.event\_log (database SQL di Azure)**](https://msdn.microsoft.com/library/dn270018.aspx).

- Per accedere alle metriche degli eventi di connettività del database, è possibile eseguire query mediante la visualizzazione [**sys.database\_connection\_stats (database SQL di Azure)**](https://msdn.microsoft.com/library/dn269986.aspx).

 

<!---HONumber=July15_HO5-->