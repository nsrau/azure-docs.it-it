---
title: 'T-SQL: regole del firewall per il database SQL di Azure | Documentazione Microsoft'
description: Come configurare le regole del firewall a livello di server e di database per gli indirizzi IP che accedono a database SQL di Azure usando Transact-SQL.
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: 71e692a1-5e2f-4a18-a6d6-527b849cf68e
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/07/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 84efba61fbb0248edef3036de702e3a1aa8ae903
ms.lasthandoff: 02/17/2017


---
# <a name="configure-azure-sql-database-server-level-and-database-level-firewall-rules-using-t-sql"></a>Configurare le regole firewall a livello di server e di database per il database SQL di Azure tramite T-SQL

Il database SQL di Microsoft Azure utilizza le regole del firewall per consentire le connessioni a server e database. È possibile definire le impostazioni del firewall a livello di server e a livello di database per un database master o utente nel server del database SQL di Azure per consentire l'accesso al database in modo selettivo.

> [!IMPORTANT]
> Per consentire alle applicazioni da Azure di stabilire la connessione al server di database, è necessario abilitare le connessioni da Azure. Per ulteriori informazioni sulle regole del firewall e l'abilitazione delle connessioni da Azure, vedere [Firewall di database SQL di Azure](sql-database-firewall-configure.md). Se si apportano le connessioni all'interno del cloud di Azure, è necessario aprire alcune porte TCP aggiuntive. Per ulteriori informazioni, vedere la sezione **V12 del database SQL: all'esterno rispetto all'interno** di [porte 1433 per 4.5 ADO.NET e SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md)
> 
> 

## <a name="server-level-firewall-rules"></a>Regole del firewall a livello di server
Solo l'account di accesso dell'amministratore del server Azure SQL o l'amministratore di Azure Active Directory possono creare una regola firewall a livello di server con Transact-SQL.

1. Avviare una finestra di query e connettersi al database master virtuale con SQL Server Management Studio.
2. Le regole del firewall a livello di server possono essere selezionate, create, aggiornate o eliminate all'interno della finestra di query.
3. Per creare o aggiornare le regole del firewall a livello di server, eseguire la stored procedure `sp_set_firewall_rule` . Nell'esempio seguente viene abilitato un intervallo di indirizzi IP nel server di Contoso.<br/>Iniziare verificando quali regole esistono già.
   
        SELECT * FROM sys.firewall_rules ORDER BY name;
   
    Successivamente, aggiungere una regola del firewall.
   
        EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
            @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
   
    Per eliminare una regola del firewall a livello di server, eseguire la stored procedure sp_delete_firewall_rule. Nell'esempio seguente viene eliminata la regola denominata ContosoFirewallRule.
   
        EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
   
   Per altre informazioni su queste stored procedure, vedere [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) e [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx).

## <a name="database-level-firewall-rules"></a>Regole del firewall a livello di database
Solo un utente del database con l'autorizzazione **CONTROL** sul database, ad esempio il proprietario del database, può creare una regola del firewall a livello di database.

1. Dopo aver creato un firewall a livello di server per l'indirizzo IP, avviare una finestra di query tramite il portale di Azure o SQL Server Management Studio.
2. Connettersi al database per cui si desidera creare una regola del firewall a livello di database.
   
    Per creare una nuova regola del firewall a livello di database o aggiornarne una esistente, eseguire la stored procedure `sp_set_database_firewall_rule` . Nell'esempio seguente viene creata una nuova regola del firewall denominata ContosoFirewallRule.
   
        EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
            @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
   
    Per eliminare una regola del firewall a livello di database, eseguire la stored procedure `sp_delete_database_firewall_rule` . Nell'esempio seguente viene eliminata la regola denominata ContosoFirewallRule.
   `
   
        EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

Per altre informazioni su queste stored procedure, vedere [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) e [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx).

> [!NOTE]
> Per un'esercitazione sull'uso dei firewall a livello di database, vedere l'articolo sull'[autenticazione e l'autorizzazione di SQL](sql-database-control-access-sql-authentication-get-started.md).
>


## <a name="next-steps"></a>Passaggi successivi
Per articoli su come creare regole firewall a livello di server con altri metodi, vedere: 

* [Configurare regole firewall a livello di server del database SQL di Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md)
* [Configurare le regole firewall a livello di server per il database SQL di Azure con PowerShell](sql-database-configure-firewall-settings-powershell.md)
* [Configurare regole firewall a livello di server del database SQL di Azure tramite l'API REST](sql-database-configure-firewall-settings-rest.md)

Per un'esercitazione sulla creazione di un database, vedere [Primo database SQL di Azure](sql-database-get-started.md).
Per informazioni sulla connessione a un database SQL di Azure da applicazioni open source o di terze parti, vedere [Esempi di codice di avvio rapido del client per il database SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Per informazioni su come passare ai database, vedere [Gestire l'accesso al database e la sicurezza degli account di accesso](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Risorse aggiuntive
* [Protezione del database](sql-database-security-overview.md)
* [Centro sicurezza per il motore di Database di SQL Server e il Database SQL di Azure](https://msdn.microsoft.com/library/bb510589)


