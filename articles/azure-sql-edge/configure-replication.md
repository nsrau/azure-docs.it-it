---
title: Configurare la replica in SQL Edge di Azure (anteprima)
description: Informazioni sulla configurazione della replica in Azure SQL Edge (anteprima).
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: ad92f796205d84a372de610cb210bbf8878a6c9b
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282773"
---
# <a name="configure-replication-to-azure-sql-edge-preview"></a>Configurare la replica in SQL Edge di Azure (anteprima) 

È possibile configurare un'istanza di Azure SQL Edge come Sottoscrittore push per la replica transazionale unidirezionale o la replica snapshot. Questa istanza non può fungere da server di pubblicazione o da server di distribuzione per una configurazione della replica transazionale. Si noti che Azure SQL Edge non supporta la replica di tipo merge, la replica peer-to-peer o la pubblicazione Oracle.

## <a name="supported-configurations"></a>Configurazioni supportate
  
- L'istanza di Azure SQL Edge deve essere un Sottoscrittore push per un server di pubblicazione.
- Il server di pubblicazione e il server di distribuzione possono essere uno dei seguenti:
   - Un'istanza di SQL Server eseguita in locale o un'istanza di SQL Server in esecuzione in una macchina virtuale di Azure. Per altre informazioni, vedere [Panoramica di SQL Server in Macchine virtuali di Azure](https://docs.microsoft.com/azure/azure-sql/virtual-machines/). SQL Server le istanze devono usare una versione successiva a SQL Server 2016.
   - Istanza di Istanza gestita SQL di Azure. SQL Istanza gestita può ospitare database di server di pubblicazione, server di distribuzione e Sottoscrittore. Per altre informazioni, vedere [Replica con Istanza gestita di database SQL](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/).

- Il database di distribuzione e gli agenti di replica non possono essere inseriti in un'istanza di Azure SQL Edge.  

> [!NOTE]
> Se si tenta di configurare la replica utilizzando una versione non supportata, è possibile che vengano visualizzati i due errori seguenti: MSSQL_REPL20084 ("Impossibile connettersi al Sottoscrittore") e MSSQL_REPL40532 ("Impossibile aprire il server \<name> richiesto dall'account di accesso. Accesso non riuscito. ").  

## <a name="remarks"></a>Osservazioni

Quando si configura la replica, è importante comprendere i requisiti e le procedure consigliate seguenti:

- Per configurare la replica, è possibile utilizzare [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). È anche possibile eseguire questa operazione eseguendo istruzioni Transact-SQL nel server di pubblicazione, utilizzando SQL Server Management Studio o [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio).
- Per eseguire la replica in un'istanza di Azure SQL Edge, è necessario usare l'autenticazione SQL Server per eseguire l'accesso.
- Le tabelle replicate devono avere una chiave primaria.
- Una singola pubblicazione in SQL Server può supportare sia sottoscrittori di SQL Edge di Azure, sia sottoscrittori di SQL Server (in locale e in una macchina virtuale di Azure).  
- È necessario eseguire la gestione della replica, il monitoraggio e la risoluzione dei problemi dall'istanza di SQL Server.  
- Sono supportate solo le sottoscrizioni push in SQL Edge di Azure.  
- `@subscriber_type = 0`È supportato solo nella stored procedure `sp_addsubscription` per Azure SQL Edge.  
- Azure SQL Edge non supporta la replica bidirezionale, immediata, aggiornabile o peer-to-peer.
- Azure SQL Edge supporta solo un subset di funzionalità disponibili in SQL Server o SQL Istanza gestita. Se si tenta di eseguire la replica di un database o di oggetti all'interno del database che contiene una o più funzionalità non supportate, il tentativo avrà esito negativo. Se, ad esempio, si tenta di replicare un database contenente oggetti con tipi di dati spaziali, si riceverà un errore. Per altre informazioni, vedere [funzionalità supportate di Azure SQL Edge](features.md).

## <a name="initialize-reference-data-on-an-instance-of-azure-sql-edge"></a>Inizializzare i dati di riferimento in un'istanza di Azure SQL Edge

Potrebbe essere necessario inizializzare l'istanza con i dati di riferimento che cambiano nel tempo. Ad esempio, potrebbe essere necessario aggiornare i modelli di machine learning nell'istanza di Azure SQL Edge, dopo aver eseguito il training su un'istanza di SQL Server. Ecco come inizializzare l'istanza in uno scenario di questo tipo:

1. Creazione di una pubblicazione di replica transazionale in un database SQL Server.  
2. Nell'istanza SQL Server usare la **creazione guidata nuova sottoscrizione** o istruzioni Transact-SQL per creare un push alla sottoscrizione in Azure SQL Edge.  
3. È possibile inizializzare il database replicato in Azure SQL Edge usando uno snapshot generato dall'agente snapshot e distribuito e fornito dall'agente di distribuzione. In alternativa, è possibile inizializzare utilizzando un backup del database dal server di pubblicazione. Tenere presente che se il backup del database contiene oggetti o funzionalità non supportate da Azure SQL Edge, l'operazione di ripristino ha esito negativo.

## <a name="limitations"></a>Limitazioni

Le opzioni seguenti non sono supportate per le sottoscrizioni di Azure SQL Edge:

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
- Copia tipi di `hierarchyid` dati spaziali FILESTREAM, o
- Converti `hierarchyid` in tipi di dati max  
- Converti tipo spaziale in tipi di dati MAX  
- Copia proprietà estese  
- Copia autorizzazioni  

## <a name="examples"></a>Esempi

Creare una pubblicazione e una sottoscrizione push. Per altre informazioni, vedere:
  
- [Creazione di una pubblicazione](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Creare una sottoscrizione push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) usando il nome e l'indirizzo IP del server perimetrale di Azure SQL come Sottoscrittore (ad esempio, **myEdgeinstance, 1433**) e un nome di database nell'istanza di Azure SQL Edge come database di destinazione (ad esempio, **AdventureWorks**).  

## <a name="next-steps"></a>Passaggi successivi  

- [Creazione di una pubblicazione](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Creare una sottoscrizione push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Tipi di replica](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitoraggio (replica)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inizializzare una sottoscrizione](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  


