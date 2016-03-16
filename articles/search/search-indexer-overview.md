<properties
	pageTitle="Indicizzatori in Ricerca di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Ricerca per indicizzazione in un database per estrarre dati ricercabili e popolare un indice di Ricerca di Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="03/09/2016"
	ms.author="heidist"/>

# Indicizzatori in Ricerca di Azure
> [AZURE.SELECTOR]
- [Panoramica](search-indexer-overview.md)
- [Portale](search-import-data-portal.md)
- [Archivio BLOB (anteprima)](search-howto-indexing-azure-blob-storage.md)
- [SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [DocumentDB](../documentdb/documentdb-search-indexer.md)

Un **indicizzatore** in Ricerca di Azure è un crawler che estrae dati ricercabili e metadati da un'origine dati esterna e popola un indice in base a mapping dei campi tra l'indice e l'origine dati. Questo approccio viene talvolta definito modello pull perché il servizio esegue il pull dei dati senza la necessità di scrivere codice che esegua il push dei dati in un indice.

È possibile usare un indicizzatore come unico mezzo per l'inserimento di dati o una combinazione di tecniche, tra cui l'uso di un indicizzatore per caricare solo alcuni dei campi nell'indice.

Gli indicizzatori possono essere eseguiti su richiesta o in base a una pianificazione di aggiornamento dati ricorrente che viene eseguita ogni quindici minuti. Aggiornamenti più frequenti richiedono un modello push che aggiorna contemporaneamente i dati sia in Ricerca di Azure che nell'origine dati esterna.

Un indicizzatore effettua il pull dei dati da un'**origine dati** che contiene informazioni come la stringa di connessione. Attualmente sono supportate le origini dati seguenti:

- [Database SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) o SQL Server in una macchina virtuale di Azure.
- [DocumentDB.](../documentdb/documentdb-search-indexer.md)
- [Archivio BLOB di Azure](search-howto-indexing-azure-blob-storage.md), attualmente in anteprima. Estrae testo da file PDF, documenti di Office, HTML e XML.

Le origini dati vengono configurate e gestite indipendentemente dagli indicizzatori che le usano. Ciò significa che un'origine dati può essere usata da più indicizzatori per caricare più di un indice alla volta.

Sia [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx) che l'[API REST del servizio](https://msdn.microsoft.com/library/azure/dn946891.aspx) supportano la gestione degli indicizzatori e delle origini dati.

In alternativa, è anche possibile configurare un indicizzatore nel portale quando si usa la procedura guidata **Importa dati**. Per un'esercitazione rapida che usa dati di esempio e l'indicizzatore DocumentDB per creare e caricare un indice usando la procedura guidata, vedere [Introduzione a Ricerca di Azure nel portale](search-get-started-portal).

<!---HONumber=AcomDC_0309_2016-->