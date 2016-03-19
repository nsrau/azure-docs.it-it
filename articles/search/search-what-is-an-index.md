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
	ms.date="02/09/2016"
	ms.author="heidist"/>

# Indici in Ricerca di Azure
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

Ricerca di Azure è un servizio di ricerca cloud ospitato che fornisce un motore di ricerca, funzionalità di ricerca che consentono un'esperienza analoga a Bing o Google nell'applicazione personalizzata, API .NET e REST per l'integrazione di Ricerca di Azure con l'applicazione Web o desktop e un archivio dati con dati correlati alla ricerca sotto forma di *indice*.

##Informazioni sugli indici

Un *indice* è un archivio persistente di *documenti* e altri costrutti usati da un servizio di Ricerca di Azure. I documenti sono un'unità di base di dati che supportano la ricerca. Ad esempio, un rivenditore può avere un documento per ogni SKU, un'agenzia di stampa può avere un documento per ogni articolo e un distributore di contenuti multimediali in streaming può avere un documento per ogni video o brano nella propria libreria. Applicando questi concetti ai più familiari elementi di database equivalenti, un *indice* è concettualmente analogo a una *tabella* e i *documenti* equivalgono in linea di massima alle *righe* di una tabella.

L'indice è un set di dati flat, in genere un subset di dati creati o acquisiti durante le normali attività aziendali mediante processi computerizzati, come le transazioni di vendita, la pubblicazione di contenuto, l'attività di acquisto che spesso sono archiviate in database relazionali o NoSQL. Un indice ottiene i documenti quando si effettua il push o il pull di un set di dati contenente righe da altre origini dati per l'indice.

##Quando creare un indice

Come parte del provisioning di un servizio di ricerca che verrà usato dall'applicazione, sarà necessario creare un indice. Rendere disponibile un indice per le operazioni di ricerca è un'attività in 2 parti. Definire prima di tutto lo schema e inserirlo in Ricerca di Azure. Popolare quindi l'indice con un set di dati definito dall'utente conforme allo schema.

Per creare uno schema che definisce un indice, è possibile usare il portale o scrivere codice che chiama .NET SDK o l'API REST. Vedere le schede nella parte superiore di questo argomento. Per altre informazioni sull'inserimento dei dati dopo la creazione dell'indice, vedere [Importare i dati in Ricerca di Azure](search-what-is-data-import.md).

##Specifica dello schema JSON di un indice

Un indice è un documento JSON che contiene sezioni per campi e attributi, profili di punteggio, suggerimenti, un profilo di punteggio predefinito e opzioni CORS.

 ![][1]

Le sezioni principali di un indice di Ricerca di Azure, come descritto nel formato di interscambio dati (DIF) JSON, sono i seguenti.

|Sezione|Descrizione|
|--------------|-----------|
|Raccolta di campi|I campi definiscono un documento. Un set di dati di cui si effettua il push o il pull in un indice deve fornire valori o valori Null per ogni campo, compatibili con il tipo di dati e la lunghezza dei campi espressi nello schema. I campi hanno [attributi](#index-attributes), ovvero proprietà o annotazioni usate per contrassegnare un campo per abilitare i comportamenti correlati alla ricerca per tale campo. Ad esempio, è possibile specificare se ogni singolo campo supporta la ricerca, è recuperabile oppure ordinabile. È anche possibile specificare gli override dell'analizzatore del linguaggio a livello di campo.
|[Componenti per il suggerimento](https://msdn.microsoft.com/library/azure/mt131377.aspx)|Noti anche come completamento automatico o query con completamento automatico, sono definiti come una sezione nell'indice.|
|[Profili di punteggio](https://msdn.microsoft.com/library/azure/dn798928.aspx)|Criteri usati per aumentare la priorità di un risultato della ricerca che include il maggior numero di caratteristiche definite dal profilo. Ad esempio, si supponga che un termine di ricerca corrisponda a un nome di prodotto e alla descrizione di un prodotto. È possibile che si voglia attribuire alle corrispondenze con il nome del prodotto una priorità superiore rispetto alle corrispondenze con la descrizione. È possibile creare più profili.|
|Profilo di assegnazione dei punteggi|Facoltativamente, è possibile eseguire l'override della logica incorporata che calcola il punteggio di classificazione della ricerca, specificando uno dei profili di punteggio come impostazione predefinita.|
|Opzioni CORS|Facoltativamente, abilita la condivisione di risorse tra le origini, dove le richieste per una risorsa usata da una pagina Web vengono eseguite oltre limite di un dominio. CORS è sempre disattivata, a meno che non venga appositamente abilitata per l'indice.|

<a name="index-attributes"></a>
##Attributi dell'indice

Gli attributi sono impostati nei singoli campi per specificare come viene usato il campo. La tabella seguente enumera gli attributi che è possibile specificare.

|**Proprietà**|Descrizione|
|------------|-----------|
|**Recuperabile**|Specifica se il campo può essere restituito nel risultato di una ricerca.|
|**Filtrabile**|Consente di usare il campo nelle query **$filter**.|
|**Ordinabile**|Consente di usare il campo come colonna di ordinamento in un set di risultati.|
|**Con facet**|Consente di usare un campo in una struttura di esplorazione in base a facet per i filtri autoindirizzati. In genere, i campi che contengono valori ricorrenti che è possibile usare per raggruppare più documenti, ad esempio, più documenti che rientrano in una categoria di servizi o una singola marca, funzionano meglio come facet.|
|**Chiave**|Stringa che fornisce l'ID univoco di ogni documento, usata per la ricerca di documenti. Ogni indice deve avere una chiave. Un solo campo può essere la chiave e deve essere impostata su Edm.|
|**Ricercabile**|Contrassegna il campo come disponibile per la ricerca full-text.|


##Uso degli indici in Ricerca di Azure

I dati dall'indice vengono usati per costruire un elenco di risultati della ricerca, una struttura di esplorazione in base a facet o una pagina dei dettagli per singolo documento. In Ricerca di Azure un indice può anche contenere dati che non supportano la ricerca usati internamente in espressioni di filtro o nei profili di assegnazione dei punteggi che forniscono i criteri usati per incrementare il punteggio di classificazione della ricerca.

Tutte le operazioni correlate ai dati eseguite da Ricerca di Azure utilizzano o restituiscono dati da un indice, senza eccezioni. Non è infatti possibile impostare il servizio perché faccia riferimento a origini dati oltre all'indice per restituire dati esterni in una risposta generata dal servizio di ricerca.

Naturalmente, per le applicazioni che accumulano dati ed eseguono operazioni sui dati, ad esempio transazioni di vendita di un'app per il commercio online, un indice di Ricerca di Azure sarà un'origine dati aggiuntiva nella soluzione complessiva. Anche se può sembrare ridondante avere un'origine dati dedicata solo per la ricerca, la disponibilità di questa risorsa offre vantaggi quali coerenza nei risultati della ricerca, riduzione della volatilità e numero inferiore di round trip tra l'applicazione e Ricerca di Azure, oltre al miglioramento delle prestazioni complessive delle operazioni di ricerca, grazie all'assenza di contesa per risorse o dati.

##Indicazioni per la definizione di uno schema

Gli schemi vengono creati come strutture JSON. È consigliabile creare un indice per ogni soluzione personalizzata integrata con Ricerca di Azure. Non è possibile collegare o unire più indici, ma è possibile creare indici complessi che includono svariati scenari di ricerca necessari per la soluzione. Ad esempio, se l'applicazione supporta più lingue, la raccolta di campi nell'indice potrà avere varianti specifiche di ogni lingua per ogni campo.

Durante la progettazione dell'indice, dedicare alla fase di pianificazione il tempo necessario per valutare attentamente ogni decisione. La modifica di un indice dopo la distribuzione comporta la ricompilazione e il ricaricamento dei dati. Se si crea l'indice nel codice, questo passaggio sarà molto più semplice rispetto alla creazione manuale nel portale.

Una buona comprensione dei dati di origine è essenziale per la creazione di uno schema valido. È consigliabile definire le corrispondenze tra tipi di dati, determinare quali campi usare come *chiave* per identificare in modo univoco ogni documento nell'indice e quali campi esporre nell'elenco dei risultati della ricerca o nella pagina dei dettagli.

**Inizio con dati relazionali**

I dati relazionali possono essere difficili da trasformare in un set di dati flat. Se possibile, provare a usare il data warehouse o il database di report dell'azienda, se disponibile. In genere è la scelta migliore perché il modello è già denormalizzato. In caso contrario, è necessario basarsi sulle query per ottenere il set di righe necessario. Per un esempio e una descrizione di come eseguire questa operazione, vedere il blog sulla [modellazione del database di inventario di AdventureWorks per Ricerca di Azure](http://blogs.technet.com/b/onsearch/archive/2015/09/08/modeling-the-adventureworks-inventory-database-for-azure-search.aspx).

**Inclusione di dati binari**

Potrebbe essere necessario includere contenuto di tipo immagine, audio o video nei risultati della ricerca. In questo caso, i file dovranno essere archiviati in una posizione diversa e nell'indice dovrà essere incluso un campo rappresentativo con un URL della risorsa. Qualsiasi contenuto, in particolare il contenuto binario che non supporta la ricerca, dovrà essere incluso in un archivio più economico e si dovrà quindi farvi riferimento tramite URI in un campo dell'indice.

**Sincronizzazione dei dati**

Come si può immaginare, l'indice deve essere sincronizzato regolarmente con altre origini dati usate nella soluzione. In un catalogo per la vendita al dettaglio online il database di inventario che acquisisce le transazioni di vendita deve avere gli stessi codici di riferimento del prodotto, gli stessi prezzi e la stessa disponibilità dei dati esposti tramite i risultati della ricerca. In base al livello di latenza accettabile per la soluzione, è possibile eseguire la sincronizzazione dei dati solo una volta alla settimana, una volta al giorno o quasi in tempo reale mediante operazioni di scrittura concorrenti nel database di inventario e in un indice di Ricerca di Azure. Le opzioni disponibili sono illustrate in dettaglio in [Importare i dati in Ricerca di Azure](search-what-is-data-import.md).

## Archiviazione sul cloud

Poiché Ricerca di Azure è un servizio di ricerca completamente gestito, l'archiviazione dei dati viene gestita come un'operazione interna. È importante che i programmatori capiscano che non è possibile ignorare le API di Ricerca di Azure per connettersi direttamente all'indice e che le dimensioni o lo stato dell'indice possono essere monitorati solo tramite le notifiche del portale o le chiamate API. Non sono disponibili funzionalità per il backup o il ripristino di un indice o per l'ottimizzazione o perfezionamento delle prestazioni a livello di archiviazione. In background, i dati vengono archiviati sotto forma di archivio BLOB, ma è possibile considerare l'archiviazione fisica e la gestione di un indice come un dettaglio dell'implementazione che può essere modificato in futuro. Uno dei principali vantaggi del servizio è l'approccio automatico alla gestione fisica delle risorse di calcolo dell'applicazione di ricerca.

Se i volumi delle query o i requisiti per l'archiviazione dei dati cambiano nel tempo, è possibile aumentare o ridurre la capacità aggiungendo o spostando repliche e partizioni. Per informazioni dettagliate, vedere [Gestire il servizio di ricerca in Microsoft Azure](search-manage.md) o [Limiti dei servizi in Ricerca di Azure](search-limits-quotas-capacity.md).

<!--Image References-->
[1]: ./media/search-what-is-an-index/search-JSON-indexSchema.png

<!---HONumber=AcomDC_0224_2016-->