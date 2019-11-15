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
ms.openlocfilehash: 932031f545ccc75cb12f368f5c7894c1b5e454cd
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091695"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Connettersi a origini dati da Azure Databricks

Questo articolo fornisce collegamenti a tutte le origini dati diverse in Azure che possono essere connesse ad Azure Databricks. Seguire gli esempi in questi collegamenti per estrarre dati dalle origini dati di Azure (ad esempio, Archiviazione BLOB di Azure, hub eventi di Azure e così via) in un cluster Azure Databricks ed eseguire processi analitici su di essi. 

## <a name="prerequisites"></a>prerequisiti

* È necessario disporre di un'area di lavoro di Azure Databricks e di un cluster Spark. Seguire le istruzioni fornite in [Introduzione ad Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Origini dati per Azure Databricks

L'elenco seguente include le origini dati in Azure che è possibile usare con Azure Databricks. Per un elenco completo di origini dati da usare con Azure Databricks, vedere [Origini dati per Azure Databricks](/azure/databricks/data/data-sources/index).

- [Database SQL di Azure](/azure/databricks/data/data-sources/sql-databases)

    Questo collegamento fornisce l'API del frame di dati per la connessione ai database SQL mediante JDBC, oltre a informazioni su come controllare il parallelismo di letture tramite l'interfaccia JDBC. In questo argomento vengono forniti esempi dettagliati mediante l'API Scala, con Python abbreviato ed esempi di Spark SQL alla fine.
- [Archiviazione di Azure Data Lake](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)

    Questo collegamento fornisce esempi su come usare l'entità servizio Azure Active Directory per l'autenticazione con Azure Data Lake Storage. Vengono inoltre fornite istruzioni su come accedere ai dati in Azure Data Lake Storage da Azure Databricks.

- [Archivio BLOB di Azure](/azure/databricks/data/data-sources/azure/azure-storage)

    Questo collegamento fornisce esempi su come accedere direttamente ad Archiviazione BLOB di Azure da Azure Databricks tramite la chiave di accesso o la firma di accesso condiviso per un contenitore specificato. Il collegamento contiene anche informazioni su come accedere ad Archiviazione BLOB di Azure da Azure Databricks usando l'API RDD.

- [Azure Cosmos DB](/azure/databricks/data/data-sources/azure/cosmosdb-connector)

    Questo collegamento fornisce istruzioni su come usare il [connettore Spark di Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark) da Azure Databricks per accedere ai dati in Azure Cosmos DB.

- [Hub eventi di Azure](/azure/databricks/data/data-sources/azure/eventhubs-connector)

    Questo collegamento fornisce istruzioni su come usare il [connettore Spark degli Hub eventi di Azure](https://github.com/Azure/azure-event-hubs-spark) da Azure Databricks per accedere ai dati negli Hub eventi di Azure.

- [Azure SQL Data Warehouse](/azure/databricks/data/data-sources/azure/sql-data-warehouse)

    Questo collegamento fornisce istruzioni su come usare il connettore di Azure SQL Data Warehouse per la connessione da Azure Databricks.
    

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulle origini da cui è possibile importare dati in Azure Databricks, vedere [Origini dati per Azure Databricks](/azure/databricks/data/data-sources/index).


