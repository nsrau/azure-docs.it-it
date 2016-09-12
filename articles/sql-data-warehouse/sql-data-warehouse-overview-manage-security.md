<properties
   pageTitle="Proteggere un database in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per proteggere un database in Azure SQL Data Warehouse per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/30/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# Proteggere un database in SQL Data Warehouse

> [AZURE.SELECTOR]
- [Proteggere un database in SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md)
- [Introduzione al rilevamento delle minacce](sql-data-warehouse-security-threat-detection.md)
- [Introduzione a Transparent Data Encryption (TDE) di SQL Data Warehouse](sql-data-warehouse-encryption-tde.md)
- [Introduzione a Transparent Data Encryption (TDE)](sql-data-warehouse-encryption-tde-tsql.md)
- [Servizio di controllo di Azure SQL Data Warehouse](sql-data-warehouse-auditing-overview.md)
- [SQL Data Warehouse - Supporto client di livello inferiore per controllo e maschera dati dinamica](sql-data-warehouse-auditing-downlevel-clients.md)



Questo articolo illustra i concetti di base relativi alla protezione del proprio database di Azure SQL Data Warehouse. In particolare l'articolo spiega come iniziare a usare le risorse per limitare l'accesso, proteggere i dati e monitorare le attività in un database.

## Sicurezza delle connessioni

La sicurezza delle connessioni fa riferimento al modo che si limitano e proteggono le connessioni al database mediante regole del firewall e crittografia di connessione.

Le regole del firewall vengono usate dal server e dal database per rifiutare i tentativi di connessione da indirizzi IP che non sono stati esplicitamente inclusi nell'elenco di IP consentiti. Prima di consentire le connessioni dall'applicazione o dall'indirizzo IP pubblico del computer client è necessario creare una regola firewall di livello server tramite il portale di Azure classico, l'API REST o PowerShell. Come procedura consigliata, si suggerisce di limitare gli intervalli di indirizzi IP consentiti attraverso il firewall del server quanto più possibile. Per accedere ad Azure SQL Data Warehouse dal computer locale, verificare che il firewall in rete e nel computer locale consenta le comunicazioni in uscita sulla porta TCP 1433. Per altre informazioni, vedere [Configurare le regole del firewall per il database SQL di Azure - Panoramica][], [sp\_set\_firewall\_rule][] e [sp\_set\_database\_firewall\_rule][].

Le connessioni a SQL Data Warehouse possono essere crittografate mediante l'impostazione della modalità di crittografia nella stringa di connessione. La sintassi per l'attivazione della crittografia per la connessione varia a seconda del protocollo. Per semplificare l'impostazione della stringa di connessione, passare al database nel portale di Azure. In *Informazioni di base* fare clic su *Mostra stringhe di connessione del database*.


## Autenticazione

Per autenticazione si intende il modo in cui viene dimostrata la propria identità durante la connessione al database. SQL Data Warehouse attualmente supporta l'autenticazione di SQL Server con un nome utente e una password, nonché con Azure Active Directory.

Durante la creazione del server logico per il database, è stato specificato un account di accesso "amministratore del server" con un nome utente e una password. Con queste credenziali è possibile eseguire l'autenticazione in qualsiasi database di tale server come proprietario del database, o "dbo", tramite l'autenticazione di SQL Server.

Tuttavia, come procedura consigliata, è opportuno che gli utenti dell'organizzazione usino un account diverso per l'autenticazione. Questo modo può possibile limitare le autorizzazioni concesse all'applicazione e ridurre i rischi di attività dannose nel caso in cui il codice dell'applicazione sia vulnerabile a un attacco SQL injection.

Per creare un utente autenticato in SQL Server, connettersi al database **master** nel proprio server con l'account di accesso amministratore e creare un nuovo accesso al server. Inoltre, è consigliabile creare un utente nel database master per gli utenti di SQL Data Warehouse di Azure. La creazione di un utente nel database master consente all'utente di accedere tramite strumenti come SSMS senza specificare un nome di database. Consente inoltre di usare Esplora oggetti per visualizzare tutti i database in SQL server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Quindi connettersi al **database di SQL Data Warehouse** con l'account di accesso amministratore server e creare un utente di database basato sull'account di accesso al server appena creato.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Se un utente esegue altre operazioni, ad esempio crea accessi o nuovi database, avrà anche bisogno dell'assegnazione dei ruoli `Loginmanager` e `dbmanager` nel database master. Per altre informazioni sui questi ruoli aggiuntivi e su come avviene l'autenticazione per un database SQL, vedere [Gestione di database e account di accesso in database SQL di Azure][]. Per altre informazioni su Azure AD per SQL Data Warehouse, vedere [Connessione a SQL Data Warehouse con l'autenticazione di Azure Active Directory][].


## Autorizzazione

Per autorizzazione si intendono le operazioni che è possibile effettuare all'interno di un database di Azure SQL Data Warehouse e questa funzionalità viene controllata mediante le autorizzazioni e le appartenenze ai ruoli dell'account utente. Come procedura consigliata, è opportuno concedere agli utenti i privilegi minimi necessari. Azure SQL Data Warehouse ne semplifica la gestione con i ruoli in T-SQL:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

L'account di amministrazione del server a cui ci si sta connettendo è un membro del ruolo db\_owner, che è autorizzato a eseguire qualsiasi operazione all'interno del database. Salvare questo account per la distribuzione degli aggiornamenti allo schema e altre operazioni di gestione. Utilizzare l'account "ApplicationUser" con autorizzazioni più limitate per la connessione dall'applicazione al database con i privilegi minimi richiesti dall'applicazione.

Esistono modi per limitare ulteriormente le operazioni possibili con il database SQL di Azure:

- È possibile utilizzare [autorizzazioni][] che consentono di controllare le operazioni possibili per le singole colonne, tabelle, viste, procedure e altri oggetti nel database. Usare le autorizzazioni granulari per avere il massimo controllo e concedere solo le autorizzazioni necessarie. Il sistema di autorizzazioni granulari è complesso; per usarlo in modo efficace è necessario un certo grado di preparazione.
- È possibile utilizzare [ruoli del database][] diversi da db\_datareader e db\_datawriter per creare account utente dell'applicazione più potenti o account di gestione meno potenti. I ruoli del database predefiniti incorporati sono una soluzione semplice per la concessione di autorizzazioni, ma possono concedere più autorizzazioni di quelle necessarie.
- È possibile utilizzare le [Stored procedure][] per limitare le operazioni che possono essere eseguite nel database.

La gestione di database e server logici dal portale di Azure classico o mediante l'API di gestione risorse di Azure viene controllata dalle assegnazioni di ruolo dell'account utente del portale. Per ulteriori informazioni su questo argomento, vedere [Controllo di accesso basato sui ruoli nel portale di Azure][].

## Crittografia

Azure SQL Data Warehouse consente di proteggere i dati mediante la crittografia dei dati quando sono in stato "inattivo" o vengono archiviati in file di database e backup, utilizzando la funzionalità [Transparent Data Encryption][]. Per abilitare Transparent Data Encryption è necessario essere un amministratore o un membro del ruolo dbmanager nel database master. Per crittografare il database, connettersi al database master nel server ed eseguire:


```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

È anche possibile abilitare Transparent Data Encryption dalle impostazioni del database del [portale di Azure][]. Per altre informazioni, vedere [Introduzione a Transparent Data Encryption (TDE) di SQL Data Warehouse][].

## Controllo

Il controllo e il monitoraggio di eventi del database consentono di mantenere la conformità alle normative e di identificare attività sospette. La funzionalità di controllo di SQL Data Warehouse consente di registrare gli eventi nel database in un log di controllo nell'account di archiviazione Azure. La funzionalità di controllo di SQL Data Warehouse si integra inoltre con Microsoft Power BI per facilitare l'esecuzione di analisi e report drill-down. Per ulteriori informazioni, vedere l'[Introduzione al controllo del database SQL][].

## Passaggi successivi

Per informazioni dettagliate ed esempi di connessione a SQL Data Warehouse con protocolli diversi, vedere [Connettersi ad Azure SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Connettersi ad Azure SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Introduzione al controllo del database SQL]: ./sql-data-warehouse-auditing-overview.md
[Introduzione a Transparent Data Encryption (TDE) di SQL Data Warehouse]: ./sql-data-warehouse-encryption-tde.md
[Connessione a SQL Data Warehouse con l'autenticazione di Azure Active Directory]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Configurare le regole del firewall per il database SQL di Azure - Panoramica]: https://msdn.microsoft.com/library/ee621782.aspx
[sp\_set\_firewall\_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp\_set\_database\_firewall\_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[ruoli del database]: https://msdn.microsoft.com/library/ms189121.aspx
[Gestione di database e account di accesso in database SQL di Azure]: https://msdn.microsoft.com/library/ee336235.aspx
[autorizzazioni]: https://msdn.microsoft.com/library/ms191291.aspx
[Stored procedure]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: https://go.microsoft.com/fwlink/?LinkId=526242
[portale di Azure]: https://portal.azure.com/

<!--Other Web references-->
[Controllo di accesso basato sui ruoli nel portale di Azure]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure

<!---HONumber=AcomDC_0831_2016-->