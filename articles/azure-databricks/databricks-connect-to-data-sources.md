---
title: 'Connettersi a origini dati diverse da Azure Databricks '
description: Informazioni su come connettersi al database SQL di Azure, all'archivio di dati di Azure, all'archiviazione BLOB, al database Cosmos, agli hub eventi e al data warehouse SQL di Azure da Azure Databrick.Learn how to connect to Azure SQL Database, Azure Data Lake Store, blob storage, Cosmos DB, Event Hubs, and Azure SQL Data Warehouse from Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: 80ec9b2bbf10d8f500e7f9e0369faca3b9663633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129397"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Connettersi a origini dati da Azure Databricks

Questo articolo fornisce collegamenti a tutte le origini dati diverse in Azure che possono essere connesse ad Azure Databricks. Seguire gli esempi in questi collegamenti per estrarre dati dalle origini dati di Azure (ad esempio, Archiviazione BLOB di Azure, hub eventi di Azure e così via) in un cluster Azure Databricks ed eseguire processi analitici su di essi. 

## <a name="prerequisites"></a>Prerequisiti

* È necessario disporre di un'area di lavoro di Azure Databricks e di un cluster Spark. Seguire le istruzioni fornite in [Introduzione ad Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Origini dati per Azure Databricks

L'elenco seguente include le origini dati in Azure che è possibile usare con Azure Databricks. Per un elenco completo di origini dati da usare con Azure Databricks, vedere [Origini dati per Azure Databricks](/azure/databricks/data/data-sources/index).

- [Database SQL di AzureAzure SQL database](/azure/databricks/data/data-sources/sql-databases)

    Questo collegamento fornisce l'API del frame di dati per la connessione ai database SQL mediante JDBC, oltre a informazioni su come controllare il parallelismo di letture tramite l'interfaccia JDBC. In questo argomento vengono forniti esempi dettagliati mediante l'API Scala, con Python abbreviato ed esempi di Spark SQL alla fine.
- [Azure Data Lake Storage](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)

    Questo collegamento fornisce esempi su come usare l'entità servizio Azure Active Directory per eseguire l'autenticazione con Archiviazione dati di Azure.This link provides examples on how to use the Azure Active Directory service principal to authenticate with Azure Data Lake Storage. Vengono inoltre fornite istruzioni su come accedere ai dati in Archiviazione data lake di Azure da Azure Databricks.It also provides instructions on how to access the data in Azure Data Lake Storage from Azure Databricks.

- [Archiviazione BLOB di AzureAzure Blob Storage](/azure/databricks/data/data-sources/azure/azure-storage)

    Questo collegamento fornisce esempi su come accedere direttamente ad Archiviazione BLOB di Azure da Azure Databricks tramite la chiave di accesso o la firma di accesso condiviso per un contenitore specificato. Il collegamento contiene anche informazioni su come accedere ad Archiviazione BLOB di Azure da Azure Databricks usando l'API RDD.

- [Azure Cosmos DB](/azure/databricks/data/data-sources/azure/cosmosdb-connector)

    Questo collegamento fornisce istruzioni su come usare il [connettore Spark di Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark) da Azure Databricks per accedere ai dati in Azure Cosmos DB.

- [Hub eventi di Azure](/azure/event-hubs/event-hubs-spark-connector)

    Questo collegamento fornisce istruzioni su come usare il [connettore Spark degli Hub eventi di Azure](https://github.com/Azure/azure-event-hubs-spark) da Azure Databricks per accedere ai dati negli Hub eventi di Azure.

- [Azure SQL Data Warehouse](/azure/databricks/data/data-sources/azure/sql-data-warehouse)

    Questo collegamento fornisce istruzioni su come usare il connettore di Azure SQL Data Warehouse per la connessione da Azure Databricks.
    

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulle origini da cui è possibile importare dati in Azure Databricks, vedere [Origini dati per Azure Databricks](/azure/databricks/data/data-sources/index).


