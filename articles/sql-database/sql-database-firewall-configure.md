---
title: Regole del firewall IP per il database SQL di Azure e Azure SQL Data Warehouse | Microsoft Docs
description: Informazioni su come configurare un firewall per il database SQL o SQL Data Warehouse con regole del firewall IP a livello di server per la gestione dell'accesso e su come configurare un database singolo o in pool con regole del firewall IP a livello di database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 513836257a292069da709ad7a71e480f2b4d069d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66158325"
---
# <a name="azure-sql-database-and-sql-data-warehouse-ip-firewall-rules"></a>Regole del firewall IP per il database SQL di Azure e SQL Data Warehouse

Il [database SQL di Microsoft Azure](sql-database-technical-overview.md) e [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) forniscono un servizio di database relazionale per Azure e altre applicazioni basate su Internet. Per proteggere i dati, il firewall impedisce qualsiasi accesso al server di database finché non vengono specificati i computer autorizzati. Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta.

> [!NOTE]
> Questo articolo è applicabile al server SQL di Azure e ai database SQL e di SQL Data Warehouse creati nel server SQL di Azure. Per semplicità, "database SQL" viene usato per fare riferimento sia al database SQL che al database di SQL Data Warehouse.
> [!IMPORTANT]
> Le informazioni di questo articolo *non* sono valide per **Istanza gestita di database SQL di Azure**. Vedere l'articolo seguente sulla [connessione a un'istanza gestita](sql-database-managed-instance-connect-app.md) per ulteriori informazioni sulla configurazione di rete necessaria.

## <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>Regole di rete virtuale come alternativa alle regole IP

Oltre alle regole IP, il firewall gestisce anche le *regole di rete virtuale*. Le regole di rete virtuale si basano sugli endpoint servizio di rete virtuale. In alcuni casi le regole di rete virtuale potrebbero essere preferibili alle regole IP. Per altre informazioni, vedere [Endpoint servizio di rete virtuale e regole per il database SQL di Azure](sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="overview"></a>Panoramica

Inizialmente tutti gli accessi al server di database SQL di Azure sono bloccati dal firewall del database SQL. Per accedere a un server di database, è necessario specificare una o più regole del firewall IP a livello di server che consentano di accedere al server di database SQL di Azure. Usare le regole del firewall IP per specificare quali intervalli di indirizzi IP da Internet sono consentiti e se le applicazioni Azure possono provare a connettersi al server di database SQL di Azure.

Per concedere in modo selettivo l'accesso solo a uno dei database in SQL Server di Azure, è necessario creare una regola a livello di database per il database richiesto. Specificare un intervallo di indirizzi IP per la regola del firewall IP del database che sia successivo all'indirizzo IP specificato nella regola del firewall IP a livello di server e assicurarsi che l'indirizzo IP del client ricada nell'intervallo specificato nella regola a livello di database.

> [!IMPORTANT]
> SQL Data Warehouse supporta solo le regole del firewall IP a livello di server (non supporta le regole del firewall IP a livello di database).

I tentativi di connessione da Internet e Azure devono prima superare il firewall per poter raggiungere il server SQL di Azure o un database SQL, come illustrato nel diagramma seguente:

   ![Diagramma che descrive la configurazione del firewall.][1]

- **Regole del firewall IP a livello di server:**

  Queste regole consentono ai client di accedere all'intero server SQL di Azure, ovvero a tutti i database all'interno dello stesso server di database SQL. Queste regole sono archiviate nel database **master** . Le regole del firewall IP a livello di server possono essere configurate usando il portale o istruzioni Transact-SQL. Per creare regole del firewall IP a livello di server usando il portale di Azure o PowerShell, è necessario essere il proprietario o un collaboratore della sottoscrizione. Per creare una regola del firewall IP a livello di server usando Transact-SQL, è necessario connettersi all'istanza di database SQL come account di accesso entità di livello server o amministratore di Azure Active Directory, il che implica che una regola del firewall IP a livello di server debba essere prima creata da un utente con autorizzazioni a livello di Azure.

- **Regole del firewall IP a livello di database:**

  Queste regole consentono ai client di accedere a determinati database (sicuri) nello stesso server di database SQL. È possibile creare queste regole per ogni database (incluso il database **master**) ed esse vengono archiviate nei singoli database. Le regole del firewall IP a livello di database per i database utente e master possono essere create e gestite solo usando le istruzioni Transact-SQL e solo dopo aver configurato il primo firewall a livello di server. Se nella regola del firewall IP a livello di database si specifica un intervallo di indirizzi IP che non rientra nell'intervallo specificato nella regola del firewall IP a livello di server, solo i client che dispongono di indirizzi IP compresi nell'intervallo a livello di database possono accedere al database. Per un database è possibile avere un massimo di 128 regole del firewall IP a livello di database. Per altre informazioni su come configurare le regole del firewall IP a livello di database, vedere l'esempio presente in questo articolo e vedere [sp_set_database_firewall_rule (Database SQL di Azure)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendation"></a>Recommendation

È consigliabile usare le regole del firewall IP a livello di database quando è possibile, allo scopo di migliorare la sicurezza e la portabilità del database. Usare le regole del firewall IP a livello di server per gli amministratori e quando sono disponibili molti database che presentano gli stessi requisiti di accesso e non si vuole dedicare tempo alla configurazione di ogni singolo database.

> [!IMPORTANT]
> Il database SQL di Microsoft Azure supporta un massimo di 128 regole del firewall IP.
> [!NOTE]
> Per informazioni sui database portabili per la continuità aziendale, vedere nel contesto della continuità aziendale, vedere i [requisiti di autenticazione per il ripristino di emergenza](sql-database-geo-replication-security-config.md).

### <a name="connecting-from-the-internet"></a>Connessione da Internet

Quando un computer cerca di connettersi al server di database da Internet, il firewall confronta prima l'indirizzo IP di origine della richiesta con le regole del firewall IP a livello di database, per il database richiesto dalla connessione:

- Se l'indirizzo IP della richiesta rientra in uno degli intervalli specificati nelle regole del firewall IP a livello di database, viene consentita la connessione al database SQL che contiene le regole.
- Se l'indirizzo IP della richiesta non rientra in uno degli intervalli specificati nella regola del firewall IP a livello di database, vengono controllate le regole del firewall IP a livello di server. Se l'indirizzo IP della richiesta rientra in uno degli intervalli specificati nelle regole del firewall IP a livello di server, viene consentita la connessione. Le regole del firewall IP a livello di server si applicano a tutti i database SQL nel server di Azure SQL.  
- Se l'indirizzo IP della richiesta non rientra negli intervalli specificati in una delle regole del firewall IP a livello di database o di server, la richiesta di connessione ha esito negativo.

> [!NOTE]
> Per accedere al database SQL di Azure dal computer locale, verificare che il firewall in rete e nel computer locale consenta le comunicazioni in uscita sulla porta TCP 1433.

### <a name="connecting-from-azure"></a>Connessione da Azure

Per consentire alle applicazioni da Azure di stabilire la connessione al server di Azure SQL, è necessario abilitare le connessioni da Azure. Quando si tenta di connettersi al server di database da un'applicazione di Azure, il firewall verifica che le connessioni da Azure siano consentite. Un'impostazione del firewall con indirizzo iniziale e finale uguale a 0.0.0.0 indica che le connessioni da Azure sono consentite. Se il tentativo di connessione non è consentito, la richiesta non raggiungerà il server di Database SQL di Azure.

> [!IMPORTANT]
> Questa opzione permette di configurare il firewall in maniera tale da consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, assicurarsi che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.

## <a name="creating-and-managing-ip-firewall-rules"></a>Creazione e gestione di regole del firewall IP

La prima impostazione del firewall a livello di server può essere creata usando il [Portale di Azure](https://portal.azure.com/) o a livello di programmazione tramite [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.sql), [l'interfaccia della riga di comando di Azure](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create), o l'[API REST](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate). Le regole del firewall IP a livello di server successive possono essere create e gestite utilizzando questi metodi e tramite Transact-SQL.

> [!IMPORTANT]
> Le regole del firewall IP a livello di database possono essere create e gestite solo con Transact-SQL.

Per migliorare le prestazioni, le regole del firewall IP a livello di server vengono temporaneamente memorizzate nella cache a livello di database. Per aggiornare la cache, vedere [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> È possibile usare il [servizio di controllo del database SQL](sql-database-auditing.md) per controllare le modifiche del firewall a livello di server e di database.

## <a name="manage-server-level-ip-firewall-rules-using-the-azure-portal"></a>Gestire regole del firewall IP a livello di server tramite il portale di Azure

Per impostare una regola del firewall IP a livello di server nel portale di Azure, è possibile consultare la pagina Panoramica per il database SQL di Azure o la pagina Panoramica per il server di database SQL.

> [!TIP]
> Per un'esercitazione, vedere [Creare un database tramite il portale di Azure](sql-database-single-database-get-started.md).

### <a name="from-database-overview-page"></a>Dalla pagina di panoramica del database

1. Per impostare una regola del firewall IP a livello di server dalla pagina di panoramica del database, fare clic su **Imposta firewall server** sulla barra degli strumenti, come illustrato nella figura seguente: Si apre la pagina **Impostazioni del firewall** per il server del database SQL.

      ![regola del firewall IP del server](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. Fare clic su **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP del computer attualmente in uso e quindi fare clic su **Salva**. Una regola del firewall IP a livello di server viene creata per l'indirizzo IP corrente.

      ![impostare la regola del firewall IP a livello di server](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

### <a name="from-server-overview-page"></a>Dalla pagina di panoramica del server

Si apre la pagina di panoramica per il server che visualizza il nome completo del server, ad esempio **mynewserver20170403.database.windows.net**, e offre altre opzioni di configurazione.

1. Per impostare una regola a livello di server dalla pagina di panoramica del server, fare clic su **Firewall** nel menu a sinistra sotto Impostazioni:

2. Fare clic su **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP del computer attualmente in uso e quindi fare clic su **Salva**. Una regola del firewall IP a livello di server viene creata per l'indirizzo IP corrente.

## <a name="manage-ip-firewall-rules-using-transact-sql"></a>Gestire le regole del firewall IP con Transact-SQL

| Vista del catalogo o una Stored Procedure | Level | DESCRIZIONE |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Server |Visualizza le regole del firewall IP a livello di server correnti |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Server |Crea o aggiorna regole del firewall IP a livello di server |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Server |Rimuove regole del firewall IP a livello di server |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Database |Visualizza le regole del firewall IP a livello di database correnti |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Database |Crea o aggiorna regole del firewall IP a livello di database |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Database |Rimuove le regole del firewall IP a livello di database |

Negli esempi seguenti si rivedono le regole esistenti, si attiva un intervallo di indirizzi IP nel server di Contoso e si elimina una regola del firewall IP:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Aggiungere quindi una regola del firewall IP a livello di server.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Per eliminare una regola del firewall IP a livello di server, eseguire la stored procedure sp_delete_firewall_rule. Nell'esempio seguente viene eliminata la regola denominata ContosoFirewallRule:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

## <a name="manage-server-level-ip-firewall-rules-using-azure-powershell"></a>Gestire regole del firewall IP a livello di server tramite Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure PowerShell per Resource Manager è ancora supportato dal Database SQL di Azure, ma i progetti di sviluppo future è per il modulo Az.Sql. Per questi cmdlet, vedere [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az e nei moduli AzureRm sono sostanzialmente identici.

| Cmdlet | Level | DESCRIZIONE |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Server |Restituisce le regole del firewall a livello di server correnti |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Server |Crea una nuova regola del firewall a livello di server |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Server |Aggiorna le proprietà di una regola del firewall a livello di server esistente |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Server |Rimuove regole del firewall a livello di server |

Nell'esempio seguente si imposta una regola del firewall IP a livello di server con PowerShell:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> Per esempi di PowerShell nel contesto di un avvio rapido, vedere [Creare un database usando PowerShell](sql-database-powershell-samples.md) e [Creare un singolo database e configurare una regola del firewall IP a livello di server di database SQL usando PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)

## <a name="manage-server-level-ip-firewall-rules-using-azure-cli"></a>Gestire regole del firewall IP a livello di server tramite l'interfaccia della riga di comando di Azure

| Cmdlet | Level | DESCRIZIONE |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Server|Crea una regola del firewall IP del server|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Server|Elenca le regole del firewall IP in un server|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Server|Mostra i dettagli di una regola del firewall IP|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Server|Aggiorna una regola del firewall IP|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Server|Elimina una regola del firewall IP|

Nell'esempio seguente si imposta una regola del firewall IP a livello di server con l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Per un esempio di interfaccia della riga di comando di Azure nel contesto di un avvio rapido, vedere [Creare un database usando l'interfaccia della riga di comando di Azure](sql-database-cli-samples.md) e [Creare un singolo database e configurare una regola del firewall IP del database SQL tramite l'interfaccia della riga di comando di Azure](scripts/sql-database-create-and-configure-database-cli.md)

## <a name="manage-server-level-ip-firewall-rules-using-rest-api"></a>Gestire regole del firewall IP a livello di server tramite l'API REST

| API | Level | DESCRIZIONE |
| --- | --- | --- |
| [Elencare le regole del firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Server |Visualizza le regole del firewall IP a livello di server correnti |
| [Creare o aggiornare la regola del firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Server |Crea o aggiorna regole del firewall IP a livello di server |
| [Eliminare la regola del firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Server |Rimuove regole del firewall IP a livello di server |
| [Ottenere le regole del firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Server | Ottiene regole del firewall IP a livello di server |

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Regole del firewall IP a livello di server e a livello di database a confronto

D: Gli utenti di un database devono essere completamente isolati da un altro database?
In caso affermativo, concedere l'accesso tramite le regole del firewall IP a livello di database. Per evitare di usare le regole del firewall IP a livello di server, consentendo l'accesso attraverso il firewall a tutti i database, ridurre la profondità delle difese.

D: Gli utenti degli indirizzi IP devono avere accesso a tutti i database?
Usare le regole del firewall IP a livello di server per ridurre il numero di volte in cui è necessario configurare le regole del firewall IP.

D: La persona o il team che configura le regole del firewall IP ha accesso solo tramite il portale di Azure, PowerShell o l'API REST?
È necessario usare le regole del firewall IP a livello di server. Le regole del firewall IP a livello di database possono essere configurate solo con Transact-SQL.  

D: Alla persona o al team che configura le regole del firewall IP è vietato avere autorizzazioni elevate a livello di database?
Usare regole del firewall IP a livello di server. Per configurare regole del firewall IP a livello di database tramite Transact-SQL, è necessaria almeno l'autorizzazione `CONTROL DATABASE` a livello di database.  

D: La persona o il team che configura o controlla le regole del firewall IP gestisce centralmente le regole del firewall IP per molti database (forse centinaia)?
Questa selezione dipende dalle esigenze e degli ambienti. Le regole del firewall IP a livello di server potrebbero essere più facili da configurare, tuttavia lo scripting consente di configurare le regole a livello di database. E anche se si usano le regole del firewall IP a livello di server, potrebbe essere necessario controllare le regole del firewall IP a livello di database per verificare se gli utenti con l'autorizzazione `CONTROL` nel database hanno creato le regole del firewall IP a livello di database.

D: È possibile usare un insieme di regole del firewall IP a livello di server e di database?
Sì. Alcuni utenti, ad esempio gli amministratori, potrebbero aver bisogno di regole del firewall IP a livello di server. Altri utenti, ad esempio gli utenti di un'applicazione di database, potrebbero aver bisogno di regole del firewall IP a livello di database.

## <a name="troubleshooting-the-database-firewall"></a>Risoluzione dei problemi del firewall del database

Quando l'accesso al servizio di database SQL di Microsoft Azure non si comporta come previsto, considerare quanto segue:

- **Configurazione del firewall locale:**

  Per consentire al computer di accedere al database SQL di Azure, è necessario creare un'eccezione del firewall sul computer per la porta TCP 1433. Se si effettuano connessioni all'interno dei confini del cloud di Azure, può essere necessario aprire porte aggiuntive. Per altre informazioni, vedere la sezione **Esterno rispetto all'interno** di [Porte successive alla 1433 per ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).

- **NAT (Network Address Translation):**

  A causa di NAT, l'indirizzo IP usato dal computer per connettersi al database SQL di Azure potrebbe essere diverso da quello indicato nelle impostazioni di configurazione IP del computer. Per visualizzare l'indirizzo IP usato dal computer per connettersi ad Azure, accedere al portale e passare alla scheda **Configura** del server che ospita il database. Nella sezione **Indirizzi IP consentiti** viene visualizzato **lndirizzo IP client corrente**. Fare clic su **Aggiungi** in **Indirizzi IP consentiti** per consentire al computer di accedere al server.

- **Le modifiche all'elenco degli indirizzi consentiti non sono state ancora applicate:**

  Per rendere effettive le modifiche apportate alla configurazione del firewall del database SQL di Azure possono essere necessari fino a cinque minuti.

- **L'accesso non è autorizzato oppure è stata usata una password errata:**

  Quando un account di accesso non dispone di autorizzazioni per il server di database SQL di Azure o la password usata non è corretta, la connessione al server di database SQL di Azure viene negata. La creazione di un'impostazione del firewall fornisce ai client soltanto un'opportunità di connessione al server; ogni client deve fornire le credenziali di sicurezza necessarie. Per altre informazioni sulla preparazione degli account di accesso, vedere [Gestione di database, account di accesso e utenti nel database SQL di Azure](sql-database-manage-logins.md).

- **Indirizzo IP dinamico:**

  Se si dispone di una connessione Internet con indirizzi IP dinamici e si verificano problemi di comunicazione attraverso il firewall, è possibile provare una delle soluzioni seguenti:
  
  - Chiedere al Provider di servizi Internet (ISP) per l'intervallo di indirizzi IP assegnato ai computer client che accedono al server di Database SQL di Azure e quindi aggiungere l'intervallo di indirizzi IP come una regola del firewall IP.
  - Ottenere indirizzi IP statici per i computer client, quindi aggiungere gli indirizzi IP come regole del firewall IP.

## <a name="next-steps"></a>Passaggi successivi

- Verificare che l'ambiente di rete aziendali consenta le comunicazioni in ingresso dagli intervalli di indirizzi IP di calcolo (inclusi gli intervalli SQL) usate dai Data Center di Azure di Microsoft. Potrebbe essere necessario all'elenco elementi consentiti questi indirizzi IP, vedere [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653)  
- Per una guida di avvio rapido sulla creazione di un database e di una regola del firewall IP a livello di server, vedere [Creare un database SQL di Azure nel portale di Azure](sql-database-single-database-get-started.md).
- Per informazioni sulla connessione a un database SQL di Azure da applicazioni open source o di terze parti, vedere [Esempi di codice di avvio rapido del client per il database SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Per altre informazioni sulle porte aggiuntive che può essere necessario aprire, vedere la sezione **Esterno rispetto all'interno** di [Porte successive alla 1433 per ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md)
- Per una panoramica della sicurezza del Database SQL di Azure vedere [Protezione del Database SQL](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
