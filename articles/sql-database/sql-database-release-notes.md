---
title: Note sulla versione del database SQL di Azure | Microsoft Docs
description: Informazioni sulle nuove funzionalità e miglioramenti nel servizio database SQL di Azure e nella documentazione del database SQL di Azure
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: ef19d22b5c47c51f5ee3f74a4d7ab06725f7ed41
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968324"
---
# <a name="sql-database-release-notes"></a>Note sulla versione del database SQL

Questo articolo elenca le funzionalità del database SQL attualmente disponibili in anteprima pubblica. Per aggiornamenti e miglioramenti del database SQL, vedere [aggiornamenti del servizio di database SQL](https://azure.microsoft.com/updates/?product=sql-database). Per gli aggiornamenti e i miglioramenti apportati ad altri servizi di Azure, vedere [aggiornamenti dei servizi](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Funzionalità disponibili in anteprima pubblica

### <a name="single-databasetabsingle-database"></a>[Database singolo](#tab/single-database)

| Funzionalità | Dettagli |
| ---| --- |
| Recupero accelerato del database con singoli database e pool elastici | Per informazioni, vedere [accelerazione del ripristino del database](sql-database-accelerated-database-recovery.md).|
|Conteggio approssimativo Distinct|Per informazioni, vedere [conteggio approssimativo Distinct](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Modalità batch su rowstore (con livello di compatibilità 150)|Per informazioni, vedere [modalità batch su rowstore](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Individuazione dati e classificazione  |Per informazioni, vedere [database SQL di Azure e SQL data warehouse individuazione dati & classificazione](sql-database-data-discovery-and-classification.md).|
| Processi di database elastico | Per informazioni, vedere [creare, configurare e gestire processi elastici](elastic-jobs-overview.md). |
| Query elastiche | Per informazioni, vedere [Panoramica delle query elastiche](sql-database-elastic-query-overview.md). |
| Transazioni elastiche | [Transazioni distribuite tra database cloud](sql-database-elastic-transactions-overview.md). |
|Feedback delle concessioni di memoria (modalità riga) (con livello di compatibilità 150)|Per informazioni, vedere [feedback delle concessioni di memoria (modalità riga)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Editor di query nel portale di Azure |Per informazioni, vedere [usare l'editor di query SQL di portale di Azure per connettersi ed eseguire query sui dati](sql-database-connect-query-portal.md).|
| R Services/Machine Learning con database singoli e pool elastici |Per informazioni, vedere [Machine Learning Services nel database SQL di Azure](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
| Ricreare database eliminati con istanze gestite |Per informazioni, vedere [ricreare database rimossi in Azure SQL istanza gestita](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| Replica con istanze gestite |Per informazioni, vedere [configurare la replica in un database dell'istanza gestita di database SQL di Azure](replication-with-sql-database-managed-instance.md).|
| Livello di elaborazione serverless | Per informazioni, vedere [database SQL senza server (anteprima)](sql-database-serverless.md).|
|Analitica SQL|Per informazioni, vedere [analisi SQL di Azure](../azure-monitor/insights/azure-sql.md).|
|Compilazione posticipata della variabile di tabella (con livello di compatibilità 150)|Per informazioni, vedere [compilazione posticipata della variabile di tabella](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| Rilevamento delle minacce con istanze gestite |Per informazioni, vedere [configurare il rilevamento delle minacce in istanza gestita di database SQL di Azure](sql-database-managed-instance-threat-detection.md).|
| Transparent Data Encryption (Transparent Data Encryption) con Bring Your Own Key (BYOK) con istanze gestite |Per informazioni, vedere [Transparent Data Encryption SQL di Azure con chiavi gestite dal cliente in Azure Key Vault: Supporto](transparent-data-encryption-byok-azure-sql.md)Bring your own key.|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[Istanza gestita](#tab/managed-instance)

| Funzionalità | Dettagli |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Pool di istanze</a> | Un modo pratico ed economicamente conveniente per eseguire la migrazione di istanze SQL più piccole al cloud. |
| <a href="https://aka.ms/managed-instance-tde-byok">Porta le tue chiavi di crittografia</a> | Eseguire la migrazione del database da locale che ha già abilitato Transparent Data Encryption (Transparent Data Encryption) con chiavi Transparent Transparent. |
| <a href="https://aka.ms/managed-instance-failover-groups">Gruppi di failover con distribuzione geografica</a> | Mantenere una copia dell'istanza di in un'altra area e assicurarsi che i dati saranno disponibili anche nello scenario di emergenza a livello di area. |
| <a href="https://aka.ms/managed-instance-aadlogins">Entità del server Azure AD a livello di istanza (account di accesso)</a> | Creare account di accesso a livello di server utilizzando l'istruzione <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">create login from External provider</a> . |
| [Replica transazionale](sql-database-managed-instance-transactional-replication.md) | Replicare le modifiche dalle tabelle in altri database posizionati in istanze gestite, database singoli o istanze di SQL Server o aggiornare le tabelle quando alcune righe vengono modificate in altre istanze gestite o SQL Server istanza. |
| &nbsp; |

---

## <a name="updates"></a>Aggiornamenti

Per un elenco degli aggiornamenti e dei miglioramenti del database SQL, vedere [aggiornamenti del servizio di database SQL](https://azure.microsoft.com/updates/?product=sql-database).

Per aggiornamenti e miglioramenti a tutti i servizi di Azure, vedere [aggiornamenti dei servizi](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Contribuisci al contenuto

Per contribuire alla documentazione del database SQL di Azure, vedere la guida per i [collaboratori di docs](https://docs.microsoft.com/contribute/).
