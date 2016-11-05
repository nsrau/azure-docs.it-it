---
title: Monitorare e gestire un pool di database elastici con C# | Microsoft Docs
description: Usare tecniche di sviluppo di database in C# per gestire un pool di database elastici per database SQL di Microsoft Azure.
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: csharp
ms.workload: data-management
ms.date: 10/04/2016
ms.author: sstein

---
# <a name="monitor-and-manage-an-elastic-database-pool-with-c&#x23;"></a>Monitorare e gestire un pool di database elastici con C&#x23;
> [!div class="op_single_selector"]
> * [Portale di Azure](sql-database-elastic-pool-manage-portal.md)
> * [PowerShell](sql-database-elastic-pool-manage-powershell.md)
> * [C#](sql-database-elastic-pool-manage-csharp.md)
> * [T-SQL](sql-database-elastic-pool-manage-tsql.md)
> 
> 

Informazioni su come gestire un [pool di database elastici](sql-database-elastic-pool.md) con C&#x23;. 

> [!NOTE]
> Molte nuove funzionalità del database SQL sono supportate solo con il [modello di distribuzione Azure Resource Manager](../resource-group-overview.md). È quindi consigliabile usare sempre la **libreria di gestione di database SQL di Azure per .NET più recente ([documentazione](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [pacchetto NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. Le [librerie basate sul modello di distribuzione classica](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) meno recenti sono supportate solo per la compatibilità con le versioni precedenti ed è quindi consigliabile usare le nuove librerie basate su Resource Manager.
> 
> 

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

* Un pool elastico (il pool che si desidera gestire). Per creare un pool, vedere [Creare un pool di database elastici con C#](sql-database-elastic-pool-create-csharp.md).
* Visual Studio. Per una copia gratuita di Visual Studio, vedere la pagina [Download di Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) .

## <a name="move-a-database-into-an-elastic-pool"></a>Spostare un database in un pool elastico
È possibile spostare un database autonomo all'interno o all'esterno di un pool.  

    // Retrieve current database properties.

    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database.
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

## <a name="list-databases-in-an-elastic-pool"></a>Elencare i database in un pool elastico
Per recuperare tutti i database in un pool, chiamare il metodo [ListDatabases](https://msdn.microsoft.com/library/microsoft.azure.management.sql.elasticpooloperationsextensions.listdatabases) .

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## <a name="change-performance-settings-of-a-pool"></a>Modificare le impostazioni delle prestazioni di un pool
Recuperare le proprietà dei pool esistenti. Modificare i valori ed eseguire il metodo CreateOrUpdate.

    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## <a name="latency-of-elastic-pool-operations"></a>Latenza delle operazioni dei pool elastici
* La modifica del numero minimo di eDTU per database o del numero massimo di eDTU per database in genere viene completata entro cinque minuti.
* Il tempo richiesto per modificare le dimensioni del pool (eDTU) dipende dalla dimensione combinata di tutti i database nel pool. Le modifiche richiedono una media di 90 minuti o meno per 100 GB. Ad esempio, se lo spazio totale di tutti i database nel pool è pari a 200 GB, la latenza prevista per la modifica del numero di eDTU del pool per ogni pool è di 3 ore o meno.

## <a name="additional-resources"></a>Risorse aggiuntive
* [Codici di errore SQL per le applicazioni client del database SQL: errore di connessione e altri problemi del database](sql-database-develop-error-messages.md).
* [Database SQL](https://azure.microsoft.com/documentation/services/sql-database/)
* [API di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)
* [Creare un nuovo pool di database elastici con C#](sql-database-elastic-pool-create-csharp.md)
* [Quando usare un pool di database elastici](sql-database-elastic-pool-guidance.md)
* Vedere l'articolo sulla [scalabilità orizzontale con il database SQL di Azure](sql-database-elastic-scale-introduction.md): usare gli strumenti di database elastico per scalare in orizzontale, spostare dati, eseguire query o creare transazioni.

<!--HONumber=Oct16_HO2-->


