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
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Importare i dati in Ricerca di Azure
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST API](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Ricerca di Azure è un servizio di ricerca cloud ospitato che fornisce un motore di ricerca, funzionalità di ricerca e archiviazione dei dati. Il servizio esegue operazioni su dati persistenti (un indice) che forniscono documenti e informazioni usati per elaborare un indice, eseguire query o formulare risultati della ricerca. Queste operazioni generiche includono operazioni specifiche per l'analisi del testo, la classificazione, i filtri, l'ordinamento, i facet e altre funzioni usate per calcolare o visualizzare i risultati della ricerca.

La rilevanza dell'esperienza di ricerca dipende dalla qualità dei dati caricati e dalla frequenza di aggiornamento necessaria. In questo articolo verranno illustrati gli approcci di tipo push e pull (ricerca per indicizzazione) per l'importazione e l'aggiornamento di un indice.

Prima di potere importare i dati, l'indice deve esistere già e i dati da caricare devono essere conformi allo schema. Per altre informazioni, vedere [Indici in Ricerca di Azure](search-what-is-an-index.md).

##Considerazioni relative ai set di dati

Non sono previste limitazioni ai tipi di dati caricati, purché lo schema e i set di dati siano formulati come strutture JSON.

I dati caricati devono provenire da un database o un'origine dati creata o utilizzata dall'applicazione personalizzata. Ad esempio, se l'applicazione è un catalogo per la rivendita online, l'indice creato per Ricerca di Azure deve recuperare i dati dall'inventario dei prodotti o dai database di vendita che supportano l'applicazione.

Un indice in Ricerca di Azure ottiene dati da una singola tabella, visualizzazione, contenitore BLOB o equivalente. Potrebbe essere necessario creare una struttura dei dati nel database o nell'applicazione noSQL che fornisce i dati a Ricerca di Azure. In alternativa, per determinate origini dati come il database SQL di Azure o DocumentDB, è possibile creare un indicizzatore che esegue la ricerca per indicizzazione di una tabella esterna, una visualizzazione o un contenitore BLOB dei dati da caricare in Ricerca di Azure.

##Requisiti relativi alla latenza e alla sincronizzazione dei dati

La tabella seguente è un riepilogo di requisiti comuni e consigli su come soddisfarli.

|Requisiti|Consigli|
|------------|---------------|
|Sincronizzazione dei dati quasi in tempo reale|Scrivere codice, con API .NET o REST, per eseguire il push di aggiornamenti in un indice. Un approccio pull all'inserimento di dati è un'operazione pianificata, che non può essere eseguita con la velocità sufficiente per rimanere aggiornata in merito alle rapide modifiche in un'origine dati primaria.|
|Database SQL di Azure, DocumentDB o SQL Server in VM di Azure|Gli indicizzatori sono associati a tipi di origini dati specifiche. Se le origini dati primarie si trovano in un'origine dati supportata, è possibile usare un indicizzatore per eseguire una ricerca per indicizzazione nell'origine dati e pianificare l'aggiornamento dei dati con una frequenza pari a intervalli orari. È possibile configurare un indicizzatore nel portale o nel codice.|
|Aggiornamento dei dati pianificato|Usare un indicizzatore (vedere sopra).|
|Crawler|Usare un indicizzatore (vedere sopra).|
|Creazione di prototipi o modifica senza codice|Il portale include una Importazione guidata dati che configura un indicizzatore, generando a volte uno schema preliminare se il database primario include informazioni sufficienti per questo scopo. La procedura guidata include opzioni per la configurazione di un aggiornamento pianificato dei dati. È facoltativamente possibile aggiungere analizzatori di linguaggio oppure opzioni CORS. Alcuni possibili svantaggi consistono nel fatto che non è possibile aggiungere profili di punteggio o esportare uno schema creato nel portale in un file JSON da usare nel codice.| 

<!---HONumber=Nov15_HO3-->