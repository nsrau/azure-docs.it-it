---
title: Aggiungere la ricerca full-text all'archivio BLOB di Azure
titleSuffix: Azure Search
description: Estrai il contenuto e Aggiungi la struttura ai BLOB di Azure durante la compilazione di un indice di ricerca full-text in ricerca di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 1aec65ab08cd1c0711e51a222a8e674ef56ef508
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2019
ms.locfileid: "72312193"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Aggiungere la ricerca full-text ai dati BLOB di Azure con ricerca di Azure

La ricerca in un'ampia gamma di tipi di contenuto inclusi nell'archiviazione BLOB di Azure può rivelarsi un problema complesso da risolvere. Tuttavia, è possibile indicizzare e cercare il contenuto dei BLOB in pochi clic usando [ricerca di Azure](search-what-is-azure-search.md). Ricerca di Azure offre integrazione incorporata per l'indicizzazione dell'archiviazione BLOB tramite un [*indicizzatore BLOB*](search-howto-indexing-azure-blob-storage.md) che aggiunge funzionalità per l'indicizzazione in grado di riconoscere origini dati.

## <a name="supported-content-types"></a>Tipi di contenuto supportati

Eseguendo un indicizzatore BLOB su un contenitore, è possibile estrarre testo e metadati dai tipi di contenuto seguenti con una singola query:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Facoltativamente, è possibile alleghi l' [arricchimento di intelligenza artificiale](search-blob-ai-integration.md) sotto forma di un *skillt* per creare nuove informazioni e strutture dai BLOB, incluse le rappresentazioni di testo dei file di immagine. L'aggiunta di arricchimento intelligenza artificiale espande l'elenco dei tipi di contenuto per includere JPEG e PNG. Consente di aggiungere competenze di elaborazione di immagini, ad esempio il [riconoscimento ottico dei caratteri (OCR)](cognitive-search-skill-ocr.md) e l'identificazione di [funzionalità visive](cognitive-search-skill-image-analysis.md) che consentono di indicizzare il contenuto visivo trovato in ogni immagine.

## <a name="search-through-your-blob-metadata"></a>Ricerca nei metadati dei BLOB
Uno scenario comune che semplifica l'ordinamento nei BLOB di qualsiasi tipo di contenuto consiste nell'indicizzare i metadati personalizzati e le proprietà di sistema per ogni BLOB. In questo modo, le informazioni per tutti i BLOB vengono indicizzate indipendentemente dal tipo di documento. È quindi possibile ordinare, filtrare e ottimizzare tutto il contenuto dell'archiviazione BLOB.

[Altre informazioni sull'indicizzazione dei metadati dei BLOB.](https://aka.ms/azsblobmetadataindexing)

## <a name="index-and-search-through-json-blobs"></a>Indicizzare e cercare contenuto in BLOB JSON
È possibile configurare Ricerca di Azure in modo da estrarre il contenuto strutturato dei BLOB che contengono JSON. Ricerca di Azure può leggere BLOB JSON e analizzare il contenuto strutturato nei campi appropriati di un documento di Ricerca di Azure. Ricerca di Azure può anche usare BLOB che contengono una matrice di oggetti JSON ed eseguire il mapping di ogni elemento a un documento di Ricerca di Azure separato.

L'analisi JSON non è attualmente configurabile tramite il portale. [Altre informazioni sull'analisi JSON in Ricerca di Azure.](https://aka.ms/azsjsonblobindexing)

## <a name="quickstart"></a>Guida introduttiva
È possibile aggiungere Ricerca di Azure ai BLOB direttamente dalla pagina del portale dell'archiviazione BLOB.

![](./media/search-blob-storage-integration/blob-blade.png)

Fare clic su **Aggiungi Ricerca di Azure** per avviare un flusso in cui è possibile selezionare un servizio di Ricerca di Azure esistente oppure crearne uno nuovo. Se si crea un nuovo servizio, si viene disconnessi dal portale dell'account di archiviazione. È possibile tornare alla pagina del portale dell'archiviazione e riselezionare l'opzione **Aggiungi Ricerca di Azure**, in cui è possibile selezionare il servizio esistente.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sull'indicizzatore BLOB di Ricerca di Azure sono disponibili nella [documentazione](https://aka.ms/azsblobindexer) completa.
