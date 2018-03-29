---
title: Strumenti di inserimento dati della macchina virtuale per data science in Azure | Microsoft Docs
description: Strumenti di inserimento dati della macchina virtuale per data science
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 4ba735f1a6019938c8fc270ab98d7e6f8254471b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Strumenti di inserimento dati della macchina virtuale per data science

Una delle prime operazioni tecniche in un progetto di IA o di data science consiste nell'identificare i set di dati da usare e importarli nell'ambiente di analisi. La macchina virtuale per data science offre gli strumenti e librerie per importare dati provenienti da origini diverse in un archivio dati analitico in locale nella macchina virtuale per data science o in una piattaforma dati nel cloud o in locale. 

Ecco alcuni strumenti di spostamento dei dati che sono disponibili nella macchina virtuale per data science. 

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Uno strumento di copia dei dati dai BLOB di Archiviazione di Azure in Azure Data Lake Store. Consente anche di copiare dati tra due account di Azure Data Lake Store.      |
| Versioni supportate della macchina virtuale per data science      | Windows      |
| Usi tipici      | Importazione di più BLOB di Archiviazione di Azure in Azure Data Lake Store.      |
|  Come usarla o eseguirla?    |   Aprire un prompt dei comandi, quindi digitare `adlcopy` per ottenere assistenza.    |
| Collegamenti agli esempi      | [Con AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Strumenti correlati nella macchina virtuale per data science      | AzCopy, riga di comando di Azure     |

## <a name="azure-command-line"></a>Riga di comando di Azure

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Uno strumento di gestione per Azure. Contiene anche i verbi di comando per spostare i dati da piattaforme di dati di Azure come BLOB di archiviazione di Azure, archiviazione di Azure Data Lake     |
| Versioni supportate della macchina virtuale per data science      | Windows, Linux     |
| Usi tipici      | Importazione, esportazione di dati da e verso l'archivio di Azure, Azure Data Lake Store      |
|  Come usarla o eseguirla?    |   Aprire un prompt dei comandi, quindi digitare `az` per ottenere assistenza.    |
| Collegamenti agli esempi      | [Uso dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure)     |
| Strumenti correlati nella macchina virtuale per data science      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Uno strumento per copiare i dati da e verso i file locali, BLOB di archiviazione di Azure, file e tabelle.      |
| Versioni supportate della macchina virtuale per data science      | Windows      |
| Usi tipici      | Copia di file di archiviazione BLOB, copia di BLOB tra account.      |
|  Come usarla o eseguirla?    |   Aprire un prompt dei comandi, quindi digitare `azcopy` per ottenere assistenza.    |
| Collegamenti agli esempi      | [AzCopy in Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Strumenti correlati nella macchina virtuale per data science      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Utilità di migrazione dati a Azure Cosmos DB

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Uno strumento per importare dati da diverse origini, tra cui file JSON, file CSV, SQL, MongoDB, archivio tabelle di Azure, Amazon DynamoDB e raccolte di API SQL di Azure Cosmos DB in Azure Cosmos DB.      |
| Versioni supportate della macchina virtuale per data science      | Windows      |
| Usi tipici      | Importazione di file da una macchina virtuale in CosmosDB, importazione di dati dall'archiviazione in tabelle di Azure in CosmosDB o importazione di dati da un database di SQL Server in CosmosDB.     |
|  Come usarla o eseguirla?    |   Per usare la versione della riga di comando, aprire un prompt dei comandi e digitare `dt`. Per usare lo strumento GUI, aprire un prompt dei comandi e digitare `dtui`.    |
| Collegamenti agli esempi      | [Importare i dati in CosmosDB](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Strumenti correlati nella macchina virtuale per data science      | AzCopy, AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Strumento di SQL Server per copiare i dati tra SQL Server e un file di dati.      |
| Versioni supportate della macchina virtuale per data science      | Windows      |
| Usi tipici      | Importazione di un file CSV in una tabella di SQL Server, esportazione di una tabella di SQL Server in un file.      |
|  Come usarla o eseguirla?    |   Aprire un prompt dei comandi, quindi digitare `bcp` per ottenere assistenza.    |
| Collegamenti agli esempi      | [Utilità di copia bulk](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Strumenti correlati nella macchina virtuale per data science      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Uno strumento per montare un contenitore BLOB di Azure nel file system di Linux.      |
| Versioni supportate della macchina virtuale per data science      | Linux      |
| Usi tipici      | Lettura e scrittura in BLOB in un contenitore      |
|  Come usarla o eseguirla?    |   Eseguire _blobfuse_ in un terminale.    |
| Collegamenti agli esempi      | [blobfuse in GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Strumenti correlati nella macchina virtuale per data science      | Riga di comando di Azure      |


## <a name="microsoft-data-management-gateway"></a>Gateway di gestione dati di Microsoft

|    |           |
| ------------- | ------------- |
| Che cos'è?   | Uno strumento per la connessione locale delle origini dati ai servizi cloud di uso.      |
| Versioni supportate della macchina virtuale per data science      | Windows      |
| Usi tipici      | Connessione di un macchina virtuale a origini dati locali.      |
|  Come usarla o eseguirla?    |   Avviare "Gateway di gestione dati di Microsoft" dal menu Start.    |
| Collegamenti agli esempi      | [Gateway di gestione dati](https://msdn.microsoft.com/library/dn879362.aspx)      |
| Strumenti correlati nella macchina virtuale per data science      | AzCopy, AdlCopy, bcp    |
