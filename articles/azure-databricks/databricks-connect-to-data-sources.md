---
title: 'Connettersi a origini dati diverse da Azure Databricks '
description: Informazioni su come connettersi a database SQL di Azure, Azure Data Lake Store, archiviazione BLOB, Cosmos DB, Hub eventi e Azure SQL Data Warehouse da Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: 9b44db3e8ffc02d211f7f97404f0cdd8d319fe03
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597498"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Connettersi a origini dati da Azure Databricks

Questo articolo fornisce collegamenti a tutte le origini dati diverse in Azure che possono essere connesse ad Azure Databricks. Seguire gli esempi in questi collegamenti per estrarre dati dalle origini dati di Azure (ad esempio, Archiviazione BLOB di Azure, hub eventi di Azure e così via) in un cluster Azure Databricks ed eseguire processi analitici su di essi. 

## <a name="prerequisites"></a>Prerequisiti

* È necessario disporre di un'area di lavoro di Azure Databricks e di un cluster Spark. Seguire le istruzioni fornite in [Introduzione ad Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Origini dati per Azure Databricks

L'elenco seguente include le origini dati in Azure che è possibile usare con Azure Databricks. Per un elenco completo di origini dati da usare con Azure Databricks, vedere [Origini dati per Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html).

- [Database SQL di Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/sql-databases.html)

    Questo collegamento fornisce l'API del frame di dati per la connessione ai database SQL mediante JDBC, oltre a informazioni su come controllare il parallelismo di letture tramite l'interfaccia JDBC. In questo argomento vengono forniti esempi dettagliati mediante l'API Scala, con Python abbreviato ed esempi di Spark SQL alla fine.
- [Archiviazione di Azure Data Lake](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)

    Questo collegamento fornisce esempi su come usare l'entità servizio Azure Active Directory per l'autenticazione con Azure Data Lake Storage. Vengono inoltre fornite istruzioni su come accedere ai dati in Azure Data Lake Storage da Azure Databricks.

- [Archivio BLOB di Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html)

    Questo collegamento fornisce esempi su come accedere direttamente ad Archiviazione BLOB di Azure da Azure Databricks tramite la chiave di accesso o la firma di accesso condiviso per un contenitore specificato. Il collegamento contiene anche informazioni su come accedere ad Archiviazione BLOB di Azure da Azure Databricks usando l'API RDD.

- [Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

    Questo collegamento fornisce istruzioni su come usare il [connettore Spark di Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark) da Azure Databricks per accedere ai dati in Azure Cosmos DB.

- [Hub eventi di Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/eventhubs-connector.html)

    Questo collegamento fornisce istruzioni su come usare il [connettore Spark degli Hub eventi di Azure](https://github.com/Azure/azure-event-hubs-spark) da Azure Databricks per accedere ai dati negli Hub eventi di Azure.

- [Azure SQL Data Warehouse](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/sql-data-warehouse.html)

    Questo collegamento fornisce istruzioni su come usare il connettore di Azure SQL Data Warehouse per la connessione da Azure Databricks.
    

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulle origini da cui è possibile importare dati in Azure Databricks, vedere [Origini dati per Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#).


