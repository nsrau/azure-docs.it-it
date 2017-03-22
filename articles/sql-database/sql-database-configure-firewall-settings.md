---
title: 'Portale di Azure: regole firewall a livello di server del database SQL di Azure | Documentazione Microsoft'
description: Come configurare le regole del firewall a livello di server per gli indirizzi IP che accedono a un server SQL Azure usando il portale di Azure.
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: c3b206b5-af6e-41af-8306-db12ecfc1b5d
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/21/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 1939b69ee806d9091a21f3b21a276175d5a19a62
ms.lasthandoff: 02/17/2017


---
# <a name="create-and-manage-azure-sql-database-server-level-firewall-rules-using-the-azure-portal"></a>Creare e gestire regole firewall a livello di server per il database SQL di Azure tramite il portale di Azure

Le regole del firewall a livello di server consentono agli amministratori di accedere a un server di database SQL da un indirizzo IP o da un intervallo di indirizzi IP specificati. È anche possibile usare le regole del firewall a livello di server per gli utenti e quando sono disponibili molti database che presentano gli stessi requisiti di accesso e non si vuole dedicare tempo alla configurazione di ogni singolo database. È consigliabile usare le regole del firewall a livello di database quando è possibile, allo scopo di migliorare la sicurezza e la portabilità del database. Per una panoramica dei firewall per i database SQL, vedere [Panoramica: Regole del firewall per il database SQL](sql-database-firewall-configure.md).

> [!Note]
> Per informazioni sui database portabili per la continuità aziendale, vedere nel contesto della continuità aziendale, vedere i [requisiti di autenticazione per il ripristino di emergenza](sql-database-geo-replication-security-config.md).
>

[!INCLUDE [Create SQL Database firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gestione delle regole del firewall a livello di server esistenti tramite il portale di Azure
Ripetere i passaggi per gestire le regole del firewall a livello di server.

* Per aggiungere il computer corrente, fare clic su Aggiungi IP client.
* Per aggiungere ulteriori indirizzi IP, digitare nome della regola, indirizzo IP iniziale e indirizzo IP finale.
* Per modificare una regola esistente, fare clic su uno dei campi nella regola e modificare.
* Per eliminare una regola esistente, passare il mouse sulla regola fino a quando non viene visualizzata la X alla fine della riga. Fare clic su X per rimuovere la regola.

È consigliabile fare clic su **Salva** per salvare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

- Per un'esercitazione sul provisioning e sulla connessione a un server con firewall a livello di server, vedere [Esercitazione: Effettuare il provisioning e accedere a un database SQL di Azure usando il portale di Azure e SQL Server Management Studio](sql-database-get-started.md).
- Per un'esercitazione con l'autenticazione di SQL Server e i firewall a livello di database, vedere [SQL authentication and authorization](sql-database-control-access-sql-authentication-get-started.md) (Autenticazione e autorizzazione di SQL).
- Per informazioni sulla connessione a un database SQL di Azure da applicazioni open source o di terze parti, vedere [Esempi di codice di avvio rapido del client per il database SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Per informazioni su come creare utenti aggiuntivi che possano connettersi ai database, vedere [Autenticazione e autorizzazione per database SQL: concessione dell'accesso](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Risorse aggiuntive
* [Protezione del database](sql-database-security-overview.md)   
* [Centro sicurezza per il motore di Database di SQL Server e il Database SQL di Azure](https://msdn.microsoft.com/library/bb510589)   




