<properties 
	pageTitle="API REST di Ricerca di Azure: versione 2014-10-20-Preview" 
	description="API REST di Ricerca di Azure: versione 2014-10-20-Preview" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor="" />

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article"  
	ms.tgt_pltfrm="na" 
	ms.date="07/22/2015" 
	ms.author="heidist" />

# API REST di Ricerca di Azure versione 2014-10-20-Preview #

Questo documento descrive la versione **2014-10-20-Preview** precedente alla release dell'API REST di Ricerca di Azure, rilasciata come aggiornamento alla prima anteprima di Ricerca di Azure. Dal momento che questa versione verrà presto eliminata, si consiglia di usare la versione associata alla release disponibile a livello generale. Per indicazioni sulla migrazione del codice, vedere [Transizione dalla versione di anteprima alla versione dell'API disponibile a livello generale](search-transition-from-preview.md).

Alla versione **2014-10-20-Preview** è correlato anche il contenuto API seguente:

- [Profili di punteggio (API REST di Ricerca di Azure: 2014-10-20-Preview)](search-api-scoring-profiles-2014-10-20-preview.md)

La documentazione relativa alla versione corrente e disponibile a livello generale dell'API REST di Ricerca di Azure è disponibile in MSDN. Per altre informazioni, vedere [API REST di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn798935.aspx).

##Informazioni sull'API REST del servizio##

Ricerca di Azure è un servizio basato sul cloud che è possibile usare per creare applicazioni di ricerca personalizzate. Questo servizio è basato sui concetti di *servizi di ricerca* e *indici*, dove un servizio di ricerca contiene uno o più indici. Il servizio di ricerca è identificato in modo univoco da un nome di dominio completo, ad esempio `mysearchservice.search.windows.net`. Per effetto del provisioning del servizio viene generata una chiave API, usata per autenticare le richieste inviate a Ricerca di Azure.

In Ricerca di Azure è possibile eseguire due tipi di azioni:

- **Gestione degli indici**: le attività amministrative eseguite su un servizio o un indice di ricerca.

- **Azioni sui documenti**: le azioni di query e gestione dell'insieme di dati per un determinato indice.

Le API illustrate in questa sezione consentono di accedere alle operazioni sui dati di ricerca, ad esempio creazione e popolamento degli indici, caricamento di documenti e query. Quando si chiama l'API, tenere presente quanto segue:

- Tutte le API sono definite in termini di richieste e risposte HTTP in formato JSON OData.

- Tutte le API devono disporre di un elemento `api-key` nell'intestazione o nella stringa di query, come descritto nelle note seguenti.

- Tutte le API devono essere eseguite su HTTPS (sulla porta predefinita 443).

- Tutte le richieste API devono includere il parametro della stringa di query `api-version`. Il valore di questo elemento deve essere impostato sulla versione corrente del servizio, come illustrato nell'esempio seguente:

    GET /indexes?api-version=2014-10-20-Preview

- Tutte le richieste API possono impostare l'intestazione HTTP `Accept`. Se il valore non viene impostato, l'impostazione predefinita è `application/json`.

Per l'amministrazione del servizio viene fornita un'API distinta. Tra le operazioni di amministrazione del servizio è incluso, ad esempio, il provisioning del servizio o la modifica della capacità. Per altre informazioni su questa API, vedere l'articolo API REST di gestione di Ricerca di Azure.

### Endpoint ###

L'endpoint per le operazioni del servizio è l'URL di Ricerca di Azure di cui è stato effettuato il provisioning: https://*your-service-name*.search.windows.net.


### Versioni ###

Sono disponibili più versioni dell'API di Ricerca di Azure. Per un elenco delle versioni disponibili, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).


<a name="Authentication"></a>
### Autenticazione e controllo di accesso###

L'autenticazione a Ricerca di Azure richiede due tipi di informazioni: un URL del servizio di ricerca e un elemento `api-key`. Gli elementi `api-keys` vengono generati quando viene creato il servizio e possono essere rigenerati su richiesta dopo il provisioning del servizio. Una `api-key` è sempre una delle seguenti chiavi:

- una chiave amministratore che concede l'accesso a tutte le operazioni, incluse le operazioni di scrittura come la creazione e l'eliminazione di indici;
- una chiave di query che autentica le richieste di sola lettura.

È possibile avere 2 chiavi amministratore e fino a 50 chiavi di query per servizio. La disponibilità di 2 chiavi amministratore è utile se occorre effettuare il rollover di una delle chiavi.

Il controllo di accesso è limitato all'amministrazione del servizio tramite i controlli degli accessi in base al ruolo forniti nel portale di anteprima di Azure. I ruoli vengono usati per impostare i livelli di accesso per l'amministrazione del servizio. Ad esempio, la visualizzazione della chiave amministratore è consentita solo ai ruoli Proprietario e Collaboratore, mentre la visualizzazione dello stato del servizio è consentita ai membri di qualsiasi ruolo.

L'accesso alle operazioni sui dati eseguite in un endpoint del servizio di ricerca, incluse le operazioni di gestione degli indici, popolamento degli indici e query, avviene esclusivamente tramite elementi `api-keys`. Il controllo degli accessi in base al ruolo non si applica alle operazioni correlate ai documenti o agli indici. Per altre informazioni sugli elementi `api-keys` o sul controllo degli accessi in base al ruolo in Ricerca di Azure, vedere [Gestire il servizio di ricerca in Microsoft Azure](search-manage.md).

**Nota**: dal punto di vista della sicurezza, in genere non è consigliabile passare dati sensibili, come un elemento `api-key` nell'URI della richiesta. Per questo motivo, Ricerca di Azure accetta solo una chiave di query come `api-key` nella stringa di query ed è consigliabile evitare di eseguire questa operazione, a meno che i contenuti dell'indice non debbano essere disponibili pubblicamente. In alternativa, è consigliabile passare l'elemento `api-key` come intestazione della richiesta.

###Riepilogo delle API###

L'API di Ricerca di Azure supporta due tipi di sintassi per la ricerca di entità: sintassi [semplice](https://msdn.microsoft.com/library/dn798920.aspx) e sintassi alternativa di OData. Per informazioni dettagliate, vedere [Supporto per OData (API di Ricerca di Azure)](http://msdn.microsoft.com/library/azure/dn798932.aspx). L'elenco seguente mostra la sintassi semplice.

[Creare un indice](#CreateIndex)

    POST /indexes?api-version=2014-10-20-Preview

[Aggiornare un indice](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2014-10-20-Preview

[Ottenere un indice](#GetIndex)

    GET /indexes/[index name]?api-version=2014-10-20-Preview

[Elencare gli indici](#ListIndexes)

    GET /indexes?api-version=2014-10-20-Preview

[Ottenere le statistiche di un indice](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2014-10-20-Preview

[Eliminare un indice](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2014-10-20-Preview

[Aggiungere, aggiornare o eliminare documenti](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2014-10-20-Preview

[Eseguire ricerche nei documenti](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]

[Cercare un documento](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[Contare i documenti](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2014-10-20-Preview

[Suggerimenti](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]

________________________________________
<a name="IndexOps"></a>
## Operazioni sugli indici

È possibile creare e gestire gli indici in Ricerca di Azure tramite semplici richieste HTTP (POST, GET, PUT, DELETE) su una risorsa indice specifica. Per creare un indice, eseguire innanzitutto la richiesta POST di un documento JSON che descrive lo schema dell'indice. Lo schema definisce i campi dell'indice, i relativi tipi di dati e la modalità d'uso, ad esempio ricerche full-text, filtri, ordinamento, uso di facet o suggerimenti. Definisce anche i profili di punteggio, i componenti per il suggerimento e altri attributi per configurare il comportamento dell'indice.

L'esempio seguente illustra uno schema usato per la ricerca di informazioni sugli hotel, con il campo relativo alla descrizione definito in due lingue. Si noti come gli attributi controllano la modalità d'uso del campo. Ad esempio, `hotelId` viene usato come chiave del documento (`"key": true`) ed è escluso dalle ricerche full-text (`"searchable": false`).

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
	  {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, analyzer: "fr.lucene"},
      {"name": "hotelName", "type": "Edm.String"},
      {"name": "category", "type": "Edm.String"},
      {"name": "tags", "type": "Collection(Edm.String)"},
      {"name": "parkingIncluded", "type": "Edm.Boolean"},
      {"name": "smokingAllowed", "type": "Edm.Boolean"},
      {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
      {"name": "rating", "type": "Edm.Int32"},
      {"name": "location", "type": "Edm.GeographyPoint"}
     ],
     "suggesters": [
      {
       "name": "sg",
       "searchMode": "analyzingInfixMatching",
       "sourceFields": ["hotelName"]
      }
     ]
    }

Dopo la creazione dell'indice, si caricano i documenti per popolarlo. Per questo passaggio successivo, vedere [Aggiungere, aggiornare o eliminare documenti](#AddOrUpdateDocuments).

Per un video introduttivo all'indicizzazione di Ricerca di Azure, vedere l'[episodio di Channel 9 Cloud Cover relativo a Ricerca di Azure](http://go.microsoft.com/fwlink/p/?LinkId=511509).


<a name="CreateIndex"></a>
## Creare un indice ##

È possibile creare un nuovo indice in Ricerca di Azure mediante una richiesta HTTP POST. Il corpo della richiesta è un documento JSON che specifica il nome dell'indice, i campi, gli attributi per il controllo del comportamento delle query, il punteggio dei risultati, i componenti per il suggerimento e le opzioni CORS.

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

In alternativa, è possibile usare PUT e specificare il nome dell'indice nell'URI. Se l'indice non esiste, verrà creato.

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

**Nota**: il numero massimo di indici consentiti varia in base al livello di prezzo. Nel servizio gratuito sono consentiti fino a 3 indici. Nel servizio standard sono consentiti 50 indici per servizio di ricerca. Per dettagli, vedere [Limitazioni e vincoli](http://msdn.microsoft.com/library/azure/dn798934.aspx).

**Richiesta**

Per tutte le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta di **creazione di un indice** può essere creata mediante il metodo POST o PUT. Se si usa il metodo POST, è necessario specificare nel corpo della richiesta il nome di un indice e la definizione del relativo schema. Nel caso di PUT, il nome dell'indice fa parte dell'URL. Se l'indice non esiste, viene creato. Se esiste già, viene aggiornato in base alla nuova definizione.

Il nome dell'indice deve essere scritto in caratteri minuscoli, deve iniziare con una lettera o un numero, non deve contenere barre o punti e deve avere una lunghezza inferiore ai 128 caratteri. Dopo l'iniziale costituita da una lettera o un numero, il resto del nome può contenere lettere, numeri e trattini, purché i trattini non siano consecutivi.

L'elemento `api-version` è obbligatorio. I valori validi includono `2014-07-31-Preview` o `2014-10-20-Preview`. È possibile specificare il valore da usare in ogni richiesta per ottenere comportamenti specifici della versione, ma è in genere consigliabile usare la stessa versione in tutto il codice. La versione consigliata per un uso generico è `2014-07-31-Preview`. In alternativa, usare `2014-10-20-Preview` per valutare funzionalità sperimentali, ad esempio il supporto per gli analizzatori di lingua espressi tramite l'attributo dell'indice relativo all'analizzatore. Per informazioni dettagliate sulle versioni dell'API, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx). Per informazioni dettagliate sugli analizzatori di lingua, vedere [Supporto per le lingue](#LanguageSupport).

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `Content-Type`: elemento obbligatorio. Impostare il valore su `application/json`.
- `api-key`: elemento obbligatorio. L'elemento `api-key` viene usato per
- per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta di **creazione dell'indice** deve includere un'intestazione `api-key` impostata sulla chiave amministratore, anziché su una chiave di query.

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio sia `api-key` dal dashboard servizi nel portale di anteprima di Azure. Per informazioni, vedere [Introduzione a Ricerca di Azure](search-get-started.md).

<a name="RequestData"></a> **Sintassi del corpo della richiesta**

Il corpo della richiesta contiene una definizione di schema che include l'elenco dei campi dati all'interno dei documenti che verranno inseriti nell'indice, i tipi di dati, gli attributi e un elenco facoltativo dei profili di punteggio che vengono usati per assegnare un punteggio ai documenti corrispondenti quando si esegue la query.

Si noti che per una richiesta POST, è necessario specificare il nome dell'indice nel corpo della richiesta.

Può essere presente un solo campo chiave nell'indice e tale campo deve essere di tipo stringa. Il campo rappresenta l'identificatore univoco per ogni documento archiviato all'interno dell'indice.

La sintassi per la strutturazione del payload della richiesta è la seguente: Più avanti in questo argomento è riportata una richiesta di esempio.

    {
      "name": (optional on PUT; required on POST) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "suggestions": true | false (default, only Edm.String and Collection(Edm.String) fields can be used for suggestions),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,
		  "analyzer": "name of text analyzer"
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive #'s),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
			  "tag": {
				"tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }

Nota: il tipo di dati `Edm.Int64` è supportato a partire dalla versione 2014-10-20-Preview dell'API.

**Attributi dell'indice**

Quando si crea un indice, è possibile impostare gli attributi seguenti. Per informazioni dettagliate sull'assegnazione dei punteggi e sui profili di punteggio, vedere [Profili di punteggio (API REST di Ricerca di Azure: 2014-10-20-Preview)](search-api-scoring-profiles-2014-10-20-preview.md).

`name`: imposta il nome del campo.

`type`: imposta il tipo di dati per il campo. Per un elenco di tipi supportati, vedere [Tipi di dati supportati](#DataTypes).

`searchable`: contrassegna il campo come disponibile per la ricerca full-text. Ciò significa che verrà sottoposto ad analisi, ad esempio la suddivisione in parole durante l'indicizzazione. Se si imposta un campo `searchable` su un valore come "sunny day", questo viene suddiviso internamente nei singoli token "sunny" e "day". È così possibile eseguire ricerche full-text di questi termini. I campi di tipo `Edm.String` o `Collection(Edm.String)` sono `searchable` per impostazione predefinita. I campi di altri tipi non possono essere `searchable`.

  - **Nota**: i campi `searchable` utilizzano spazio aggiuntivo nell'indice perché Ricerca di Azure archivia una versione supplementare in formato token del valore del campo per le ricerche full-text. Se si vuole risparmiare spazio nell'indice e non è necessario includere un campo nelle ricerche, impostare `searchable` su `false`.

`filterable`: consente il riferimento al campo nelle query `$filter`. La differenza tra `filterable` e `searchable` consiste nel modo in cui vengono gestite le stringhe. I campi di tipo `Edm.String` o `Collection(Edm.String)` che sono `filterable` non sono sottoposti a suddivisione delle parole e quindi i confronti riguardano solo le corrispondenze esatte. Se ad esempio si imposta un campo `f` su "sunny day", `$filter=f eq 'sunny'` non troverà corrispondenze, mentre `$filter=f eq 'sunny day'` ne troverà. Tutti i campi sono `filterable` per impostazione predefinita.

`sortable`: per impostazione predefinita, il sistema ordina i risultati in base al punteggio, ma in molti casi gli utenti preferiscono eseguire l'ordinamento in base ai campi nei documenti. I campi di tipo `Collection(Edm.String)` non possono essere `sortable`. Tutti gli altri campi sono `sortable` per impostazione predefinita.

`facetable`: usato generalmente in una presentazione dei risultati di ricerca che include il numero di risultati per categoria (ad esempio, per cercare fotocamere digitali e visualizzare i risultati in base alla marca, ai megapixel, al prezzo e così via). Questa opzione non può essere usata con i campi di tipo `Edm.GeographyPoint`. Tutti gli altri campi sono `facetable` per impostazione predefinita.

  - **Nota**: i campi di tipo `Edm.String` che sono `filterable`, `sortable` o `facetable` possono avere al massimo una lunghezza di 32 KB. Questa limitazione è dovuta al fatto che questi campi sono considerati un unico termine di ricerca e la lunghezza massima di un termine in Ricerca di Azure è 32 KB. Se è necessario archiviare più testo in un campo a stringa singola, impostare esplicitamente `filterable`, `sortable` e `facetable` su `false` nella definizione dell'indice.

`suggestions`: specifica se il campo può essere usato per il completamento automatico durante la digitazione. Può essere impostato solo per i campi di tipo `Edm.String` o `Collection(Edm.String)`. `suggestions` è `false` per impostazione predefinita poiché richiede spazio aggiuntivo nell'indice. **Nota**: per i suggerimenti è consigliabile usare la proprietà `suggesters` introdotta nella versione 2014-10-20-Preview in sostituzione di questa opzione. In una futura versione la proprietà `suggestions` verrà deprecata a favore di una specifica di `suggesters` separata.

  - **Nota**: se per un campo nessuno degli attributi elencati è impostato su `true` (`searchable`, `filterable`, `sortable`, `facetable` o `suggestions`), il campo viene effettivamente escluso dall'indice invertito. Questa opzione è utile per i campi che non vengono usati nelle query, ma che sono necessari nei risultati della ricerca. L'esclusione di tali campi dall'indice consente di ottenere migliori prestazioni.

`key`: contrassegna il campo come contenente identificatori univoci per i documenti all'interno dell'indice. È necessario scegliere un singolo campo come `key` e questo deve essere di tipo `Edm.String`. I campi chiave possono essere usati per la ricerca diretta di documenti tramite l'[API di ricerca](#LookupAPI).

`retrievable`: specifica se il campo può essere restituito nel risultato di una ricerca. Questo attributo è utile quando si vuole usare un campo, ad esempio quello relativo al margine, come meccanismo di filtro, ordinamento o punteggio ma si preferisce che il campo non sia visibile all'utente finale. L'attributo deve essere `true` per i campi `key`.

`scoringProfiles`: definisce i comportamenti di punteggio personalizzati che consentono di determinare quali elementi verranno visualizzati più in alto nei risultati della ricerca. I profili di punteggio sono costituiti da funzioni e campi ponderati. Per altre informazioni sugli attributi usati in un profilo di punteggio, vedere [Profili di punteggio (API REST di Ricerca di Azure: 2014-10-20-Preview)](search-api-scoring-profiles-2014-10-20-preview.md).

`analyzer`: imposta il nome dell'analizzatore di testo da usare per il campo. Per il set di valori consentito, vedere [Supporto per le lingue](#LanguageSupport). Questa opzione può essere usata solo con i campi `searchable`. Una volta scelto, l'analizzatore non può essere cambiato per il campo.


<a name="LanguageSupport"></a> **Supporto per le lingue**

I campi disponibili per la ricerca vengono sottoposti a un processo di analisi, che spesso comporta la suddivisione in parole, la normalizzazione del testo e l'esclusione di termini tramite filtro. Per impostazione predefinita, i campi disponibili per la ricerca in Ricerca di Azure vengono analizzati con l'[analizzatore Apache Lucene Standard](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html), che suddivide il testo in elementi sulla base delle regole di [segmentazione del testo Unicode](http://unicode.org/reports/tr29/). L'analizzatore standard, inoltre, converte tutti i caratteri nel rispettivo formato minuscolo. I documenti indicizzati e i termini di ricerca vengono sottoposti ad analisi durante l'indicizzazione e l'elaborazione delle query.

Ricerca di Azure consente l'indicizzazione di campi in numerose lingue. Ogni lingua richiede un analizzatore di testo non standard che tenga conto delle caratteristiche specifiche della lingua. Ad esempio, l'analizzatore per la lingua francese applica uno stemmer denominato [Light French Stemmer](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/fr/FrenchLightStemmer.html) per ridurre le parole alle rispettive [forme base](http://en.wikipedia.org/wiki/Stemming). Rimuove inoltre le [elisioni](http://en.wikipedia.org/wiki/Elision) e le parole francesi non significative dal testo analizzato. L'analizzatore per la lingua inglese estende l'analizzatore standard. Rimuove il genitivo sassone (la 's finale) dalle parole, applica lo stemming in base all'[algoritmo Porter Stemming](http://tartarus.org/~martin/PorterStemmer/) e rimuove le [parole non significative](http://en.wikipedia.org/wiki/Stop_words) per la lingua inglese.

È possibile configurare l'analizzatore in modo indipendente per ogni campo nella definizione dell'indice impostando la proprietà `analyzer`. Ad esempio, nello stesso indice possono essere presenti campi separati per le descrizioni di hotel in lingua inglese, francese e spagnola. La query definisce il campo specifico della lingua da restituire nei risultati della ricerca.

Di seguito è riportato l'elenco di analizzatori supportati con una breve descrizione delle funzionalità disponibili:

<table style="font-size:12">
    <tr>
		<th>Lingua</th>
		<th>Nome analizzatore</th>
		<th>Descrizione</th>
	</tr>
    <tr>
		<td>Arabo</td>
		<td>ar.lucene</td>
		<td>
		<ul>
			<li>Implementa la normalizzazione ortografica per l'arabo</li>
			<li>Applica lo stemming algoritmico semplificato</li>
			<li>Esclude tramite filtro le parole arabe non significative</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Brasiliano</td>
		<td>pt-Br.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole brasiliane non significative</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Cinese semplificato</td>
		<td>zh-Hans.lucene</td>
		<td>
		<ul>
			<li>Usa modelli di conoscenza probabilistica per individuare la segmentazione ottimale per le parole</li>
			<li>Esclude tramite filtro le parole cinesi non significative</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Cinese tradizionale</td>
		<td>zh-Hant.lucene</td>
		<td>
		<ul>
			<li>Indicizza i bigrammi (gruppi sovrapposti di due caratteri cinesi adiacenti)</li>
			<li>Normalizza le differenze di larghezza dei caratteri</li>
		</ul>
		</td>
	<tr>
    <tr>
		<td>Ceco</td>
		<td>cs.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole ceche non significative</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Danese</td>
		<td>da.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole danesi non significative</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Olandese</td>
		<td>nl.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole olandesi non significative</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Tedesco</td>
		<td>de.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole tedesche non significative</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Greco</td>
		<td>el.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole greche non significative</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Inglese</td>
		<td>en.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole inglesi non significative</li>
			<li>Rimuove il genitivo sassone</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Finlandese</td>
		<td>fi.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole finlandesi non significative</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Francese</td>
		<td>fr.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole francesi non significative</li>
			<li>Rimuove le elisioni</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Hindi</td>
		<td>hi.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole hindi non significative</li>
			<li>Rimuove alcune differenze nelle variazioni di ortografia</li>
			<li>Normalizza la rappresentazione Unicode del testo nelle lingue indiane</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Ungherese</td>
		<td>hu.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole ungheresi non significative</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Indonesiano (Bahasa)</td>
		<td>id.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole indonesiane non significative</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Italiano</td>
		<td>it.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole italiane non significative</li>
			<li>Rimuove le elisioni</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Giapponese</td>
		<td>ja.lucene</td>
		<td>
		<ul>
			<li>Usa l'analisi morfologica</li>
			<li>Normalizza le variazioni di ortografia katakana comuni</li>
			<li>Esegue la rimozione semplificata di parole o tag non significativi</li>
			<li>Normalizza la larghezza dei caratteri</li>
			<li>Esegue la lemmatizzazione riducendo aggettivi e verbi flessi alla rispettiva forma base</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Coreano</td>
		<td>ko.lucene</td>
		<td>
		<ul>
			<li>Indicizza i bigrammi (gruppi sovrapposti di due caratteri cinesi adiacenti)</li>
			<li>Normalizza le differenze di larghezza dei caratteri</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Lettone</td>
		<td>lv.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole lettoni non significative</li>
		</ul>
		</td>
	</tr>

    <tr>
		<td>Norvegese</td>
		<td>no.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole norvegesi non significative</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Polacco</td>
		<td>pl.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming algoritmico (Stempel)</li>
			<li>Esclude tramite filtro le parole polacche non significative</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Portoghese</td>
		<td>pt-Pt.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole portoghesi non significative</li>
		</ul>
		</td>
	</tr>

    <tr>
		<td>Romeno</td>
		<td>ro.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole romene non significative</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Russo</td>
		<td>ru.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole russe non significative</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Spagnolo</td>
		<td>es.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole spagnole non significative</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Svedese</td>
		<td>sv.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole svedesi non significative</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Turco</td>
		<td>tr.lucene</td>
		<td>
		<ul>
			<li>Rimuove tutti i caratteri dopo un apostrofo (incluso l'apostrofo stesso)</li>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole turche non significative</li>
		</ul>
		</td>
	</tr>
    <tr>
		<td>Thai</td>
		<td>th.lucene</td>
		<td>
		<ul>
			<li>Applica lo stemming semplificato</li>
			<li>Esclude tramite filtro le parole thai non significative</li>
		</ul>
		</td>
	</tr>
	<td colspan="3">Ricerca di Azure fornisce inoltre configurazioni dell'analizzatore indipendenti dalla lingua</td>
    <tr>
		<td>Riduzione ASCII standard</td>
		<td>standardasciifolding.lucene</td>
		<td>
		<ul>
			<li>Segmentazione di testo Unicode (tokenizer standard)</li>
			<li>Filtro di riduzione ASCII: converte i caratteri Unicode che non appartengono al set dei primi 127 caratteri ASCII nei rispettivi equivalenti ASCII. Questo filtro è utile per la rimozione dei segni diacritici.</li>
		</ul>
		</td>
	</tr>
</table>

Tutti gli analizzatori con nomi contenenti la parola <i>lucene</i> sono basati sugli [analizzatori di lingua Apache Lucene](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html). Altre informazioni sul filtro di riduzione ASCII sono disponibili [qui](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html).

**Opzioni CORS**

JavaScript sul lato client non può chiamare API per impostazione predefinita perché il browser impedisce tutte le richieste con origini diverse. Per consentire query con origini diverse nell'indice, abilitare CORS (Cross-Origin Resource Sharing) impostando l'attributo `corsOptions`. Si noti che, per motivi di sicurezza, solo le API di query supportano CORS. Per CORS è possibile impostare le opzioni seguenti:

- `allowedOrigins` (obbligatoria): si tratta di un elenco di origini a cui verrà concesso l'accesso all'indice. Questo significa che al codice JavaScript servito da queste origini sarà consentito eseguire query sull'indice, purché fornisca la chiave API corretta. Ogni origine è in genere nel formato `protocol://fully-qualified-domain-name:port` anche se spesso la porta viene omessa. Per altri dettagli, vedere [questo articolo](http://go.microsoft.com/fwlink/?LinkId=330822).
 - Per consentire l'accesso a tutte le origini, includere `*` come unico elemento nella matrice `allowedOrigins`. Tenere presente che **questa non è la procedura consigliata per i servizi di ricerca di produzione**. Può comunque essere utile per finalità di sviluppo o debug.
- `maxAgeInSeconds` (facoltativa): i browser usano questo valore per determinare la durata (in secondi) di memorizzazione nella cache delle risposte preliminari CORS. Questo valore deve essere un intero non negativo. A un valore più grande corrispondono prestazioni migliori, ma deve trascorrere più tempo prima che le modifiche dei criteri CORS diventino effettive. Se questo valore non è impostato, viene usata una durata predefinita di 5 minuti.

<a name="Suggesters"></a> **Componenti per il suggerimento**

Un componente per il suggerimento consente il completamento automatico nelle ricerche. In genere le stringhe di ricerca parziali vengono inviate all'API per i suggerimenti mentre l'utente digita la query di ricerca e l'API restituisce un set di espressioni suggerite.

È in corso la transizione di Ricerca di Azure a una nuova API di componenti per il suggerimento. Nella versione 2014-07-31-Preview è inclusa un'API per i suggerimenti più limitata, dove un campo può essere contrassegnato con `"suggestions": true` e possono essere generati suggerimenti di prefisso per stringhe brevi (da 3 a 25 caratteri). A partire dalla versione 2014-10-20-Preview di Ricerca di Azure, sono disponibili funzionalità più avanzate per i suggerimenti basate su appositi componenti, come descritto in questa sezione. Questa nuova implementazione supporta le corrispondenze di prefissi e infissi e ha una migliore tolleranza per gli errori nelle stringhe di ricerca. A partire dalla versione 2014-10-20-Preview, si consiglia di usare la nuova API di componenti per il suggerimento.

Il supporto corrente di questi componenti funziona in modo ottimale quando viene usato per suggerire documenti specifici anziché espressioni o termini separati. I campi di origine validi per questo tipo di componenti sono i titoli, i nomi e altre espressioni relativamente brevi che possono identificare un elemento. I tipi di campi che tendono a essere meno efficaci sono quelli molto ripetitivi, come le categorie e i tag, o quelli molto lunghi, come le descrizioni o i commenti.

Come parte della definizione dell'indice è possibile aggiungere un solo componente per il suggerimento alla raccolta `suggesters`. Un componente per il suggerimento è definito dalle proprietà seguenti:

- `name`: nome del componente. Questo nome viene usato nelle chiamate all'API `suggest`.
- `searchMode`: strategia usata per la ricerca di espressioni candidate. L'unica modalità attualmente supportata è `analyzingInfixMatching`, che ricerca una corrispondenza flessibile di espressioni all'inizio o all'interno di frasi.
- `sourceFields`: elenco di uno o più campi che sono l'origine del contenuto per i suggerimenti. Solo i campi di tipo `Edm.String` e `Collection(Edm.String)` possono essere origini per i suggerimenti. Nella versione di anteprima corrente dei componenti per il suggerimento è possibile usare solo campi per cui non è impostato un analizzatore personalizzato.

Nella versione corrente dell'API è possibile avere un solo componente per il suggerimento per ogni raccolta.

<a name="CreateUpdateIndexExample"></a> **Esempio di corpo della richiesta**

    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
	    {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, analyzer="fr.lucene"},
        {"name": "hotelName", "type": "Edm.String"},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean"},
        {"name": "smokingAllowed", "type": "Edm.Boolean"},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["hotelName"]
        }
      ]
    }

**Risposta**

Per una richiesta con esito positivo: "201 Creato".

Per impostazione predefinita, il corpo della risposta contiene il codice JSON per la definizione di indice creata. Se l'intestazione della richiesta `Prefer` è impostata su `return=minimal`, il corpo della risposta sarà vuoto e il codice di stato per l'esito positivo sarà "204 Nessun contenuto" anziché "201 Creato". Questo vale indipendentemente dall'uso del metodo PUT o POST per creare l'indice.

<a name="UpdateIndex"></a>
## Aggiornare un indice ##

È possibile aggiornare un indice esistente in Ricerca di Azure usando una richiesta HTTP PUT. Nella versione di anteprima pubblica le operazioni di aggiornamento valide includono l'aggiunta di nuovi campi allo schema esistente, la modifica delle opzioni CORS e la modifica dei profili di punteggio. Vedere [Profili di punteggio (API REST di Ricerca di Azure: 2014-10-20-Preview)](search-api-scoring-profiles-2014-10-20-preview.md). È necessario specificare il nome dell'indice da aggiornare nell'URI della richiesta:

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Importante**: il supporto per gli aggiornamenti dello schema di indice è limitato alle operazioni che non richiedono la ricompilazione dell'indice di ricerca. Gli aggiornamenti dello schema che richiedono la reindicizzazione, ad esempio la modifica dei tipi di campo, non sono attualmente supportati. È possibile aggiungere nuovi campi in qualsiasi momento, anche se i campi esistenti non possono essere modificati o eliminati. Lo stesso vale per i componenti per il suggerimento. È possibile aggiungere nuovi campi a un componente di questo tipo, ma non è possibile rimuoverli né aggiungere campi esistenti.

Quando si aggiunge un nuovo campo a un indice, tutti i documenti esistenti nell'indice avranno automaticamente un valore Null per tale campo. Non verrà utilizzato ulteriore spazio di archiviazione finché non verranno aggiunti nuovi documenti all'indice.

**Richiesta**

Per tutte le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta di **aggiornamento di un indice** viene creata mediante HTTP PUT. Nel caso di PUT, il nome dell'indice fa parte dell'URL. Se l'indice non esiste, viene creato. Se esiste già, viene aggiornato in base alla nuova definizione.

Il nome dell'indice deve essere scritto in caratteri minuscoli, deve iniziare con una lettera o un numero, non deve contenere barre o punti e deve avere una lunghezza inferiore ai 128 caratteri. Dopo l'iniziale costituita da una lettera o un numero, il resto del nome può contenere lettere, numeri e trattini, purché i trattini non siano consecutivi.

Il parametro `api-version` è obbligatorio. I valori validi includono `2014-07-31-Preview` o `2014-10-20-Preview`. È possibile specificare il valore da usare in ogni richiesta per ottenere comportamenti specifici della versione, ma è in genere consigliabile usare la stessa versione in tutto il codice. La versione consigliata per un uso generico è `2014-07-31-Preview`. In alternativa, usare `2014-10-20-Preview` per valutare le funzionalità sperimentali. Per informazioni dettagliate, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `Content-Type`: elemento obbligatorio. Impostare il valore su `application/json`.
- `api-key`: elemento obbligatorio. L'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta di **aggiornamento dell'indice** deve includere un'intestazione `api-key` impostata sulla chiave amministratore, anziché su una chiave di query.

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio e `api-key` dal dashboard servizi nel portale di anteprima di Azure. Per informazioni, vedere [Introduzione a Ricerca di Azure](search-get-started.md).

**Sintassi del corpo della richiesta**

Quando si aggiorna un indice esistente, il corpo deve includere la definizione di schema originale e i nuovi campi aggiunti, nonché i profili di punteggio modificati, i componenti per il suggerimento e le opzioni CORS, se presenti. Se non si modificano i profili di punteggio e le opzioni CORS, è necessario includere i valori originali da quando è stato creato l'indice. In generale, la procedura ottimale per gli aggiornamenti consiste nel recuperare la definizione dell'indice mediante GET, modificarla e quindi aggiornarla mediante PUT.

Per comodità, di seguito è riprodotta la sintassi dello schema usata per creare un indice. Per altri dettagli, vedere [Creare un indice](#CreateIndex).

    {
      "name": (optional) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "suggestions": true | false (default, only Edm.String and Collection(Edm.String) fields can be used for suggestions),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,
		  "analyzer": "name of text analyzer"
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive #'s),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
			  "tag": {
				"tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }

Nota: il tipo di dati `Edm.Int64` è supportato a partire dalla versione 2014-10-20-Preview dell'API.

**Risposta**

Per una richiesta con esito positivo: "204 Nessun contenuto".

Per impostazione predefinita, il corpo della risposta è vuoto. Se invece l'intestazione della richiesta `Prefer` è impostata su `return=representation`, il corpo della risposta conterrà il codice JSON per la definizione di indice aggiornata. In questo caso, il codice di stato di esito positivo sarà "200 OK".

<a name="ListIndexes"></a>
## Elencare gli indici ##

L'operazione per **elencare gli indici** restituisce un elenco degli indici attualmente disponibili in Ricerca di Azure.

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**Richiesta**

Per tutte le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta per **elencare gli indici** può essere creata mediante il metodo GET.

Il parametro `api-version` è obbligatorio. I valori validi includono `2014-07-31-Preview` o `2014-10-20-Preview`. È possibile specificare il valore da usare in ogni richiesta per ottenere comportamenti specifici della versione, ma è in genere consigliabile usare la stessa versione in tutto il codice. La versione consigliata per un uso generico è `2014-07-31-Preview`. In alternativa, usare `2014-10-20-Preview` per valutare le funzionalità sperimentali. Per informazioni dettagliate, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `api-key`: elemento obbligatorio. L'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta per **elencare gli indici** deve includere un'intestazione `api-key` impostata su una chiave amministratore, anziché su una chiave di query.

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio e `api-key` dal dashboard servizi nel portale di anteprima di Azure. Per informazioni, vedere [Introduzione a Ricerca di Azure](search-get-started.md).

**Corpo della richiesta**

Nessuno.

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 200 OK.

Di seguito è riportato il corpo di una risposta di esempio:

    {
      "value": [
        {
          "name": "Books",
          "fields": [
            {"name": "ISBN", ...},
            ...
          ]
        },
        {
          "name": "Games",
          ...
        },
        ...
      ]
    }

Si noti che è possibile filtrare la risposta fino a visualizzare solo le proprietà a cui si è interessati. Ad esempio, se si vuole solo un elenco di nomi di indice, usare l'opzione di query `$select` di OData:

    GET /indexes?api-version=2014-10-20-Preview&$select=name

In questo caso, la risposta dell'esempio precedente sarà simile alla seguente:

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

Questa è una tecnica utile per risparmiare larghezza di banda, se nel servizio di ricerca sono presenti numerosi indici.

<a name="GetIndex"></a>
## Ottenere un indice ##

L'operazione per **ottenere un indice** recupera la definizione dell'indice da Ricerca di Azure.

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Richiesta**

Per le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta per **ottenere un indice** può essere creata mediante il metodo GET.

Il valore [index name] nell'URI della richiesta specifica l'indice da restituire dalla raccolta di indici.

Il parametro `api-version` è obbligatorio. I valori validi includono `2014-07-31-Preview` o `2014-10-20-Preview`. È possibile specificare il valore da usare in ogni richiesta per ottenere comportamenti specifici della versione, ma è in genere consigliabile usare la stessa versione in tutto il codice. La versione consigliata per un uso generico è `2014-07-31-Preview`. In alternativa, usare `2014-10-20-Preview` per valutare le funzionalità sperimentali. Per informazioni dettagliate, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `api-key`: l'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta per **ottenere un indice** deve includere un'intestazione `api-key` impostata su una chiave amministratore, anziché su una chiave di query.

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio e `api-key` dal dashboard servizi nel portale di anteprima di Azure. Per informazioni, vedere [Introduzione a Ricerca di Azure](search-get-started.md).

**Corpo della richiesta**

Nessuno.

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 200 OK.

Per un esempio di payload di risposta, vedere l'esempio JSON nella sezione relativa alla [creazione di un indice](#CreateUpdateIndexExample).

<a name="DeleteIndex"></a>
## Eliminare un indice ##

L'operazione di **eliminazione di un indice** rimuove un indice e i documenti associati da Ricerca di Azure. È possibile ottenere il nome dell'indice dal dashboard servizi nel portale di anteprima di Azure oppure dall'API. Per informazioni dettagliate, vedere [Elencare gli indici](#ListIndexes).

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Richiesta**

Per le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta di **eliminazione di un indice** può essere creata mediante il metodo DELETE.

Il valore [index name] nell'URI della richiesta specifica l'indice da eliminare dalla raccolta di indici.

Il parametro `api-version` è obbligatorio. I valori validi includono `2014-07-31-Preview` o `2014-10-20-Preview`. È possibile specificare il valore da usare in ogni richiesta per ottenere comportamenti specifici della versione, ma è in genere consigliabile usare la stessa versione in tutto il codice. La versione consigliata per un uso generico è `2014-07-31-Preview`. In alternativa, usare `2014-10-20-Preview` per valutare le funzionalità sperimentali. Per informazioni dettagliate, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `api-key`: elemento obbligatorio. L'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per l'URL del servizio. La richiesta di **eliminazione di un indice** deve includere un'intestazione `api-key` impostata sulla chiave amministratore, anziché su una chiave di query.

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio e `api-key` dal dashboard servizi nel portale di anteprima di Azure. Per informazioni, vedere [Introduzione a Ricerca di Azure](search-get-started.md).

**Corpo della richiesta**

Nessuno.

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 204 Nessun contenuto.

<a name="GetIndexStats"></a>
## Ottenere le statistiche di un indice ##

L'operazione per **ottenere le statistiche di un indice** restituisce il numero dei documenti per l'indice corrente da Ricerca di Azure, oltre all'utilizzo dello spazio di archiviazione.

	GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

**Richiesta**

Per tutte le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta per **ottenere le statistiche di un indice** può essere creata mediante il metodo GET.

Il valore [index name] nell'URI della richiesta indica al servizio di restituire le statistiche per l'indice specificato.

Il parametro `api-version` è obbligatorio. I valori validi includono `2014-07-31-Preview` o `2014-10-20-Preview`. È possibile specificare il valore da usare in ogni richiesta per ottenere comportamenti specifici della versione, ma è in genere consigliabile usare la stessa versione in tutto il codice. La versione consigliata per un uso generico è `2014-07-31-Preview`. In alternativa, usare `2014-10-20-Preview` per valutare le funzionalità sperimentali. Per informazioni dettagliate, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `api-key`: l'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta per **ottenere le statistiche di un indice** deve includere un'intestazione `api-key` impostata su una chiave amministratore, anziché su una chiave di query.

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio e `api-key` dal dashboard servizi nel portale di anteprima di Azure. Per informazioni, vedere [Introduzione a Ricerca di Azure](search-get-started.md).

**Corpo della richiesta**

Nessuno.

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 200 OK.

Il corpo della risposta è nel formato seguente:

    {
      "documentCount": number,
	  "storageSize": number (size of the index in bytes)
    }

________________________________________
<a name="DocOps"></a>
## Operazioni sui documenti

In Ricerca di Azure un indice viene archiviato nel cloud e popolato usando i documenti JSON che vengono caricati nel servizio. Tutti i documenti caricati costituiscono l'insieme dei dati di ricerca. I documenti includono campi, alcuni dei quali sono stati suddivisi in token corrispondenti a termini di ricerca durante il caricamento. Il segmento `/docs` dell'URL nell'API di Ricerca di Azure rappresenta la raccolta di documenti in un indice. Tutte le operazioni sulla raccolta, ad esempio caricamento, unione, eliminazione o query nei documenti, vengono eseguite nel contesto di un singolo indice e quindi gli URL per queste operazioni inizieranno sempre con `/indexes/[index name]/docs` per un nome di indice specifico.

Il codice dell'applicazione deve generare i documenti JSON da caricare in Ricerca di Azure. In genere, gli indici vengono popolati da un singolo set di dati specificato dall'utente.

È consigliabile avere a disposizione un documento per ogni elemento da cercare. Un'applicazione per il noleggio di film può avere un documento per ogni film, un'applicazione di tipo vetrina può avere un documento per ogni SKU, un'applicazione per corsi online può avere un documento per ogni corso oppure una società di ricerche può avere un documento per ogni articolo accademico disponibile nel proprio archivio e così via.

I documenti sono costituiti da uno o più campi. I campi possono contenere testo suddiviso da Ricerca di Azure in token corrispondenti a termini di ricerca, oltre a valori senza token o non di testo che possono essere usati nei filtri o nei profili di punteggio. I nomi, i tipi di dati e le funzionalità di ricerca supportati per ogni campo sono determinati dallo schema dell'indice. Uno dei campi in ogni schema di indice deve essere designato come ID e ogni documento deve avere un valore per il campo ID che identifica in modo univoco tale documento nell'indice. Tutti gli altri campi dei documenti sono facoltativi e, se non è specificato alcun valore, viene usato il valore predefinito Null. Si noti che i valori Null non occupano spazio nell'indice di ricerca.

Prima di poter caricare documenti, è necessario aver creato l'indice nel servizio. Per informazioni dettagliate su questo primo passaggio, vedere [Creare un indice](#CreateIndex).

**Nota**: questa versione dell'API fornisce funzionalità di ricerca full-text solo in inglese.

<a name="AddOrUpdateDocuments"></a>
## Aggiungere, aggiornare o eliminare documenti ##

È possibile caricare, unire o eliminare documenti da un indice specificato usando HTTP POST. Per un numero elevato di aggiornamenti, è consigliabile creare batch di documenti (fino a 1000 documenti per batch o circa 16 MB per batch).

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Richiesta**

Per tutte le richieste del servizio, è necessario usare il protocollo HTTPS. È possibile caricare, unire o eliminare documenti da un indice specificato usando HTTP POST.

L'URI della richiesta include [index name], che specifica l'indice su cui eseguire l'operazione POST sui documenti. Questa operazione può essere eseguita su un solo indice alla volta.

Il parametro `api-version` è obbligatorio. I valori validi includono `2014-07-31-Preview` o `2014-10-20-Preview`. È possibile specificare il valore da usare in ogni richiesta per ottenere comportamenti specifici della versione, ma è in genere consigliabile usare la stessa versione in tutto il codice. La versione consigliata per un uso generico è `2014-07-31-Preview`. In alternativa, usare `2014-10-20-Preview` per valutare le funzionalità sperimentali. Per informazioni dettagliate, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `Content-Type`: elemento obbligatorio. Impostare il valore su `application/json`.
- `api-key`: elemento obbligatorio. L'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta di **aggiunta di documenti** deve includere un'intestazione `api-key` impostata sulla chiave amministratore, anziché su una chiave di query.

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio e `api-key` dal dashboard servizi nel portale di anteprima di Azure. Per informazioni, vedere [Introduzione a Ricerca di Azure](search-get-started.md).

**Corpo della richiesta**

Il corpo della richiesta contiene uno o più documenti da indicizzare. I documenti sono identificati da una chiave univoca. Ogni documento è associato a un'azione: upload, merge, mergeOrUpload o delete. Le richieste di caricamento devono includere i dati del documento sotto forma di coppie chiave/valore.

    {
      "value": [
        {
          "@search.action": "upload (default) | merge | mergeOrUpload | delete",
          "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)
          "field_name": field_value (key/value pairs matching index schema)
            ...
        },
        ...
      ]
    }

**Azioni sui documenti**

- `upload`: questa azione è simile a "upsert", in cui il documento viene inserito se è nuovo e aggiornato o sostituito se già esistente. Si noti che nel caso dell'aggiornamento vengono sostituiti tutti i campi.
- `merge`: questa azione aggiorna un documento esistente con i campi specificati. Se il documento non esiste, l'unione ha esito negativo. I campi specificati in un'azione di unione sostituiscono i campi esistenti nel documento. Sono inclusi anche i campi di tipo `Collection(Edm.String)`. Ad esempio, se il documento contiene un campo "tags" con valore `["budget"]` e si esegue un'azione di unione con valore `["economy", "pool"]` per "tags", il valore finale del campo "tag" sarà `["economy", "pool"]` e **non** `["budget", "economy", "pool"]`.
- `mergeOrUpload`: questa azione si comporta come `merge` se nell'indice esiste già un documento con la chiave specificata. Se invece il documento non è presente, l'azione si comporta come `upload` con un nuovo documento.
- `delete`: questa azione rimuove il documento specificato dall'indice. Si noti che in un'operazione `delete` è possibile specificare solo il valore del campo chiave. Se si tenta di specificare altri campi, verrà generato un errore HTTP 400. Se si vuole rimuovere un singolo campo da un documento, usare invece `merge` e impostare il campo su `null` in modo esplicito.

**Risposta**

Il codice di stato 200 OK viene restituito per una risposta con esito positivo, ovvero se tutti gli elementi sono stati indicizzati correttamente, come indicato dal campo 'status' impostato su true per tutti gli elementi:

    {
      "value": [
        {
          "key": "unique_key_of_document",
          "status": true,
          "errorMessage": null
        }
      ]
    }  

Il codice di stato 207 viene restituito quando almeno un elemento non è stato indicizzato correttamente, come indicato dal campo 'status' impostato su false per gli elementi che non sono stati indicizzati:

    {
      "value": [
        {
          "key": "unique_key_of_document",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later."
        }
      ]
    }  

La proprietà `errorMessage` indicherà il motivo dell'errore di indicizzazione, se possibile.

**Nota**: se il codice del client riceve spesso una risposta 207, il problema può dipendere dal carico eccessivo del sistema. Per una conferma, controllare la proprietà `errorMessage`. Se si verifica questo problema, è consigliabile ***limitare le richieste di indicizzazione***. Se non si riduce il traffico di indicizzazione, è possibile che il sistema inizi a rifiutare tutte le richieste con errori 503.

Il codice di stato 429 indica che è stata superata la quota del numero di documenti per indice. È necessario creare un nuovo indice o effettuare l'aggiornamento per ottenere limiti di capacità più elevati.

**Esempio:**

    {
      "value": [
        {
          "@search.action": "upload",
          "hotelId": "1",
          "baseRate": 199.0,
          "description": "Best hotel in town",
		  "description_fr": "Meilleur hôtel en ville",
          "hotelName": "Fancy Stay",
		  "category": "Luxury",
          "tags": ["pool", "view", "wifi", "concierge"],
          "parkingIncluded": false,
		  "smokingAllowed": false,
          "lastRenovationDate": "2010-06-27T00:00:00Z",
          "rating": 5,
          "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
          "@search.action": "upload",
          "hotelId": "2",
          "baseRate": 79.99,
          "description": "Cheapest hotel in town",
	      "description_fr": "Hôtel le moins cher en ville",
          "hotelName": "Roach Motel",
		  "category": "Budget",
          "tags": ["motel", "budget"],
          "parkingIncluded": true,
		  "smokingAllowed": true,
          "lastRenovationDate": "1982-04-28T00:00:00Z",
          "rating": 1,
          "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
          "@search.action": "merge",
          "hotelId": "3",
          "baseRate": 279.99,
          "description": "Surprisingly expensive",
          "lastRenovationDate": null
        },
        {
          "@search.action": "delete",
          "hotelId": "4"
        }
      ]
    }
________________________________________
<a name="SearchDocs"></a>
## Eseguire ricerche nei documenti ##

Un'operazione di **ricerca** viene generata come richiesta GET e specifica i parametri della query che forniscono i criteri per la selezione di documenti corrispondenti.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin key]

**Richiesta**

Per le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta di **ricerca** può essere creata mediante il metodo GET.

L'URI della richiesta specifica l'indice in cui eseguire la query per tutti i documenti corrispondenti ai parametri della query.

**Parametri della query**

`search=[string]` (facoltativo): specifica il testo da cercare. Per impostazione predefinita, la ricerca viene eseguita in tutti i campi `searchable` a meno che non sia specificato `searchFields`. Quando si esegue la ricerca nei campi `searchable`, il testo della ricerca viene suddiviso in token. In questo modo, più termini possono essere separati con uno spazio vuoto, ad esempio `search=hello world`. Per trovare corrispondenze con qualsiasi termine, usare `*`, che può essere utile per le query con filtro booleano. L'omissione di questo parametro equivale a impostarlo su `*`. Per le specifiche della sintassi di ricerca, vedere [Semplice sintassi di query nella Ricerca di Azure](https://msdn.microsoft.com/library/dn798920.aspx).

  - **Nota**: quando si eseguono query sui campi `searchable`, talvolta si possono ottenere risultati imprevisti. Il tokenizer include logica per la gestione di casi comuni nel testo inglese, ad esempio apostrofi, virgole nei numeri e così via. Ad esempio, `search=123,456` corrisponde a un singolo termine 123,456 invece che ai termini separati 123 e 456, poiché in inglese le virgole vengono usate come separatore delle migliaia per i numeri di grandi dimensioni. È quindi consigliabile usare uno spazio vuoto in sostituzione dei segni di punteggiatura per separare i termini nel parametro `search`.

`searchMode=any|all` (facoltativo, il valore predefinito è `any`): specifica se è necessario trovare una corrispondenza con tutti i termini di ricerca o con uno qualsiasi per includere il documento nelle corrispondenze.

`searchFields=[string]` (facoltativo): specifica l'elenco di nomi di campo delimitati da virgole in cui cercare il testo specificato. I campi di destinazione devono essere contrassegnati come `searchable`.

`moreLikeThis=[key]` (facoltativo): trova documenti che sono simili al documento specificato dalla chiave del documento. Per impostazione predefinita, vengono considerati i contenuti dei campi `searchable` a meno che non sia specificato `searchFields`. Non è possibile usare questa opzione in una query che contiene il parametro di ricerca di testo 'search=[string]'.

`$skip=#` (facoltativo): specifica il numero di risultati della ricerca da ignorare. Non può essere maggiore di 100.000. Se è necessario analizzare i documenti in sequenza, ma non è possibile usare `$skip` a causa di questa limitazione, prendere in considerazione l'uso di `$orderby` su una chiave totalmente ordinata e `$filter` con una query di intervallo.

`$top=#` (facoltativo): specifica il numero di risultati della ricerca da recuperare. Il valore predefinito è 50. Se si specifica un valore maggiore di 1000 e sono presenti più di 1000 risultati, verranno restituiti solo i primi 1000 insieme a un collegamento alla pagina successiva di risultati (vedere `@odata.nextLink` nell'[esempio seguente](#SearchResponse)).

`$count=true|false` (facoltativo, il valore predefinito è `false`): specifica se recuperare il conteggio totale dei risultati. L'impostazione di questo valore su `true` può influire negativamente sulle prestazioni. Si noti che il conteggio restituito è un'approssimazione.

`$orderby=[string]` (facoltativo): specifica un elenco di espressioni delimitate da virgole in base alle quali ordinare i risultati. Ogni espressione può essere un nome campo o una chiamata alla funzione `geo.distance()`. Ogni espressione può essere seguita da `asc` per indicare l'ordine crescente e da `desc` per indicare l'ordine decrescente. Per impostazione predefinita, l'ordinamento è crescente. Le situazioni di parità di priorità vengono risolte in base ai punteggi di corrispondenza dei documenti. Se `$orderby` non è specificato, l'ordine predefinito è decrescente in base al punteggio di corrispondenza dei documenti. È previsto un limite di 32 clausole per `$orderby`.

`$select=[string]` (facoltativo): specifica un elenco di campi delimitati da virgole da recuperare. Se non è specificato, vengono inclusi tutti i campi contrassegnati come recuperabili nello schema. È anche possibile richiedere in modo esplicito tutti i campi impostando questo parametro su `*`.

`facet=[string]` (zero o più): specifica un campo da usare per l'esplorazione in base a facet. La stringa può contenere parametri per personalizzare l'esplorazione in base a facet, espressi come coppie `name:value` delimitate da virgole. I parametri validi sono:

- `count` (numero massimo di termini facet; il valore predefinito è 10). Non è previsto alcun limite massimo per il numero di termini, ma i valori elevati potrebbero influire negativamente sulle prestazioni, soprattutto se il campo con facet include un numero elevato di termini univoci.
  - Esempio: `facet=category,count:5` ottiene le prime cinque categorie nei risultati dell'esplorazione in base a facet.  
  - **Nota**: se il parametro `count` è inferiore al numero di termini univoci, è possibile che i risultati non siano precisi. Ciò è dovuto al modo in cui le query di esplorazione in base a facet vengono distribuite nelle partizioni. Se si aumenta il valore di `count`, si ottengono conteggi di termini più precisi, ma le prestazioni possono essere ridotte.
- `sort` (uno dei valori `count` per ordinare in modo *decrescente* in base al conteggio, `-count` per ordinare in modo *crescente* in base al conteggio, `value` per ordinare in modo *crescente* in base al valore o `-value` per ordinare in modo *decrescente* in base al valore)
  - Esempio: `facet=category,count:3,sort:count` ottiene le prime tre categorie nei risultati dell'esplorazione in base a facet in ordine decrescente secondo il numero di documenti che includono ogni nome di città. Se le prime tre categorie sono Budget, Motel e Luxury e sono stati trovati 5 risultati per Budget, 6 per Motel e 4 per Luxury, l'ordine dei bucket sarà Motel, Budget, Luxury.
  - Esempio: `facet=rating,sort:-value` genera bucket per tutte le classificazioni possibili, in ordine decrescente in base al valore. Se le classificazioni sono da 1 a 5, i bucket avranno l'ordine 5, 4, 3, 2, 1, indipendentemente dal numero di documenti corrispondenti a ogni classificazione.
- `values` (valori numerici delimitati da pipe o valori `Edm.DateTimeOffset` che specificano un set dinamico di valori di immissione di facet)
  - Esempio: `facet=baseRate,values:10|20` genera tre bucket, uno per la tariffa di base da 0 a 10 escluso, uno da 10 a 20 escluso e uno per 20 e oltre.
  - Esempio: `facet=lastRenovationDate,values:2010-02-01T00:00:00Z` genera due bucket, uno per hotel rinnovati prima del febbraio 2010 e uno per hotel rinnovati a partire dal 1° febbraio 2010.
- `interval` (intervallo di tipo Integer maggiore di 0 per i numeri o `minute`, `hour`, `day`, `week`, `month`, `quarter`, `year` per i valori di tipo data/ora)
  - Esempio: `facet=baseRate,interval:100` genera bucket in base agli intervalli di tariffe di base con dimensioni pari a 100. Se le tariffe di base sono tutte comprese tra € 60 e € 600, saranno presenti bucket per 0-100, 100-200, 200-300, 300-400, 400-500 e 500-600.
  - Esempio: `facet=lastRenovationDate,interval:year` genera un bucket per ogni anno in cui gli hotel sono stati rinnovati.
- **Nota**: `count` e `sort` possono essere combinati nella stessa specifica di facet, ma non possono essere combinati con `interval` o `values` e inoltre `interval` e `values` non possono essere combinati tra loro.

`$filter=[string]` (facoltativo): specifica un'espressione di ricerca strutturata nella sintassi standard di OData. Per informazioni dettagliate sul sottoinsieme della grammatica delle espressioni OData supportato da Ricerca di Azure, vedere l'articolo relativo alla [sintassi delle espressioni OData](#ODataExpressionSyntax).

`highlight=[string]` (facoltativo): specifica un set di nomi di campo delimitati da virgole usati per evidenziare i risultati. Per l'evidenziazione dei risultati è possibile usare solo i campi `searchable`.

  `highlightPreTag=[string]` (facoltativo, il valore predefinito è `<em>`): specifica un tag di stringa che viene aggiunto all'inizio delle evidenziazioni dei risultati. Deve essere impostato con `highlightPostTag`. I caratteri riservati nell'URL devono essere codificati in percentuale (ad esempio, %23 anziché #).

  `highlightPostTag=[string]` (facoltativo, il valore predefinito è `</em>`): specifica un tag di stringa che viene aggiunto alla fine delle evidenziazioni dei risultati. Deve essere impostato con `highlightPreTag`. I caratteri riservati nell'URL devono essere codificati in percentuale (ad esempio, %23 anziché #).

`scoringProfile=[string]` (facoltativo): specifica il nome di un profilo di punteggio da usare per valutare i punteggi di corrispondenza per i documenti trovati, in modo da ordinare i risultati.

`scoringParameter=[string]` (zero o più): indica il valore per ogni parametro definito in una funzione di assegnazione di punteggio (ad esempio, `referencePointParameter`) usando il formato nome:valore. Ad esempio, se il profilo di punteggio definisce una funzione con un parametro denominato "mylocation", l'opzione della stringa di query sarà &scoringParameter=mylocation:-122.2,44.8

`api-version=[string]` (obbligatorio). I valori validi includono `2014-07-31-Preview` o `2014-10-20-Preview`. È possibile specificare il valore da usare in ogni richiesta per ottenere comportamenti specifici della versione, ma è in genere consigliabile usare la stessa versione in tutto il codice. La versione consigliata per un uso generico è `2014-07-31-Preview`. In alternativa, usare `2014-10-20-Preview` per valutare le funzionalità sperimentali. Per informazioni dettagliate, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).

Nota: per questa operazione, l'elemento `api-version` è specificato come parametro di query.

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `api-key`: l'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per l'URL del servizio. La richiesta di **ricerca** può specificare una chiave amministratore o una chiave di query per `api-key`.

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio e `api-key` dal dashboard servizi nel portale di anteprima di Azure. Per informazioni, vedere [Introduzione a Ricerca di Azure](search-get-started.md).

**Corpo della richiesta**

Nessuno.

<a name="SearchResponse"></a> **Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 200 OK.

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.facets": { (if faceting was specified in the query)
        "facet_field": [
          {
            "value": facet_entry_value (for non-range facets),
            "from": facet_entry_value (for range facets),
            "to": facet_entry_value (for range facets),
            "count": number_of_documents
          }
        ],
        ...
      },
      "value": [
        {
          "@search.score": document_score (if a text query was provided),
          "@search.highlights": {
            field_name: [ subset of text, ... ],
            ...
          },
          key_field_name: document_key,
          field_name: field_value (retrievable fields or specified projection),
          ...
        },
        ...
      ],
      "@odata.nextLink": (URL to fetch the next page of results if $top is greater than 1000)
    }

**Esempi:**

È possibile trovare altri esempi nella pagina relativa alla [sintassi delle espressioni OData per Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798921.aspx).

1) Eseguire una ricerca nell'indice ordinato in modo decrescente in base alla data.

    GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2014-10-20-Preview

NOTA: la precisione dei campi DateTime è limitata a millisecondi. Se si esegue il push di un timestamp che specifica valori inferiori (ad esempio, vedere la parte relativa ai secondi di questo timestamp: 10:30:09.7552052), il valore restituito verrà arrotondato per eccesso (ovvero 10:30:09.7550000 in questo esempio).

2) In una ricerca con facet, eseguire una ricerca nell'indice e recuperare i facet per categorie, classificazioni, tag, oltre a elementi con baseRate in intervalli specifici:

    GET /indexes/hotels/docs?search=test&facet=category&facet=rating&facet=tags&facet=baseRate,values:80|150|220&api-version=2014-10-20-Preview

3) Usando un filtro, limitare i risultati della precedente query con facet dopo che l'utente ha fatto clic su Rating 3 e sulla categoria "Motel":

    GET /indexes/hotels/docs?search=test&facet=tags&facet=baseRate,values:80|150|220&$filter=rating eq 3 and category eq 'Motel'&api-version=2014-10-20-Preview

4) In una ricerca con facet, impostare un limite massimo per i termini univoci restituiti in una query. Il valore predefinito è 10, ma è possibile aumentare o ridurre questo valore usando il parametro `count` nell'attributo `facet`:

    GET /indexes/hotels/docs?search=test&facet=city,count:5&api-version=2014-10-20-Preview

5) Eseguire una ricerca nell'indice entro campi specifici, ad esempio un campo relativo alla lingua:

    GET /indexes/hotels/docs?search=hôtel&searchFields=description_fr&api-version=2014-10-20-Preview

6) Eseguire una ricerca nell'indice in più campi. Ad esempio, è possibile archiviare ed eseguire query nei campi disponibili per la ricerca in più lingue, all'interno dello stesso indice. Se nello stesso documento coesistono descrizioni in inglese e in francese, sarà possibile restituirle tutte, o solo alcune, nei risultati della query:

	GET /indexes/hotels/docs?search=hotel&searchFields=description,description_fr&api-version=2014-10-20-Preview

Si noti che è possibile eseguire query su un solo indice alla volta. Non creare più indici per una lingua a meno che non si preveda di eseguire query su un indice alla volta.

7) Paging: ottenere la prima pagina degli elementi (la dimensione di pagina è 10):

    GET /indexes/hotels/docs?search=*&$skip=0&$top=10&api-version=2014-10-20-Preview

8) Paging: ottenere la seconda pagina degli elementi (la dimensione di pagina è 10):

    GET /indexes/hotels/docs?search=*&$skip=10&$top=10&api-version=2014-10-20-Preview

9) Recuperare un set specifico di campi:

    GET /indexes/hotels/docs?search=*&$select=hotelName,description&api-version=2014-10-20-Preview

10) Recuperare i documenti corrispondenti a un'espressione di query specifica:

    GET /indexes/hotels/docs?$filter=(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'&api-version=2014-10-20-Preview

11) Eseguire una ricerca nell'indice e restituire frammenti con evidenziazioni dei risultati:

    GET /indexes/hotels/docs?search=something&highlight=description&api-version=2014-10-20-Preview

12) Eseguire una ricerca nell'indice e restituire documenti ordinati dal più vicino al più lontano rispetto a una posizione di riferimento:

    GET /indexes/hotels/docs?search=something&$orderby=geo.distance(location, geography'POINT(-122.12315 47.88121)')&api-version=2014-10-20-Preview

13) Eseguire una ricerca nell'indice presupponendo che sia disponibile un profilo di punteggio denominato "geo" con due funzioni di assegnazione di punteggio in base alla distanza, che definiscono rispettivamente i parametri "currentLocation" e "lastLocation":

    GET /indexes/hotels/docs?search=something&scoringProfile=geo&scoringParameter=currentLocation:-122.123,44.77233&scoringParameter=lastLocation:-121.499,44.2113&api-version=2014-10-20-Preview


<a name="LookupAPI"></a>
##Cercare un documento##

L'operazione di **ricerca di un documento** recupera un documento da Ricerca di Azure. È utile quando un utente fa clic su un risultato della ricerca e si desidera cercare dettagli specifici su tale documento.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters]
    api-key: [admin key]

**Richiesta**

Per le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta di **ricerca di un documento** può essere creata nel modo seguente.

    GET /indexes/[index name]/docs/key?[query parameters]

In alternativa, è possibile usare la sintassi tradizionale di OData per la ricerca delle chiavi:

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

L'URI della richiesta include i valori [index name] e [key], che specificano il documento da recuperare da un determinato indice. È possibile recuperare un solo documento alla volta. Per ottenere più documenti con una singola richiesta, eseguire l'operazione di **ricerca nei documenti**.

**Parametri della query**

`$select=[string]` (facoltativo): specifica un elenco di campi delimitati da virgole da recuperare. Se non è specificato o se è impostato su `*`, nella proiezione vengono inclusi tutti i campi contrassegnati come recuperabili nello schema.

`api-version=[string]` (obbligatorio). I valori validi includono `2014-07-31-Preview` o `2014-10-20-Preview`. È possibile specificare il valore da usare in ogni richiesta per ottenere comportamenti specifici della versione, ma è in genere consigliabile usare la stessa versione in tutto il codice. La versione consigliata per un uso generico è `2014-07-31-Preview`. In alternativa, usare `2014-10-20-Preview` per valutare le funzionalità sperimentali. Per informazioni dettagliate, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).

Nota: per questa operazione, l'elemento `api-version` è specificato come parametro di query.

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `api-key`: l'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per l'URL del servizio. La richiesta di **ricerca di un documento** può specificare una chiave amministratore o una chiave di query per `api-key`.

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio e `api-key` dal dashboard servizi nel portale di anteprima di Azure. Per informazioni, vedere [Introduzione a Ricerca di Azure](search-get-started.md).

**Corpo della richiesta**

Nessuno.

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 200 OK.

    {
      field_name: field_value (fields matching the default or specified projection)
    }

**Esempio**

Cercare il documento con chiave '2':

    GET /indexes/hotels/docs/2?api-version=2014-10-20-Preview

Cercare il documento con chiave '3' usando la sintassi di OData:

    GET /indexes('hotels')/docs('3')?api-version=2014-10-20-Preview

<a name="CountDocs"></a>
##Conteggio documenti##

L'operazione di **conteggio dei documenti** recupera un conteggio del numero di documenti in un indice di ricerca. La sintassi `$count` fa parte del protocollo OData.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin key]

**Richiesta**

Per le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta di **conteggio dei documenti** può essere creata mediante il metodo GET.

Il valore [index name] nell'URI della richiesta indica al servizio di restituire un conteggio di tutti gli elementi disponibili nella raccolta di documenti dell'indice specificato.

Il parametro `api-version` è obbligatorio. I valori validi includono `2014-07-31-Preview` o `2014-10-20-Preview`. È possibile specificare il valore da usare in ogni richiesta per ottenere comportamenti specifici della versione, ma è in genere consigliabile usare la stessa versione in tutto il codice. La versione consigliata per un uso generico è `2014-07-31-Preview`. In alternativa, usare `2014-10-20-Preview` per valutare le funzionalità sperimentali. Per informazioni dettagliate, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `Accept`: questo valore deve essere impostato su `text/plain`.
- `api-key`: l'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per l'URL del servizio. La richiesta di **conteggio dei documenti** può specificare una chiave amministratore o una chiave di query per `api-key`.

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio sia `api-key` dal dashboard servizi nel portale di anteprima di Azure. Per informazioni, vedere [Introduzione a Ricerca di Azure](search-get-started.md).

**Corpo della richiesta**

Nessuno.

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 200 OK.

Il corpo della risposta include il valore count sotto forma di Integer formattato in testo normale.

<a name="Suggestions"></a>
##Suggerimenti##

L'operazione di recupero dei **suggerimenti** ottiene i suggerimenti in base a un input di ricerca parziale. In genere, viene usata nelle caselle di ricerca per fornire suggerimenti durante la digitazione quando gli utenti immettono i termini di ricerca.

Le richieste di suggerimenti hanno lo scopo di suggerire documenti di destinazione, pertanto il testo suggerito può essere ripetuto se più documenti corrispondono allo stesso input di ricerca. È possibile usare `$select` per recuperare altri campi di documento (inclusa la chiave) in modo da poter determinare quale documento è l'origine di ogni suggerimento.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin key]


**Richiesta**

Per le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta di recupero dei **suggerimenti** può essere creata mediante il metodo GET.

L'URI della richiesta specifica il nome dell'indice su cui eseguire la query. Contiene inoltre il termine di ricerca immesso parzialmente nella stringa di query.

**Parametri della query**

`search=[string]`: specifica il testo della ricerca da usare per i suggerimenti per le query. Deve essere composto da un minimo di 3 caratteri e da un massimo di 25 caratteri.

`highlightPreTag=[string]` (facoltativo): specifica un tag di stringa che viene aggiunto all'inizio dei risultati della ricerca. Deve essere impostato con `highlightPostTag`. I caratteri riservati nell'URL devono essere codificati in percentuale (ad esempio, %23 anziché #).

`highlightPostTag=[string]` (facoltativo): specifica un tag di stringa che viene aggiunto alla fine dei risultati della ricerca. Deve essere impostato con `highlightPreTag`. I caratteri riservati nell'URL devono essere codificati in percentuale (ad esempio, %23 anziché #).

`suggesterName=[string]` (facoltativo): specifica il nome del componente per il suggerimento, come definito nella raccolta `suggesters` che fa parte della definizione di indice. Se si omette questa opzione, i suggerimenti sono basati sull'implementazione della versione precedente che agisce sui campi contrassegnati con `"suggestions": true` e supporta solo la corrispondenza del prefisso.

`fuzzy=[boolean]` (facoltativo, il valore predefinito è false): quando il valore è true, questa API trova i suggerimenti anche in caso di carattere sostituito o mancante nel testo della ricerca. Anche se offre un'esperienza migliore in alcuni scenari, influisce negativamente sulle prestazioni poiché le ricerche con suggerimenti fuzzy sono più lente e utilizzano più risorse.

`searchFields=[string]` (facoltativo): specifica l'elenco di nomi di campo delimitati da virgole in cui cercare il testo specificato. I campi di destinazione devono essere abilitati per suggerimenti.

`$top=#` (facoltativo, il valore predefinito è 5): specifica il numero di suggerimenti da recuperare. Deve essere un numero compreso tra 1 e 100.

`$filter=[string]` (facoltativo): specifica un'espressione che filtra i documenti considerati per i suggerimenti.

`$orderby=[string]` (facoltativo): specifica un elenco di espressioni delimitate da virgole in base alle quali ordinare i risultati. Ogni espressione può essere un nome campo o una chiamata alla funzione `geo.distance()`. Ogni espressione può essere seguita da `asc` per indicare l'ordine crescente e da `desc` per indicare l'ordine decrescente. Per impostazione predefinita, l'ordinamento è crescente. È previsto un limite di 32 clausole per `$orderby`.

`$select=[string]` (facoltativo): specifica un elenco di campi delimitati da virgole da recuperare. Se non è specificato, vengono restituiti solo la chiave del documento e il testo del suggerimento.

`api-version=[string]` (obbligatorio). I valori validi includono `2014-07-31-Preview` o `2014-10-20-Preview`. È possibile specificare il valore da usare in ogni richiesta per ottenere comportamenti specifici della versione, ma è in genere consigliabile usare la stessa versione in tutto il codice. La versione consigliata per un uso generico è `2014-07-31-Preview`. In alternativa, usare `2014-10-20-Preview` per valutare le funzionalità sperimentali. Per informazioni dettagliate, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).

Nota: per questa operazione, l'elemento `api-version` è specificato come parametro di query.

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `api-key`: l'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per l'URL del servizio. La richiesta di recupero dei **suggerimenti** può specificare una chiave amministratore o una chiave di query per `api-key`.

  Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio sia `api-key` dal dashboard servizi nel portale di anteprima di Azure. Per informazioni, vedere [Introduzione a Ricerca di Azure](search-get-started.md).

**Corpo della richiesta**

Nessuno.

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 200 OK.

    {
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

Se per recuperare i campi viene usata l'opzione di proiezione, tali campi vengono inclusi in ogni elemento della matrice:

    {
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
          <other projected data fields>
        },
        ...
      ]
    }

 **Esempio**

Recuperare 5 suggerimenti per cui l'input di ricerca parziale è 'lux':

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&suggesterName=sg&api-version=2014-10-20-Preview

<!---HONumber=July15_HO5-->