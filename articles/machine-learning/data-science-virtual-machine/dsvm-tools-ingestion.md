---
title: Strumenti per l'inserimento di dati
titleSuffix: Azure Data Science Virtual Machine
description: Informazioni sugli strumenti e le utilità di inserimento dati preinstallati nella Data Science Virtual Machine.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: 2d3d37bcb6ee4bae82d82cf676211e9e1546cfad
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70191980"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Strumenti di inserimento dati della macchina virtuale per data science

Come uno dei primi passaggi tecnici di un progetto data science o intelligenza artificiale, è necessario identificare i set di dati da usare e portarli nell'ambiente di analisi. Il Data Science Virtual Machine (DSVM) fornisce gli strumenti e le librerie per portare i dati da origini diverse all'archiviazione dei dati analitici localmente in DSVM o in una piattaforma di dati nel cloud o in locale.

Ecco alcuni strumenti di spostamento dei dati disponibili in DSVM.

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Uno strumento per copiare i dati dall'archiviazione BLOB di Azure in Azure Data Lake Store. Consente anche di copiare dati tra due account di Azure Data Lake Store.      |
| Versioni di DSVM supportate      | Windows      |
| Usi tipici      | Importazione di più BLOB dall'archiviazione BLOB di Azure in Azure Data Lake Store.      |
|  Come usarla o eseguirla?    |   Aprire un prompt dei comandi e `adlcopy` digitare per visualizzare la guida.    |
| Collegamenti agli esempi      | [Con AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Strumenti correlati in DSVM      | AzCopy, interfaccia della riga di comando di Azure     |

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Uno strumento di gestione per Azure. Contiene anche i verbi di comando per spostare i dati dalle piattaforme dati di Azure, ad esempio archiviazione BLOB di Azure e Azure Data Lake Store.     |
| Versioni di DSVM supportate      | Windows, Linux     |
| Usi tipici      | Importazione ed esportazione di dati da e verso archiviazione di Azure e Azure Data Lake Store.      |
|  Come usarla o eseguirla?    |   Aprire un prompt dei comandi e `az` digitare per visualizzare la guida.    |
| Collegamenti agli esempi      | [Uso dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure)     |
| Strumenti correlati in DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Strumento per copiare dati da e verso file locali, archiviazione BLOB di Azure, file e tabelle.      |
| Versioni di DSVM supportate      | Windows      |
| Usi tipici      | Copia di file nell'archivio BLOB di Azure e copia di BLOB tra account.      |
|  Come usarla o eseguirla?    |   Aprire un prompt dei comandi e `azcopy` digitare per visualizzare la guida.    |
| Collegamenti agli esempi      | [AzCopy in Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Strumenti correlati in DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Utilità di migrazione dati a Azure Cosmos DB

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Strumento per importare dati da diverse origini in Azure Cosmos DB. Queste origini includono file JSON, file CSV, SQL, MongoDB, archiviazione tabelle di Azure, Amazon DynamoDB e raccolte di API SQL Azure Cosmos DB.      |
| Versioni di DSVM supportate      | Windows      |
| Usi tipici      | Importazione di file da una macchina virtuale a CosmosDB, importazione di dati da archiviazione tabelle di Azure a CosmosDB e importazione di dati da un database di Microsoft SQL Server a CosmosDB.     |
|  Come usarla o eseguirla?    |   Per utilizzare la versione della riga di comando, aprire un prompt dei comandi `dt`e digitare. Per utilizzare lo strumento GUI, aprire un prompt dei comandi e `dtui`digitare.    |
| Collegamenti agli esempi      | [CosmosDB importare dati](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Strumenti correlati in DSVM      | AzCopy, AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Strumento di SQL Server per copiare i dati tra SQL Server e un file di dati.      |
| Versioni di DSVM supportate      | Windows      |
| Usi tipici      | Importazione di un file CSV in una tabella SQL Server e esportazione di una tabella SQL Server in un file.      |
|  Come usarla o eseguirla?    |   Aprire un prompt dei comandi e `bcp` digitare per visualizzare la guida.    |
| Collegamenti agli esempi      | [utilità bcp](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Strumenti correlati in DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Uno strumento per montare un contenitore di archiviazione BLOB di Azure nel file system Linux.      |
| Versioni di DSVM supportate      | Linux      |
| Usi tipici      | Lettura e scrittura in BLOB in un contenitore.      |
|  Come usarlo ed eseguirlo    |   Eseguire _blobfuse_ in un terminale.    |
| Collegamenti agli esempi      | [blobfuse in GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Strumenti correlati in DSVM      | Interfaccia della riga di comando di Azure      |


## <a name="microsoft-data-management-gateway"></a>Gateway di gestione dati di Microsoft

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Uno strumento per la connessione locale delle origini dati ai servizi cloud di uso.      |
| Versioni di DSVM supportate      | Windows      |
| Usi tipici      | Connessione di un macchina virtuale a origini dati locali.      |
|  Come usarla o eseguirla?    |   Aprire Microsoft Gestione dati gateway dal menu Start.    |
| Collegamenti agli esempi      | [Gateway di gestione dati](https://msdn.microsoft.com/library/dn879362.aspx)      |
| Strumenti correlati in DSVM      | AzCopy, AdlCopy, bcp    |
