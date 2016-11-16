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
ms.date: 08/29/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2ebe71b3456420f29ffe06bc15471d3fd2121f8c


---
# <a name="upload-data-to-azure-search"></a>Caricare i dati in Ricerca di Azure
> [!div class="op_single_selector"]
> * [Panoramica](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

## <a name="data-upload-models-in-azure-search"></a>Modelli di caricamento dei dati in Ricerca di Azure
Esistono due modi per popolare l'indice di Ricerca di Azure con i dati. La prima opzione consiste nell'effettuare manualmente il push dei dati nell'indice tramite l'[API REST](search-import-data-rest-api.md) o [.NET SDK](search-import-data-dotnet.md) di Ricerca di Azure. La seconda opzione consiste nell' [indirizzare un'origine dati supportata](search-indexer-overview.md) all'indice di Ricerca di Azure e lasciare che il servizio effettui automaticamente il pull dei dati nel servizio di ricerca.

Questa guida include solo istruzioni sull'uso del modello push di caricamento dei dati, supportato solo nell'[API REST](search-import-data-rest-api.md) e in [.NET SDK](search-import-data-dotnet.md), ma è comunque possibile apprendere altre informazioni sul modello pull riportate di seguito.

### <a name="push-data-to-an-index"></a>Push dei dati in un indice
Questo approccio si riferisce all'invio di dati a livello di codice a Ricerca di Azure per renderli disponibili per la ricerca. Per applicazioni con requisiti di latenza molto bassa, ad esempio se è necessario che le operazioni di ricerca siano sincronizzate con i database di inventario dinamici, il modello push è l'unica opzione disponibile.

È possibile usare l'[API REST](https://msdn.microsoft.com/library/azure/dn798930.aspx) o [.NET SDK](search-import-data-dotnet.md) per il push dei dati in un indice. Non sono attualmente supportati strumenti per il push dei dati tramite il portale.

Questo approccio è più flessibile rispetto al modello pull, perché si possono caricare i documenti individualmente o in batch, fino a 1000 per batch o 16 MB, a seconda del limite che viene raggiunto per primo. Il modello push consente anche di caricare i documenti in Ricerca di Azure indipendentemente dalla posizione dei dati.

### <a name="pull-data-into-an-index"></a>Effettuare il pull dei dati in un indice
Il modello pull effettua una ricerca per indicizzazione su un'origine dati supportata e carica automaticamente i dati nell'indice di Ricerca di Azure. Tramite il rilevamento di modifiche ed eliminazioni nei documenti esistenti, oltre al riconoscimento di nuovi documenti, gli indicizzatori eliminano la necessità di gestire attivamente i dati nell'indice.

In Ricerca di Azure questa funzionalità viene implementata tramite *indicizzatori*, attualmente disponibili per [Archivio BLOB (anteprima)](search-howto-indexing-azure-blob-storage.md), [DocumentDB](http://aka.ms/documentdb-search-indexer), [Database SQL di Azure e SQL Server nelle VM di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md).

La funzionalità di indicizzatore viene esposta nel [portale di Azure ](search-import-data-portal.md) e nell'[API REST](https://msdn.microsoft.com/library/azure/dn946891.aspx).




<!--HONumber=Nov16_HO2-->


