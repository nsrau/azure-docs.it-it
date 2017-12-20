---
title: Importazione di dati in Ricerca di Azure | Microsoft Docs
description: Informazioni su come caricare i dati in un indice in Ricerca di Azure
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: 
ms.assetid: aa8d47c1-4ae6-4209-a8ce-48d5a9474707
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 11/01/2017
ms.author: ashmaka
ms.openlocfilehash: ebf7319f0017b4adef25fe5840864e002c88fea7
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="data-import-in-azure-search"></a>Importazione di dati in Ricerca di Azure
> [!div class="op_single_selector"]
> * [Panoramica](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

In Ricerca di Azure vengono eseguite query sul contenuto caricato in un [indice di ricerca](search-what-is-an-index.md). Questo articolo esamina i due approcci di base per caricare il contenuto in un indice: *eseguire il push* dei dati nell'indice a livello di codice o puntare a un [indicizzatore di Ricerca di Azure](search-indexer-overview.md) in un'origine dati supportata per *eseguire il pull* nei dati.

## <a name="pushing-data-to-an-index"></a>Push dei dati in un indice
Il modello basato sul push, usato per inviare a livello di codice i dati a Ricerca di Azure, è l'approccio più flessibile. Prima di tutto non ha restrizioni per il tipo di origine dati. È possibile eseguire il push di qualsiasi set di dati costituito da documenti JSON a un indice di Ricerca di Azure, presumendo che ai campi definiti nello schema dell'indice venga eseguito il mapping dei campi di ogni documento del set di dati. In secondo luogo non ha restrizioni per la frequenza di esecuzione. È possibile eseguire il push delle modifiche a un indice ogni volta che si vuole. Per applicazioni con requisiti di latenza molto bassa, ad esempio se è necessario che le operazioni di ricerca siano sincronizzate con i database di inventario dinamici, il modello push è l'unica opzione disponibile.

Questo approccio è più flessibile rispetto al modello pull, perché si possono caricare i documenti individualmente o in batch, fino a 1000 per batch o 16 MB, a seconda del limite che viene raggiunto per primo. Il modello push consente anche di caricare i documenti in Ricerca di Azure indipendentemente dalla posizione dei dati.

### <a name="how-to-push-data-to-an-azure-search-index"></a>Come eseguire il push dei dati a un indice di Ricerca di Azure

Per caricare uno o più documenti in un indice, è possibile usare le API seguenti:

+ [Aggiungere, aggiornare o eliminare documenti (API REST)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Classe indexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) o [classe indexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Non sono attualmente supportati strumenti per il push dei dati tramite il portale.

Per un'introduzione a ogni metodologia, vedere [Importare dati usando REST](search-import-data-rest-api.md) o [Importare dati usando .NET](search-import-data-dotnet.md).


## <a name="pulling-data-into-an-index"></a>Pull dei dati in un indice
Il modello pull effettua una ricerca per indicizzazione su un'origine dati supportata e carica automaticamente i dati nell'indice. In Ricerca di Azure questa funzionalità viene implementata tramite gli *indicizzatori*, attualmente disponibili per queste piattaforme:

+ [Archiviazione BLOB](search-howto-indexing-azure-blob-storage.md)
+ [Archiviazione tabelle](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](http://aka.ms/documentdb-search-indexer)
+ [Database SQL di Azure e SQL Server in VM di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Gli indicizzatori connettono un indice a un'origine dati, in genere una tabella, una vista o una struttura equivalente, e mappano i campi di origine ai campi equivalenti nell'indice. Durante l'esecuzione il set di righe viene automaticamente trasformato in JSON e caricato nell'indice specificato. Tutti gli indicizzatori supportano la pianificazione, in modo da consentire di specificare la frequenza dell'aggiornamento dei dati. La maggior parte degli indicizzatori fornisce il rilevamento delle modifiche se l'origine dati lo supporta. Tramite il rilevamento di modifiche ed eliminazioni nei documenti esistenti, oltre al riconoscimento di nuovi documenti, gli indicizzatori eliminano la necessità di gestire attivamente i dati nell'indice. 


### <a name="how-to-pull-data-into-an-azure-search-index"></a>Come eseguire il pull dei dati in un indice di Ricerca di Azure

La funzionalità di indicizzatore viene esposta nel [portale di Azure](search-import-data-portal.md), nell'[API REST](/rest/api/searchservice/Indexer-operations) e in [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperations). 

Un vantaggio dell'uso del portale consiste nel fatto che Ricerca di Azure può solitamente generare automaticamente uno schema di indice predefinito leggendo i metadati del set di dati di origine. È possibile modificare l'indice generato fino al termine dell'elaborazione dell'indice. In seguito, sono consentite solo le modifiche che non richiedono la reindicizzazione. Se le modifiche da apportare influiscono direttamente sullo schema, è necessario ricompilare l'indice. 

## <a name="verify-data-import-with-search-explorer"></a>Verificare l'importazione dei dati con Esplora ricerche

Un modo rapido per eseguire un controllo preliminare sul caricamento del documento consiste nell'usare **Esplora ricerche** nel portale. Esplora ricerche consente di eseguire query su un indice senza che sia necessario scrivere codice. L'esperienza di ricerca è basata sulle impostazioni predefinite, ad esempio la [sintassi semplice](/rest/api/searchservice/simple-query-syntax-in-azure-search) e il [parametro di query searchMode](/rest/api/searchservice/search-documents) predefinito. I risultati vengono restituiti in JSON, in modo che sia possibile esaminare l'intero documento.

> [!TIP]
> Numerosi [esempi di codice di Ricerca di Azure](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) includono set di dati incorporati o immediatamente disponibili, che consentono di iniziare con facilità. Il portale offre anche un indicizzatore e un'origine dati di esempio, costituita da un piccolo set di dati immobiliari, denominato "realestate-us-sample". Quando si esegue l'indicizzatore preconfigurato nell'origine dati di esempio, viene creato un indice, che viene caricato con i documenti su cui possono essere eseguite query in Esplora ricerche o tramite codice scritto dall'utente.

## <a name="see-also"></a>Vedere anche

+ [Panoramica degli indicizzatori](search-indexer-overview.md)
+ [Procedura dettagliata per il portale: creare, caricare ed eseguire query su un indice](search-get-started-portal.md)