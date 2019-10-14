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
ms.openlocfilehash: 7e5eb73cc6abc72689bbc674b29f4d288dd66b6f
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302901"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Aggiungere la ricerca full-text ai dati BLOB di Azure con ricerca di Azure

La ricerca in un'ampia gamma di tipi di contenuto inclusi nell'archiviazione BLOB di Azure può rivelarsi un problema complesso da risolvere. Tuttavia, è possibile indicizzare e cercare il contenuto dei BLOB in pochi clic usando Ricerca di Azure. La ricerca nell'archiviazione BLOB richiede il provisioning di un servizio di Ricerca di Azure. I diversi limiti del servizio e i piani tariffari di Ricerca di Azure sono disponibili nella [pagina dei prezzi](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>Che cos'è la Ricerca di Azure?
[Ricerca di Azure](https://aka.ms/whatisazsearch) è un servizio di ricerca che aiuta gli sviluppatori ad aggiungere solide esperienze di ricerca full-text ad applicazioni Web e per dispositivi mobili. In qualità di servizio, Ricerca di Azure elimina la necessità di gestire qualsiasi infrastruttura di ricerca, offrendo un [contratto di servizio con tempo di attività del 99,9%](https://aka.ms/azuresearchsla).

## <a name="index-and-search-enterprise-document-formats"></a>Indicizzazione e ricerca in formati di documenti aziendali
Con il supporto per l'[estrazione di documenti](https://aka.ms/azsblobindexer) nell'archiviazione BLOB di Azure, è possibile indicizzare il contenuto seguente:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Estraendo testo e metadati da questi tipi di file, è possibile cercare in più formati di file con un'unica query. 

## <a name="search-through-your-blob-metadata"></a>Ricerca nei metadati dei BLOB
Uno scenario comune che semplifica l'ordinamento nei BLOB di qualsiasi tipo di contenuto consiste nell'indicizzare i metadati personalizzati e le proprietà di sistema per ogni BLOB. In questo modo, le informazioni per tutti i BLOB vengono indicizzate indipendentemente dal tipo di documento. È quindi possibile ordinare, filtrare e ottimizzare tutto il contenuto dell'archiviazione BLOB.

[Altre informazioni sull'indicizzazione dei metadati dei BLOB.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Ricerca di immagini
Le funzionalità di ricerca full-text, esplorazione in base a facet e ordinamento di Ricerca di Azure possono ora essere applicate ai metadati delle immagini archiviate in BLOB.

La ricerca cognitiva include competenze di elaborazione di immagini quali il [riconoscimento ottico dei caratteri (OCR)](cognitive-search-skill-ocr.md) e l'identificazione di [funzionalità visive](cognitive-search-skill-image-analysis.md) che consentono di indicizzare il contenuto visivo trovato in ogni immagine.

## <a name="index-and-search-through-json-blobs"></a>Indicizzare e cercare contenuto in BLOB JSON
È possibile configurare Ricerca di Azure in modo da estrarre il contenuto strutturato dei BLOB che contengono JSON. Ricerca di Azure può leggere BLOB JSON e analizzare il contenuto strutturato nei campi appropriati di un documento di Ricerca di Azure. Ricerca di Azure può anche usare BLOB che contengono una matrice di oggetti JSON ed eseguire il mapping di ogni elemento a un documento di Ricerca di Azure separato.

L'analisi JSON non è attualmente configurabile tramite il portale. [Altre informazioni sull'analisi JSON in Ricerca di Azure.](https://aka.ms/azsjsonblobindexing)

## <a name="quickstart"></a>Guida introduttiva
È possibile aggiungere Ricerca di Azure ai BLOB direttamente dalla pagina del portale dell'archiviazione BLOB.

![](./media/search-blob-storage-integration/blob-blade.png)

Fare clic su **Aggiungi Ricerca di Azure** per avviare un flusso in cui è possibile selezionare un servizio di Ricerca di Azure esistente oppure crearne uno nuovo. Se si crea un nuovo servizio, si viene disconnessi dal portale dell'account di archiviazione. È possibile tornare alla pagina del portale dell'archiviazione e riselezionare l'opzione **Aggiungi Ricerca di Azure**, in cui è possibile selezionare il servizio esistente.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sull'indicizzatore BLOB di Ricerca di Azure sono disponibili nella [documentazione](https://aka.ms/azsblobindexer) completa.
