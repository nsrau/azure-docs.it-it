---
title: Regole del firewall per il database SQL di Azure | Microsoft Docs
description: Informazioni su come configurare un firewall del database SQL con regole del firewall a livello di server e a livello di database per la gestione dell'accesso.
keywords: firewall del database
services: sql-database
documentationcenter: 
author: BYHAM
manager: craigg
editor: cgronlun
tags: 
ms.assetid: ac57f84c-35c3-4975-9903-241c8059011e
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/15/2017
ms.author: rickbyh
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 445689cb067d33b0da45d63730e5e755bc799909
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="azure-sql-database-server-level-and-database-level-firewall-rules"></a>Regole firewall a livello di server e di database per il database SQL di Azure 

Il database SQL di Microsoft Azure fornisce un servizio di database relazionale per Azure e altre applicazioni basate su Internet. Per proteggere i dati, il firewall impedisce qualsiasi accesso al server di database finché non vengono specificati i computer autorizzati. Il firewall concede l'accesso ai database in base all'indirizzo IP di origine di ogni richiesta.

#### <a name="virtual-netowrk-rules-as-alternatives-to-ip-rules"></a>Regole della rete virtuale come alternative alle regole IP

Oltre alle regole IP, il firewall gestisce anche le *regole della rete virtuale*. Le regole della rete virtuale si basano sugli endpoint di servizio di Rete virtuale. In alcuni casi le regole della rete virtuale potrebbero essere preferibili alle regole IP. Per altre informazioni, vedere [Endpoint del servizio Rete virtuale e regole per il database SQL di Azure](sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="overview"></a>Panoramica

Inizialmente tutti gli accessi Transact-SQL al server di database SQL di Azure sono bloccati dal firewall. Per iniziare a usare il server SQL di Azure, è necessario specificare una o più regole del firewall a livello di server che consentano di accedere al server SQL di Azure. Usare le regole del firewall per specificare quali intervalli di indirizzi IP da Internet sono consentiti e se le applicazioni Azure possono provare a connettersi al server SQL di Azure.

Per concedere in modo selettivo l'accesso solo a uno dei database in SQL Server di Azure, è necessario creare una regola a livello di database per il database richiesto. Specificare un intervallo di indirizzi IP per la regola firewall del database che sia successivo all'indirizzo IP specificato nella regola firewall a livello di server e assicurarsi che l'indirizzo IP del client ricada nell'intervallo specificato nella regola a livello di database.

I tentativi di connessione da Internet e Azure devono prima superare il firewall per poter raggiungere il server SQL di Azure o un database SQL, come illustrato nel diagramma seguente:

   ![Diagramma che descrive la configurazione del firewall.][1]

* **Regole del firewall a livello di server:** queste regole consentono ai client di accedere all'intero server SQL di Azure, ovvero tutti i database all'interno dello stesso server logico. Queste regole sono archiviate nel database **master** . Le regole del firewall a livello di server possono essere configurate usando il portale o istruzioni Transact-SQL. Per creare regole del firewall a livello di server usando il portale di Azure o PowerShell, è necessario essere il proprietario o un collaboratore della sottoscrizione. Per creare una regola del firewall a livello di server usando Transact-SQL, è necessario connettersi all'istanza di database SQL come account di accesso entità di livello server o amministratore di Azure Active Directory, il che implica che una regola del firewall a livello di server debba essere prima creata da un utente con autorizzazioni a livello di Azure.
* **Regole del firewall a livello di database:**. Queste regole consentono ai client di accedere a determinati database (sicuri) nello stesso server logico. È possibile creare queste regole per ogni database (incluso il database **master**) ed esse vengono archiviate nei singoli database. Le regole del firewall a livello di database per i database utente e master possono essere create e gestite solo usando le istruzioni Transact-SQL e solo dopo aver configurato il primo firewall a livello di server. Se nella regola del firewall a livello di database si specifica un intervallo di indirizzi IP che non rientra nell'intervallo specificato nella regola del firewall a livello di server, solo i client che dispongono di indirizzi IP compresi nell'intervallo a livello di database possono accedere al database. Per un database è possibile avere un massimo di 128 regole del firewall a livello di database. Per altre informazioni su come configurare le regole del firewall a livello di database, vedere l'esempio presente in questo articolo e vedere [sp_set_database_firewall_rule (Database SQL di Azure)](https://msdn.microsoft.com/library/dn270010.aspx).

**Indicazione:** è consigliabile usare le regole del firewall a livello di database quando è possibile, allo scopo di migliorare la sicurezza e la portabilità del database. Usare le regole del firewall a livello di server per gli amministratori e quando sono disponibili molti database che presentano gli stessi requisiti di accesso e non si vuole dedicare tempo alla configurazione di ogni singolo database.

> [!Note]
> Per informazioni sui database portabili per la continuità aziendale, vedere nel contesto della continuità aziendale, vedere i [requisiti di autenticazione per il ripristino di emergenza](sql-database-geo-replication-security-config.md).
>

### <a name="connecting-from-the-internet"></a>Connessione da Internet

Quando un computer cerca di connettersi al server di database da Internet, il firewall confronta prima l'indirizzo IP di origine della richiesta con le regole firewall a livello di database, per il database richiesto dalla connessione:

* Se l'indirizzo IP della richiesta rientra in uno degli intervalli specificati nelle regole firewall a livello di database, viene consentita la connessione al database SQL che contiene le regole.
* Se l'indirizzo IP della richiesta non rientra in uno degli intervalli specificati nelle regole firewall a livello di database, vengono controllate le regole firewall a livello di server. Se l'indirizzo IP della richiesta rientra in uno degli intervalli specificati nelle regole firewall a livello di server, viene consentita la connessione. Le regole firewall a livello di server si applicano a tutti i database SQL nel server di Azure SQL.  
* Se l'indirizzo IP della richiesta non rientra negli intervalli specificati in una delle regole firewall a livello di database o di server, la richiesta di connessione ha esito negativo.

> [!NOTE]
> Per accedere al database SQL di Azure dal computer locale, verificare che il firewall in rete e nel computer locale consenta le comunicazioni in uscita sulla porta TCP 1433.
> 

### <a name="connecting-from-azure"></a>Connessione da Azure
Per consentire alle applicazioni da Azure di stabilire la connessione al server di Azure SQL, è necessario abilitare le connessioni da Azure. Quando si tenta di connettersi al server di database da un'applicazione di Azure, il firewall verifica che le connessioni da Azure siano consentite. Un'impostazione del firewall con indirizzo iniziale e finale uguale a 0.0.0.0 indica che queste connessioni sono consentite. Se il tentativo di connessione non è consentito, la richiesta non raggiungerà il server di Database SQL di Azure.

> [!IMPORTANT]
> Questa opzione permette di configurare il firewall in maniera tale da consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, assicurarsi che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.
> 

## <a name="creating-and-managing-firewall-rules"></a>Creazione e gestione di regole del firewall
La prima impostazione del firewall a livello di server può essere creata usando il [Portale di Azure](https://portal.azure.com/) o a livello di programmazione tramite [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql), [l'interfaccia della riga di comando di Azure](/cli/azure/sql/server/firewall-rule#create), o l'[API REST](https://docs.microsoft.com/rest/api/sql/firewallrules). Le regole del firewall a livello di server successive possono essere create e gestite utilizzando questi metodi e tramite Transact-SQL. 

> [!IMPORTANT]
> Le regole del firewall a livello di database possono essere create e gestite solo con Transact-SQL. 
>

Per migliorare le prestazioni, le regole del firewall a livello di server vengono temporaneamente memorizzate nella cache a livello di database. Per aggiornare la cache, vedere [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx). 

> [!TIP]
> È possibile usare il [servizio di controllo del database SQL](sql-database-auditing.md) per controllare le modifiche del firewall a livello di server e di database.
>

## <a name="manage-firewall-rules-using-the-azure-portal"></a>Gestire le regole del firewall con il portale di Azure

Per impostare una regola del firewall a livello di server nel portale di Azure, è possibile passare alla pagina di Panoramica per il database SQL di Azure o alla pagina di Panoramica per il server logico del Database di Azure.

> [!TIP]
> Per un'esercitazione, vedere [Creare un database tramite il portale di Azure](sql-database-get-started-portal.md).
>

**Dalla pagina di Panoramica del database**

1. Per impostare una regola del firewall a livello di server dalla pagina di panoramica del database, fare clic su **Imposta firewall server** sulla barra degli strumenti, come illustrato nella figura seguente: la pagina **Impostazioni del Firewall** verrà visualizzata per il server del Database SQL.

      ![Regola del firewall del server](./media/sql-database-get-started-portal/server-firewall-rule.png) 

2. Fare clic su **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP del computer attualmente in uso e quindi fare clic su **Salva**. Una regola del firewall a livello di server viene creata per l'indirizzo IP corrente.

      ![Impostare la regola del firewall del server](./media/sql-database-get-started-portal/server-firewall-rule-set.png) 

**Dalla pagina della panoramica del server**

Si apre la pagina di panoramica per il server che visualizza il nome completo del server, ad esempio **mynewserver20170403.database.windows.net**, e offre altre opzioni di configurazione.

1. Per impostare una regola a livello di server dalla pagina di panoramica del server, fare clic su **Firewall** nel menu a sinistra sotto Impostazioni: 

2. Fare clic su **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP del computer attualmente in uso e quindi fare clic su **Salva**. Una regola del firewall a livello di server viene creata per l'indirizzo IP corrente.

## <a name="manage-firewall-rules-using-transact-sql"></a>Gestire le regole del firewall con Transact-SQL
| Vista del catalogo o una Stored Procedure | Level | Descrizione |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Server |Visualizza le regole del firewall a livello di server correnti |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Server |Crea o aggiorna regole del firewall a livello di server |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Server |Rimuove regole del firewall a livello di server |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Database |Visualizza le regole del firewall a livello di database correnti |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Database |Crea o aggiorna regole del firewall a livello di database |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Database |Rimuove le regole del firewall a livello di database |


Negli esempi seguenti si rivedono le regole esistenti, si attiva un intervallo di indirizzi IP nel server di Contoso e si elimina una regola del firewall:
   
```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```
  
Successivamente, aggiungere una regola del firewall.
   
```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Per eliminare una regola del firewall a livello di server, eseguire la stored procedure sp_delete_firewall_rule. Nell'esempio seguente viene eliminata la regola denominata ContosoFirewallRule:
   
```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```   

## <a name="manage-firewall-rules-using-azure-powershell"></a>Gestire le regole del firewall con Azure PowerShell
| Cmdlet | Level | Descrizione |
| --- | --- | --- |
| [Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule) |Server |Restituisce le regole del firewall a livello di server correnti |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) |Server |Crea una nuova regola del firewall a livello di server |
| [Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule) |Server |Aggiorna le proprietà di una regola del firewall a livello di server esistente |
| [Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule) |Server |Rimuove regole del firewall a livello di server |


Nell'esempio seguente si imposta una regola del firewall a livello di server con PowerShell:

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> Per esempi di PowerShell nel contesto di una Guida introduttiva, vedere [Creare un singolo database SQL di Azure usando PowerShell](sql-database-get-started-powershell.md) e [Creare un singolo database SQL e configurare una regola del firewall usando PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).
>

## <a name="manage-firewall-rules-using-azure-cli"></a>Gestire le regole del firewall con l'interfaccia della riga di comando di Azure
| Cmdlet | Level | Descrizione |
| --- | --- | --- |
| [az sql server firewall create](/cli/azure/sql/server/firewall-rule#create) | Consente di creare una regola del firewall per consentire l'accesso a tutti i database SQL presenti sul server dall'intervallo di indirizzi IP immesso.|
| [az sql server firewall delete](/cli/azure/sql/server/firewall-rule#delete)| Elimina una regola del firewall.|
| [az sql server firewall list](/cli/azure/sql/server/firewall-rule#list)| Elenca le regole del firewall.|
| [az sql server firewall rule show](/cli/azure/sql/server/firewall-rule#show)| Visualizza i dettagli di una regola del firewall.|
| [ax sql server firewall rule update](/cli/azure/sql/server/firewall-rule#update)| Aggiorna una regola del firewall.

Nell'esempio seguente si imposta una regola del firewall a livello di server con l'interfaccia della riga di comando di Azure: 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Per un esempio di interfaccia della riga di comando di Azure nel contesto di una Guida introduttiva, vedere [Creare un singolo database SQL di Azure usando l'interfaccia della riga di comando di Azure](sql-database-get-started-cli.md) e [Creare un singolo database SQL e configurare una regola del firewall tramite l'interfaccia della riga di comando di Azure](scripts/sql-database-create-and-configure-database-cli.md)
>

## <a name="manage-firewall-rules-using-rest-api"></a>Gestire le regole del firewall con l'API REST
| API | Level | Descrizione |
| --- | --- | --- |
| [Elencare le regole del firewall](https://docs.microsoft.com/rest/api/sql/FirewallRules/ListByServer) |Server |Visualizza le regole del firewall a livello di server correnti |
| [Creare o aggiornare la regola del firewall](https://docs.microsoft.com/rest/api/sql/FirewallRules/CreateOrUpdate) |Server |Crea o aggiorna regole del firewall a livello di server |
| [Eliminare la regola del firewall](https://docs.microsoft.com/rest/api/sql/FirewallRules/Delete) |Server |Rimuove regole del firewall a livello di server |

## <a name="server-level-firewall-rule-versus-a-database-level-firewall-rule"></a>Regola del firewall a livello di server contro una regola del firewall a livello di database
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

* **Configurazione del firewall locale:** prima che il computer possa accedere al database SQL di Azure, è necessario creare un'eccezione del firewall sul computer per la porta TCP 1433. Se si effettuano connessioni all'interno dei confini del cloud di Azure, può essere necessario aprire porte aggiuntive. Per altre informazioni, vedere la sezione **Esterno rispetto all'interno** di [Porte successive alla 1433 per ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
* **Rete NAT (Network address translation):** a causa di NAT, l'indirizzo IP utilizzato dal computer per connettersi al database SQL di Azure potrebbe essere diverso da quello indicato nelle impostazioni di configurazione IP del computer. Per visualizzare l'indirizzo IP usato dal computer per connettersi ad Azure, accedere al portale e passare alla scheda **Configura** del server che ospita il database. Nella sezione **Indirizzi IP consentiti** viene visualizzato **lndirizzo IP client corrente**. Fare clic su **Aggiungi** in **Indirizzi IP consentiti** per consentire al computer di accedere al server.
* **Le modifiche all'elenco di consentiti non sono state ancora applicate:** per rendere effettive le modifiche apportate alla configurazione del firewall del database SQL di Azure possono essere necessari fino a cinque minuti.
* **L'accesso non è autorizzato oppure è stata usata una password errata:** quando un account di accesso non dispone di autorizzazioni per il server di database SQL di Azure o la password usata non è corretta, la connessione al server di database SQL di Azure verrà negata. La creazione di un'impostazione del firewall fornisce ai client soltanto un'opportunità di connessione al server; ogni client deve fornire le credenziali di sicurezza necessarie. Per ulteriori informazioni sulla preparazione degli account di accesso, vedere Gestione di database, account di accesso e utenti nel database SQL di Azure.
* **Indirizzo IP dinamico:** se si dispone di una connessione Internet con indirizzi IP dinamici e si verificano problemi di comunicazione attraverso il firewall, è possibile provare una delle seguenti soluzioni:
  
  * Chiedere al provider di servizi Internet (ISP) l'intervallo di indirizzi IP assegnato ai computer client che accedono al server di database SQL di Azure, quindi aggiungere l'intervallo di indirizzi IP come una regola del firewall.
  * Ottenere indirizzi IP statici per i computer client, quindi aggiungere gli indirizzi IP come regole del firewall.

## <a name="next-steps"></a>Passaggi successivi

- Per una Guida introduttiva alla creazione di un database e di una regola del firewall a livello di server, vedere [Creare un database SQL di Azure nel portale di Azure](sql-database-get-started-portal.md).
- Per informazioni sulla connessione a un database SQL di Azure da applicazioni open source o di terze parti, vedere [Esempi di codice di avvio rapido del client per il database SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Per informazioni sulle porte aggiuntive che si possono dover aprire vedere la sezione **Esterno rispetto all'interno** di [Porte successive alla 1433 per ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Per una panoramica della sicurezza del Database SQL di Azure vedere [Protezione del Database SQL](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
