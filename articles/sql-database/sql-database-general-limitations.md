<properties 
   pageTitle="Limitazioni e linee guida generali per il database SQL di Azure"
   description="Questa pagina descrive alcune limitazioni generali per il database SQL di Azure, oltre agli ambiti dell'interoperabilità e del supporto."
   services="sql-database"
   documentationCenter="na"
   authors="rothja"
   manager="jeffreyg"
   editor="monicar" />
<tags 
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="11/16/2015"
   ms.author="jroth" />

# Limitazioni e linee guida generali per il database SQL di Azure

Questo argomento illustra le limitazioni e le linee guida generali per il database SQL di Azure. Per informazioni complete su quote, gestione delle risorse e supporto, vedere le [risorse aggiuntive](#additional-guidelines) alla fine di questo argomento.

## Connettività

 - L'autenticazione Windows non è supportata. Vedere [Gestione di database e account di accesso in database SQL di Azure](sql-database-manage-logins.md). 

 - Il database SQL di Microsoft Azure supporta la versione client 7.3 o successiva del protocollo TDS (Tabular Data Stream).

 - Sono consentite solo connessioni TCP/IP.

 - SQL Server Browser di SQL Server 2008 non è supportato perché il database SQL di Microsoft Azure non ha porte dinamiche, ma solo la porta 1433.

## SQL Server Agent/Processi

Il database SQL di Microsoft Azure non supporta SQL Server Agent o i processi. È possibile, tuttavia, eseguire SQL Server Agent su SQL Server locale e connettersi al database SQL di Microsoft Azure.

## Transazioni

Il database SQL di Azure non supporta le transazioni distribuite, che hanno effetto su diverse risorse. Per altre informazioni, vedere [Transazioni distribuite (ADO.NET)](https://msdn.microsoft.com/library/ms254973.aspx). È possibile che il database SQL non conservi i valori di timestamp non sottoposti a commit del database corrente (DBTS) attraverso i failover.

> [AZURE.NOTE]In alcune situazioni, una transazione può essere automaticamente alzata al livello di transazione distribuita. Per altre informazioni, vedere [Integrazione di System.Transactions con SQL Server](https://msdn.microsoft.com/library/ms172070.aspx).

## Supporto delle regole di confronto di SQL Server

La regola di confronto del database predefinita usata dal database SQL di Microsoft Azure è **SQL\_LATIN1\_GENERAL\_CP1\_CI\_AS**, dove **LATIN1\_GENERAL** è l'inglese (Stati Uniti), **CP1** è la tabella codici 1252, **CI** indica l'assenza di distinzione tra maiuscole e minuscole e **AS** indica la distinzione tra caratteri accentati e non accentati. È possibile modificare le regole di confronto per i database V12 tramite Transact-SQL. Per altre informazioni su come impostare le regole di confronto, vedere [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## Requisiti di denominazione

Alcuni nomi utente non sono consentiti per motivi di sicurezza. Non è possibile usare i nomi seguenti:

 - **admin** 
 - **administrator** 
 - **guest** 
 - **root** 
 - **sa** 

I nomi di tutti i nuovi oggetti devono essere conformi alle regole di SQL Server per gli identificatori. Per altre informazioni, vedere [Identificatori](https://msdn.microsoft.com/library/ms175874.aspx).

I nomi utente e di accesso non possono nemmeno contenere il carattere \\ (l'autenticazione di Windows non è supportata).

## Linee guida aggiuntive

- Oltre alle limitazioni generali illustrate in questo articolo, il database SQL prevede limitazioni e quote di risorse specifiche in base al **livello di servizio**. Per una panoramica dei livelli di servizio, vedere [Livelli di servizio del Database SQL](sql-database-service-tiers.md).

- Per altri limiti del database SQL, vedere [Limiti delle risorse del database SQL di Azure](sql-database-resource-limits.md).

- Per le linee guida relative alla sicurezza, vedere [Linee guida e limitazioni per il database SQL di Azure](sql-database-security-guidelines.md).

- Un altro ambito correlato è quello della compatibilità tra il database SQL di Azure e le versioni locali di SQL Server, ad esempio SQL Server 2014. La più recente versione V12 del database SQL di Azure ha apportato numerosi miglioramenti in quest'ambito. Per altri dettagli, vedere [Novità della versione 12 del database SQL](sql-database-v12-whats-new.md).

- Per informazioni sulla disponibilità di driver e sul supporto per il database SQL, vedere [Raccolte di connessioni per database SQL e SQL Server](sql-database-libraries.md).

<!---HONumber=Nov15_HO4-->