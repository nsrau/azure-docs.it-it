---
title: Eseguire la migrazione dei database esistenti per ottenere scalabilità orizzontale
description: Convertire i database partizionati in modo da usare gli strumenti di database elastici creando un gestore delle mappe partizioni
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: c6ad8b4c80f4b9c2fdb3c1a14209dcf0febc89e9
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92787143"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Eseguire la migrazione dei database esistenti per ottenere scalabilità orizzontale
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Gestisci con facilità i database partizionati con scalabilità orizzontale esistenti usando gli strumenti, ad esempio la [libreria client dei database elastici](elastic-database-client-library.md). Come prima cosa convertire un set di database esistente per l'uso del [gestore delle mappe partizioni](elastic-scale-shard-map-management.md).

## <a name="overview"></a>Panoramica

Per eseguire la migrazione di un database partizionato esistente:

1. Preparare il [database del gestore delle mappe partizioni](elastic-scale-shard-map-management.md).
2. Creare la mappa partizioni.
3. Preparare le singole partizioni.  
4. Aggiungere i mapping alla mappa partizioni.

Queste tecniche possono essere implementate usando la [libreria client .NET Framework](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)o gli script di PowerShell disponibili nel [database SQL di Azure: script degli strumenti di database elastici](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Negli esempi in questo articolo vengono usati gli script PowerShell.

Per altre informazioni su ShardMapManager, vedere [Gestione mappe partizioni](elastic-scale-shard-map-management.md). Per una panoramica degli strumenti di database elastici, vedere [Cenni preliminari sulle funzionalità di database elastico](elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Preparare il database del gestore delle mappe partizioni

Il gestore delle mappe partizioni è un database speciale che contiene i dati per la gestione dei database con un numero maggiore di istanze. È possibile usare un database esistente o crearne un nuovo. Un database che funge da gestore delle mappe partizioni non deve essere lo stesso della partizione. Lo script di PowerShell non crea automaticamente il database.

## <a name="step-1-create-a-shard-map-manager"></a>Passaggio 1: creare un gestore delle mappe partizioni

```powershell
# Create a shard map manager
New-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
#<server_name> and <smm_db_name> are the server name and database name
# for the new or existing database that should be used for storing
# tenant-database mapping information.
```

### <a name="to-retrieve-the-shard-map-manager"></a>Per recuperare il gestore mappe partizioni

Dopo la creazione, è possibile recuperare il gestore mappe partizioni con questo cmdlet. Questo passaggio è necessario ogni volta che si deve usare l'oggetto ShardMapManager.

```powershell
# Try to get a reference to the Shard Map Manager  
$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
```

## <a name="step-2-create-the-shard-map"></a>Passaggio 2: creare la mappa partizioni

Selezionare il tipo di mappa partizioni da creare. La scelta dipende dall'architettura del database:

1. Singolo tenant per database. Per informazioni sui i termini, vedere il [glossario](elastic-scale-glossary.md).
2. Più tenant per database (due tipi):
   1. Mapping di tipo elenco
   2. Mapping di tipo intervallo

Per un modello a singolo tenant, creare una mappa partizioni con **mapping di tipo elenco** . Il modello single-tenant assegna un database per tenant. Si tratta di un modello efficace per gli sviluppatori SaaS in quanto semplifica la gestione.

![Mapping di tipo elenco][1]

Il modello multi-tenant assegna diversi tenant a un database singolo ed è possibile distribuire gruppi di tenant tra più database. Usare questo modello quando si prevedono esigenze di dati ridotte per ogni tenant. In questo modello viene assegnato un intervallo di tenant a un database usando il **mapping di tipo intervallo** .

![Mapping di tipo intervallo][2]

In alternativa è possibile implementare un modello di database multi-tenant usando il *mapping di tipo elenco* per assegnare più tenant a un database singolo. Ad esempio, DB1 viene usato per archiviare le informazioni sugli ID tenant 1 e 5 e DB2 archivia i dati per i tenant 7 e 10.

![Tenant multipli su database singolo][3]

**In base alla scelta effettuata, procedere con una delle opzioni seguenti:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Opzione 1: creare una mappa partizioni per un mapping di elenco

Creare una mappa partizioni usando l'oggetto ShardMapManager.

```powershell
# $ShardMapManager is the shard map manager object
$ShardMap = New-ListShardMap -KeyType $([int]) -ListShardMapName 'ListShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Opzione 2: creare una mappa partizioni per un mapping di intervallo

Per usare questo modello di mapping, i valori dell'ID tenant devono essere a intervalli continui ed è accettabile avere gap negli intervalli ignorando l'intervallo durante la creazione dei database.

```powershell
# $ShardMapManager is the shard map manager object
# 'RangeShardMap' is the unique identifier for the range shard map.  
$ShardMap = New-RangeShardMap -KeyType $([int]) -RangeShardMapName 'RangeShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-3-list-mappings-on-an-individual-database"></a>Opzione 3: elencare i mapping in un singolo database

Per l'impostazione di questo modello è necessario anche creare una mappa di tipo elenco, come illustrato nel passaggio 2, opzione 1.

## <a name="step-3-prepare-individual-shards"></a>Passaggio 3: preparare le singole partizioni

Aggiungere ciascuna partizione (database) al gestore mappe partizioni. Ciò consente di preparare i singoli database per l'archiviazione delle informazioni di mapping. Eseguire questo metodo su ogni partizione.

```powershell
Add-Shard -ShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
# The $ShardMap is the shard map created in step 2.
```

## <a name="step-4-add-mappings"></a>Passaggio 4: aggiungere mapping

L'aggiunta di mapping dipende dal tipo di mappa partizioni creato. Se è stata creata una mappa di tipo elenco, aggiungere mapping di tipo elenco. Se è stata creata una mappa di tipo intervallo, aggiungere mapping di tipo intervallo.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Opzione 1: eseguire il mapping dei dati per un mapping di elenco

Eseguire il mapping dei dati aggiungendo un mapping di tipo elenco per ogni tenant.  

```powershell
# Create the mappings and associate it with the new shards
Add-ListMapping -KeyType $([int]) -ListPoint '<tenant_id>' -ListShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Opzione 2: eseguire il mapping dei dati per un mapping di intervallo

Aggiungere il mapping di tipo intervallo per tutte le associazioni intervallo ID tenant - database:

```powershell
# Create the mappings and associate it with the new shards
Add-RangeMapping -KeyType $([int]) -RangeHigh '5' -RangeLow '1' -RangeShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>Passaggio 4 opzione 3: eseguire il mapping dei dati per più tenant in un singolo database

Per ogni tenant eseguire Add-ListMapping (opzione 1).

## <a name="checking-the-mappings"></a>Verifica dei mapping

È possibile eseguire query per ottenere informazioni sulle partizioni esistenti e sui mapping a esse associati usando i comandi seguenti:  

```powershell
# List the shards and mappings
Get-Shards -ShardMap $ShardMap
Get-Mappings -ShardMap $ShardMap
```

## <a name="summary"></a>Riepilogo

Dopo aver completato l'installazione, è possibile iniziare a usare la libreria client di database elastici. È anche possibile usare il [routing dipendente dai dati](elastic-scale-data-dependent-routing.md) e le [query su più partizioni](elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Passaggi successivi

Ottenere gli script di PowerShell dagli [script di Azure SQL Database-Elastic Database Tools](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Gli strumenti sono disponibili anche in GitHub: [Azure/elastic-db-tools](https://github.com/Azure/elastic-db-tools).

Usare lo strumento di suddivisione-unione per spostare dati in o da un modello multi-tenant a un modello single-tenant. Vedere la pagina relativa allo [strumento di divisione-unione](elastic-scale-get-started.md).

## <a name="additional-resources"></a>Risorse aggiuntive

Per informazioni sugli schemi di architettura dati comuni delle applicazioni di database multi-tenant software come un servizio (SaaS), vedere [Schemi progettuali per applicazioni SaaS multi-tenant con il database SQL di Azure](saas-tenancy-app-design-patterns.md).

## <a name="questions-and-feature-requests"></a>Domande e richieste di funzionalità

Per domande, usare la pagina Domande e risposte [di Microsoft&per il database SQL](/answers/topics/azure-sql-database.html) e per le richieste di funzionalità, aggiungerle al [Forum dei commenti e suggerimenti sul database SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/elastic-convert-to-use-elastic-tools/multipleonsingledb.png