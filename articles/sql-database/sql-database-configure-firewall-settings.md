---
title: Configurare una regola firewall a livello di server per il database SQL di Azure | Microsoft Docs
description: Come configurare il firewall per gli indirizzi IP che accedono a database SQL di Azure.
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: c3b206b5-af6e-41af-8306-db12ecfc1b5d
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/28/2016
ms.author: rickbyh;carlrab
translationtype: Human Translation
ms.sourcegitcommit: ad72adf626fb5f59f239f57ff0158db101017a1b
ms.openlocfilehash: 7925663bd4233e368f54b38c94888ac1b0f5dcab


---
# <a name="configure-an-azure-sql-database-server-level-firewall-rule-using-the-azure-portal"></a>Configurare una regola firewall a livello di server per il database SQL di Azure tramite il portale di Azure
> [!div class="op_single_selector"]
> * [Panoramica](sql-database-firewall-configure.md)
> * [Portale di Azure](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [API REST](sql-database-configure-firewall-settings-rest.md)
> 
> 

Il server di Azure SQL utilizza le regole del firewall per consentire le connessioni a server e database. È possibile definire le impostazioni del firewall a livello di server e a livello di database per un database master o utente nel server logico del server di Azure SQL per consentire l'accesso al database in modo selettivo. Questo argomento descrive le regole del firewall a livello di server.

> [!IMPORTANT]
> Per consentire alle applicazioni da Azure di stabilire la connessione al server di Azure SQL, è necessario abilitare le connessioni da Azure. Per comprendere il funzionamento delle regole del firewall, vedere [Come configurare un firewall del database SQL di Azure\- panoramica](sql-database-firewall-configure.md). Se si apportano le connessioni all'interno del cloud di Azure, è necessario aprire alcune porte TCP aggiuntive. Per ulteriori informazioni, vedere la sezione **V12 del database SQL: all'esterno rispetto all'interno** di [porte 1433 per 4.5 ADO.NET e SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md)
> 
> 

**Suggerimento:** usare le regole del firewall a livello di server per gli amministratori e quando sono disponibili molti database che presentano gli stessi requisiti di accesso e non si vuole dedicare tempo alla configurazione di ogni singolo database. È consigliabile usare le regole del firewall a livello di database quando è possibile, allo scopo di migliorare la sicurezza e la portabilità del database.

[!INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gestione delle regole del firewall a livello di server esistenti tramite il portale di Azure
Ripetere i passaggi per gestire le regole del firewall a livello di server.

* Per aggiungere il computer corrente, fare clic su Aggiungi IP client.
* Per aggiungere ulteriori indirizzi IP, digitare nome della regola, indirizzo IP iniziale e indirizzo IP finale.
* Per modificare una regola esistente, fare clic su uno dei campi nella regola e modificare.
* Per eliminare una regola esistente, passare il mouse sulla regola fino a quando non viene visualizzata la X alla fine della riga. Fare clic su X per rimuovere la regola.

È consigliabile fare clic su **Salva** per salvare le modifiche.

## <a name="next-steps"></a>Passaggi successivi
L'articolo [Configurare le regole firewall a livello di server e di database per il database SQL di Azure tramite T-SQL](sql-database-configure-firewall-settings-tsql.md)illustra come usare Transact-SQL per creare regole firewall a livello di server e di database. 

Per informazioni su come creare regole firewall a livello di server con altri metodi, vedere: 

* [Configurare le regole firewall a livello di server per il database SQL di Azure con PowerShell](sql-database-configure-firewall-settings-powershell.md)
* [Configurare regole firewall a livello di server del database SQL di Azure tramite l'API REST](sql-database-configure-firewall-settings-rest.md)

Per un'esercitazione sulla creazione di un database, vedere [Creare un database SQL in pochi minuti usando il portale di Azure](sql-database-get-started.md).   

Per informazioni su come creare utenti aggiuntivi che possano connettersi ai database, vedere [Autenticazione e autorizzazione per database SQL: concessione dell'accesso](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Risorse aggiuntive
* [Protezione del database](sql-database-security.md)   
* [Centro sicurezza per il motore di Database di SQL Server e il Database SQL di Azure](https://msdn.microsoft.com/library/bb510589)   






<!--HONumber=Nov16_HO5-->


