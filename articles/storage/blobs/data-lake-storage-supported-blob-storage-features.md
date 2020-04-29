---
title: Funzionalità di archiviazione BLOB disponibili in Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informazioni sulle funzionalità di archiviazione BLOB che è possibile usare con Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b270ce3cd15dbd1e8dd53bd60376a87d6e08f75c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80637205"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Funzionalità di archiviazione BLOB disponibili in Azure Data Lake Storage Gen2

Le funzionalità di archiviazione BLOB come la [registrazione diagnostica](../common/storage-analytics-logging.md), i [livelli di accesso](storage-blob-storage-tiers.md)e i criteri di gestione del ciclo di vita dell' [archiviazione BLOB](storage-lifecycle-management-concepts.md) ora funzionano con gli account che hanno uno spazio dei nomi gerarchico. Pertanto, è possibile abilitare gli spazi dei nomi gerarchici negli account di archiviazione BLOB senza perdere l'accesso a queste funzionalità.

Questa tabella elenca le funzionalità di archiviazione BLOB che è possibile usare con Azure Data Lake Storage Gen2. Gli elementi visualizzati in queste tabelle verranno modificati nel corso del tempo poiché il supporto continua ad espandersi.

## <a name="supported-blob-storage-features"></a>Funzionalità di archiviazione BLOB supportate

> [!NOTE]
> Il livello di supporto si riferisce solo al modo in cui la funzionalità è supportata con Data Lake Storage Gen2.

|Funzionalità di archiviazione BLOB |Livello di supporto |Articoli correlati |
|---------------|-------------------|---|
|Livello di accesso frequente|Disponibile a livello generale|[Archiviazione BLOB di Azure: livelli di accesso frequente, sporadico e archivio](storage-blob-storage-tiers.md)|
|Livello di accesso sporadico|Disponibile a livello generale|[Archiviazione BLOB di Azure: livelli di accesso frequente, sporadico e archivio](storage-blob-storage-tiers.md)|
|Events|Disponibile a livello generale|[Reazione a eventi di archiviazione BLOB](storage-blob-event-overview.md)|
|Metriche (versione classica)|Disponibile a livello generale|[Metriche di analisi archiviazione di Azure (versione classica)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Metriche in Monitoraggio di Azure|Disponibile a livello generale|[Metriche di Archiviazione di Azure in Monitoraggio di Azure](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Comandi di PowerShell per l'archiviazione BLOB|Disponibile a livello generale|[Guida introduttiva: caricare, scaricare ed elencare BLOB con PowerShell](storage-quickstart-blobs-powershell.md)|
|Comandi dell'interfaccia della riga di comando di Azure archiviazione BLOB|Disponibile a livello generale|[Guida introduttiva: creare, scaricare ed elencare BLOB con interfaccia della riga di comando di Azure](storage-quickstart-blobs-cli.md)|
|Api di archiviazione BLOB|Disponibile a livello generale|[Guida introduttiva: libreria client di archiviazione BLOB di Azure V12 per .NET](storage-quickstart-blobs-dotnet.md)<br>[Guida introduttiva: gestire i BLOB con Java V12 SDK](storage-quickstart-blobs-java.md)<br>[Guida introduttiva: gestire i BLOB con Python V12 SDK](storage-quickstart-blobs-python.md)<br>[Guida introduttiva: gestire i BLOB con JavaScript V12 SDK in node. js](storage-quickstart-blobs-nodejs.md)|
|Livello di accesso archivio|Anteprima|[Archiviazione BLOB di Azure: livelli di accesso frequente, sporadico e archivio](storage-blob-storage-tiers.md)|
|Criteri di gestione del ciclo di vita|Anteprima|[Gestire il ciclo di vita di Archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md)|
|Log di diagnostica|Disponibile a livello generale|[Registrazione di Analisi archiviazione di Azure](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Feed delle modifiche|Non ancora supportato|[Supporto del feed delle modifiche nell'archiviazione BLOB di Azure](storage-blob-change-feed.md)|
|Failover dell'account|Non ancora supportato|[Ripristino di emergenza e failover degli account](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|ACL del contenitore BLOB|Non ancora supportato|[Set Container ACL](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Domini personalizzati|Non ancora supportato|[Eseguire il mapping di un dominio personalizzato a un endpoint di archiviazione BLOB di Azure](storage-custom-domain-name.md)|
|Archiviazione non modificabile|Non ancora supportato|[Archiviare dati BLOB critici per l'azienda con archiviazione non modificabile](storage-blob-immutable-storage.md)|
|Snapshot|Non ancora supportato|[Creare e gestire uno snapshot BLOB in .NET](storage-blob-snapshots.md)|
|Eliminazione temporanea|Non ancora supportato|[Eliminazione temporanea per i BLOB di Archiviazione di Azure ](storage-blob-soft-delete.md)|
|Siti Web statici|Non ancora supportato|[Hosting di siti Web statici in Archiviazione di Azure](storage-blob-static-website.md)|
|Registrazione in monitoraggio di Azure|Non ancora supportato|Non ancora disponibile|
|BLOB in blocchi Premium|Non ancora supportato|[Creare un account BlockBlobStorage](storage-blob-create-account-block-blob.md)|

## <a name="see-also"></a>Vedere anche

- [Problemi noti con Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Servizi di Azure che supportano Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Piattaforme open source che supportano Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Accesso multi-protocollo in Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)