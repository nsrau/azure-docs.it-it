---
title: Funzionalità di archiviazione BLOB disponibili in Archiviazione di Azure Data Lake Gen2 Documenti Microsoft
description: Informazioni sulle funzionalità di archiviazione BLOB che è possibile usare con Azure Data Lake Storage Gen2Learn about which Blob storage features you can use with Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 215244204aa58cc2fdedc639d48e01b514759694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196009"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Blob storage features available in Azure Data Lake Storage Gen2

Le funzionalità di archiviazione BLOB, ad esempio [la registrazione diagnostica,](../common/storage-analytics-logging.md) [i livelli](storage-blob-storage-tiers.md)di accesso e i criteri di gestione del ciclo di [vita dell'archiviazione BLOB,](storage-lifecycle-management-concepts.md) ora funzionano con gli account con uno spazio dei nomi gerarchico. Pertanto, è possibile abilitare gli spazi dei nomi gerarchici negli account di archiviazione BLOB senza perdere l'accesso a queste funzionalità.

Questa tabella elenca le funzionalità di archiviazione BLOB che è possibile usare con Archiviazione dati di Azure 2.This table lists the Blob storage features that you can use with Azure Data Lake Storage Gen2. Gli elementi visualizzati in queste tabelle cambieranno nel tempo man mano che il supporto continua a espandersi.

## <a name="supported-blob-storage-features"></a>Funzionalità di archiviazione BLOB supportateSupported Blob storage features

> [!NOTE]
> Il livello di supporto si riferisce solo al modo in cui la funzionalità è supportata con Data Lake Storage Gen2.Support level refers only to how the feature is supported with Data Lake Storage Gen2.

|Funzionalità di archiviazione BLOBBlob Storage feature |Livello di supporto |Articoli correlati |
|---------------|-------------------|---|
|Livello di accesso frequente|Disponibile a livello generale|[Archiviazione BLOB di Azure: livelli di accesso frequente, sporadico e archivio](storage-blob-storage-tiers.md)|
|Livello di accesso sporadico|Disponibile a livello generale|[Archiviazione BLOB di Azure: livelli di accesso frequente, sporadico e archivio](storage-blob-storage-tiers.md)|
|Events|Disponibile a livello generale|[Reazione agli eventi di archiviazione BLOBReacting to Blob storage events](storage-blob-event-overview.md)|
|Metriche (classico)|Disponibile a livello generale|[Metriche di analisi di Archiviazione di Azure (classica)Azure Storage analytics metrics (Classic)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Metriche in Monitoraggio di Azure|Disponibile a livello generale|[Metriche di Archiviazione di Azure in Monitoraggio di Azure](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Comandi di PowerShell per l'archiviazione BLOBBlob storage PowerShell commands|Disponibile a livello generale|[Guida introduttiva: Caricare, scaricare ed elencare BLOB con PowerShellQuickstart: Upload, download, and list blobs with PowerShell](storage-quickstart-blobs-powershell.md)|
|Comandi dell'interfaccia della riga di comando di Archiviazione BLOB di AzureBlob storage Azure CLI|Disponibile a livello generale|[Guida introduttiva: Creare, scaricare ed elencare BLOB con l'interfaccia della riga di comando di AzureQuickstart: Create, download, and list blobs with Azure CLI](storage-quickstart-blobs-cli.md)|
|Api di archiviazione BLOB|Disponibile a livello generale|[Guida introduttiva: Libreria client di archiviazione BLOB di Azure v12 per .NET](storage-quickstart-blobs-dotnet.md)<br>[Guida introduttiva: Gestire i BLOB con Java v12 SDK](storage-quickstart-blobs-java.md)<br>[Guida introduttiva: Gestire i BLOB con Python v12 SDK](storage-quickstart-blobs-python.md)<br>[Guida introduttiva: Gestire i BLOB con JavaScript v12 SDK in Node.js](storage-quickstart-blobs-nodejs.md)|
|Livello di accesso agli archivi|Anteprima|[Archiviazione BLOB di Azure: livelli di accesso frequente, sporadico e archivio](storage-blob-storage-tiers.md)|
|Criteri di gestione del ciclo di vita|Anteprima|[Gestire il ciclo di vita di Archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md)|
|Log di diagnostica|Anteprima|[Registrazione di Analisi archiviazione di Azure](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Feed delle modifiche|Non ancora supportato|[Modificare il supporto dei feed nell'archiviazione BLOB di AzureChange feed support in Azure Blob storage](storage-blob-change-feed.md)|
|Failover dell'account|Non ancora supportato|[Ripristino di emergenza e failover dell'account](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|ACL contenitore BLOB|Non ancora supportato|[Set Container ACL](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Domini personalizzati|Non ancora supportato|[Eseguire il mapping di un dominio personalizzato a un endpoint di archiviazione BLOB di AzureMap a custom domain to an Azure Blob storage endpoint](storage-custom-domain-name.md)|
|Archiviazione non modificabile|Non ancora supportato|[Archiviare dati BLOB critici per il business con archiviazione non modificabileStore business-critical blob data with immutable storage](storage-blob-immutable-storage.md)|
|Snapshot|Non ancora supportato|[Creare e gestire uno snapshot BLOB in .NETCreate and manage a blob snapshot in .NET](storage-blob-snapshots.md)|
|Eliminazione temporanea|Non ancora supportato|[Eliminazione temporanea per i BLOB di Archiviazione di Azure ](storage-blob-soft-delete.md)|
|Siti Web statici|Non ancora supportato|[Hosting di siti Web statici in Archiviazione di Azure](storage-blob-static-website.md)|
|Registrazione in Monitoraggio di AzureLogging in Azure Monitor|Non ancora supportato|Non ancora disponibile|
|Blob di blocchi PremiumPremium block blobs|Non ancora supportato|[Creare un account BlockBlobStorageCreate a BlockBlobStorage account](storage-blob-create-account-block-blob.md)|

## <a name="see-also"></a>Vedere anche

- [Problemi noti con Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Azure services that support Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Open source platforms that support Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Accesso multi-protocollo in Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)