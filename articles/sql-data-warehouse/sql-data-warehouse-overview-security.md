<properties
   pageTitle="Proteggere un database in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per proteggere un database in Azure SQL Data Warehouse per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/15/2015"
   ms.author="sahajs"/>

# Proteggere un database in SQL Data Warehouse

Questo articolo illustra i concetti di base relativi alla protezione del proprio database di Azure SQL Data Warehouse. In particolare l'articolo spiegherà come iniziare a usare le risorse per limitare l'accesso, proteggere i dati e monitorare le attività in un database.

## Sicurezza delle connessioni

La sicurezza delle connessioni fa riferimento al modo che si limitano e proteggono le connessioni al database mediante regole del firewall e crittografia di connessione.

Le regole del firewall vengono usate dal server e dal database per rifiutare i tentativi di connessione da indirizzi IP che non sono stati esplicitamente inclusi nell'elenco di IP consentiti. Per consentire all'applicazione o all'indirizzo IP pubblico del computer client di connettersi a un nuovo database, è innanzitutto necessario creare una regola firewall di livello server tramite il portale di gestione di Azure, l'API REST o PowerShell. Come procedura consigliata, si suggerisce di limitare gli intervalli di indirizzi IP consentiti attraverso il firewall del server quanto più possibile. Per altre informazioni, vedere [Firewall di database SQL di Azure][].


## Autenticazione

Per autenticazione si intende il modo in cui viene dimostrata la propria identità durante la connessione al database. SQL Data Warehouse attualmente supporta l'autenticazione SQL con un nome utente e una password.

Durante la creazione del server logico per il database, è stato specificato un account di accesso "amministratore del server" con un nome utente e una password. Utilizzando queste credenziali, è possibile essere autenticati in qualsiasi database di tale server in qualità di proprietario del database o "dbo".

Tuttavia, come procedura consigliata, gli utenti dell'organizzazione dovrebbero utilizzare un account diverso per l'autenticazione. Questo modo può possibile limitare le autorizzazioni concesse all'applicazione e ridurre i rischi di attività dannose nel caso in cui il codice dell'applicazione sia vulnerabile a un attacco SQL injection. Per creare un utente di database basato sull’accesso al server:

Innanzitutto, connettersi al database master sul proprio server con l'account di accesso amministratore e creare un nuovo accesso al server.

```
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'strong_password';

```

Quindi, connettersi al database del SQL Data Warhouse con l'account di accesso amministratore del server e creare un utente di database basato su account di accesso al server appena creato.

```

-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;

```

Per altre informazioni su come avviene l'autenticazione per un database SQL, vedere [Gestione di database, account di accesso e utenti in database SQL di Azure][].


## Autorizzazione

Per autorizzazione si intendono le operazioni che è possibile effettuare all'interno di un database di Azure SQL Data Warehouse e questa funzionalità viene controllata mediante le autorizzazioni e le appartenenze ai ruoli dell'account utente. Come procedura consigliata, è opportuno concedere agli utenti i privilegi minimi necessari. Azure SQL Data Warehouse ne semplifica la gestione con i ruoli in T-SQL:

```
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

L'account di amministrazione del server a cui ci si sta connettendo è un membro del ruolo db\_owner, che è autorizzato a eseguire qualsiasi operazione all'interno del database. Salvare questo account per la distribuzione degli aggiornamenti allo schema e altre operazioni di gestione. Utilizzare l'account "ApplicationUser" con autorizzazioni più limitate per la connessione dall'applicazione al database con i privilegi minimi richiesti dall'applicazione.

Esistono modi per limitare ulteriormente le operazioni possibili con il database SQL di Azure:

- È possibile utilizzare [ruoli del database][] diversi da db\_datareader e db\_datawriter per creare account utente dell'applicazione più potenti o account di gestione meno potenti.
- È possibile utilizzare [autorizzazioni][] che consentono di controllare le operazioni possibili per le singole colonne, tabelle, viste, procedure e altri oggetti nel database.
- È possibile utilizzare le [Stored procedure][] per limitare le operazioni che possono essere eseguite nel database.

La gestione di database e server logici dal portale di gestione di Azure o mediante l'API di gestione risorse di Azure viene controllata dalle assegnazioni di ruolo dell'account utente del portale. Per altre informazioni su questo argomento, vedere l'articolo relativo al [controllo degli accessi in base al ruolo nel portale di anteprima di Azure][].



## Crittografia

Azure SQL Data Warehouse consente di proteggere i dati mediante la crittografia dei dati quando sono in stato "inattivo" o vengono archiviati in file di database e backup, utilizzando la funzionalità [Transparent Data Encryption][]. Per crittografare il database, connettersi al database master nel server ed eseguire:


```

ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;

```

È inoltre possibile abilitare Transparent Data Encryption dalle impostazioni del database del [portale di Azure][].



## Controllo

Il controllo e il monitoraggio di eventi del database consentono di mantenere la conformità alle normative e di identificare attività sospette. La funzionalità di controllo di SQL Data Warehouse consente di registrare gli eventi nel database in un log di controllo nell'account di archiviazione Azure. La funzionalità di controllo di SQL Data Warehouse si integra inoltre con Microsoft Power BI per facilitare l'esecuzione di analisi e report drill-down. Per ulteriori informazioni, vedere l'[Introduzione al controllo del database SQL][].



## Passaggi successivi
Per altri suggerimenti relativi allo sviluppo, vedere [Panoramica sullo sviluppo per SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Panoramica sullo sviluppo per SQL Data Warehouse]: sql-data-warehouse-overview-develop.md


<!--MSDN references-->
[Firewall di database SQL di Azure]: https://msdn.microsoft.com/library/ee621782.aspx
[ruoli del database]: https://msdn.microsoft.com/library/ms189121.aspx
[Gestione di database, account di accesso e utenti in database SQL di Azure]: https://msdn.microsoft.com/library/ee336235.aspx
[autorizzazioni]: https://msdn.microsoft.com/library/ms191291.aspx
[Stored procedure]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: http://go.microsoft.com/fwlink/?LinkId=526242
[Introduzione al controllo del database SQL]: sql-database-auditing-get-started.md
[portale di Azure]: https://portal.azure.com/

<!--Other Web references-->
[controllo degli accessi in base al ruolo nel portale di anteprima di Azure]: http://azure.microsoft.com/documentation/articles/role-based-access-control-configure.aspx

<!---HONumber=Oct15_HO4-->