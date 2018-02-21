---
title: "Eseguire la migrazione dei database esistenti per ottenere scalabilità orizzontale | Microsoft Docs"
description: Convertire database partizionati per l'uso di strumenti dei database elastici mediante la creazione di un gestore mappe partizioni
services: sql-database
documentationcenter: 
author: ddove
manager: jhubbard
editor: 
ms.assetid: 8c851d8e-8fd5-4327-89c1-9178b20ddd69
ms.service: sql-database
ms.custom: scale out apps
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 356c4223ff3ae844552b7bee40aa3ffc6aad7ea0
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="migrate-existing-databases-to-scale-out"></a>Eseguire la migrazione dei database esistenti per ottenere scalabilità orizzontale
È possibile gestire facilmente i database partizionati con scalabilità orizzontale esistenti usando gli strumenti di database del database SQL di Azure, come ad esempio la [libreria client dei database elastici](sql-database-elastic-database-client-library.md). Come prima cosa convertire un set di database esistente per l'uso del [gestore delle mappe partizioni](sql-database-elastic-scale-shard-map-management.md). 

## <a name="overview"></a>Panoramica
Per eseguire la migrazione di un database partizionato esistente: 

1. Preparare il [database del gestore delle mappe partizioni](sql-database-elastic-scale-shard-map-management.md).
2. Creare la mappa partizioni.
3. Preparare le singole partizioni.  
4. Aggiungere i mapping alla mappa partizioni.

Queste tecniche possono essere implementate tramite la [libreria client .NET Framework](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) o gli script di PowerShell disponibili nella pagina relativa agli [script degli strumenti di database elastico del database SQL di Azure](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Negli esempi in questo articolo vengono usati gli script PowerShell.

Per altre informazioni su ShardMapManager, vedere [Gestione mappe partizioni](sql-database-elastic-scale-shard-map-management.md). Per una panoramica sugli strumenti di database elastici, vedere [Panoramica sulle funzionalità di database elastico](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Preparare il database del gestore delle mappe partizioni
Il gestore delle mappe partizioni è un database speciale che contiene i dati per la gestione dei database con un numero maggiore di istanze. È possibile usare un database esistente o crearne un nuovo. Un database che funge da gestore delle mappe partizioni non deve essere lo stesso della partizione. Lo script di PowerShell non crea automaticamente il database. 

## <a name="step-1-create-a-shard-map-manager"></a>Passaggio 1: Creare un gestore mappe partizioni
    # Create a shard map manager. 
    New-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 
    #<server_name> and <smm_db_name> are the server name and database name 
    # for the new or existing database that should be used for storing 
    # tenant-database mapping information.

### <a name="to-retrieve-the-shard-map-manager"></a>Per recuperare il gestore mappe partizioni
Dopo la creazione, è possibile recuperare il gestore mappe partizioni con questo cmdlet. Questo passaggio è necessario ogni volta che si deve usare l'oggetto ShardMapManager.

    # Try to get a reference to the Shard Map Manager  
    $ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 


## <a name="step-2-create-the-shard-map"></a>Passaggio 2: Creare la mappa partizioni
Selezionare il tipo di mappa partizioni da creare. La scelta dipende dall'architettura del database: 

1. Singolo tenant per database. Per informazioni sui i termini, vedere il [glossario](sql-database-elastic-scale-glossary.md). 
2. Più tenant per database (due tipi):
   1. Mapping di tipo elenco
   2. Mapping di tipo intervallo

Per un modello a singolo tenant, creare una mappa partizioni con **mapping di tipo elenco** . Il modello single-tenant assegna un database per tenant. Si tratta di un modello efficace per gli sviluppatori SaaS in quanto semplifica la gestione.

![Mapping di tipo elenco][1]

Il modello multi-tenant assegna diversi tenant a un database singolo ed è possibile distribuire gruppi di tenant tra più database. Usare questo modello quando si prevedono esigenze di dati ridotte per ogni tenant. In questo modello viene assegnato un intervallo di tenant a un database usando il **mapping di tipo intervallo**. 

![Mapping di tipo intervallo][2]

In alternativa è possibile implementare un modello di database multi-tenant usando il *mapping di tipo elenco* per assegnare più tenant a un database singolo. Ad esempio, DB1 viene usato per archiviare le informazioni sugli ID tenant 1 e 5 e DB2 archivia i dati per i tenant 7 e 10. 

![Tenant multipli su database singolo][3] 

**In base alla scelta effettuata, procedere con una delle opzioni seguenti:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Opzione 1: creare una mappa partizioni per un mapping di tipo elenco
Creare una mappa partizioni usando l'oggetto ShardMapManager. 

    # $ShardMapManager is the shard map manager object. 
    $ShardMap = New-ListShardMap -KeyType $([int]) 
    -ListShardMapName 'ListShardMap' 
    -ShardMapManager $ShardMapManager 


### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Opzione 2: creare una mappa partizioni per un mapping di tipo intervallo
Per usare questo modello di mapping, i valori dell'ID tenant devono essere a intervalli continui ed è accettabile avere gap negli intervalli ignorando l'intervallo durante la creazione dei database.

    # $ShardMapManager is the shard map manager object 
    # 'RangeShardMap' is the unique identifier for the range shard map.  
    $ShardMap = New-RangeShardMap 
    -KeyType $([int]) 
    -RangeShardMapName 'RangeShardMap' 
    -ShardMapManager $ShardMapManager 

### <a name="option-3-list-mappings-on-a-single-database"></a>Opzione 3: eseguire il mapping di tipo elenco in un database singolo
Per l'impostazione di questo modello è necessario anche creare una mappa di tipo elenco, come illustrato nel passaggio 2, opzione 1.

## <a name="step-3-prepare-individual-shards"></a>Passaggio 3: preparare le singole partizioni
Aggiungere ciascuna partizione (database) al gestore mappe partizioni. Ciò consente di preparare i singoli database per l'archiviazione delle informazioni di mapping. Eseguire questo metodo su ogni partizione.

    Add-Shard 
    -ShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>'
    # The $ShardMap is the shard map created in step 2.


## <a name="step-4-add-mappings"></a>Passaggio 4: aggiungere mapping
L'aggiunta di mapping dipende dal tipo di mappa partizioni creato. Se è stata creata una mappa di tipo elenco, aggiungere mapping di tipo elenco. Se è stata creata una mappa di tipo intervallo, aggiungere mapping di tipo intervallo.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Opzione 1: eseguire il mapping dei dati per il mapping di tipo elenco
Eseguire il mapping dei dati aggiungendo un mapping di tipo elenco per ogni tenant.  

    # Create the mappings and associate it with the new shards 
    Add-ListMapping 
    -KeyType $([int]) 
    -ListPoint '<tenant_id>' 
    -ListShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Opzione 2: eseguire il mapping dei dati per il mapping di tipo intervallo
Aggiungere il mapping di tipo intervallo per tutte le associazioni intervallo ID tenant - database:

    # Create the mappings and associate it with the new shards 
    Add-RangeMapping 
    -KeyType $([int]) 
    -RangeHigh '5' 
    -RangeLow '1' 
    -RangeShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 


### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-a-single-database"></a>Passaggio 4, opzione 3: eseguire il mapping dei dati per più tenant su un database singolo
Per ogni tenant eseguire Add-ListMapping (opzione 1). 

## <a name="checking-the-mappings"></a>Verifica dei mapping
È possibile eseguire query per ottenere informazioni sulle partizioni esistenti e sui mapping a esse associati usando i comandi seguenti:  

    # List the shards and mappings 
    Get-Shards -ShardMap $ShardMap 
    Get-Mappings -ShardMap $ShardMap 

## <a name="summary"></a>Summary
Dopo aver completato l'installazione, è possibile iniziare a usare la libreria client di database elastici. È anche possibile usare il [routing dipendente dai dati](sql-database-elastic-scale-data-dependent-routing.md) e le [query su più partizioni](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Passaggi successivi
Gli script di PowerShell sono disponibili nella pagina relativa agli [script degli strumenti di database elastici del database SQL di Azure](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Gli strumenti sono disponibili anche in GitHub: [Azure/elastic-db-tools](https://github.com/Azure/elastic-db-tools).

Usare lo strumento di suddivisione-unione per spostare dati in o da un modello multi-tenant a un modello single-tenant. Vedere la pagina relativa allo [strumento di divisione-unione](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Risorse aggiuntive
Per informazioni sugli schemi di architettura dati comuni delle applicazioni di database multi-tenant software come un servizio (SaaS), vedere [Schemi progettuali per applicazioni SaaS multi-tenant con il database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Domande e richieste di funzionalità
Se ci sono domande, usare il [forum sul database SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) mentre è possibile inserire le richieste di nuove funzionalità nel [forum relativo a commenti e suggerimenti sul database SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png

