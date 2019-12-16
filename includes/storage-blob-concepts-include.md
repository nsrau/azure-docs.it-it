---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/05/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: cac6c40397e272d6d65931029c500bdcc2b774cc
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875436"
---
L'archivio BLOB di Azure è la soluzione di archiviazione di oggetti Microsoft per il cloud. L'archiviazione BLOB è ottimizzata per archiviare enormi quantità di dati non strutturati. I dati non strutturati sono dati che non seguono una definizione o un modello di dati specifico, ad esempio dati di testo o binari.

## <a name="about-blob-storage"></a>Informazioni sull'archiviazione BLOB

L'archiviazione BLOB è progettata per:

* Invio di immagini o documenti direttamente in un browser.
* Archiviazione di file per l'accesso distribuito.
* Flussi audio e video.
* Scrittura in file di log.
* Archiviazione di dati per backup e ripristino, ripristino di emergenza e archiviazione.
* Archiviazione di dati a scopo di analisi da parte di un servizio locale o ospitato in Azure.

Gli utenti o le applicazioni client possono accedere agli oggetti nell'archiviazione BLOB tramite HTTP/HTTPS ovunque si trovino. Gli oggetti nell'archiviazione BLOB sono accessibili tramite l'[API REST di Archiviazione di Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/storage) o una libreria client di Archiviazione di Azure. Le librerie client sono disponibili per diversi linguaggi, tra cui:

* [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client)
* [Java](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Node.js](https://azure.github.io/azure-storage-node) 
* [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)
* [Go](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Informazioni su Azure Data Lake Storage Gen2

L'archiviazione BLOB supporta Azure Data Lake Storage Gen2, la soluzione aziendale di analisi di Big Data Microsoft per il cloud. Azure Data Lake Storage Gen2 offre un file system gerarchico e i vantaggi dell'archiviazione BLOB, tra cui:

* Archiviazione a più livelli e a basso costo
* Disponibilità elevata
* Coerenza di alto livello
* Funzionalità di ripristino di emergenza

Per altre informazioni su Data Lake Storage Gen2, vedere [Introduzione ad Azure Data Lake Storage Gen2](../articles/storage/data-lake-storage/introduction.md).
