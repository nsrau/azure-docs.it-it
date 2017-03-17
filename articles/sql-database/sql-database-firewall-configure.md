---
title: 'Panoramica: Regole del firewall per il database SQL | Microsoft Docs'
description: Informazioni su come configurare un firewall del database SQL con regole del firewall a livello di server e a livello di database per la gestione dell&quot;accesso.
keywords: firewall del database
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: ac57f84c-35c3-4975-9903-241c8059011e
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/28/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 6654b49658818a3dfd5297dc551a45de0f0968dc
ms.openlocfilehash: 196a627b4c5cf921ddc99a0634f48f807593f8cc
ms.lasthandoff: 03/02/2017


---
# <a name="overview-of-azure-sql-database-firewall-rules"></a>Panoramica sulle regole del firewall per il database SQL di Azure 

Il database SQL di Microsoft Azure fornisce un servizio di database relazionale per Azure e altre applicazioni basate su Internet. Per proteggere i dati, il firewall impedisce qualsiasi accesso al server di database finché non vengono specificati i computer autorizzati. Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta.

Per configurare il firewall, creare regole del firewall che specificano gli intervalli di indirizzi IP accettabili. È possibile creare regole del firewall a livello di server e database.

* **Regole del firewall a livello di server:** queste regole consentono ai client di accedere all'intero server SQL di Azure, ovvero tutti i database all'interno dello stesso server logico. Queste regole sono archiviate nel database **master** . Le regole del firewall a livello di server possono essere configurate usando il portale o istruzioni Transact-SQL. Per creare regole del firewall a livello di server usando il portale di Azure o PowerShell, è necessario essere il proprietario o un collaboratore della sottoscrizione. Per creare una regola del firewall a livello di server usando Transact-SQL, è necessario connettersi all'istanza di database SQL come account di accesso entità di livello server o amministratore di Azure Active Directory, il che implica che una regola del firewall a livello di server debba essere prima creata da un utente con autorizzazioni a livello di Azure.
* **Regole del firewall a livello di database:** queste regole consentono ai client di accedere ai singoli database nel server di database SQL di Azure. È possibile creare queste regole per ogni database ed esse sono archiviate nei singoli database (è possibile creare regole del firewall a livello di database per il database **master**). Tali regole possono essere utili per limitare l'accesso a determinati database (accesso sicuro) all'interno dello stesso server logico. Le regole del firewall a livello di database possono essere configurate solo con istruzioni Transact-SQL.

   > [!NOTE]
   > Per un'esercitazione sull'uso dei firewall a livello di database, vedere l'articolo sull'[autenticazione e l'autorizzazione di SQL](sql-database-control-access-sql-authentication-get-started.md).
   >

**Indicazione:** è consigliabile usare le regole del firewall a livello di database quando è possibile, allo scopo di migliorare la sicurezza e la portabilità del database. Usare le regole del firewall a livello di server per gli amministratori e quando sono disponibili molti database che presentano gli stessi requisiti di accesso e non si vuole dedicare tempo alla configurazione di ogni singolo database.

> [!Note]
> Per informazioni sui database portabili per la continuità aziendale, vedere nel contesto della continuità aziendale, vedere i [requisiti di autenticazione per il ripristino di emergenza](sql-database-geo-replication-security-config.md).
>

## <a name="firewall-overview"></a>Panoramica del firewall
Inizialmente tutti gli accessi Transact-SQL al server di database SQL di Azure sono bloccati dal firewall. Per iniziare a usare il server SQL di Azure, è necessario accedere al portale di Azure e specificare una o più regole del firewall a livello di server che consentano di accedere al server SQL di Azure. Usare le regole del firewall per specificare quali intervalli di indirizzi IP da Internet sono consentiti e se le applicazioni Azure possono provare a connettersi al server SQL di Azure.

Per concedere in modo selettivo l'accesso solo a uno dei database in SQL Server di Azure, è necessario creare una regola a livello di database per il database richiesto. Specificare un intervallo di indirizzi IP per la regola firewall del database che sia successivo all'indirizzo IP specificato nella regola firewall a livello di server e assicurarsi che l'indirizzo IP del client ricada nell'intervallo specificato nella regola a livello di database.

I tentativi di connessione da Internet e Azure devono prima superare il firewall per poter raggiungere il server SQL di Azure o un database SQL, come illustrato nel diagramma seguente:

   ![Diagramma che descrive la configurazione del firewall.][1]

## <a name="connecting-from-the-internet"></a>Connessione da Internet

Quando un computer cerca di connettersi al server di database da Internet, il firewall confronta prima l'indirizzo IP di origine della richiesta con le regole firewall a livello di database, per il database richiesto dalla connessione:

* Se l'indirizzo IP della richiesta rientra in uno degli intervalli specificati nelle regole firewall a livello di database, viene consentita la connessione al database SQL che contiene le regole.
* Se l'indirizzo IP della richiesta non rientra in uno degli intervalli specificati nelle regole firewall a livello di database, vengono controllate le regole firewall a livello di server. Se l'indirizzo IP della richiesta rientra in uno degli intervalli specificati nelle regole firewall a livello di server, viene consentita la connessione. Le regole firewall a livello di server si applicano a tutti i database SQL nel server di Azure SQL.  
* Se l'indirizzo IP della richiesta non rientra negli intervalli specificati in una delle regole firewall a livello di database o di server, la richiesta di connessione ha esito negativo.

> [!NOTE]
> Per accedere al database SQL di Azure dal computer locale, verificare che il firewall in rete e nel computer locale consenta le comunicazioni in uscita sulla porta TCP 1433.
> 

## <a name="connecting-from-azure"></a>Connessione da Azure
Per consentire alle applicazioni da Azure di stabilire la connessione al server di Azure SQL, è necessario abilitare le connessioni da Azure. Quando si tenta di connettersi al server di database da un'applicazione di Azure, il firewall verifica che le connessioni da Azure siano consentite. Un'impostazione del firewall con indirizzo iniziale e finale uguale a 0.0.0.0 indica che queste connessioni sono consentite. Se il tentativo di connessione non è consentito, la richiesta non raggiungerà il server di Database SQL di Azure.

> [!IMPORTANT]
> Questa opzione permette di configurare il firewall in maniera tale da consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, assicurarsi che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.
> 

> [!NOTE]
>  Per ulteriori informazioni, vedere la sezione **database SQL: all'esterno rispetto all'interno** di [porte 1433 per 4.5 ADO.NET e SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)
>  

## <a name="creating-the-first-server-level-firewall-rule"></a>Creazione della prima regola del firewall a livello di server
La prima impostazione del firewall a livello di server può essere creata usando il [portale di Azure](https://portal.azure.com/) o a livello di programmazione con l'API REST o Azure PowerShell. Le regole del firewall a livello di server successive possono essere create e gestite utilizzando questi metodi e tramite Transact-SQL. Per migliorare le prestazioni, le regole del firewall a livello di server vengono temporaneamente memorizzate nella cache a livello di database. Per aggiornare la cache, vedere [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx). Per altre informazioni sulle regole del firewall a livello di server, vedere [Procedura: Configurare un firewall del server SQL di Azure con il portale di Azure](sql-database-configure-firewall-settings.md).

## <a name="creating-database-level-firewall-rules"></a>Creazione di regole del firewall a livello di database
 Dopo aver configurato il primo firewall a livello di server, è possibile limitare l'accesso a determinati database. Se nella regola del firewall a livello di database si specifica un intervallo di indirizzi IP che non rientra nell'intervallo specificato nella regola del firewall a livello di server, solo i client che dispongono di indirizzi IP compresi nell'intervallo a livello di database possono accedere al database. Per un database è possibile avere un massimo di 128 regole del firewall a livello di database. Le regole del firewall a livello di database per database master e utente possono essere create e gestite tramite Transact-SQL. Per altre informazioni su come configurare le regole del firewall a livello di database, vedere [sp_set_database_firewall_rule (Database SQL di Azure)](https://msdn.microsoft.com/library/dn270010.aspx).

## <a name="programmatically-managing-firewall-rules"></a>Gestione di regole del firewall a livello di programmazione
Oltre al portale di Azure, le regole del firewall possono essere gestite a livello di programmazione usando Transact-SQL, API REST e Azure PowerShell. Le tabelle seguenti descrivono il set di comandi disponibili per ogni metodo.

### <a name="transact-sql"></a>Transact-SQL
| Vista del catalogo o una Stored Procedure | Level | Descrizione |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Server |Visualizza le regole del firewall a livello di server correnti |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Server |Crea o aggiorna regole del firewall a livello di server |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Server |Rimuove regole del firewall a livello di server |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Database |Visualizza le regole del firewall a livello di database correnti |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Database |Crea o aggiorna regole del firewall a livello di database |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Database |Rimuove le regole del firewall a livello di database |

### <a name="rest-api"></a>API REST
| API | Level | Descrizione |
| --- | --- | --- |
| [Elencare le regole del firewall](https://msdn.microsoft.com/library/azure/dn505715.aspx) |Server |Visualizza le regole del firewall a livello di server correnti |
| [Creare la regola del firewall](https://msdn.microsoft.com/library/azure/dn505712.aspx) |Server |Crea o aggiorna regole del firewall a livello di server |
| [Impostare la regola del firewall](https://msdn.microsoft.com/library/azure/dn505707.aspx) |Server |Aggiorna le proprietà di una regola del firewall a livello di server esistente |
| [Eliminare la regola del firewall](https://msdn.microsoft.com/library/azure/dn505706.aspx) |Server |Rimuove regole del firewall a livello di server |

### <a name="azure-powershell"></a>Azure PowerShell
| Cmdlet | Level | Descrizione |
| --- | --- | --- |
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx) |Server |Restituisce le regole del firewall a livello di server correnti |
| [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx) |Server |Crea una nuova regola del firewall a livello di server |
| [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx) |Server |Aggiorna le proprietà di una regola del firewall a livello di server esistente |
| [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) |Server |Rimuove regole del firewall a livello di server |

> [!NOTE]
> le modifiche alle impostazioni del firewall potrebbero impiegare fino a cinque minuti prima di avere effetto.
 
 
### <a name="faq-when-should-you-use-a-server-level-firewall-rule-and-when-should-you-use-a-database-level-firewall-rule"></a>Domande frequenti: Quando è necessario usare una regola del firewall a livello di server e quando, invece, una regola del firewall a livello di database?   
D: Gli utenti di un database devono essere completamente isolati da un altro database?   
  In caso affermativo, concedere l'accesso tramite le regole del firewall a livello di database. Per evitare di usare le regole del firewall a livello di server, consentendo l'accesso attraverso il firewall a tutti i database, ridurre la profondità delle difese.   
 
D: Gli utenti degli indirizzi IP devono avere accesso a tutti i database?   
  Usare le regole del firewall a livello di server per ridurre il numero di volte in cui è necessario configurare le regole del firewall.   

D: La persona o il team che configura le regole del firewall ha accesso solo attraverso il Portale di Azure, PowerShell o l'API REST?   
  È necessario usare le regole del firewall a livello di server. Le regole del firewall a livello di database possono essere configurate solo con Transact-SQL.  

D: Alla persona o al team che configura le regole del firewall è vietato avere autorizzazioni elevate a livello di database?   
  Usare le regole del firewall a livello di server. Per configurare regole del firewall a livello di database tramite Transact-SQL, è necessaria almeno l'autorizzazione `CONTROL DATABASE` a livello di database.  

D: La persona o il team che configura o controlla le regole del firewall gestisce centralmente le regole del firewall per molti database (forse centinaia)?   
  Questa selezione dipende dalle esigenze e degli ambienti. Le regole del firewall a livello di server potrebbero essere più facili da configurare, tuttavia lo scripting consente di configurare le regole a livello di database. E anche se si usano le regole del firewall a livello di server, potrebbe essere necessario controllare le regole del firewall di database, per verificare se gli utenti con l'autorizzazione `CONTROL` nel database hanno creato le regole del firewall a livello di database.   

D: È possibile usare un insieme di regole del firewall a livello di server e di database?   
  Sì. Alcuni utenti, ad esempio gli amministratori, potrebbero aver bisogno di regole del firewall a livello di server. Altri utenti, ad esempio gli utenti di un'applicazione di database, potrebbero aver bisogno di regole del firewall a livello di database.   

## <a name="troubleshooting-the-database-firewall"></a>Risoluzione dei problemi del firewall del database
Quando l'accesso al servizio di database SQL di Microsoft Azure non si comporta come previsto, considerare quanto segue:

* **Configurazione del firewall locale:** prima che il computer possa accedere al database SQL di Azure, è necessario creare un'eccezione del firewall sul computer per la porta TCP 1433. Se si effettuano connessioni all'interno dei confini del cloud di Azure, può essere necessario aprire porte aggiuntive. Per altre informazioni, vedere la sezione **V12 del database SQL: all'esterno rispetto all'interno** di [porte 1433 per 4.5 ADO.NET e SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md)
* **Rete NAT (Network address translation):** a causa di NAT, l'indirizzo IP utilizzato dal computer per connettersi al database SQL di Azure potrebbe essere diverso da quello indicato nelle impostazioni di configurazione IP del computer. Per visualizzare l'indirizzo IP usato dal computer per connettersi ad Azure, accedere al portale e passare alla scheda **Configura** del server che ospita il database. Nella sezione **Indirizzi IP consentiti** viene visualizzato **lndirizzo IP client corrente**. Fare clic su **Aggiungi** in **Indirizzi IP consentiti** per consentire al computer di accedere al server.
* **Le modifiche all'elenco di consentiti non sono state ancora applicate:** per rendere effettive le modifiche apportate alla configurazione del firewall del database SQL di Azure possono essere necessari fino a cinque minuti.
* **L'accesso non è autorizzato oppure è stata usata una password errata:** quando un account di accesso non dispone di autorizzazioni per il server di database SQL di Azure o la password usata non è corretta, la connessione al server di database SQL di Azure verrà negata. La creazione di un'impostazione del firewall fornisce ai client soltanto un'opportunità di connessione al server; ogni client deve fornire le credenziali di sicurezza necessarie. Per ulteriori informazioni sulla preparazione degli account di accesso, vedere Gestione di database, account di accesso e utenti nel database SQL di Azure.
* **Indirizzo IP dinamico:** se si dispone di una connessione Internet con indirizzi IP dinamici e si verificano problemi di comunicazione attraverso il firewall, è possibile provare una delle seguenti soluzioni:
  
  * Chiedere al provider di servizi Internet (ISP) l'intervallo di indirizzi IP assegnato ai computer client che accedono al server di database SQL di Azure, quindi aggiungere l'intervallo di indirizzi IP come una regola del firewall.
  * Ottenere indirizzi IP statici per i computer client, quindi aggiungere gli indirizzi IP come regole del firewall.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulla creazione di regole del firewall a livello di server e di database, vedere:

* [Configurare regole firewall a livello di server del database SQL di Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md)
* [Configurare le regole firewall a livello di server e di database per il database SQL di Azure tramite T-SQL](sql-database-configure-firewall-settings-tsql.md)
* [Configurare le regole firewall a livello di server per il database SQL di Azure con PowerShell](sql-database-configure-firewall-settings-powershell.md)
* [Configurare regole firewall a livello di server del database SQL di Azure tramite l'API REST](sql-database-configure-firewall-settings-rest.md)

Per un'esercitazione sulla creazione di un database, vedere [Primo database SQL di Azure](sql-database-get-started.md).
Per informazioni sulla connessione a un database SQL di Azure da applicazioni open source o di terze parti, vedere [Esempi di codice di avvio rapido del client per il database SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Per informazioni su come passare ai database, vedere [Gestire l'accesso al database e la sicurezza degli account di accesso](https://msdn.microsoft.com/library/azure/ee336235.aspx).   
Per un'esercitazione end-to-end sulla creazione di accessi, utenti e firewall, vedere [Autenticazione di SQL Server, accesso e regole del firewall a livello di database](sql-database-control-access-sql-authentication-get-started.md).

## <a name="additional-resources"></a>Risorse aggiuntive
* [Protezione del database](sql-database-security-overview.md)
* [Centro sicurezza per il motore di Database di SQL Server e il Database SQL di Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png

