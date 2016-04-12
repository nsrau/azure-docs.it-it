<properties
   pageTitle="Configurare un firewall del database SQL | Microsoft Azure"
   description="Informazioni su come configurare un firewall del database SQL con regole del firewall a livello di server e a livello di database per la gestione dell'accesso."
   keywords="firewall del database"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor="cgronlun"
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="02/18/2016"
   ms.author="rickbyh"/>

# Come configurare un firewall del database SQL di Azure

Il database SQL di Microsoft Azure fornisce un servizio di database relazionale per Azure e altre applicazioni basate su Internet. Per proteggere i dati, il firewall del database SQL impedisce qualsiasi accesso al server di database SQL finché non vengono specificati i computer autorizzati. Il firewall concede l'accesso al database in base all'indirizzo IP di origine di ogni richiesta.

Per configurare il firewall del database, creare regole del firewall che specificano gli intervalli di indirizzi IP accettabili. È possibile creare regole del firewall a livello di server e database.

- **Regole del firewall a livello di server:** queste regole consentono ai client di accedere all'intero server di database SQL di Azure, vale a dire, tutti i database all'interno dello stesso server logico. Queste regole sono archiviate nel database **master**.
- **Regole del firewall a livello di database:** queste regole consentono ai client di accedere ai singoli database nel server di database SQL di Azure. Queste regole vengono create per ogni database e vengono archiviate nei singoli database (incluso il database **master**). Tali regole possono essere utili per limitare l'accesso a determinati database (accesso sicuro) all'interno dello stesso server logico.

**Raccomandazione:** Microsoft consiglia di utilizzare le regole del firewall a livello di database quando è possibile al fine di aumentare la portabilità del database. Utilizzare le regole del firewall a livello di server quando si dispone di molti database che presentano gli stessi requisiti di accesso e non si desidera dedicare tempo a configurare singolarmente ogni database.

**Informazioni sulle federazioni:** l'implementazione corrente delle federazioni verrà ritirata con i livelli di servizio Web e Business. Valutare la possibilità di distribuire soluzioni di partizionamento orizzontale personalizzato per l’ottimizzazione di scalabilità, flessibilità e prestazioni. Per altre informazioni sul partizionamento orizzontale personalizzato, vedere [Scalabilità orizzontale dei database SQL di Azure](https://msdn.microsoft.com/library/dn495641.aspx).

> [AZURE.NOTE] Se si crea una federazione di database nel database SQL di Azure in cui il database radice contiene regole del firewall a livello di database, le regole non vengono copiate nei database membri della federazione. Quando sono necessarie regole del firewall a livello di database per i membri della federazione, è necessario creare nuovamente le regole per i membri della federazione. Tuttavia, se un membro della federazione contenente una regola del firewall a livello di database viene diviso in nuovi membri della federazione utilizzando l'istruzione ALTER FEDERATION … SPLIT, i nuovi membri di destinazione avranno le stesse regole del firewall a livello di database del membro della federazione di origine. Per altre informazioni sulle federazioni, vedere [Federazioni in database SQL di Azure](https://msdn.microsoft.com/library/hh597452.aspx).

## Panoramica sul firewall del database SQL

Inizialmente, tutti gli accessi al server di database SQL di Azure sono bloccati dal firewall. Per iniziare a usare il server di database SQL di Azure, è necessario accedere al portale di Azure e specificare una o più regole del firewall a livello di server che consentano di accedere al server di database SQL di Azure. Utilizzare le regole del firewall per specificare quali intervalli di indirizzi IP di Internet sono consentiti e se le applicazioni Azure possono tentare di connettersi al server di database SQL di Azure oppure no.

Tuttavia, se si desidera concedere in modo selettivo l'accesso solo a uno dei database nel server di database SQL di Azure, è necessario creare una regola a livello di database per il database richiesto con un intervallo di indirizzi IP superiore all’intervallo di indirizzi IP specificato nella regola del firewall a livello di server e assicurarsi che l'indirizzo IP del client rientri nell'intervallo specificato nella regola a livello di database.

I tentativi di connessione da Internet e Azure devono prima superare il firewall per poter raggiungere il server di database SQL di Azure o un database, come illustrato nel diagramma seguente.

   ![Diagramma che descrive la configurazione del firewall.][1]

## Connessione da Internet

Quando un computer tenta di connettersi al server di database da Internet, il firewall controlla l'indirizzo IP di origine della richiesta rispetto al set completo di regole del firewall a livello di server e, se necessario, a livello di database:

- Se l'indirizzo IP della richiesta rientra in uno degli intervalli specificati nelle regole del firewall a livello di server, è consentita la connessione al server di database SQL di Azure.

- Se l'indirizzo IP della richiesta non rientra in uno degli intervalli specificati nelle regole del firewall a livello di server, vengono controllate le regole del firewall a livello di database. Se l'indirizzo IP della richiesta rientra in uno degli intervalli specificati nelle regole del firewall a livello di database, è consentita la connessione solo al database con una regola a livello di database corrispondente.

- Se l'indirizzo IP della richiesta non rientra negli intervalli specificati in una delle regole del firewall a livello di server o a livello di database, la richiesta di connessione non riesce.

> [AZURE.NOTE] Per accedere al database SQL di Azure dal computer locale, verificare che il firewall in rete e nel computer locale consenta le comunicazioni in uscita sulla porta TCP 1433.


## Connessione da Azure

Quando si tenta di connettersi al server di database da un'applicazione di Azure, il firewall verifica che le connessioni da Azure siano consentite. Un'impostazione del firewall con indirizzo iniziale e finale uguale a 0.0.0.0 indica che queste connessioni sono consentite. Se il tentativo di connessione non è consentito, la richiesta non raggiungerà il server di Database SQL di Azure.

È possibile abilitare le connessioni da Azure in due modi:

- Nel [portale di Microsoft Azure](https://portal.azure.com/) selezionare la casella di controllo **Consenti ai servizi di Azure di accedere al server** durante la creazione di un nuovo server.

- Nel [portale classico](http://go.microsoft.com/fwlink/p/?LinkID=161793) nella scheda **Configura** in un server, nella sezione **Servizi consentiti**, fare clic su **Sì** per **Servizi di Microsoft Azure**.

## Creazione della prima regola del firewall a livello di server

La prima impostazione del firewall a livello di server può essere creata usando il [portale di Azure](https://portal.azure.com/) o a livello di programmazione usando l'API REST o Azure PowerShell. Le regole del firewall a livello di server successive possono essere create e gestite utilizzando questi metodi e tramite Transact-SQL. Per ulteriori informazioni sulle regole del firewall a livello di server, vedere [Procedura: configurare le impostazioni del firewall (Database SQL di Azure)](sql-database-configure-firewall-settings.md).

## Creazione di regole del firewall a livello di database

Dopo aver configurato il primo firewall a livello di server, è possibile limitare l'accesso a determinati database. Se nella regola del firewall a livello di database si specifica un intervallo di indirizzi IP che non rientra nell'intervallo specificato nella regola del firewall a livello di server, solo i client che dispongono di indirizzi IP compresi nell'intervallo a livello di database possono accedere al database. Per un database è possibile avere un massimo di 128 regole del firewall a livello di database. Le regole del firewall a livello di database per database master e utente possono essere create e gestite tramite Transact-SQL. Per altre informazioni su come configurare le regole del firewall a livello del database, vedere [sp\_set\_database\_firewall\_rule (database SQL di Azure)](https://msdn.microsoft.com/library/dn270010.aspx)

## Gestione di regole del firewall a livello di programmazione

Oltre al portale di Azure, le regole del firewall possono essere gestite a livello di programmazione usando Transact-SQL, API REST e Azure PowerShell. Nelle tabelle seguenti viene descritto il set di comandi disponibili per ogni metodo.


### Transact-SQL

| Vista del catalogo o una Stored Procedure | Level | Descrizione |
|--------------------------------------------------------------------------------------------|-----------|------------------------------------------------------|
| [sys. firewall\_rules](https://msdn.microsoft.com/library/dn269980.aspx) | Server | Visualizza le regole del firewall a livello di server correnti |
| [sp\_set\_firewall\_rule](https://msdn.microsoft.com/library/dn270017.aspx) | Server | Crea o aggiorna regole del firewall a livello di server |
| [sp\_delete\_firewall\_rule](https://msdn.microsoft.com/library/dn270024.aspx) | Server | Rimuove regole del firewall a livello di server |
| [sys.database\_firewall\_rules](https://msdn.microsoft.com/library/dn269982.aspx) | Database | Visualizza le regole del firewall a livello di database correnti |
| [sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) | Database | Crea o aggiorna regole del firewall a livello di database |
| [sp\_delete\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270030.aspx) | Database | Rimuove le regole del firewall a livello di database |

### API REST

| API | Level | Descrizione |
|----------------------|--------|------------------------------------------------------------------|
| [Elencare le regole del firewall](https://msdn.microsoft.com/library/azure/dn505715.aspx) | Server | Visualizza le regole del firewall a livello di server correnti |
| [Creare la regola del firewall](https://msdn.microsoft.com/library/azure/dn505712.aspx) | Server | Crea o aggiorna regole del firewall a livello di server |
| [Impostare la regola del firewall](https://msdn.microsoft.com/library/azure/dn505707.aspx) | Server | Aggiorna le proprietà di una regola del firewall a livello di server esistente |
| [Eliminare la regola del firewall](https://msdn.microsoft.com/library/azure/dn505706.aspx) | Server | Rimuove regole del firewall a livello di server |


### Azure PowerShell

| Cmdlet | Level | Descrizione |
|-----------------------------------------------------------------------------------------------------|--------|------------------------------------------------------------------|
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx) | Server | Restituisce le regole del firewall a livello di server correnti |
| [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx) | Server | Crea una nuova regola del firewall a livello di server |
| [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx) | Server | Aggiorna le proprietà di una regola del firewall a livello di server esistente |
| [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) | Server | Rimuove regole del firewall a livello di server |

> [AZURE.NOTE] le modifiche alle impostazioni del firewall potrebbero impiegare fino a cinque minuti prima di avere effetto.

## Risoluzione dei problemi del firewall del database

Quando l'accesso al servizio di database SQL di Microsoft Azure non si comporta come previsto, considerare quanto segue:

- **Configurazione del firewall locale:** prima che il computer possa accedere al database SQL di Azure, è necessario creare un'eccezione del firewall sul computer per la porta TCP 1433. È necessario aprire delle porte aggiuntive se si apportano le connessioni all'interno del cloud di Azure. Per altre informazioni, vedere la sezione **V12 del database SQL: all'esterno rispetto all'interno** di [porte 1433 per 4.5 ADO.NET e SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md)

- **Rete NAT (Network address translation):** a causa di NAT, l'indirizzo IP utilizzato dal computer per connettersi al database SQL di Azure potrebbe essere diverso da quello indicato nelle impostazioni di configurazione IP del computer. Per visualizzare l'indirizzo IP usato dal computer connettersi ad Azure, accedere al portale e spostarsi nella scheda **Configura** sul server che ospita il database. Nella sezione **Indirizzi IP consentiti**, viene visualizzato **lndirizzo IP client corrente**. Fare clic su **Aggiungi** negli **Indirizzi IP consentiti ** per consentire al computer di accedere al server.

- **Le modifiche all'elenco di consentiti non sono state ancora applicate:** per rendere effettive le modifiche apportate alla configurazione del firewall del database SQL di Azure possono essere necessari fino a cinque minuti.

- **L’accesso non è autorizzato oppure è stata utilizzata una password errata:** quando un account di accesso non dispone di autorizzazioni per il server di database SQL di Azure o la password utilizzata non è corretta, la connessione al server di database SQL di Azure verrà negata. La creazione di un'impostazione del firewall fornisce ai client soltanto un'opportunità di connessione al server; ogni client deve fornire le credenziali di sicurezza necessarie. Per ulteriori informazioni sulla preparazione degli account di accesso, vedere Gestione di database, account di accesso e utenti nel database SQL di Azure.

- **Indirizzo IP dinamico:** se si dispone di una connessione Internet con indirizzi IP dinamici e si verificano problemi di comunicazione attraverso il firewall, è possibile provare una delle seguenti soluzioni:

 - Chiedere al provider di servizi Internet (ISP) l'intervallo di indirizzi IP assegnato ai computer client che accedono al server di database SQL di Azure, quindi aggiungere l'intervallo di indirizzi IP come una regola del firewall.

 - Ottenere indirizzi IP statici per i computer client, quindi aggiungere gli indirizzi IP come regole del firewall.

## Vedere anche

[Procedura: configurare le impostazioni del firewall del database (Database SQL di Azure)](sql-database-configure-firewall-settings.md)

[Centro sicurezza per il motore di Database di SQL Server e il Database SQL di Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png

<!---HONumber=AcomDC_0323_2016-->