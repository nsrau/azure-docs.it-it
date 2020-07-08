---
title: Replica di Azure SQL Server nel database SQL di Azure
description: È possibile configurare un database nel database SQL di Azure come Sottoscrittore push in una topologia di replica snapshot o transazionale unidirezionale.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 6f1eb48655c4e38e2cf0520409e5e2b38750baf5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84324148"
---
# <a name="replication-to-azure-sql-database"></a>Replica nel database SQL di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

È possibile configurare un database SQL di Azure come Sottoscrittore push in una topologia di replica snapshot o transazionale unidirezionale.

> [!NOTE]
> Questo articolo descrive l'uso della [replica transazionale](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) nel database SQL di Azure. Non è correlato alla [replica geografica attiva](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication), una funzionalità del database SQL di Azure che consente di creare repliche leggibili complete dei singoli database.

## <a name="supported-configurations"></a>Configurazioni supportate
  
- Il database SQL di Azure può essere solo l'abbonato push di un SQL Server server di pubblicazione e di un server di distribuzione  
- L'istanza SQL Server che funge da server di pubblicazione e/o database di distribuzione può essere un'istanza di [SQL Server in esecuzione in locale](https://www.microsoft.com/sql-server/sql-server-downloads), un [istanza gestita SQL di Azure](../managed-instance/instance-create-quickstart.md)o un'istanza di [SQL Server in esecuzione in una macchina virtuale di Azure nel cloud](../virtual-machines/windows/sql-vm-create-portal-quickstart.md). 
- Il database di distribuzione e gli agenti di replica non possono essere inseriti in un database nel database SQL di Azure.  
- [Snapshot](/sql/relational-databases/replication/snapshot-replication) Sono supportate la replica [transazionale snapshot e unidirezionale](/sql/relational-databases/replication/transactional/transactional-replication) . La replica transazionale peer-to-peer e la replica di tipo merge non sono supportate.

### <a name="versions"></a>Versioni  

Per eseguire correttamente la replica in un database nel database SQL di Azure, SQL Server gli autori e i distributori devono usare almeno una delle versioni seguenti:

La pubblicazione in un database SQL di Azure da un database di SQL Server è supportata dalle seguenti versioni di SQL Server:

- SQL Server 2016 e versioni successive
- SQL Server 2014 [RTM CU10 dalla (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) o [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) o [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> Il tentativo di configurare la replica con una versione non supportata può causare il numero di errore MSSQL_REPL20084 (il processo non è riuscito a connettersi al Sottoscrittore) e MSSQL_REPL40532 (non è possibile aprire \<name> il server richiesto dall'account di accesso. Accesso non riuscito).  

Per usare tutte le funzionalità del database SQL di Azure, è necessario usare le versioni più recenti di [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt).  

### <a name="types-of-replication"></a>Tipi di replica

Esistono diversi [tipi di replica](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication):

| Replica | database SQL di Azure | Istanza gestita di SQL di Azure |
| :----| :------------- | :--------------- |
| [**Transazionale standard**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Sì (solo come sottoscrittore) | Sì | 
| [**Snapshot**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Sì (solo come sottoscrittore) | Sì|
| [**Replica di tipo merge**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | No | No|
| [**Peer-to-peer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | No | No|
| [**Bidirezionale**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | No | Sì|
| [**Sottoscrizioni aggiornabili**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | No | No|
| &nbsp; | &nbsp; | &nbsp; |

  
## <a name="remarks"></a>Osservazioni

- Sono supportate solo le sottoscrizioni push nel database SQL di Azure.  
- La replica può essere configurata usando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) o eseguendo istruzioni Transact-SQL sul database di pubblicazione. Non è possibile configurare la replica tramite il portale di Azure.  
- La replica può usare solo account di accesso con autenticazione SQL Server per connettersi al database SQL di Azure.
- Le tabelle replicate devono avere una chiave primaria.  
- È necessario avere una sottoscrizione di Azure.  
- Il Sottoscrittore del database SQL di Azure può trovarsi in qualsiasi area.  
- Una singola pubblicazione in SQL Server può supportare sia sottoscrittori di database SQL di Azure sia sottoscrittori di SQL Server (in locale e in una macchina virtuale di Azure).  
- La gestione della replica, il monitoraggio e la risoluzione dei problemi devono essere eseguiti da SQL Server piuttosto che dal database SQL di Azure.  
- È supportato solo `@subscriber_type = 0` in **sp_addsubscription** per il database SQL.  
- Il database SQL di Azure non supporta la replica bidirezionale, immediata, aggiornabile o peer-to-peer.

## <a name="replication-architecture"></a>Architettura della replica  

![replica in un database sql](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Scenari  

### <a name="typical-replication-scenario"></a>Scenario di replica tipico  

1. Creazione di una pubblicazione di replica transazionale in un database SQL Server.  
2. In SQL Server usare la **creazione guidata nuova sottoscrizione** o istruzioni Transact-SQL per creare un push nella sottoscrizione al database SQL di Azure.  
3. Con i database singoli e i database in pool nel database SQL di Azure, il set di dati iniziale è uno snapshot creato dall'agente di snapshot e distribuito e applicato dall'agente di distribuzione. Con un server di pubblicazione SQL Istanza gestita è anche possibile usare un backup del database per eseguire il seeding del sottoscrittore del database SQL di Azure.

### <a name="data-migration-scenario"></a>Scenario di migrazione dei dati  

1. Usare la replica transazionale per replicare i dati da un database di SQL Server al database SQL di Azure.  
2. Reindirizzare le applicazioni client o di livello intermedio per aggiornare la copia del database.  
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

### <a name="limitations-to-be-determined"></a>Limitazioni da determinare

- Copia regole di confronto  
- Esecuzione in una transazione serializzata della stored procedure  

## <a name="examples"></a>Esempi

Creare una pubblicazione e una sottoscrizione push. Per altre informazioni, vedere:
  
- [Creare una pubblicazione](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Creare una sottoscrizione push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) usando il nome del server come Sottoscrittore (ad esempio, **n'azuresqldbdns. database. Windows. NET '**) e il nome del database SQL di Azure come database di destinazione (ad esempio **AdventureWorks**).  

## <a name="see-also"></a>Vedere anche  

- [Replica transazionale](../managed-instance/replication-transactional-overview.md)
- [Creare una pubblicazione](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Creare una sottoscrizione push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Tipi di replica](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitoraggio (replica)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inizializzare una sottoscrizione](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
