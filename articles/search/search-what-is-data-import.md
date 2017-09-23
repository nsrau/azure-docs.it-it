---
title: Caricamento di dati in Ricerca di Azure | Documentazione di Microsoft
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
ms.date: 05/01/2017
ms.author: ashmaka
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5a601b75ec67824e72d8736bc3c45f8e1231ca86
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---
# <a name="upload-data-to-azure-search"></a>Caricare i dati in Ricerca di Azure
> [!div class="op_single_selector"]
> * [Panoramica](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

È possibile popolare un indice con i dati in due modi. La prima opzione consiste nell'effettuare manualmente il push dei dati nell'indice tramite l'[API REST](search-import-data-rest-api.md) o [.NET SDK](search-import-data-dotnet.md) di Ricerca di Azure. La seconda opzione consiste nell'[indirizzare un'origine dati supportata](search-indexer-overview.md) all'indice e lasciare che Ricerca di Azure esegua automaticamente il pull dei dati.

## <a name="push-data-to-an-index"></a>Push dei dati in un indice
Questo approccio si riferisce all'invio di dati a livello di codice a Ricerca di Azure per renderli disponibili per la ricerca. Per applicazioni con requisiti di latenza molto bassa, ad esempio se è necessario che le operazioni di ricerca siano sincronizzate con i database di inventario dinamici, il modello push è l'unica opzione disponibile.

È possibile usare l'[API REST](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) o [.NET SDK](search-import-data-dotnet.md) per il push dei dati in un indice. Non sono attualmente supportati strumenti per il push dei dati tramite il portale.

Questo approccio è più flessibile rispetto al modello pull, perché si possono caricare i documenti individualmente o in batch, fino a 1000 per batch o 16 MB, a seconda del limite che viene raggiunto per primo. Il modello push consente anche di caricare i documenti in Ricerca di Azure indipendentemente dalla posizione dei dati.

Il formato dei dati riconosciuto da Ricerca di Azure è JSON e tutti i documenti del set di dati devono avere campi mappati ai campi definiti nello schema di indice. 

## <a name="pull-data-into-an-index"></a>Effettuare il pull dei dati in un indice
Il modello pull effettua una ricerca per indicizzazione su un'origine dati supportata e carica automaticamente i dati nell'indice. In Ricerca di Azure questa funzionalità viene implementata tramite *indicizzatori*, attualmente disponibili per [Archivio BLOB](search-howto-indexing-azure-blob-storage.md), [Archivio tabelle](search-howto-indexing-azure-tables.md), [Azure Cosmos DB](http://aka.ms/documentdb-search-indexer), [Database SQL di Azure e SQL Server nelle macchine virtuali di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md). 

Gli indicizzatori connettono un indice a un'origine dati, in genere una tabella, una vista o una struttura equivalente, e mappano i campi di origine ai campi equivalenti nell'indice. Durante l'esecuzione il set di righe viene automaticamente trasformato in JSON e caricato nell'indice specificato. Tutti gli indicizzatori supportano la pianificazione, in modo da consentire di specificare la frequenza dell'aggiornamento dei dati. La maggior parte degli indicizzatori fornisce il rilevamento delle modifiche se l'origine dati lo supporta. Tramite il rilevamento di modifiche ed eliminazioni nei documenti esistenti, oltre al riconoscimento di nuovi documenti, gli indicizzatori eliminano la necessità di gestire attivamente i dati nell'indice. 

La funzionalità di indicizzatore viene esposta nel [portale di Azure](search-import-data-portal.md), nell'[API REST](/rest/api/searchservice/Indexer-operations) e in [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperations). 

Un vantaggio dell'uso del portale consiste nel fatto che Ricerca di Azure può solitamente generare automaticamente uno schema di indice predefinito leggendo i metadati del set di dati di origine. È possibile modificare l'indice generato fino al termine dell'elaborazione dell'indice. In seguito, sono consentite solo le modifiche che non richiedono la reindicizzazione. Se le modifiche da apportare influiscono direttamente sullo schema, è necessario ricompilare l'indice. 

Al termine del popolamento dell'indice, è possibile usare **Esplora ricerche** sulla barra dei comandi del portale come passaggio di verifica.

## <a name="query-an-index-using-search-explorer"></a>Eseguire query in un indice usando Esplora ricerche

Un modo rapido per eseguire un controllo preliminare sul caricamento del documento consiste nell'usare **Esplora ricerche** nel portale. Esplora ricerche consente di eseguire query su un indice senza che sia necessario scrivere codice. L'esperienza di ricerca è basata sulle impostazioni predefinite, ad esempio la [sintassi semplice](/rest/api/searchservice/simple-query-syntax-in-azure-search) e il [parametro di query searchMode](/rest/api/searchservice/search-documents) predefinito. I risultati vengono restituiti in JSON, in modo che sia possibile esaminare l'intero documento.

> [!TIP]
> Numerosi [esempi di codice di Ricerca di Azure](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) includono set di dati incorporati o immediatamente disponibili, che consentono di iniziare con facilità. Il portale offre anche un indicizzatore e un'origine dati di esempio, costituita da un piccolo set di dati immobiliari, denominato "realestate-us-sample". Quando si esegue l'indicizzatore preconfigurato nell'origine dati di esempio, viene creato un indice, che viene caricato con i documenti su cui possono essere eseguite query in Esplora ricerche o tramite codice scritto dall'utente.
