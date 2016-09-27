<properties
	pageTitle="Creare un indice di Ricerca di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Informazioni su un indice in Ricerca di Azure e su come viene usato."
	services="search"
	documentationCenter=""
authors="ashmaka"
/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="08/29/2016"
	ms.author="ashmaka"/>

# Creare un indice di Ricerca di Azure
> [AZURE.SELECTOR]
- [Panoramica](search-what-is-an-index.md)
- [Portale](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

## Informazioni sugli indici

Un *indice* è un archivio persistente di *documenti* e altri costrutti usati da un servizio di Ricerca di Azure. Un documento è un singola unità di dati ricercabili nell'indice. Ad esempio, un rivenditore di e-commerce può avere un documento per ogni elemento in vendita, un'agenzia di stampa può avere un documento per ogni articolo e così via. Applicando questi concetti ai più familiari elementi di database equivalenti, un *indice* è concettualmente analogo a una *tabella* e i *documenti* equivalgono in linea di massima alle *righe* di una tabella.

Quando si aggiungono o caricano documenti e si inviano le query di ricerca in Ricerca di Azure, si inviano le richieste in un indice specifico nel servizio di ricerca.

## Tipi di campo e attributi in un indice di Ricerca di Azure

Quando si definisce lo schema, è necessario specificare il nome, tipo e gli attributi di ogni campo nell'indice. Il tipo di campo classifica i dati archiviati in quel campo. Gli attributi sono impostati nei singoli campi per specificare come viene usato il campo. La tabella seguente enumera gli attributi che è possibile specificare.


### Tipi di campo
|Tipo|Descrizione|
|------------|-----------|
|*Edm.String*|Testo facoltativamente soggetto a tokenizzazione per la ricerca full-text (suddivisione delle parole, stemming e così via).|
|*Collection(Edm.String)*|Elenco di stringhe facoltativamente soggette a tokenizzazione per la ricerca full-text. Non esiste alcun limite superiore teorico al numero di elementi in una raccolta, ma alle raccolte si applica il limite massimo di 16 MB di dimensioni del payload.|
|*Edm.Boolean*|Contiene valori true/false.|
|*Edm.Int32*|Valori integer a 32 bit.|
|*Edm.Int64*|Valori integer a 64 bit.|
|*Edm.Double*|Dati numerici a precisione doppia.|
|*Edm.DateTimeOffset*|Valori di ora rappresentati in formato OData V4 (ad esempio `yyyy-MM-ddTHH:mm:ss.fffZ` o `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`).|
|*Edm.GeographyPoint*|Punto che rappresenta una località geografica del mondo.|

È possibile trovare altre informazioni sui [tipi di dati supportati di Ricerca di Azure in MSDN](https://msdn.microsoft.com/library/azure/dn798938.aspx).



### Attributi dei campi
|Attributo|Descrizione|
|------------|-----------|
|*Chiave*|Stringa che fornisce l'ID univoco di ogni documento, usata per la ricerca di documenti. Ogni indice deve avere una chiave. Un solo campo può essere la chiave e deve essere impostata su Edm.String.|
|*Recuperabile*|Specifica se il campo può essere restituito nel risultato di una ricerca.|
|*Filtrabile*|Consente di usare il campo nelle query di filtro.|
|*Ordinabile*|Consente a una query ordinare i risultati della ricerca usando questo campo.|
|*Con facet*|Consente di usare un campo in una struttura di [esplorazione in base a facet](search-faceted-navigation.md) per i filtri autoindirizzati. In genere, i campi che contengono valori ricorrenti che è possibile usare per raggruppare più documenti, ad esempio, più documenti che rientrano in una categoria di servizi o una singola marca, funzionano meglio come facet.|
|*Ricercabile*|Contrassegna il campo come disponibile per la ricerca full-text.|

È possibile trovare altre informazioni sugli [attributi di indice di Ricerca di Azure in MSDN](https://msdn.microsoft.com/library/azure/dn798941.aspx).



## Indicazioni per la definizione di uno schema di indice

Durante la progettazione dell'indice, dedicare alla fase di pianificazione il tempo necessario per valutare attentamente ogni decisione. È importante tenere in considerazione l'esperienza di ricerca dell'utente e le esigenze aziendali quando si progetta l'indice, perché a ogni campo devono essere assegnati gli [attributi appropriati](https://msdn.microsoft.com/library/azure/dn798941.aspx). La modifica di un indice dopo la distribuzione comporta la ricompilazione e il ricaricamento dei dati.


Se i requisiti per l'archiviazione dei dati cambiano nel tempo, è possibile aumentare o ridurre la capacità aggiungendo o spostando le partizioni. Per informazioni dettagliate, vedere [Gestire il servizio di ricerca in Microsoft Azure](search-manage.md) o [Limiti dei servizi in Ricerca di Azure](search-limits-quotas-capacity.md).

<!---HONumber=AcomDC_0921_2016-->