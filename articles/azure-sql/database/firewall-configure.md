---
title: Regole del firewall IP
description: Configurare le regole del firewall IP a livello di server per un database nel database SQL di Azure o nel firewall di Azure sinapsi Analytics. Gestire l'accesso e configurare le regole del firewall IP a livello di database per il database SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 06/17/2020
ms.openlocfilehash: 802c126548a6fa7062a262e2f939c9a214480794
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789642"
---
# <a name="azure-sql-database-and-azure-synapse-ip-firewall-rules"></a>Regole del firewall IP del database SQL di Azure e della sinapsi di Azure
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Quando si crea un nuovo server nel database SQL di Azure o in Azure sinapsi Analytics denominato *SqlServer* , ad esempio, un firewall a livello di server blocca tutti gli accessi all'endpoint pubblico per il server (accessibile in *MySQLServer.database.Windows.NET* ). Per semplicità, il *database SQL* viene usato per fare riferimento sia al database SQL che ad Azure sinapsi Analytics (in precedenza SQL Data Warehouse).

> [!IMPORTANT]
> Questo articolo *non* si applica a *Istanza gestita di SQL di Azure* . Per informazioni sulla configurazione di rete, vedere [connettere l'applicazione ad Azure SQL istanza gestita](../managed-instance/connect-application-instance.md).
>
> La sinapsi di Azure supporta solo le regole del firewall IP a livello di server. Non supporta le regole del firewall IP a livello di database.

## <a name="how-the-firewall-works"></a>Funzionamento del firewall

I tentativi di connessione da Internet e Azure devono passare attraverso il firewall prima che raggiungano il server o il database, come illustrato nella figura seguente.

   ![Diagramma di configurazione del firewall][1]

### <a name="server-level-ip-firewall-rules"></a>Regole del firewall IP a livello di server

consentono ai client di accedere all'intero server ovvero a tutti i database gestiti dal server. Le regole vengono archiviate nel database *Master* . Per un server è possibile avere un massimo di 128 regole del firewall IP a livello di server. Se è abilitata l'opzione **Consenti ai servizi e alle risorse di Azure di accedere a questo server** , viene conteggiata come una singola regola del firewall per il server.
  
È possibile configurare le regole del firewall IP a livello di server usando le istruzioni portale di Azure, PowerShell o Transact-SQL.

- Per usare il portale o PowerShell, è necessario essere il proprietario della sottoscrizione o un collaboratore della sottoscrizione.
- Per utilizzare Transact-SQL, è necessario connettersi al database *Master* come account di accesso dell'entità di livello server o come amministratore Azure Active Directory. Una regola del firewall IP a livello di server deve prima essere creata da un utente che dispone di autorizzazioni a livello di Azure.

### <a name="database-level-ip-firewall-rules"></a>Regole del firewall IP a livello di database

Le regole del firewall IP a livello di database consentono ai client di accedere a determinati database (sicuri). È possibile creare le regole per ogni database (incluso il database *Master* ) e memorizzate nel database singolo.
  
- È possibile creare e gestire solo le regole del firewall IP a livello di database per i database master e utente usando le istruzioni Transact-SQL e solo dopo aver configurato il primo firewall a livello di server.
- Se si specifica un intervallo di indirizzi IP nella regola del firewall IP a livello di database non compreso nell'intervallo nella regola del firewall IP a livello di server, solo i client che dispongono di indirizzi IP nell'intervallo a livello di database potranno accedere al database.
- Per un database è possibile avere un massimo di 128 regole del firewall IP a livello di database. Per ulteriori informazioni sulla configurazione delle regole del firewall IP a livello di database, vedere l'esempio più avanti in questo articolo e vedere [sp_set_database_firewall_rule (database SQL di Azure)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database).

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Indicazioni su come impostare le regole del firewall

Quando possibile, è consigliabile usare le regole del firewall IP a livello di database. Questa pratica migliora la sicurezza e rende più portabile il database. Usare le regole del firewall IP a livello di server per gli amministratori. È inoltre possibile utilizzarli quando si dispone di molti database con gli stessi requisiti di accesso e non si desidera configurare singolarmente ogni database.

> [!NOTE]
> Per informazioni sui database portabili per la continuità aziendale, vedere nel contesto della continuità aziendale, vedere i [requisiti di autenticazione per il ripristino di emergenza](active-geo-replication-security-configure.md).

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Regole del firewall IP a livello di server e a livello di database a confronto

*Gli utenti di un database devono essere completamente isolati da un altro database?*

In caso *affermativo* , usare le regole del firewall IP a livello di database per concedere l'accesso. Questo metodo consente di evitare l'utilizzo di regole firewall IP a livello di server, che consentono l'accesso attraverso il firewall a tutti i database. Questo ridurrebbe la profondità delle difese.

*Gli utenti degli indirizzi IP devono accedere a tutti i database?*

In caso *affermativo* , usare le regole del firewall IP a livello di server per ridurre il numero di volte in cui è necessario configurare le regole del firewall IP.

*La persona o il team che configura le regole del firewall IP può accedere solo tramite il portale di Azure, PowerShell o l'API REST?*

In tal caso, è necessario usare le regole del firewall IP a livello di server. Le regole del firewall IP a livello di database possono essere configurate solo tramite Transact-SQL.  

*La persona o il team che configura le regole del firewall IP non è autorizzato ad avere un'autorizzazione di alto livello a livello di database?*

In tal caso, usare le regole del firewall IP a livello di server. Per configurare le regole del firewall IP a livello di database tramite Transact-SQL, è necessaria almeno l'autorizzazione *Control database* a livello di database.  

*La persona o il team che configura o controlla le regole del firewall IP gestisce centralmente le regole del firewall IP per molti (forse centinaia) di database?*

In questo scenario, le procedure consigliate sono determinate in base alle esigenze e all'ambiente. Le regole del firewall IP a livello di server potrebbero essere più facili da configurare, tuttavia lo scripting consente di configurare le regole a livello di database. Anche se si usano regole del firewall IP a livello di server, potrebbe essere necessario controllare le regole del firewall IP a livello di database per verificare se gli utenti con l'autorizzazione *Control* per il database creano regole del firewall IP a livello di database.

*È possibile utilizzare una combinazione di regole del firewall IP a livello di server e di database?*

Sì. Alcuni utenti, ad esempio gli amministratori, potrebbero avere bisogno di regole del firewall IP a livello di server. Altri utenti, ad esempio gli utenti di un'applicazione di database, potrebbero aver bisogno di regole del firewall IP a livello di database.

### <a name="connections-from-the-internet"></a>Connessioni da Internet

Quando un computer tenta di connettersi al server da Internet, il firewall verifica innanzitutto l'indirizzo IP di origine della richiesta in base alle regole del firewall IP a livello di database per il database richiesto dalla connessione.

- Se l'indirizzo è compreso in un intervallo specificato nelle regole del firewall IP a livello di database, la connessione viene concessa al database che contiene la regola.
- Se l'indirizzo non rientra in un intervallo nelle regole del firewall IP a livello di database, il firewall controlla le regole del firewall IP a livello di server. Se l'indirizzo si trova all'interno di un intervallo incluso nelle regole del firewall IP a livello di server, la connessione viene concessa. Le regole del firewall IP a livello di server si applicano a tutti i database gestiti dal server.  
- Se l'indirizzo non rientra in un intervallo che si trova in una delle regole del firewall IP a livello di database o di server, la richiesta di connessione ha esito negativo.

> [!NOTE]
> Per accedere al database SQL di Azure dal computer locale, verificare che il firewall in rete e nel computer locale consenta la comunicazione in uscita sulla porta TCP 1433.

### <a name="connections-from-inside-azure"></a>Connessioni dall'interno di Azure

Per consentire alle applicazioni ospitate all'interno di Azure di connettersi a SQL Server, è necessario abilitare le connessioni di Azure. Quando un'applicazione di Azure tenta di connettersi al server, il firewall verifica che le connessioni di Azure siano consentite. Questa operazione può essere attivata direttamente dal pannello portale di Azure impostando le regole del firewall, nonché cambiando le impostazioni **Consenti ai servizi e alle risorse di Azure di accedere al server** **nelle impostazioni** **firewall e reti virtuali** . Se la connessione non è consentita, la richiesta non raggiungerà il server.

> [!IMPORTANT]
> Questa opzione consente di configurare il firewall in modo da consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Se si seleziona questa opzione, assicurarsi che l'accesso e le autorizzazioni utente limitino l'accesso solo agli utenti autorizzati.

## <a name="permissions"></a>Autorizzazioni

Per poter creare e gestire le regole del firewall IP per SQL Server di Azure, sarà necessario avere:

- nel ruolo [SQL Server collaboratore](../../role-based-access-control/built-in-roles.md#sql-server-contributor)
- nel ruolo [Gestione sicurezza SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager)
- proprietario della risorsa che contiene il SQL Server di Azure

## <a name="create-and-manage-ip-firewall-rules"></a>Creare e gestire le regole del firewall IP

Si crea la prima impostazione del firewall a livello di server usando il [portale di Azure](https://portal.azure.com/) o a livello di codice usando [Azure PowerShell](/powershell/module/az.sql), l'interfaccia della riga di comando di [Azure](/cli/azure/sql/server/firewall-rule)o un' [API REST](/rest/api/sql/firewallrules/createorupdate)di Azure. È possibile creare e gestire regole aggiuntive del firewall IP a livello di server usando questi metodi o Transact-SQL.

> [!IMPORTANT]
> Le regole del firewall IP a livello di database possono essere create e gestite solo tramite Transact-SQL.

Per migliorare le prestazioni, le regole del firewall IP a livello di server vengono temporaneamente memorizzate nella cache a livello di database. Per aggiornare la cache, vedere [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql).

> [!TIP]
> È possibile utilizzare il [controllo del database](../../azure-sql/database/auditing-overview.md) per controllare le modifiche del firewall a livello di server e a livello di database.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Usare il portale di Azure per gestire le regole del firewall IP a livello di server

Per impostare una regola del firewall IP a livello di server nel portale di Azure, passare alla pagina Panoramica per il database o il server.

> [!TIP]
> Per un'esercitazione, vedere [creare un database usando il portale di Azure](single-database-create-quickstart.md).

#### <a name="from-the-database-overview-page"></a>Dalla pagina Panoramica database

1. Per impostare una regola del firewall IP a livello di server dalla pagina Panoramica database, selezionare **imposta firewall server** sulla barra degli strumenti, come illustrato nella figura seguente.

    ![Regola firewall IP del server](./media/firewall-configure/sql-database-server-set-firewall-rule.png)

    Verrà visualizzata la pagina **Impostazioni del firewall** per il server.

2. Selezionare **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP del computer in uso e quindi selezionare **Salva** . Una regola del firewall IP a livello di server viene creata per l'indirizzo IP corrente.

    ![Imposta regola firewall IP a livello di server](./media/firewall-configure/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>Dalla pagina di panoramica del server

Verrà visualizzata la pagina Panoramica del server. Mostra il nome completo del server (ad esempio *mynewserver20170403.database.Windows.NET* ) e fornisce le opzioni per un'ulteriore configurazione.

1. Per impostare una regola a livello di server da questa pagina, selezionare **Firewall** dal menu **Impostazioni** sul lato sinistro.

2. Selezionare **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP del computer in uso e quindi selezionare **Salva** . Una regola del firewall IP a livello di server viene creata per l'indirizzo IP corrente.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Usare Transact-SQL per gestire le regole del firewall IP

| Vista del catalogo o stored procedure | Level | Descrizione |
| --- | --- | --- |
| [sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database) |Server |Visualizza le regole del firewall IP a livello di server correnti |
| [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database) |Server |Crea o aggiorna regole del firewall IP a livello di server |
| [sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database) |Server |Rimuove regole del firewall IP a livello di server |
| [sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database) |Database |Visualizza le regole del firewall IP a livello di database correnti |
| [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) |Database |Crea o aggiorna regole del firewall IP a livello di database |
| [sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database) |Database |Rimuove le regole del firewall IP a livello di database |

Nell'esempio seguente vengono esaminate le regole esistenti, viene abilitato un intervallo di indirizzi IP nel server *Contoso* e viene eliminata una regola del firewall IP:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Aggiungere quindi una regola del firewall IP a livello di server.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Per eliminare una regola del firewall IP a livello di server, eseguire il *sp_delete_firewall_rule* stored procedure. Nell'esempio seguente viene eliminata la regola *ContosoFirewallRule* :

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Usare PowerShell per gestire le regole del firewall IP a livello di server

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato dal database SQL di Azure, ma tutto lo sviluppo è ora per il modulo AZ. SQL. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli AZ e AzureRm sono sostanzialmente identici.

| Cmdlet | Level | Descrizione |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Server |Restituisce le regole del firewall a livello di server correnti |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Server |Crea una nuova regola del firewall a livello di server |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Server |Aggiorna le proprietà di una regola del firewall a livello di server esistente |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Server |Rimuove regole del firewall a livello di server |

Nell'esempio seguente viene usato PowerShell per impostare una regola del firewall IP a livello di server:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```

> [!TIP]
> Per $servername specificare il nome del server e non il nome DNS completo, ad esempio, specificare **mysqldbserver** anziché **mysqldbserver.database.Windows.NET**
>
> Per esempi di PowerShell nel contesto di una guida introduttiva, vedere [creare DB-PowerShell](powershell-script-content-guide.md) e [creare un singolo database e configurare una regola del firewall IP a livello di server tramite PowerShell](scripts/create-and-configure-database-powershell.md).

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Usare l'interfaccia della riga di comando per gestire le regole firewall IP a livello di server

| Cmdlet | Level | Descrizione |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Server|Crea una regola del firewall IP del server|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Server|Elenca le regole del firewall IP in un server|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Server|Mostra i dettagli di una regola del firewall IP|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Server|Aggiorna una regola del firewall IP|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Server|Elimina una regola del firewall IP|

Nell'esempio seguente viene usata l'interfaccia della riga di comando per impostare una regola del firewall IP a livello di server:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```

> [!TIP]
> Per $servername specificare il nome del server e non il nome DNS completo, ad esempio, specificare **mysqldbserver** anziché **mysqldbserver.database.Windows.NET**
>
> Per un esempio dell'interfaccia della riga di comando nel contesto di una guida introduttiva, vedere [creare un'interfaccia](az-cli-script-samples-content-guide.md) della riga di comando di Azure e [creare un singolo database e configurare una regola firewall IP a livello di server tramite l'interfaccia della riga di comando](scripts/create-and-configure-database-cli.md)

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Usare un'API REST per gestire le regole del firewall IP a livello di server

| API | Level | Descrizione |
| --- | --- | --- |
| [Elencare le regole del firewall](/rest/api/sql/firewallrules/listbyserver) |Server |Visualizza le regole del firewall IP a livello di server correnti |
| [Creare o aggiornare le regole del firewall](/rest/api/sql/firewallrules/createorupdate) |Server |Crea o aggiorna regole del firewall IP a livello di server |
| [Elimina regole firewall](/rest/api/sql/firewallrules/delete) |Server |Rimuove regole del firewall IP a livello di server |
| [Ottenere le regole del firewall](/rest/api/sql/firewallrules/get) | Server | Ottiene regole del firewall IP a livello di server |

## <a name="troubleshoot-the-database-firewall"></a>Eseguire la risoluzione dei problemi del firewall del database

Quando l'accesso al database SQL di Azure non si comporta come previsto, considerare i punti seguenti.

- **Configurazione del firewall locale:**

  Per consentire al computer di accedere al database SQL di Azure, è necessario creare un'eccezione del firewall sul computer per la porta TCP 1433. Per stabilire connessioni entro il limite del cloud di Azure, potrebbe essere necessario aprire porte aggiuntive. Per ulteriori informazioni, vedere la sezione "database SQL: all'esterno rispetto all'interno" di [porte oltre 1433 per ADO.NET 4,5 e il database SQL di Azure](adonet-v12-develop-direct-route-ports.md).

- **NAT (Network Address Translation):**

  A causa di Network Address Translation (NAT), l'indirizzo IP usato dal computer per connettersi al database SQL di Azure può essere diverso dall'indirizzo IP nelle impostazioni di configurazione IP del computer. Per visualizzare l'indirizzo IP usato dal computer per la connessione ad Azure:
    1. Accedere al portale.
    1. Passare alla scheda **Configura** sul server che ospita il database.
    1. L' **indirizzo IP del client corrente** viene visualizzato nella sezione **indirizzi IP consentiti** . Selezionare **Aggiungi** per **indirizzi IP consentiti** per consentire al computer di accedere al server.

- **Le modifiche apportate all'elenco di consentiti non sono ancora state applicate:**

  Per rendere effettive le modifiche alla configurazione del firewall del database SQL di Azure, potrebbe essere necessario attendere fino a cinque minuti.

- **L'account di accesso non è autorizzato oppure è stata usata una password errata:**

  Se un account di accesso non dispone delle autorizzazioni per il server o la password non è corretta, la connessione al server viene negata. La creazione di un'impostazione del firewall consente solo ai *client di provare* a connettersi al server. Il client deve comunque fornire le credenziali di sicurezza necessarie. Per ulteriori informazioni sulla preparazione degli account di accesso, vedere [controllo e concessione dell'accesso al database](logins-create-manage.md).

- **Indirizzo IP dinamico:**

  Se si dispone di una connessione Internet che usa indirizzi IP dinamici e si riscontrano problemi durante l'accesso al firewall, provare una delle soluzioni seguenti:
  
  - Chiedere al provider di servizi Internet l'intervallo di indirizzi IP assegnato ai computer client che accedono al server. Aggiungere l'intervallo di indirizzi IP come regola del firewall IP.
  - Ottenere indirizzi IP statici per i computer client. Aggiungere gli indirizzi IP come regole del firewall IP.

## <a name="next-steps"></a>Passaggi successivi

- Verificare che l'ambiente di rete aziendale consenta le comunicazioni in ingresso dagli intervalli di indirizzi IP di calcolo (inclusi gli intervalli SQL) usati dai Data Center di Azure. Potrebbe essere necessario aggiungere gli indirizzi IP all'elenco Consenti. Vedere [Microsoft Azure intervalli IP del Data Center](https://www.microsoft.com/download/details.aspx?id=41653).  
- Per una guida introduttiva sulla creazione di una regola del firewall IP a livello di server, vedere [creare un database singolo nel database SQL di Azure](single-database-create-quickstart.md).
- Per informazioni sulla connessione a un database nel database SQL di Azure da applicazioni open source o di terze parti, vedere [esempi di codice di avvio rapido del client per il database SQL di Azure](connect-query-content-reference-guide.md#libraries).
- Per informazioni sulle porte aggiuntive che potrebbe essere necessario aprire, vedere la sezione "database SQL: all'esterno rispetto all'interno" di [porte oltre 1433 per ADO.NET 4,5 e database SQL](adonet-v12-develop-direct-route-ports.md)
- Per una panoramica della sicurezza del database SQL di Azure, vedere [protezione del database](security-overview.md).

<!--Image references-->
[1]: ./media/firewall-configure/sqldb-firewall-1.png