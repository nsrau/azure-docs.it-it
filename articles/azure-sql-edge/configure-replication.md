---
title: Configurare la replica in SQL Edge di Azure (anteprima)
description: Informazioni sulla configurazione della replica di SQL Edge di Azure (anteprima)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e2b37e0f3ccf5fcebe4723c05d644f2cbb7c1d56
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593990"
---
# <a name="configure-replication-to-azure-sql-edge-preview"></a>Configurare la replica in SQL Edge di Azure (anteprima) 

L'istanza di SQL Edge di Azure può essere configurata come sottoscrittore push per una replica transazionale unidirezionale o una replica snapshot. L'istanza di SQL Edge di Azure non può agire come server di pubblicazione o distribuzione per una configurazione di replica transazionale. La replica di tipo merge, la replica P2P e la pubblicazione Oracle non sono supportate con SQL Edge di Azure.

## <a name="supported-configurations"></a>**Configurazioni supportate**:
  
- L'istanza di SQL Edge di Azure deve essere un sottoscrittore push per un server di pubblicazione.
- Il server di pubblicazione e il server di distribuzione possono essere:
   - Un'istanza di SQL Server in esecuzione in locale o un'istanza di SQL Server in esecuzione in una macchina virtuale di Azure. Per altre informazioni, vedere [Panoramica di SQL Server in Macchine virtuali di Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/). Le istanze di SQL Server devono usare una versione successiva a SQL Server 2016.
   - Un'istanza di Istanza gestita di database SQL di Azure. L'istanza gestita può ospitare database di pubblicazione, di distribuzione e sottoscrittore. Per altre informazioni, vedere [Replica con Istanza gestita di database SQL](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/).

- Il database di distribuzione e gli agenti di replica non possono essere collocati in un'istanza di SQL Edge di Azure.  

> [!NOTE]
> Se si cerca di configurare la replica usando una versione non supportata, potrebbero essere generati gli errori MSSQL_REPL20084 (Il processo non è riuscito a connettersi al sottoscrittore) e MSSQL_REPL40532 (Impossibile aprire il server \<nome> richiesto dall'account di accesso. Accesso non riuscito).  

Per usare tutte le funzionalità di SQL Edge di Azure, è necessario usare le versioni più recenti di [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  

## <a name="remarks"></a>Osservazioni

- La replica può essere configurata usando [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) o eseguendo istruzioni Transact-SQL sul server di pubblicazione usando SQL Server Management Studio o [Azure Database Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio).
- La replica può usare solo account di accesso per l'autenticazione di SQL Server per connettersi a un'istanza di SQL Edge di Azure.
- Le tabelle replicate devono avere una chiave primaria.
- Una singola pubblicazione in SQL Server può supportare sia sottoscrittori di SQL Edge di Azure, sia sottoscrittori di SQL Server (in locale e in una macchina virtuale di Azure).  
- La gestione, il monitoraggio e la risoluzione dei problemi di replica devono essere eseguiti dall'istanza locale di SQL Server.  
- Sono supportate solo le sottoscrizioni push in SQL Edge di Azure.  
- È supportato solo `@subscriber_type = 0` in **sp_addsubscription** per SQL Edge di Azure.  
- SQL Edge di Azure non supporta la replica bidirezionale, immediata, aggiornabile o peer-to-peer.
- SQL Edge di Azure supporta solo un subset di funzionalità disponibili in SQL Server o Istanza gestita di database SQL di Azure, di conseguenza un tentativo di replicare un database (o gli oggetti all'interno del database) che contiene una o più funzionalità non supportate restituirà un errore. Ad esempio, se si tenta di replicare un database contenente oggetti con tipi di dati spaziali, verrà generato un errore. Per altre informazioni sulle funzionalità supportate da SQL Edge di Azure, vedere [Funzionalità supportate di SQL Edge di Azure](features.md).

## <a name="scenarios"></a>Scenari  

### <a name="initializing-reference-data-on-an-edge-instance"></a>Inizializzazione dei dati di riferimento in un'istanza di Edge

Uno scenario comune in cui la replica può essere utile è quando è necessario inizializzare l'istanza di Edge con i dati di riferimento che cambiano nel tempo. Ad esempio, l'aggiornamento dei modelli di ML nell'istanza di Edge sottoposti a training su un'istanza di SQL Server locale.

1. Creare una pubblicazione di replica transazionale in un database di SQL Server locale.  
2. Nell'istanza di SQL Server locale usare la **Creazione guidata nuova sottoscrizione** o istruzioni Transact-SQL per creare una sottoscrizione push a SQL Edge di Azure.  
3. Il database replicato in SQL Edge di Azure può essere inizializzato usando uno snapshot generato dall'agente di snapshot e distribuito dall'agente di distribuzione o usando un backup del database dal server di pubblicazione. Anche in questo caso, se il backup del database contiene oggetti o funzionalità non supportati da SQL Edge di Azure, l'operazione di ripristino avrà esito negativo.

## <a name="limitations"></a>Limitazioni

Le opzioni seguenti non sono supportate per le sottoscrizioni di SQL Edge di Azure:

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
- Copia di FileStream, hierarchyId o tipi di dati spaziali.
- Converti tipo hierarchyId in tipi di dati MAX  
- Converti tipo spaziale in tipi di dati MAX  
- Copia proprietà estese  
- Copia autorizzazioni  

## <a name="examples"></a>Esempi

Creare una pubblicazione e una sottoscrizione push. Per altre informazioni, vedere:
  
- [Creare una pubblicazione](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Creare una sottoscrizione push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) usando il nome del server o l'IP di SQL Edge di Azure come sottoscrittore (ad esempio **myEdgeinstance,1433**) e il nome dell'istanza SQL Edge di Azure come database di destinazione (ad esempio **AdventureWorks**).  

## <a name="see-also"></a>Vedere anche  

- [Creare una pubblicazione](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Creare una sottoscrizione push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Tipi di replica](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitoraggio (replica)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inizializzare una sottoscrizione](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  


