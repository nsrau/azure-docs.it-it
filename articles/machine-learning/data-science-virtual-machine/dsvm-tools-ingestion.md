---
title: Strumenti per l'inserimento di dati
titleSuffix: Azure Data Science Virtual Machine
description: Informazioni sugli strumenti e le utilità per l'inserimento dei dati preinstallati nella macchina virtuale di data science.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d86858f8d7f09628457b718ca3c481934d720081
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270056"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Strumenti di inserimento dati della macchina virtuale per data science

Come uno dei primi passaggi tecnici in un progetto di analisi scientifica dei dati o di applicazione della dati, è necessario identificare i set di dati da usare e inserirli nell'ambiente di analisi. Data Science Virtual Machine (DSVM) fornisce strumenti e librerie per inserire dati provenienti da origini diverse nell'archiviazione dei dati analitici in locale nella dSVM o in una piattaforma dati nel cloud o in locale.

Ecco alcuni strumenti di spostamento dei dati disponibili in DSVM.

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Strumento per copiare i dati dall'archiviazione BLOB di Azure in Azure Data Lake Store.A tool to copy data from Azure Blob storage into Azure Data Lake Store. Consente anche di copiare dati tra due account di Azure Data Lake Store.      |
| Versioni DSVM supportate      | WINDOWS      |
| Usi tipici      | Importazione di più BLOB dall'archiviazione BLOB di Azure in Azure Data Lake Store.Importing multiple blobs from Azure Blob storage into Azure Data Lake Store.      |
|  Come usarla o eseguirla?    |   Aprire un prompt `adlcopy` dei comandi e digitare per ottenere assistenza.    |
| Collegamenti agli esempi      | [Con AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Strumenti correlati su DSVM      | AzCopy, Azure CLI     |

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Uno strumento di gestione per Azure. Contiene anche verbi di comando per spostare i dati dalle piattaforme di dati di Azure come Archiviazione BLOB di Azure e Archivio dati di Azure.It also contains command verbs to move data from Azure data platforms like Azure Blob storage and Azure Data Lake Store.     |
| Versioni DSVM supportate      | Windows, Linux     |
| Usi tipici      | Importazione ed esportazione di dati da e verso Archiviazione di Azure e Azure Data Lake Store.      |
|  Come usarla o eseguirla?    |   Aprire un prompt `az` dei comandi e digitare per ottenere assistenza.    |
| Collegamenti agli esempi      | [Uso dell'interfaccia della riga di comando di AzureUsing](https://docs.microsoft.com/cli/azure)     |
| Strumenti correlati su DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Strumento per copiare dati da e verso file locali, Archiviazione BLOB di Azure, file e tabelle.      |
| Versioni DSVM supportate      | WINDOWS      |
| Usi tipici      | Copia di file nell'archiviazione BLOB di Azure e copia blob tra account.      |
|  Come usarla o eseguirla?    |   Aprire un prompt `azcopy` dei comandi e digitare per ottenere assistenza.    |
| Collegamenti agli esempi      | [AzCopy in Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Strumenti correlati su DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Utilità di migrazione dati a Azure Cosmos DB

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Strumento per importare dati da varie origini in Azure Cosmos DB, un database NoSQL nel cloud. Queste origini includono file JSON, file CSV, SQL, MongoDB, archiviazione tabelle di Azure, Amazon DynamoDB e raccolte di API SQL del database cosmos di Azure.These sources include JSON files, CSV files, SQL, MongoDB, Azure Table storage, Amazon DynamoDB, and Azure Cosmos DB SQL API collections.      |
| Versioni DSVM supportate      | WINDOWS      |
| Usi tipici      | Importazione di file da una macchina virtuale a CosmosDB, importazione di dati dall'archiviazione delle tabelle di Azure in CosmosDB e importazione di dati da un database di Microsoft SQL Server a CosmosDB.     |
|  Come usarla o eseguirla?    |   Per utilizzare la versione della riga di `dt`comando, aprire un prompt dei comandi e digitare . Per utilizzare lo strumento GUI, aprire `dtui`un prompt dei comandi e digitare .    |
| Collegamenti agli esempi      | [CosmosDB Dati di importazione](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Strumenti correlati su DSVM      | AzCopy, AdlCopy      |

## <a name="azure-storage-explorer"></a>Esplora archivi Azure

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Interfaccia utente grafica per l'interazione con i file archiviati nel cloud di Azure.Graphical User Interface for interacting with files stored in the Azure cloud. |
| Versioni DSVM supportate      | WINDOWS      |
| Usi tipici      | Importazione ed esportazione di dati da DSVM.    |
|  Come usarla o eseguirla?    | Cercare "Azure Storage Explorer" nel menu Start.Search for "Azure Storage Explorer" in the Start menu. |
| Collegamenti agli esempi      | [Esplora archivi di AzureAzure Storage Explorer](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Strumento di SQL Server per copiare i dati tra SQL Server e un file di dati.      |
| Versioni DSVM supportate      | WINDOWS      |
| Usi tipici      | Importazione di un file CSV in una tabella di SQL Server ed esportazione di una tabella di SQL Server in un file.      |
|  Come usarla o eseguirla?    |   Aprire un prompt `bcp` dei comandi e digitare per ottenere assistenza.    |
| Collegamenti agli esempi      | [Utilità bcp](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Strumenti correlati su DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Strumento per montare un contenitore di archiviazione BLOB di Azure nel file system Linux.A tool to mount an Azure Blob storage container in the Linux file system.      |
| Versioni DSVM supportate      | Linux      |
| Usi tipici      | Lettura e scrittura in BLOB in un contenitore.      |
|  Come usarlo ed eseguirlo?    |   Eseguire _blobfuse_ in un terminale.    |
| Collegamenti agli esempi      | [blobfuse in GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Strumenti correlati su DSVM      | Interfaccia della riga di comando di Azure      |
