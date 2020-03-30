---
title: Aggiungere la ricerca full-text ad Archiviazione BLOB di AzureAdd full text search to Azure Blob Storage
titleSuffix: Azure Cognitive Search
description: Estrarre contenuto e aggiungere struttura ai BLOB di Azure quando si crea un indice di ricerca full-text in Azure Cognitive earch.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: af7d04bd74ada296b9f0e0f7c149c2a781cec579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73496467"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>Aggiungere la ricerca full-text ai dati BLOB di Azure usando Ricerca cognitiva di AzureAdd full text search to Azure blob data using Azure Cognitive Search

La ricerca in un'ampia gamma di tipi di contenuto inclusi nell'archiviazione BLOB di Azure può rivelarsi un problema complesso da risolvere. Tuttavia, è possibile indicizzare ed eseguire ricerche nel contenuto dei BLOB in pochi clic usando [Ricerca cognitiva](search-what-is-azure-search.md)di Azure. Ricerca cognitiva di Azure include l'integrazione incorporata per l'indicizzazione nell'archiviazione BLOB tramite un [*indicizzatore BLOB*](search-howto-indexing-azure-blob-storage.md) che aggiunge funzionalità in grado di riconoscere l'origine dati all'indicizzazione.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Significato dell'aggiunta di una ricerca full-text ai dati BLOB

Ricerca cognitiva di Azure è un servizio di ricerca cloud che offre motori di indicizzazione e query che operano su indici definiti dall'utente ospitati nel servizio di ricerca. La co-localizzazione del contenuto ricercabile con il motore di query nel cloud è necessaria per le prestazioni, restituendo risultati a una velocità che gli utenti si aspettano dalle query di ricerca.

Ricerca cognitiva di Azure si integra con l'archiviazione BLOB di Azure a livello di indicizzazione, importando il contenuto BLOB come documenti di ricerca indicizzati in *indici invertiti* e altre strutture di query che supportano query di testo libero ed espressioni di filtro. Poiché il contenuto BLOB è indicizzato in un indice di ricerca, l'accesso al contenuto BLOB può sfruttare l'intera gamma di funzionalità di query in Ricerca cognitiva di Azure.Because your blob content is indexed into a search index, access to blob content can leverage the full range of query features in Azure Cognitive Search.

Una volta creato e popolato, l'indice esiste indipendentemente dal contenitore BLOB, ma è possibile rieseguire le operazioni di indicizzazione per aggiornare l'indice con le modifiche apportate al contenitore sottostante. Le informazioni sul timestamp sui singoli BLOB vengono usate per il rilevamento delle modifiche. È possibile scegliere l'esecuzione pianificata o l'indicizzazione su richiesta come meccanismo di aggiornamento.

Gli input sono i BLOB, in un singolo contenitore, nell'archiviazione BLOB di Azure.Inputs are your blobs, in a single container, in Azure Blob storage. I BLOB possono essere quasi tutti i tipi di dati di testo. Se i BLOB contengono immagini, è possibile aggiungere [l'arricchimento dell'iA all'indicizzazione BLOB](search-blob-ai-integration.md) per creare ed estrarre testo dalle immagini.

L'output è sempre un indice di Ricerca cognitiva di Azure, usato per la ricerca di testo veloce, il recupero e l'esplorazione nelle applicazioni client. In mezzo è l'architettura della pipeline di indicizzazione stessa. La pipeline si basa sulla funzionalità *dell'indicizzatore,* descritta più avanti in questo articolo.

## <a name="start-with-services"></a>Inizia con i servizi

Sono necessari Ricerca cognitiva di Azure e archiviazione BLOB di Azure.You need Azure Cognitive Search and Azure Blob storage. All'interno dell'archiviazione BLOB è necessario un contenitore che fornisce contenuto di origine.

È possibile iniziare direttamente nella pagina del portale dell'account di archiviazione. Nella pagina di spostamento sinistra, in **Servizio BLOB** fare clic su Aggiungi **Ricerca cognitiva** di Azure per creare un nuovo servizio o selezionarne uno esistente. 

Dopo aver aggiunto Ricerca cognitiva di Azure all'account di archiviazione, è possibile seguire il processo standard per indicizzare i dati BLOB. È consigliabile la procedura guidata Importa dati in Ricerca cognitiva di Azure per una semplice introduzione iniziale o chiamare le API REST usando uno strumento come Postman.We recommend the **Import data** wizard in Azure Cognitive Search for an easy initial introduction, or call the REST APIs using a tool like Postman. Questa esercitazione illustra i passaggi per chiamare l'API REST in Postman: [indicizza e cerca dati semistrutturati (BLOB JSON) in Ricerca cognitiva](search-semi-structured-data.md)di Azure.This tutorial walks you through the steps of calling the REST API in Postman: Index and search semi-structured data (JSON blobs) in Azure Cognitive Search . 

## <a name="use-a-blob-indexer"></a>Usare un indicizzatore BLOBUse a Blob indexer

Un *indicizzatore* è un servizio secondario in grado di riconoscere le origini dati dotato di logica interna per il campionamento dei dati, la lettura dei dati dei metadati, il recupero dei dati e la serializzazione dei dati da formati nativi in documenti JSON per l'importazione successiva. 

I BLOB in Archiviazione di Azure vengono indicizzati usando [l'indicizzatore](search-howto-indexing-azure-blob-storage.md)di archiviazione BLOB di Ricerca cognitiva di Azure.Blobs in Azure Storage are indexed using the Azure Cognitive Search Blob storage indexer . È possibile richiamare questo indicizzatore usando la procedura guidata **Importa dati,** un'API REST o .NET SDK. Nel codice si usa questo indicizzatore impostando il tipo e fornendo informazioni di connessione che includono un account di Archiviazione di Azure insieme a un contenitore BLOB. È possibile sottoinsiemi dei BLOB creando una directory virtuale, che è quindi possibile passare come parametro, oppure filtrando in base a un'estensione di file.

Un indicizzatore esegue il "cracking del documento", aprendo un BLOB per esaminare il contenuto. Dopo la connessione all'origine dati, è il primo passaggio nella pipeline. Per i dati BLOB, è qui che vengono rilevati PDF, documenti d'ufficio e altri tipi di contenuto. Il cracking di documenti con l'estrazione del testo è gratuito. Se i BLOB includono contenuto di immagine, le immagini vengono ignorate a meno che non si [aggiunge l'arricchimento dell'iA.](search-blob-ai-integration.md) L'indicizzazione standard si applica solo al contenuto di testo.

L'indicizzatore BLOB viene fornito con i parametri di configurazione e supporta il rilevamento delle modifiche se i dati sottostanti forniscono informazioni sufficienti. Per altre informazioni sulle funzionalità di base [dell'indicizzatore](search-howto-indexing-azure-blob-storage.md)di archiviazione BLOB di Ricerca cognitiva di Azure, vedere .

### <a name="supported-content-types"></a>Tipi di contenuto supportati

Eseguendo un indicizzatore BLOB su un contenitore, è possibile estrarre testo e metadati dai tipi di contenuto seguenti con una singola query:By running a Blob indexer over a container, you can extract text and metadata from the following content types with a single query:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Indicizzazione dei metadati BLOBIndexing blob metadata

Uno scenario comune che semplifica l'ordinamento dei BLOB di qualsiasi tipo di contenuto consiste nell'indicizzare sia i metadati personalizzati che le proprietà di sistema per ogni BLOB. In questo modo, le informazioni per tutti i BLOB vengono indicizzate indipendentemente dal tipo di documento, archiviate in un indice nel servizio di ricerca. Usando il nuovo indice, è quindi possibile procedere all'ordinamento, al filtro e al facet in tutto il contenuto di archiviazione BLOB.

### <a name="indexing-json-blobs"></a>Indicizzazione di BLOB JSONIndexing JSON blobs
Gli indicizzatori possono essere configurati per estrarre il contenuto strutturato trovato nei BLOB che contengono JSON. Un indicizzatore può leggere i BLOB JSON e analizzare il contenuto strutturato nei campi appropriati di un documento di ricerca. Gli indicizzatori possono anche accettare BLOB che contengono una matrice di oggetti JSON ed eseguire il mapping di ogni elemento a un documento di ricerca separato. È possibile impostare una modalità di analisi per influire sul tipo di oggetto JSON creato dall'indicizzatore.

## <a name="search-blob-content-in-a-search-index"></a>Cercare contenuto BLOB in un indice di ricercaSearch blob content in a search index 

L'output di un'indicizzazione è un indice di ricerca, usato per l'esplorazione interattiva tramite testo libero e query filtrate in un'app client. Per l'esplorazione iniziale e la verifica del contenuto, è consigliabile iniziare da [Esplora ricerche](search-explorer.md) nel portale per esaminare la struttura del documento. È possibile utilizzare [la sintassi](query-simple-syntax.md)di query semplice, la [sintassi di query completa](query-lucene-syntax.md)e la [sintassi](query-odata-filter-orderby-syntax.md) delle espressioni di filtro in Esplora ricerche.

Una soluzione più permanente consiste nel raccogliere gli input delle query e presentare la risposta come risultati della ricerca in un'applicazione client. Nell'esercitazione di Cè seguente viene illustrato come creare un'applicazione di ricerca: [Creare la prima applicazione in Ricerca cognitiva](tutorial-csharp-create-first-app.md)di Azure .

## <a name="next-steps"></a>Passaggi successivi

+ [Caricare, scaricare ed elencare BLOB con il portale di Azure (Archiviazione BLOB di Azure)Upload, download, and list blobs with the Azure portal (Azure Blob storage)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Configurare un indicizzatore BLOB (Ricerca cognitiva di Azure)Set up a blob indexer (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
