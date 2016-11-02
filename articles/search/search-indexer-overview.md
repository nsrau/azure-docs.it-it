<properties
	pageTitle="Indicizzatori in Ricerca di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Effettuare una ricerca per indicizzazione in un database SQL, DocumentDB o Archiviazione di Azure per estrarre dati ricercabili e popolare un indice di Ricerca di Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="jhubbard"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="08/08/2016"
	ms.author="heidist"/>

# Indicizzatori in Ricerca di Azure
> [AZURE.SELECTOR]
- [Panoramica](search-indexer-overview.md)
- [Portale](search-import-data-portal.md)
- [SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Archivio BLOB (anteprima)](search-howto-indexing-azure-blob-storage.md)
- [Archivio tabelle (anteprima)](search-howto-indexing-azure-tables.md)

Un **indicizzatore** in Ricerca di Azure è un crawler che estrae dati e metadati ricercabili da un'origine dati esterna e popola un indice in base a mapping dei campi tra l'indice e l'origine dati dell'utente. Questo approccio viene talvolta definito modello pull perché il servizio esegue il pull dei dati senza la necessità di scrivere codice che esegua il push dei dati in un indice.

È possibile usare un indicizzatore come unico mezzo per l'inserimento di dati o una combinazione di tecniche, tra cui l'uso di un indicizzatore per caricare solo alcuni dei campi nell'indice.

È possibile eseguire gli indicizzatori su richiesta o in base a una pianificazione di aggiornamento dati ricorrente che viene eseguita ogni quindici minuti. Aggiornamenti più frequenti richiedono un modello push che aggiorna contemporaneamente i dati sia in Ricerca di Azure che nell'origine dati esterna.

## Approcci per la creazione e la gestione degli indicizzatori

Per gli indicizzatori disponibili a livello generale, come Azure SQL o DocumentDB, è possibile creare e gestire gli indicizzatori con gli approcci seguenti:

- [Portale > procedura guidata Importa dati ](search-get-started-portal.md)
- [API REST del servizio](https://msdn.microsoft.com/library/azure/dn946891.aspx)
- [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx)

Gli indicizzatori in anteprima, come l'archiviazione BLOB o tabelle di Azure, richiedono invece l'uso di codice e di API in anteprima, ad esempio l'[API REST di Ricerca di Azure per gli indicizzatori (anteprima)](search-api-indexers-2015-02-28-preview.md). Gli strumenti del portale non sono in genere disponibili per le funzionalità in anteprima.

## Procedura di configurazione di base

Gli indicizzatori possono offrire funzionalità univoche per l'origine dati. In questo senso, alcuni aspetti della configurazione dell'indicizzatore o dell'origine dati possono variare a seconda del tipo di indicizzatore. Tutti gli indicizzatori, tuttavia, condividono la stessa composizione e gli stessi requisiti di base. Le procedure comuni a tutti gli indicizzatori sono descritte sotto.

### Passaggio 1: Creare un indice

Un indicizzatore consente di automatizzare alcune attività relative all'inserimento dei dati, ma la creazione di un indice non fa parte di esse. Uno dei prerequisiti prevede che sia disponibile un indice predefinito con campi corrispondenti a quelli dell'origine dati esterna. Per altre informazioni su come strutturare un indice, vedere [Creare l'indice (API REST di Ricerca di Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx).

### Passaggio 2: Creare un'origine dati

Un indicizzatore effettua il pull dei dati da un'**origine dati** che contiene informazioni, ad esempio la stringa di connessione. Attualmente sono supportate le origini dati seguenti:

- [Database SQL di Azure o SQL Server in una macchina virtuale di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Archiviazione BLOB di Azure (anteprima)](search-howto-indexing-azure-blob-storage.md), che consente di estrarre il testo da documenti PDF, di Office, HTML o XML
- [Archiviazione tabelle di Azure (anteprima)](search-howto-indexing-azure-tables.md)

Le origini dati vengono configurate e gestite indipendentemente dagli indicizzatori che le usano. Ciò significa che un'origine dati può essere usata da più indicizzatori per caricare più di un indice alla volta.

### Passaggio 3: Creare e pianificare un indicizzatore

La definizione dell'indicizzatore è un costrutto che specifica l'indice, l'origine dati e una pianificazione. Un indicizzatore può fare riferimento a un'origine dati di un altro servizio, purché tale origine dati appartenga alla stessa sottoscrizione. Per altre informazioni su come strutturare un indicizzatore, vedere [Creare un indicizzatore (API REST di Ricerca di Azure)](https://msdn.microsoft.com/library/azure/dn946899.aspx).

## Passaggi successivi

Dopo aver acquisito queste informazioni di base, il passaggio successivo prevede l'analisi dei requisiti e delle attività specifici per ogni tipo di origine dati.

- [Database SQL di Azure o SQL Server in una macchina virtuale di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Archiviazione BLOB di Azure (anteprima)](search-howto-indexing-azure-blob-storage.md), che consente di estrarre il testo da documenti PDF, di Office, HTML o XML
- [Archiviazione tabelle di Azure (anteprima)](search-howto-indexing-azure-tables.md)
- [Indicizzazione di BLOB CSV con l'indicizzatore di BLOB di Ricerca di Azure (anteprima)](search-howto-index-csv-blobs.md)
- [Indicizzazione di BLOB JSON con l'indicizzatore di BLOB di Ricerca di Azure (anteprima)](search-howto-index-json-blobs.md)

<!---HONumber=AcomDC_0914_2016-->