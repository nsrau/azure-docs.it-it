---
title: 'REST API: regole firewall a livello di server del database SQL di Azure | Documentazione Microsoft'
description: Come configurare le regole del firewall a livello di server per gli indirizzi IP che accedono a database SQL di Azure usando l&quot;API REST.
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: fc874f3c-c623-4924-8cb7-32a8c31e666c
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: e164e1da7ec5f2da157900c86b3313fff1affed9
ms.lasthandoff: 02/17/2017


---
# <a name="configure-azure-sql-database-server-level-firewall-rules-using-the-rest-api"></a>Configurare regole firewall a livello di server del database SQL di Azure tramite l'API REST

Il database SQL di Microsoft Azure utilizza le regole del firewall per consentire le connessioni a server e database. È possibile definire le impostazioni del firewall a livello di server e a livello di database per un database master o utente nel server del database SQL di Azure per consentire l'accesso al database in modo selettivo.

> [!IMPORTANT]
> Per consentire alle applicazioni da Azure di stabilire la connessione al server di database, è necessario abilitare le connessioni da Azure. Per ulteriori informazioni sulle regole del firewall e l'abilitazione delle connessioni da Azure, vedere [Firewall di database SQL di Azure](sql-database-firewall-configure.md). Se si apportano le connessioni all'interno del cloud di Azure, è necessario aprire alcune porte TCP aggiuntive. Per ulteriori informazioni, vedere la sezione **V12 del database SQL: all'esterno rispetto all'interno** di [porte 1433 per 4.5 ADO.NET e SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md)
> 
> 

## <a name="manage-server-level-firewall-rules-through-rest-api"></a>Gestione delle regole del firewall a livello di server tramite l'API REST
1. La gestione delle regole del firewall tramite l'API REST deve essere autenticata. Per informazioni, vedere [Guida per gli sviluppatori all'autorizzazione con l'API di Azure Resource Manager](../azure-resource-manager/resource-manager-api-authentication.md).
2. È possibile creare, aggiornare o eliminare regole a livello di server tramite l'API REST
   
    Per creare o aggiornare una regola del firewall a livello di server, eseguire il metodo PUT con le seguenti operazioni:
   
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}
   
    Corpo della richiesta
   
        {
         "properties": { 
            "startIpAddress": "{start-ip-address}", 
            "endIpAddress": "{end-ip-address}
            }
        } 

    Per rimuovere una regola del firewall a livello di server esistente, eseguire il metodo DELETE con le seguenti operazioni:

        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}


## <a name="manage-firewall-rules-using-the-rest-api"></a>Gestione delle regole del firewall tramite l'API REST
* [Creare o aggiornare la regola del firewall](https://msdn.microsoft.com/library/azure/mt445501.aspx)
* [Eliminare la regola del firewall](https://msdn.microsoft.com/library/azure/mt445502.aspx)
* [Recuperare la regola del firewall](https://msdn.microsoft.com/library/azure/mt445503.aspx)
* [Elencare tutte le regole del firewall](https://msdn.microsoft.com/library/azure/mt604478.aspx)

## <a name="next-steps"></a>Passaggi successivi
L'articolo [Configurare le regole firewall a livello di server e di database per il database SQL di Azure tramite T-SQL](sql-database-configure-firewall-settings-tsql.md) illustra come usare Transact-SQL per creare regole firewall a livello di server e di database. 

Per articoli su come creare regole firewall a livello di server con altri metodi, vedere: 

* [Configurare regole firewall a livello di server del database SQL di Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md)
* [Configurare le regole firewall a livello di server per il database SQL di Azure con PowerShell](sql-database-configure-firewall-settings-powershell.md)

Per un'esercitazione sulla creazione di un database, vedere [Primo database SQL di Azure](sql-database-get-started.md).
Per informazioni sulla connessione a un database SQL di Azure da applicazioni open source o di terze parti, vedere [Esempi di codice di avvio rapido del client per il database SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Per informazioni su come passare ai database, vedere [Gestire l'accesso al database e la sicurezza degli account di accesso](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Risorse aggiuntive
* [Protezione del database](sql-database-security-overview.md)
* [Centro sicurezza per il motore di Database di SQL Server e il Database SQL di Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->



