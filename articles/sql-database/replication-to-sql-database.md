---
title: Replica nel database SQL di Azure | Microsoft Docs
description: Informazioni sull'uso della replica di SQL Server con database SQL di Azure singoli e in pool elastici
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: eae7f97799e38d5a4b09e3f193a84b423dced780
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2018
ms.locfileid: "48869330"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>Replica in database SQL singoli e in pool

La replica di SQL Server può essere configurata in database singoli e in pool su un [ server logico](sql-database-logical-servers.md) in Database SQL di Azure.  

## <a name="supported-configurations"></a>**Configurazioni supportate:**
  
- Il server SQL può essere un'istanza di SQL Server in esecuzione in locale o un'istanza di SQL Server in esecuzione in una macchina virtuale di Azure nel cloud. Per altre informazioni, vedere [Panoramica di SQL Server in Macchine virtuali di Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/).  
- Il database SQL di Azure deve essere un sottoscrittore push di un server di pubblicazione SQL Server.  
- Il database di distribuzione e gli agenti di replica non possono essere collocati in un database SQL di Azure.  
- La replica snapshot e la replica transazionale unidirezionale sono supportate. La replica transazionale peer-to-peer e la replica di tipo merge non sono supportate.
- La replica è disponibile in anteprima pubblica in Istanza gestita di database SQL di Azure. L'istanza gestita può ospitare database di pubblicazione, di distribuzione e sottoscrittore. Per altre informazioni, vedere [Replica con Istanza gestita di database SQL](replication-with-sql-database-managed-instance.md).

## <a name="versions"></a>Versioni  

- I requisiti minimi per la versione del database di pubblicazione e del database di distribuzione sono i seguenti:  
- SQL Server 2017 (14.x)
- SQL Server 2016 (13.x)
- SQL Server 2014 (12.x) SP1 CU3
- SQL Server 2014 (12.x) RTM CU10
- SQL Server 2012 (11.x) SP2 CU8 o SP3
- Se si cerca di configurare la replica usando una versione meno recente, potrebbero essere generati gli errori MSSQL_REPL20084 (Il processo non è riuscito a connettersi al sottoscrittore) e MSSQL_REPL40532 (Impossibile aprire il server \<nome> richiesto dall'account di accesso. Accesso non riuscito).  
- Per usare tutte le funzionalità del database SQL di Azure, è necessario usare le versioni più recenti di [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools](https://docs.microsoft.com//sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017).  
  
## <a name="remarks"></a>Osservazioni

- La replica può essere configurata usando [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) o eseguendo istruzioni Transact-SQL sul database di pubblicazione. Non è possibile configurare la replica tramite il portale di Azure.  
- La replica può usare solo account di accesso per l'autenticazione di SQL Server per connettersi a un database SQL di Azure.
- Le tabelle replicate devono avere una chiave primaria.  
- È necessario avere una sottoscrizione di Azure.  
- Il sottoscrittore del database SQL di Azure può trovarsi in qualsiasi area.  
- Una singola pubblicazione in SQL Server può supportare sia sottoscrittori di database SQL di Azure sia sottoscrittori di SQL Server (in locale e in una macchina virtuale di Azure).  
- La gestione, il monitoraggio e la risoluzione dei problemi di replica devono essere eseguiti dall'istanza locale di SQL Server.  
- Sono supportate solo le sottoscrizioni push nel database SQL di Azure.  
- È supportato solo `@subscriber_type = 0` in **sp_addsubscription** per il database SQL.  
- Il database SQL di Azure non supporta la replica bidirezionale, immediata, aggiornabile o peer-to-peer.

## <a name="replication-architecture"></a>Architettura della replica  

![replica in un database sql](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Scenari  

### <a name="typical-replication-scenario"></a>Scenario di replica tipico  

1. Creare una pubblicazione di replica transazionale in un database di SQL Server locale.  
2. Nell'istanza di SQL Server locale usare la **Creazione guidata nuova sottoscrizione** o istruzioni Transact-SQL per creare una sottoscrizione al database SQL di Azure.  
3. Il set di dati iniziale è in genere uno snapshot creato dall'agente di snapshot e distribuito e applicato dall'agente di distribuzione. Il set di dati iniziale può anche essere fornito tramite un backup o altri mezzi, come SQL Server Integration Services.  

### <a name="data-migration-scenario"></a>Scenario di migrazione dei dati  

1. Usare la replica transazionale per replicare i dati da un database SQL Server locale al database SQL di Azure.  
2. Reindirizzare le applicazioni client o di livello intermedio in modo da aggiornare la copia del database SQL di Azure.  
3. Interrompere l'aggiornamento della versione SQL Server della tabella e rimuovere la pubblicazione.  

## <a name="limitations"></a>Limitazioni

Le opzioni seguenti non sono supportate per le sottoscrizioni del database SQL di Azure:

- Copia associazioni filegroup  
- Copia schemi di partizionamento delle tabelle  
- Copia schemi di partizionamento dell'indice  
- Copia statistiche definite dall'utente  
- Copia associazioni predefinite  
- Copia associazioni regola  
- Copia indici full-text  
- Copia XSD per colonna XML  
- Copia indici XML  
- Copia autorizzazioni  
- Copia indici spaziali  
- Copia indici filtrati  
- Copia attributo di compressione dati  
- Copia attributo di colonna di tipo sparse  
- Converti tipo FILESTREAM in tipi di dati MAX  
- Converti tipo hierarchyId in tipi di dati MAX  
- Converti tipo spaziale in tipi di dati MAX  
- Copia proprietà estese  
- Copia autorizzazioni  

### <a name="limitations-to-be-determined"></a>Limitazioni da determinare

- Copia regole di confronto  
- Esecuzione in una transazione serializzata della stored procedure  

## <a name="examples"></a>Esempi

Creare una pubblicazione e una sottoscrizione push. Per altre informazioni, vedere:
  
- [Creare una pubblicazione](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Creare una sottoscrizione push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) usando il nome del server logico del database SQL di Azure come sottoscrittore (ad esempio **N'azuresqldbdns.database.windows.net'**) e il nome del database SQL di Azure SQL come database di destinazione (ad esempio **AdventureWorks**).  

## <a name="see-also"></a>Vedere anche  

- [Creare una pubblicazione](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Creare una sottoscrizione push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Tipi di replica](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitoraggio (replica)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inizializzare una sottoscrizione](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  