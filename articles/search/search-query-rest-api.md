<properties
	pageTitle="Compilare query in Ricerca di Azure con chiamate REST | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Compilare una query di ricerca in Ricerca di Azure e usare i parametri di ricerca per filtrare, ordinare ed esplorare in base a facet i risultati della ricerca con la libreria .NET o l'SDK."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/17/2016"
	ms.author="heidist"/>

# Compilare query in Ricerca di Azure con chiamate REST
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Questo articolo illustra come creare una query su un indice usando l'[API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Parte del contenuto riportato di seguito è estratto dall'articolo [Eseguire ricerche nei documenti (API REST di Ricerca di Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx). Per altro contesto, vedere l'articolo padre.

I prerequisiti per l'importazione includono la disponibilità di un indice esistente in cui sono già caricati i documenti che forniscono dati ricercabili.

Per cercare nell'indice con l'API REST, eseguire una richiesta HTTP GET. I parametri di query saranno definiti nell'URL della richiesta HTTP.

**Richiesta e intestazioni della richiesta**:

Nell'URL è necessario fornire il nome del servizio e il nome dell'indice, oltre alla versione dell'API appropriata. Specificare i parametri query nella stringa di query alla fine dell'URL. Uno dei parametri nella stringa di query deve essere la versione dell'API appropriata. Al momento della pubblicazione di questo documento la versione dell'API corrente è "2015-02-28".

Come intestazioni della richiesta è necessario definire il tipo di contenuto e fornire la chiave amministratore primaria o secondaria del servizio.

	GET https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Ricerca di Azure offre numerose opzioni per creare query estremamente avanzate. Per altre informazioni su tutti i diversi parametri di una stringa di query, visitare [questa pagina](https://msdn.microsoft.com/library/azure/dn798927.aspx).

**Esempi**:

Di seguito sono riportati alcuni esempi con diverse stringhe di query. Questi esempi usano un indice fittizio denominato "Hotel":

Cercare il termine "quality" nell'intero indice:

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=quality&$orderby=lastRenovationDate desc&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Cercare nell'intero indice:

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Cercare nell'intero indice e ordinare in base a un campo specifico (lastRenovationDate):

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Una richiesta di query riuscita restituirà un codice di stato "200 OK" e i risultati della ricerca saranno riportati in formato JSON nel corpo della risposta. Per altre informazioni, vedere la sezione "Risposta" di [questa pagina](https://msdn.microsoft.com/library/azure/dn798927.aspx).

<!---HONumber=AcomDC_0224_2016-->