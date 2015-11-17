<properties
	pageTitle="Indici in Ricerca di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato"
	description="Informazioni su un indice in Ricerca di Azure e sul relativo uso"
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
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Indici in Ricerca di Azure
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

Ricerca di Azure è un servizio di ricerca cloud ospitato che fornisce un motore di ricerca, funzionalità di ricerca che consentono un'esperienza analoga a Bing o Google nell'applicazione personalizzata, API .NET e REST per l'integrazione di Ricerca di Azure con l'applicazione Web o desktop e un archivio dati con dati ricercabili sotto forma di *indici*.

##Informazioni sugli indici

Un *indice* è un archivio persistente di documenti e altri costrutti usati da un servizio Ricerca di Azure.

In particolare, include tutti i dati ricercabili usati nell'elaborazione di un indice, nell'esecuzione di una query o nella restituzione di un elenco di risultati della ricerca, una struttura di esplorazione in base a facet o una pagina di dettagli all'applicazione. In Ricerca di Azure un indice includerà anche dati non ricercabili da usare nelle espressioni di filtro o in profili di punteggio, usati per perfezionare una query o migliorare i punteggi di classificazione della ricerca, oltre a strutture con ambito specifico dell'applicazione personalizzata integrata con Ricerca di Azure.

Se si ha familiarità con i concetti relativi ai database, un indice è concettualmente analogo a una tabella e i documenti equivalgono quasi alle righe di una tabella.

Le sezioni principali di un indice, illustrate più avanti in questo articolo, sono queste: - Campi, che definiscono il corpo di un *documento*, con attributi per comportamenti specifici. - Profili di punteggio - Suggerimenti- Opzioni CORS - Analizzatore predefinito

Come parte del provisioning di un servizio di ricerca che verrà usato dall'applicazione, sarà necessario creare un indice. La creazione di un indice è un'attività incentrata sulla definizione del relativo schema. Consiste almeno nella definizione dei campi e nella configurazione degli attributi. È facoltativamente possibile definire i profili di punteggio e i suggerimenti per migliorare l'esperienza di ricerca.

Per creare uno schema, è possibile usare il portale o scrivere codice che chiama .NET SDK o l'API REST.

Dopo avere definito lo schema e creato l'indice, sarà necessario eseguire un'operazione separata per popolarlo. Per altre informazioni sull'inserimento dei dati dopo la creazione dell'indice, vedere [Importare i dati in Ricerca di Azure](search-what-is-data-import.md).

##Uso degli indici in Ricerca di Azure

Tutte le operazioni correlate ai dati eseguite da Ricerca di Azure utilizzano o restituiscono dati da un indice, senza eccezioni: non è possibile fare in modo che il servizio faccia riferimento a origini dati diverse rispetto all'indice per la restituzione di dati esterni in una risposta.

Per le applicazioni che accumulano ed eseguono operazioni sui dati, ad esempio transazioni di vendita di un'app per il commercio online che archivia dati in un database OLTP, un indice di Ricerca di Azure costituirà un'origine dati aggiuntiva nella soluzione complessiva. L'indice è un archivio dati dedicato usato solo dal servizio di ricerca. Un'origine dati dedicata con prossimità al servizio assicura la coerenza nei risultati della ricerca, riduce la volatilità e il numero di round trip tra l'applicazione e Ricerca di Azure e infine migliora le prestazioni complessive delle operazioni di ricerca, grazie all'assenza di competizione per le risorse o per i dati.

##Indicazioni per la definizione di uno schema

Gli schemi vengono creati come strutture JSON. È consigliabile creare un indice per ogni soluzione personalizzata integrata con Ricerca di Azure. Non è possibile collegare o unire più indici, ma è possibile creare indici complessi che includono svariati scenari di ricerca necessari per la soluzione. Ad esempio, se l'applicazione supporta più lingue, la raccolta di campi nell'indice potrà avere varianti specifiche di ogni lingua per ogni campo.

Durante la progettazione dell'indice, dedicare alla fase di pianificazione il tempo necessario per valutare attentamente ogni decisione. La modifica di un indice dopo la distribuzione comporta la ricompilazione e il ricaricamento dei dati. Se si crea l'indice nel codice, questo passaggio sarà molto più semplice rispetto alla creazione manuale nel portale.

Una buona comprensione dei dati di origine è essenziale per la creazione di uno schema valido. È consigliabile definire le corrispondenze tra tipi di dati, determinare quali campi usare come *chiave* per identificare in modo univoco ogni documento nell'indice e quali campi esporre nell'elenco dei risultati della ricerca o nella pagina dei dettagli.

È consigliabile archiviare in una posizione diversa i contenuti di tipo immagine, audio o video, purché l'indice includa un campo rappresentativo con un URL per la risorsa. È necessario archiviare altrove qualsiasi contenuto, in particolare i contenuti binari, non ricercabile e farvi quindi riferimento tramite URI in un campo dell'indice.

Come si può immaginare, l'indice deve essere sincronizzato regolarmente con altre origini dati usate nella soluzione. In un catalogo per la vendita al dettaglio online il database di inventario che acquisisce le transazioni di vendita deve avere gli stessi codici di riferimento del prodotto, gli stessi prezzi e la stessa disponibilità dei dati esposti tramite i risultati della ricerca. In base al livello di latenza accettabile per la soluzione, è possibile eseguire la sincronizzazione dei dati solo una volta alla settimana, una volta al giorno o quasi in tempo reale mediante operazioni di scrittura concorrenti nel database di inventario e in un indice di Ricerca di Azure. Le opzioni disponibili sono illustrate in modo dettagliato in [Importare i dati in Ricerca di Azure](search-what-is-data-import.md).

##Elementi dello schema

Gli indici sono costituiti da campi con attributi, valori di dati e altri costrutti che definiscono la modalità di creazione della ricerca e dei risultati.

- I campi definiscono i corpo di un documento. Un rivenditore online vorrà documenti per ogni codice di riferimento del prodotto, una nuova organizzazione vorrà documenti per ogni articolo e un portale di infomedia vorrà documenti per ogni fornitore o vetrina.
- Gli attributi o proprietà sono annotazioni nei campi che specificano il tipo di dati, la lunghezza, il valore e i comportamenti consentiti per il campo specifico. È possibile specificare se ogni singolo campo è ricercabile, recuperabile oppure ordinabile. È anche possibile specificare l'analizzatore di linguaggio da usare a livello di campo. Una *chiave* è un campo riservato nello schema, che fornisce un identificatore univoco per ogni documento della raccolta di campi.
- I profili di punteggio sono criteri usati per aumentare la priorità di un risultato della ricerca che include più caratteristiche definite dal profilo. Ad esempio, si supponga che un termine di ricerca corrisponda a un nome di prodotto e alla descrizione di un prodotto. È possibile che si voglia attribuire alle corrispondenze con il nome del prodotto una priorità superiore rispetto alle corrispondenze con la descrizione.
- I suggerimenti, noti anche come completamento automatico o query con completamento automatico, sono definiti come una sezione nell'indice.
- Gli analizzatori del linguaggio predefiniti possono essere specificati a livello di indice e possono essere applicati globalmente a tutti i campi.
- Le opzioni CORS abilitano gli script tra domini e fanno parte della definizione di un indice.

## Attributi dell'indice

|**Proprietà**|Descrizione|
|------------|-----------|
|**Recuperabile**|Specifica se il campo può essere restituito nel risultato di una ricerca.|
|**Filtrabile**|Consente di usare il campo nelle query **$filter**.|
|**Ordinabile**|Consente di usare il campo come opzione di ordinamento.|
|**Con facet**|Consente di usare un campo in una struttura di esplorazione in base a facet per i filtri autoindirizzati. In genere i campi contenenti valori ricorrenti che è possibile utilizzare per raggruppare più documenti (ad esempio, più documenti che rientrano in un singolo prodotto o una categoria di servizi) funzionano meglio come facet.|
|**Chiave**|Fornisce l'ID univoco di ogni documento e viene usata per la ricerca di documenti. Ogni indice deve avere una chiave. Un solo campo può essere la chiave e deve essere impostata su Edm.|
|**Ricercabile**|Contrassegna il campo come disponibile per la ricerca full-text.|

## Archiviazione sul cloud

Poiché Ricerca di Azure è un servizio di ricerca completamente gestito, l'archiviazione dei dati viene gestita come un'operazione interna. È importante che i programmatori capiscano che non è possibile ignorare le API di Ricerca di Azure per connettersi direttamente all'indice e che le dimensioni o lo stato dell'indice possono essere monitorati solo tramite le notifiche del portale o le chiamate API. Non sono disponibili funzionalità per il backup o il ripristino di un indice o per l'ottimizzazione o perfezionamento delle prestazioni a livello di archiviazione. In background, i dati vengono archiviati sotto forma di archivio BLOB, ma è possibile considerare l'archiviazione fisica e la gestione di un indice come un dettaglio dell'implementazione che può essere modificato in futuro. Uno dei vantaggi principali del servizio è costituito dall'approccio automatico all'amministrazione dei dati.

Se i volumi delle query o i requisiti per l'archiviazione dei dati cambiano nel tempo, è possibile aumentare o ridurre la capacità aggiungendo o spostando repliche e partizioni. Per informazioni dettagliate, vedere [Gestire il servizio di ricerca in Microsoft Azure](search-manage.md) o [Limiti dei servizi](search-limits-quotas-capacity.md).

<!---HONumber=Nov15_HO3-->