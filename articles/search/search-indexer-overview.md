---
title: Indicizzatori in Ricerca di Azure | Documentazione Microsoft
description: Effettuare una ricerca per indicizzazione in un database SQL, in Azure Cosmos DB o in Archiviazione di Azure per estrarre dati ricercabili e popolare un indice di Ricerca di Azure.
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 34a7694c-8fd9-46b1-8900-cefdd7236323
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: heidist
ms.openlocfilehash: 1ea9dfcaec188bac2e92909f4811568b589357d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="indexers-in-azure-search"></a>Indicizzatori in Ricerca di Azure
> [!div class="op_single_selector"]
>
> * [Panoramica](search-indexer-overview.md)
> * [Portale](search-import-data-portal.md)
> * [SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
> * [Azure Cosmos DB](search-howto-index-documentdb.md)
> * [Archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md)
> * [Archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md)
>
>

Un **indicizzatore** in Ricerca di Azure è un crawler che estrae dati e metadati ricercabili da un'origine dati esterna e popola un indice in base a mapping dei campi tra l'indice e l'origine dati dell'utente. Questo approccio viene talvolta definito modello pull perché il servizio esegue il pull dei dati senza la necessità di scrivere codice che esegua il push dei dati in un indice.

È possibile usare un indicizzatore come unico mezzo per l'inserimento di dati o una combinazione di tecniche, tra cui l'uso di un indicizzatore per caricare solo alcuni dei campi nell'indice.

È possibile eseguire gli indicizzatori su richiesta o in base a una pianificazione di aggiornamento dati ricorrente che viene eseguita ogni quindici minuti. Aggiornamenti più frequenti richiedono un modello push che aggiorna contemporaneamente i dati sia in Ricerca di Azure che nell'origine dati esterna.

## <a name="approaches-for-creating-and-managing-indexers"></a>Approcci per la creazione e la gestione degli indicizzatori
Per creare e gestire gli indicizzatori disponibili a livello generale, come Azure SQL o Azure Cosmos DB, è possibile procedere nei modi seguenti:

* [Portale > Importazione guidata dati](search-get-started-portal.md)
* [API REST del servizio](https://msdn.microsoft.com/library/azure/dn946891.aspx)
* [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx)

## <a name="basic-configuration-steps"></a>Procedura di configurazione di base
Gli indicizzatori possono offrire funzionalità univoche per l'origine dati. In questo senso, alcuni aspetti della configurazione dell'indicizzatore o dell'origine dati possono variare a seconda del tipo di indicizzatore. Tutti gli indicizzatori, tuttavia, condividono la stessa composizione e gli stessi requisiti di base. Le procedure comuni a tutti gli indicizzatori sono descritte sotto.

### <a name="step-1-create-an-index"></a>Passaggio 1: Creare un indice
Un indicizzatore consente di automatizzare alcune attività relative all'inserimento dei dati, ma la creazione di un indice non fa parte di esse. Uno dei prerequisiti prevede che sia disponibile un indice predefinito con campi corrispondenti a quelli dell'origine dati esterna. Per altre informazioni su come strutturare un indice, vedere [Creare l'indice (API REST di Ricerca di Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx).

### <a name="step-2-create-a-data-source"></a>Passaggio 2: Creare un'origine dati
Un indicizzatore effettua il pull dei dati da un' **origine dati** che contiene informazioni, ad esempio la stringa di connessione. Attualmente sono supportate le origini dati seguenti:

* [Database SQL di Azure o SQL Server in una macchina virtuale di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-documentdb.md)
* [Archivio BLOB di Azure](search-howto-indexing-azure-blob-storage.md) per i tipi di contenuti selezionati
* [Archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md)

Le origini dati vengono configurate e gestite indipendentemente dagli indicizzatori che le usano. Ciò significa che un'origine dati può essere usata da più indicizzatori per caricare più di un indice alla volta.

### <a name="step-3create-and-schedule-the-indexer"></a>Passaggio 3: Creare e pianificare un indicizzatore
La definizione dell'indicizzatore è un costrutto che specifica l'indice, l'origine dati e una pianificazione. Un indicizzatore può fare riferimento a un'origine dati di un altro servizio, purché tale origine dati appartenga alla stessa sottoscrizione. Per altre informazioni su come strutturare un indicizzatore, vedere [Creare un indicizzatore (API REST di Ricerca di Azure)](https://msdn.microsoft.com/library/azure/dn946899.aspx).

## <a name="next-steps"></a>Passaggi successivi
Dopo aver acquisito queste informazioni di base, il passaggio successivo prevede l'analisi dei requisiti e delle attività specifici per ogni tipo di origine dati.

* [Database SQL di Azure o SQL Server in una macchina virtuale di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-documentdb.md)
* [Archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md)
* [Archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md)
* [Indicizzazione di BLOB CSV con l'indicizzatore di BLOB di Ricerca di Azure](search-howto-index-csv-blobs.md)
* [Indicizzazione di BLOB JSON con l'indicizzatore di BLOB di Ricerca di Azure](search-howto-index-json-blobs.md)
