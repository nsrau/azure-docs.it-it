<properties
	pageTitle="Importare i dati in Ricerca di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Informazioni su come caricare i dati in un indice in Ricerca di Azure"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="heidist"/>

# Importare i dati in Ricerca di Azure
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

In Ricerca di Azure il servizio esegue operazioni su dati persistenti (un indice) che forniscono documenti e informazioni usati per elaborare un indice, eseguire query o formulare risultati della ricerca. Per popolare un indice, è possibile usare un modello push o pull per il caricamento dei dati.

È necessario che l'indice esista prima dell'importazione. Per altre informazioni, vedere [Indici in Ricerca di Azure](search-what-is-an-index.md).

##Push dei dati in un indice

Questo approccio fa riferimento alla selezione di un set di dati esistente conforme allo schema dell'indice e all'inserimento del set di dati nel servizio di ricerca. Per applicazioni con requisiti di latenza molto bassa, ad esempio se è necessario che le operazioni di ricerca siano sincronizzate con i database di inventario, il modello push è l'unica opzione disponibile.

È possibile usare l'API REST o .NET SDK per il push dei dati in un indice. Non sono attualmente supportati strumenti per il push dei dati tramite il portale.

Questo approccio è più flessibile rispetto a un modello pull, perché i documento vengono caricati individualmente o in batch, fino a 1000 per batch o 16 MB, a seconda del limite che viene raggiunto per primo.

##Pull (ricerca per indicizzazione) dei dati 

Un modello pull esegue la ricerca per indicizzazione di un'origine dati supportata e carica automaticamente l'indice. In Ricerca di Azure questa funzionalità viene implementata tramite *indicizzatori*, attualmente disponibili per il database SQL di Azure, DocumentDB e SQL Server in VM di Azure. Per altre informazioni sul caricamento dei dati del database SQL di Azure, vedere [Indicizzatori](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md).

È possibile usare il portale, l'API REST o .NET SDK per il pull dei dati in un indice.

##Requisiti del set di dati

Non sono previste limitazioni ai tipi di dati caricati, purché lo schema e i set di dati siano formulati come strutture JSON.

I dati devono provenire da un database o un'origine dati creata o usata dall'applicazione personalizzata. Ad esempio, se l'applicazione è un catalogo per la rivendita online, l'indice creato per Ricerca di Azure deve recuperare i dati dall'inventario dei prodotti o dai database di vendita che supportano l'applicazione.

Il set di dati deve derivare da una singola tabella, visualizzazione, contenitore BLOB o equivalente. Potrebbe essere necessario creare una struttura dei dati nel database o nell'applicazione noSQL che fornisce i dati a Ricerca di Azure. In alternativa, per determinate origini dati come il database SQL di Azure o DocumentDB, è possibile creare un indicizzatore che esegue la ricerca per indicizzazione di una tabella esterna, una visualizzazione o un contenitore BLOB dei dati da caricare in Ricerca di Azure.

##Scelta di un approccio per l'importazione dei dati

|Criteri|Approccio consigliato|
|------------|---------------|
|Sincronizzazione dei dati quasi in tempo reale|Scrivere codice, con API .NET o REST, per eseguire il push di aggiornamenti in un indice. Un approccio pull all'inserimento di dati è un'operazione pianificata, che non può essere eseguita con la velocità sufficiente per rimanere aggiornata in merito alle rapide modifiche in un'origine dati primaria.|
|Database SQL di Azure, DocumentDB o SQL Server in VM di Azure|Gli indicizzatori sono associati a tipi di origini dati specifiche. Se le origine dati primarie sono un tipo di origine dati supportato, un indicizzatore costituisce il modo più semplice per caricare un indice. È possibile pianificare l'aggiornamento dei dati con frequenze pari a ogni ora. È possibile configurare un indicizzatore nel portale o nel codice.|
|Aggiornamento dei dati pianificato|Usare un indicizzatore (vedere sopra).|
|Creazione di prototipi o modifica senza codice|Il portale include una Importazione guidata dati che configura un indicizzatore, generando a volte uno schema preliminare se il database primario include informazioni sufficienti per questo scopo. La procedura guidata include opzioni per la configurazione di un aggiornamento pianificato dei dati. È facoltativamente possibile aggiungere analizzatori di linguaggio oppure opzioni CORS. Alcuni possibili svantaggi consistono nel fatto che non è possibile aggiungere profili di punteggio o esportare uno schema creato nel portale in un file JSON da usare nel codice.| 

<!---HONumber=AcomDC_0224_2016-->