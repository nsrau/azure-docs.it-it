---
title: Regole del firewall IP
description: Configurare le regole del firewall IP a livello di server per un database SQL o un firewall SQL Data Warehouse. Gestire l'accesso e configurare le regole del firewall IP a livello di database per un database singolo o in pool.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/18/2019
ms.openlocfilehash: af88fdf3378a6290c773c658ea6dd3469d7c92cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531278"
---
# <a name="azure-sql-database-and-azure-sql-data-warehouse-ip-firewall-rules"></a>Regole del firewall IP del database SQL di Azure e di Azure SQL Data WarehouseAzure SQL Database and Azure SQL Data Warehouse IP firewall rules

> [!NOTE]
> Questo articolo si applica ai server SQL di Azure e ai database del database SQL di Azure e del data warehouse SQL di Azure in un server SQL di Azure.This article applies to Azure SQL servers, and to both Azure SQL Database and Azure SQL Data Warehouse databases on an Azure SQL server. Per semplicità, il *database SQL* viene usato per fare riferimento sia al database SQL che al data warehouse SQL.

> [!IMPORTANT]
> Le informazioni di questo articolo *non* sono valide per *Istanza gestita di database SQL di Azure*. Per informazioni sulla configurazione di rete, vedere [Connettere l'applicazione all'istanza gestita del database SQL](sql-database-managed-instance-connect-app.md)di Azure.For information about network configuration, see Connect your application to Azure SQL Database Managed Instance.

Quando si crea un nuovo server SQL di Azure denominato *mysqlserver*, ad esempio, il firewall del database SQL blocca tutti gli accessi all'endpoint pubblico per il server (accessibile in *mysqlserver.database.windows.net*).

> [!IMPORTANT]
> SQL Data Warehouse supporta solo le regole del firewall IP a livello di server. Non supporta le regole del firewall IP a livello di database.

## <a name="how-the-firewall-works"></a>Funzionamento del firewall
I tentativi di connessione da Internet e Azure devono passare attraverso il firewall prima di raggiungere il server SQL o il database SQL, come illustrato nel diagramma seguente.

   ![Diagramma di configurazione del firewall][1]

### <a name="server-level-ip-firewall-rules"></a>Regole del firewall IP a livello di server

  Queste regole consentono ai client di accedere all'intero server SQL di Azure, ovvero a tutti i database all'interno dello stesso server di database SQL. Le regole vengono archiviate nel database *master.* È possibile avere un massimo di 128 regole del firewall IP a livello di server per un server SQL di Azure.You can have a maximum of 128 server-level IP firewall rules for an Azure SQL Server. Se l'impostazione **Consenti ai servizi e alle risorse** di Azure di accedere a questo server è abilitata, questa viene considerata una singola regola del firewall per SQL Server di Azure.If you have the Allow Azure Services and resources to access this server setting enabled, this counts as a single firewall rule for Azure SQL Server.
  
  È possibile configurare le regole del firewall IP a livello di server usando il portale di Azure, PowerShell o le istruzioni Transact-SQLTransact-SQL .
  - Per usare il portale o PowerShell, è necessario essere il proprietario della sottoscrizione o un collaboratore della sottoscrizione.
  - Per usare Transact-SQLTransact-SQL, è necessario connettersi all'istanza del database SQL come account di accesso dell'entità a livello di server o come amministratore di Azure Active Directory.To use Transact-SQLTransact-SQL, you must connect to the SQL Database instance as the server-level principal login or as the Azure Active Directory administrator. Una regola del firewall IP a livello di server deve prima essere creata da un utente con autorizzazioni a livello di Azure.

### <a name="database-level-ip-firewall-rules"></a>Regole del firewall IP a livello di database

  Queste regole consentono ai client di accedere a determinati database (sicuri) nello stesso server di database SQL. Creare le regole per ogni database (incluso il database *master)* e vengono archiviati nel singolo database.
  
  È possibile creare e gestire le regole del firewall IP a livello di database per i database master e utente utilizzando le istruzioni Transact-SQLTransact-SQL e solo dopo aver configurato il primo firewall a livello di server.
  
  Se si specifica un intervallo di indirizzi IP nella regola del firewall IP a livello di database non compreso nell'intervallo nella regola del firewall IP a livello di server, solo i client che dispongono di indirizzi IP nell'intervallo a livello di database possono accedere al database.
  
  Per un database è possibile avere un massimo di 128 regole del firewall IP a livello di database. Per altre informazioni sulla configurazione delle regole del firewall IP a livello di database, vedere l'esempio più avanti in questo articolo e vedere [sp_set_database_firewall_rule (Database SQL](https://msdn.microsoft.com/library/dn270010.aspx)di Azure).

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Consigli su come impostare le regole del firewall

Se possibile, è consigliabile utilizzare regole del firewall IP a livello di database. Questa pratica migliora la sicurezza e rende il database più portabile. Utilizzare le regole del firewall IP a livello di server per gli amministratori. Utilizzarli anche quando si dispone di molti database che hanno gli stessi requisiti di accesso e non si desidera configurare ogni database singolarmente.

> [!NOTE]
> Per informazioni sui database portabili per la continuità aziendale, vedere nel contesto della continuità aziendale, vedere i [requisiti di autenticazione per il ripristino di emergenza](sql-database-geo-replication-security-config.md).

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Regole del firewall IP a livello di server e a livello di database a confronto

*Gli utenti di un database devono essere completamente isolati da un altro database?*

In *caso affermativo,* utilizzare le regole del firewall IP a livello di database per concedere l'accesso. Questo metodo evita l'utilizzo di regole del firewall IP a livello di server, che consentono l'accesso attraverso il firewall a tutti i database. Questo ridurrebbe la profondità delle tue difese.

*Gli utenti degli indirizzi IP devono accedere a tutti i database?*

In *caso affermativo,* utilizzare le regole del firewall IP a livello di server per ridurre il numero di volte in cui è necessario configurare le regole del firewall IP.

*La persona o il team che configura le regole del firewall IP ha accesso solo tramite il portale di Azure, PowerShell o l'API REST?*

In tal caso, è necessario utilizzare le regole del firewall IP a livello di server. Le regole del firewall IP a livello di database possono essere configurate solo tramite Transact-SQLTransact-SQL.Database-level IP firewall rules can only be configured through Transact-SQLTransact-SQL.  

*La persona o il team che configura le regole del firewall IP non può disporre di autorizzazioni di alto livello a livello di database?*

In tal caso, utilizzare le regole del firewall IP a livello di server. Per configurare le regole del firewall IP a livello di database tramite Transact-SQLTransact-SQL è necessaria almeno l'autorizzazione *CONTROL DATABASE* a livello di database.  

*La persona o il team che configura o controlla le regole del firewall IP gestisce centralmente le regole del firewall IP per molti (forse centinaia) di database?*

In questo scenario, le procedure consigliate sono determinate dalle esigenze e dall'ambiente. Le regole del firewall IP a livello di server potrebbero essere più facili da configurare, tuttavia lo scripting consente di configurare le regole a livello di database. E anche se si utilizzano le regole del firewall IP a livello di server, potrebbe essere necessario controllare le regole del firewall IP a livello di database per vedere se gli utenti con autorizzazione *CONTROL* per il database creano regole del firewall IP a livello di database.

*È possibile utilizzare una combinazione di regole del firewall IP a livello di server e di database?*

Sì. Alcuni utenti, ad esempio gli amministratori, potrebbero aver bisogno di regole del firewall IP a livello di server. Altri utenti, ad esempio gli utenti di un'applicazione di database, potrebbero aver bisogno di regole del firewall IP a livello di database.

### <a name="connections-from-the-internet"></a>Connessioni da Internet

Quando un computer tenta di connettersi al server di database da Internet, il firewall controlla innanzitutto l'indirizzo IP di origine della richiesta rispetto alle regole del firewall IP a livello di database per il database richiesto dalla connessione.

- Se l'indirizzo è compreso in un intervallo specificato nelle regole del firewall IP a livello di database, la connessione viene concessa al database SQL che contiene la regola.
- Se l'indirizzo non è compreso in un intervallo nelle regole del firewall IP a livello di database, il firewall controlla le regole del firewall IP a livello di server. Se l'indirizzo è compreso in un intervallo che si trova nelle regole del firewall IP a livello di server, la connessione viene concessa. Le regole del firewall IP a livello di server si applicano a tutti i database SQL nel server di Azure SQL.  
- Se l'indirizzo non è compreso in un intervallo che si trova in una delle regole del firewall IP a livello di database o server, la richiesta di connessione ha esito negativo.

> [!NOTE]
> Per accedere al database SQL dal computer locale, assicurarsi che il firewall della rete e del computer locale consenta la comunicazione in uscita sulla porta TCP 1433.

### <a name="connections-from-inside-azure"></a>Connessioni dall'interno di AzureConnections from inside Azure

Per consentire alle applicazioni ospitate all'interno di Azure di connettersi al server SQL, è necessario abilitare le connessioni di Azure.To allow applications hosted inside Azure to connect to your SQL server, Azure connections must be enabled. Quando un'applicazione da Azure tenta di connettersi al server di database, il firewall verifica che siano consentite le connessioni di Azure.When an application from Azure tries to connect to your database server, the firewall verifies that Azure connections are allowed. Un'impostazione del firewall con indirizzi IP iniziale e finale pari a 0.0.0.0 indica che sono consentite connessioni di Azure.A firewall setting that has starting and ending IP addresses equal to *0.0.0.0* indicars that Azure connections are allowed. Questa opzione può essere attivata direttamente dal pannello Portale di Azure impostando le regole del firewall, nonché cambiando le **impostazioni Consenti ai servizi e alle risorse** di Azure di accedere a questo server su **ON** nelle impostazioni Firewall e **reti virtuali.** Se la connessione non è consentita, la richiesta non raggiunge il server di database SQL.

> [!IMPORTANT]
> Questa opzione configura il firewall per consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Se si seleziona questa opzione, assicurarsi che le autorizzazioni di accesso e utente limitino l'accesso solo agli utenti autorizzati.

## <a name="create-and-manage-ip-firewall-rules"></a>Creare e gestire le regole del firewall IP

Per creare la prima impostazione del firewall a livello di server, usare il portale di [Azure](https://portal.azure.com/) o a livello di codice usando [Azure PowerShell,](https://docs.microsoft.com/powershell/module/az.sql) [l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/sql/server/firewall-rule)di Azure o un'API [REST](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)di Azure. È possibile creare e gestire regole aggiuntive del firewall IP a livello di server utilizzando questi metodi o Transact-SQLTransact-SQL .

> [!IMPORTANT]
> Le regole del firewall IP a livello di database possono essere create e gestite solo tramite Transact-SQLTransact-SQL.

Per migliorare le prestazioni, le regole del firewall IP a livello di server vengono temporaneamente memorizzate nella cache a livello di database. Per aggiornare la cache, vedere [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> È possibile usare il [servizio di controllo del database SQL](sql-database-auditing.md) per controllare le modifiche del firewall a livello di server e di database.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Usare il portale di Azure per gestire le regole del firewall IP a livello di serverUse the Azure portal to manage server-level IP firewall rules

Per impostare una regola del firewall IP a livello di server nel portale di Azure, passare alla pagina di panoramica del database SQL di Azure o del server di database SQL.

> [!TIP]
> Per un'esercitazione, vedere [Creare un database tramite il portale di Azure](sql-database-single-database-get-started.md).

#### <a name="from-the-database-overview-page"></a>Dalla pagina di panoramica del database

1. Per impostare una regola del firewall IP a livello di server dalla pagina di panoramica del database, selezionare **Imposta firewall server** sulla barra degli strumenti, come illustrato nell'immagine seguente. 

    ![Regola firewall IP del server](./media/sql-database-get-started-portal/sql-database-server-set-firewall-rule.png)

    Si apre la pagina **Impostazioni del firewall** per il server di database SQL.

2. Selezionare **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP del computer in uso, quindi scegliere **Salva**. Una regola del firewall IP a livello di server viene creata per l'indirizzo IP corrente.

    ![Impostare la regola del firewall IP a livello di server](./media/sql-database-get-started-portal/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>Dalla pagina di panoramica del server

Viene visualizzata la pagina di panoramica del server. Mostra il nome completo del server (ad esempio *mynewserver20170403.database.windows.net*) e fornisce opzioni per un'ulteriore configurazione.

1. Per impostare una regola a livello di server da questa pagina, selezionare **Firewall** dal menu **Impostazioni** sul lato sinistro.

2. Selezionare **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP del computer in uso, quindi scegliere **Salva**. Una regola del firewall IP a livello di server viene creata per l'indirizzo IP corrente.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Usare Transact-SQLTransact-SQL per gestire le regole del firewall IPUse Transact-SQL to manage IP firewall rules

| Visualizzazione catalogo o stored procedure | Level | Descrizione |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Server |Visualizza le regole del firewall IP a livello di server correnti |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Server |Crea o aggiorna regole del firewall IP a livello di server |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Server |Rimuove regole del firewall IP a livello di server |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Database |Visualizza le regole del firewall IP a livello di database correnti |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Database |Crea o aggiorna regole del firewall IP a livello di database |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Database |Rimuove le regole del firewall IP a livello di database |

Nell'esempio seguente vengono esaminate le regole esistenti, viene abilitato un intervallo di indirizzi IP nel server *Contoso*ed eliminata una regola del firewall IP:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Aggiungere quindi una regola del firewall IP a livello di server.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Per eliminare una regola del firewall IP a livello di server, eseguire la *sp_delete_firewall_rule* stored procedure. Nell'esempio seguente viene eliminata la regola *ContosoFirewallRule*:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Usare PowerShell per gestire le regole del firewall IP a livello di serverUse PowerShell to manage server-level IP firewall rules 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo di Azure Resource Manager di PowerShell è ancora supportato dal database SQL di Azure, ma tutto lo sviluppo è ora per il modulo Az.Sql.The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all development is now for the Az.Sql module. Per questi cmdlet, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici.

| Cmdlet | Level | Descrizione |
| --- | --- | --- |
| [Oggetto Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Server |Restituisce le regole del firewall a livello di server correnti |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Server |Crea una nuova regola del firewall a livello di server |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Server |Aggiorna le proprietà di una regola del firewall a livello di server esistente |
| [Rimuovi-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Server |Rimuove regole del firewall a livello di server |

L'esempio seguente usa PowerShell per impostare una regola del firewall IP a livello di server:The following example uses PowerShell to set a server-level IP firewall rule:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```
> [!TIP]
> Ad $servername specificare il nome del server e non il nome DNS completo, ad esempio specificare **mysqldbserver** anziché **mysqldbserver.database.windows.net**

> [!TIP]
> Per esempi di PowerShell nel contesto di una guida introduttiva, vedere [Create DB - PowerShell](sql-database-powershell-samples.md) e [Create a single database e configure a SQL Database server-level IP firewall rule using PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Utilizzare l'interfaccia della riga di comando per gestire le regole del firewall IP a livello di serverUse CLI to manage server-level IP firewall rules

| Cmdlet | Level | Descrizione |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Server|Crea una regola del firewall IP del server|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Server|Elenca le regole del firewall IP in un server|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Server|Mostra i dettagli di una regola del firewall IP|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Server|Aggiorna una regola del firewall IP|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Server|Elimina una regola del firewall IP|

Nell'esempio seguente viene utilizzata l'interfaccia della riga di comando per impostare una regola del firewall IP a livello di server:The following example uses CLI to set a server-level IP firewall rule:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```
> [!TIP]
> Ad $servername specificare il nome del server e non il nome DNS completo, ad esempio specificare **mysqldbserver** anziché **mysqldbserver.database.windows.net**

> [!TIP]
> Per un esempio di interfaccia della riga di comando nel contesto di una guida introduttiva, vedere [Creare un database - l'interfaccia della riga](sql-database-cli-samples.md) di comando di Azure e Creare un singolo database e [configurare una regola del firewall IP del database SQL usando l'interfaccia della riga di comando](scripts/sql-database-create-and-configure-database-cli.md)di Azure.

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Usare un'API REST per gestire le regole del firewall IP a livello di serverUse a REST API to manage server-level IP firewall rules

| API | Level | Descrizione |
| --- | --- | --- |
| [Elencare le regole del firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Server |Visualizza le regole del firewall IP a livello di server correnti |
| [Creare o aggiornare le regole del firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Server |Crea o aggiorna regole del firewall IP a livello di server |
| [Eliminare le regole del firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Server |Rimuove regole del firewall IP a livello di server |
| [Ottenere le regole del firewallGet firewall rules](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Server | Ottiene regole del firewall IP a livello di server |

## <a name="troubleshoot-the-database-firewall"></a>Risolvere i problemi relativi al firewall del databaseTroubleshoot the database firewall

Quando l'accesso al servizio database SQL non si comporta come previsto, considerare i punti seguenti.

- **Configurazione del firewall locale:**

  Prima che il computer possa accedere al database SQL, potrebbe essere necessario creare un'eccezione del firewall nel computer per la porta TCP 1433. Per stabilire connessioni all'interno del limite del cloud di Azure, potrebbe essere necessario aprire porte aggiuntive. Per ulteriori informazioni, vedere la sezione "Database SQL: esterno e interno" di [Porte oltre 1433 per ADO.NET 4.5 e Database SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Traduzione dell'indirizzo di rete:**

  A causa della conversione degli indirizzi di rete (NAT), l'indirizzo IP utilizzato dal computer per connettersi al database SQL potrebbe essere diverso dall'indirizzo IP nelle impostazioni di configurazione IP del computer. To view the IP address that your computer is using to connect to Azure:
    1. Accedere al portale.
    1. Passare alla scheda **Configura** nel server che ospita il database.
    1. **L'indirizzo IP client corrente** viene visualizzato nella sezione Indirizzi IP **consentiti.** Selezionare **Aggiungi** per **Indirizzi IP consentiti** per consentire al computer di accedere al server.

- **Le modifiche all'elenco Consenti non sono ancora state applicate:**

  Potrebbe verificarsi un ritardo massimo di cinque minuti per rendere effettive le modifiche apportate alla configurazione del firewall del database SQL.

- **L'accesso non è autorizzato o è stata utilizzata una password errata:**

  Se un account di accesso non dispone delle autorizzazioni per il server di database SQL o la password non è corretta, la connessione al server viene negata. La creazione di un'impostazione del firewall offre solo ai client *la possibilità* di tentare di connettersi al server. Il client deve comunque fornire le credenziali di sicurezza necessarie. Per ulteriori informazioni sulla preparazione degli account di accesso, vedere [Controllo e concessione dell'accesso al database SQL al database SQL e](sql-database-manage-logins.md)al data warehouse SQL.

- **Indirizzo IP dinamico:**

  Se si dispone di una connessione a Internet che utilizza l'indirizzamento IP dinamico e si hanno problemi a passare attraverso il firewall, provare una delle seguenti soluzioni:
  
  - Richiedere al provider di servizi Internet l'intervallo di indirizzi IP assegnato ai computer client che accedono al server di database SQL. Aggiungere tale intervallo di indirizzi IP come regola del firewall IP.
  - Ottenere invece l'indirizzamento IP statico per i computer client. Aggiungere gli indirizzi IP come regole del firewall IP.

## <a name="next-steps"></a>Passaggi successivi

- Verificare che l'ambiente di rete aziendale consenta la comunicazione in ingresso dagli intervalli di indirizzi IP di calcolo (inclusi gli intervalli SQL) usati dai data center di Azure.Confirm that your corporate network environment allows inbound communication from the compute IP address ranges (including SQL ranges) that are used by the Azure datacenters. Potrebbe essere necessario aggiungere tali indirizzi IP all'elenco Consenti. Vedere [Intervalli IP del data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).  
- Per una guida introduttiva sulla creazione di una regola del firewall IP a livello di server, vedere Creare un database SQL di [Azure.](sql-database-single-database-get-started.md)
- Per informazioni sulla connessione a un database SQL di Azure da applicazioni open source o di terze parti, vedere Esempi di [codice delle guide introduttive](https://msdn.microsoft.com/library/azure/ee336282.aspx)client in Database SQL.
- Per informazioni su porte aggiuntive che potrebbe essere necessario aprire, vedere la sezione "Database SQL: esterno vs interno" di [Porte oltre 1433 per ADO.NET 4.5 e Database SQL](sql-database-develop-direct-route-ports-adonet-v12.md)
- Per una panoramica della sicurezza del database SQL di Azure, vedere [Protezione del database.](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
