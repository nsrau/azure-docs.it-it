---
title: Eseguire ricerche nel contenuto di archiviazione BLOB di Azure
titleSuffix: Azure Cognitive Search
description: Informazioni su come estrarre il testo dai BLOB di Azure e renderlo ricercabile full-text in un indice di ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: f61bf635cc61a2153a7bb016ef4b4711d7ba7391
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91355296"
---
# <a name="search-over-azure-blob-storage-content"></a>Eseguire ricerche nel contenuto di archiviazione BLOB di Azure

La ricerca in un'ampia gamma di tipi di contenuto inclusi nell'archiviazione BLOB di Azure può rivelarsi un problema complesso da risolvere. In questo articolo viene esaminato il flusso di lavoro di base per estrarre contenuto e metadati dai BLOB e inviarli a un indice di ricerca in Azure ricerca cognitiva. Per eseguire query sull'indice risultante è possibile utilizzare la ricerca full-text.

> [!NOTE]
> Si ha già familiarità con il flusso di lavoro e la composizione? [Come configurare un indicizzatore BLOB](search-howto-indexing-azure-blob-storage.md) è il passaggio successivo.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Cosa significa aggiungere la ricerca full-text ai dati BLOB

Azure ricerca cognitiva è un servizio di ricerca che supporta l'indicizzazione e i carichi di lavoro di query su indici definiti dall'utente che contengono il contenuto ricercabile remoto ospitato nel cloud. Il percorso di condivisione del contenuto ricercabile con il motore di query è necessario per le prestazioni, restituendo i risultati a una velocità prevista dagli utenti dalle query di ricerca.

Ricerca cognitiva si integra con l'archiviazione BLOB di Azure a livello di indicizzazione, importando il contenuto BLOB come documenti di ricerca indicizzati in *indici invertiti* e altre strutture di query che supportano query di testo e espressioni di filtro gratuite. Poiché il contenuto del BLOB è indicizzato in un indice di ricerca, è possibile usare l'intera gamma di funzionalità di query in Azure ricerca cognitiva per trovare informazioni nel contenuto del BLOB.

Gli input sono i BLOB, in un singolo contenitore, nell'archivio BLOB di Azure. I BLOB possono essere pressoché qualunque tipo di dati di testo. Se i BLOB contengono immagini, è possibile aggiungere l' [arricchimento di intelligenza artificiale all'indicizzazione BLOB](search-blob-ai-integration.md) per creare ed estrarre il testo dalle immagini.

L'output è sempre un indice di Ricerca cognitiva di Azure, usato per la ricerca, il recupero e l'esplorazione veloci del testo nelle applicazioni client. Al centro c'è l'architettura della pipeline di indicizzazione vera e propria. La pipeline è basata sulla funzionalità *indicizzatore*, descritta più avanti in questo articolo.

Quando l'indice viene creato e popolato, esiste indipendentemente dal contenitore BLOB, ma è possibile rieseguire le operazioni di indicizzazione per aggiornare l'indice in base ai documenti modificati. Le informazioni di data e ora nei singoli BLOB vengono usate per il rilevamento delle modifiche. Come meccanismo di aggiornamento si può optare per l'esecuzione pianificata o per l'indicizzazione su richiesta.

## <a name="required-resources"></a>Risorse necessarie

Sono necessari sia ricerca cognitiva di Azure che archiviazione BLOB di Azure. All'interno dell'archivio BLOB è necessario un contenitore che fornisca il contenuto di origine.

Si può iniziare direttamente nella pagina del portale dell'account di archiviazione. Nella pagina di spostamento a sinistra, in **Servizio BLOB** fare clic su **Aggiungi Ricerca cognitiva di Azure** per creare un nuovo servizio o selezionarne uno esistente. 

Dopo aver aggiunto Ricerca cognitiva di Azure all'account di archiviazione, è possibile seguire il processo standard per indicizzare i dati BLOB. È consigliabile usare la procedura guidata **Importa dati** in Ricerca cognitiva di Azure per un'introduzione iniziale semplice oppure chiamare le API REST usando uno strumento come Postman. Questa esercitazione illustra i passaggi per chiamare l'API REST in Postman: [Indicizzare e cercare dati semistrutturati (BLOB JSON) in Ricerca cognitiva di Azure](search-semi-structured-data.md). 

## <a name="use-a-blob-indexer"></a>Usare un indicizzatore BLOB

Un *indicizzatore* è un sottoservizio compatibile con l'origine dati in ricerca cognitiva, dotato di logica interna per il campionamento dei dati, la lettura dei dati dei metadati, il recupero dei dati e la serializzazione dei dati dai formati nativi ai documenti JSON per l'importazione successiva. 

I BLOB in Archiviazione di Azure vengono indicizzati mediante l'[indicizzatore per l'archivio BLOB di Ricerca cognitiva di Azure](search-howto-indexing-azure-blob-storage.md). Si può richiamare questo indicizzatore usando la procedura guidata **Importa dati**, un'API REST o .NET SDK. Nel codice, questo indicizzatore si usa impostando il tipo e fornendo le informazioni di connessione, che includono un account di Archiviazione di Azure e un contenitore BLOB. È possibile suddividere in subset i BLOB creando una directory virtuale, che si potrà poi passare come parametro, oppure filtrando in base a un'estensione del tipo di file.

Un indicizzatore esegue il "cracking di documenti", aprendo un BLOB per esaminare il contenuto. Dopo la connessione all'origine dati, è il primo passaggio della pipeline. Per i dati BLOB, è la fase in cui vengono rilevati PDF, documenti di Office e altri tipi di contenuto. Il cracking di documenti con estrazione del testo non prevede alcun addebito. Se i BLOB contengono contenuto di tipo immagine, le immagini vengono ignorate, a meno che non si [aggiunga l'arricchimento tramite intelligenza artificiale](search-blob-ai-integration.md). L'indicizzazione standard si applica solo al contenuto di testo.

L'indicizzatore BLOB accetta parametri di configurazione e supporta il rilevamento delle modifiche se i dati sottostanti forniscono informazioni sufficienti. Altre informazioni sulle funzionalità di base sono disponibili in [Indicizzatore per l'archivio BLOB di Ricerca cognitiva di Azure](search-howto-indexing-azure-blob-storage.md).

### <a name="supported-content-types"></a>Tipi di contenuto supportati

Eseguendo un indicizzatore BLOB su un contenitore, è possibile estrarre testo e metadati dai tipi di contenuto seguenti con una singola query:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Indicizzazione di metadati BLOB

Uno scenario comune che semplifica l'ordinamento nei BLOB di qualsiasi tipo di contenuto consiste nell'indicizzare i metadati personalizzati e le proprietà di sistema per ogni BLOB. In questo modo, le informazioni per tutti i BLOB vengono indicizzate indipendentemente dal tipo di documento, archiviate in un indice nel servizio di ricerca. Usando il nuovo indice, è quindi possibile ordinare, filtrare e ottimizzare tutto il contenuto dell'archivio BLOB.

> [!NOTE]
> I tag indice dei BLOB sono indicizzati in modo nativo dal servizio di archiviazione BLOB ed esposti per l'esecuzione di query. Se gli attributi chiave/valore dei BLOB richiedono funzionalità di indicizzazione e filtro, è consigliabile usare i tag indice dei BLOB anziché i metadati.
>
> Per altre informazioni sull'indice BLOB, vedere [Manage and find data on Azure Blob Storage with Blob Index](../storage/blobs/storage-manage-find-blobs.md) (Gestire e trovare i dati nell'archiviazione BLOB di Azure con l'indice BLOB).

### <a name="indexing-json-blobs"></a>Indicizzazione di BLOB JSON

È possibile configurare gli indicizzatori in modo da estrarre il contenuto strutturato trovato nei BLOB che contengono JSON. Un indicizzatore può leggere BLOB JSON e analizzare il contenuto strutturato nei campi appropriati di un documento di ricerca. Gli indicizzatori possono anche usare BLOB che contengono una matrice di oggetti JSON ed eseguire il mapping di ogni elemento a un documento di ricerca separato. È possibile impostare una modalità di analisi per influenzare il tipo di oggetto JSON creato dall'indicizzatore.

## <a name="search-blob-content-in-a-search-index"></a>Cercare contenuto BLOB in un indice di ricerca 

L'output di un indicizzatore è un indice di ricerca, usato per l'esplorazione interattiva con testo libero e query filtrate in un'app client. Per l'esplorazione e la verifica iniziale del contenuto, è consigliabile iniziare con [Esplora ricerche](search-explorer.md) nel portale per esaminare la struttura del documento. In Esplora ricerche è possibile usare una [sintassi di query semplice](query-simple-syntax.md), una [sintassi di query completa](query-lucene-syntax.md) e una [sintassi delle espressioni di filtro](query-odata-filter-orderby-syntax.md).

Una soluzione più permanente consiste nel raccogliere gli input della query e presentare la risposta sotto forma di risultati della ricerca in un'applicazione client. L'esercitazione per C# seguente illustra come creare un'applicazione di ricerca: [Creare la prima applicazione in Ricerca cognitiva di Azure](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>Passaggi successivi

+ [Caricare, scaricare ed elencare BLOB con il portale di Azure (Archiviazione BLOB di Azure)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [Configurare un indicizzatore BLOB (Ricerca cognitiva di Azure)](search-howto-indexing-azure-blob-storage.md)