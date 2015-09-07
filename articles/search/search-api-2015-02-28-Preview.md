<properties
   pageTitle="API REST di Ricerca di Azure versione 2015-02-28-Preview | Microsoft Azure"
	description="L'API REST di Ricerca di Azure versione 2015-02-28-Preview include funzionalità sperimentali come gli analizzatori del linguaggio naturale e le ricerche moreLikeThis."
	services="search"
	documentationCenter="na"
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
   ms.service="search"
	ms.devlang="rest-api"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="search"
	ms.date="08/25/2015"
	ms.author="heidist"/>

# API REST del servizio Ricerca di Azure: versione 2015-02-28-Preview

Questo articolo è la documentazione di riferimento per `api-version=2015-02-28-Preview`. Questa versione di anteprima estende l'attuale versione disponibile per il pubblico, [api-version=2015-02-28](https://msdn.microsoft.com/library/dn798935.aspx), con le seguenti funzionalità sperimentali:

- I [processori per il linguaggio naturale](#LanguageSupport) di Microsoft (gli stessi usati da Office e Bing) offrono una maggiore precisione nei risultati delle query e per più lingue.
- `moreLikeThis` è un parametro di query usato nelle [operazioni di ricerca](#SearchDocs) che trova documenti rilevanti rispetto a un altro documento specifico.
- Alternativa POST alla sintassi GET per entrambe le API di [Ricerca](#SearchDocs) e [Suggestions](#Suggestions), utile quando la lunghezza complessiva dell'URL supererebbe altrimenti 8 KB.

Alcune funzionalità aggiuntive di `2015-02-28-Preview` sono documentate separatamente, incluse le seguenti:

- [Profili di punteggio](search-api-scoring-profiles-2015-02-28-preview.md)
- [Indicizzatori](search-api-indexers-2015-02-28-preview.md)

Ricerca di Azure è disponibile in più versioni. Per informazioni dettagliate, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).

##API in questo documento

L'API di Ricerca di Azure supporta due tipi di sintassi per la ricerca di entità: sintassi [semplice](https://msdn.microsoft.com/library/dn798920.aspx) e sintassi alternativa di OData. Per informazioni dettagliate, vedere [Supporto per OData (API di Ricerca di Azure)](http://msdn.microsoft.com/library/azure/dn798932.aspx). L'elenco seguente mostra la sintassi semplice.

[Creare un indice](#CreateIndex)

    POST /indexes?api-version=2015-02-28-Preview

[Aggiornare un indice](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2015-02-28-Preview

[Ottenere un indice](#GetIndex)

    GET /indexes/[index name]?api-version=2015-02-28-Preview

[Elencare gli indici](#ListIndexes)

    GET /indexes?api-version=2015-02-28-Preview

[Ottenere le statistiche di un indice](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2015-02-28-Preview

[Eliminare un indice](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2015-02-28-Preview

[Aggiungere, aggiornare o eliminare documenti](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2015-02-28-Preview

[Eseguire ricerche nei documenti](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]
    POST /indexes/[index name]/docs/search?api-version=2015-02-28-Preview

[Cercare un documento](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[Contare i documenti](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2015-02-28-Preview

[Suggerimenti](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]
    POST /indexes/[index name]/docs/suggest?api-version=2015-02-28-Preview

________________________________________
<a name="IndexOps"></a>
## Operazioni sugli indici

È possibile creare e gestire gli indici in Ricerca di Azure tramite semplici richieste HTTP (POST, GET, PUT, DELETE) su una risorsa indice specifica. Per creare un indice, eseguire innanzitutto la richiesta POST di un documento JSON che descrive lo schema dell'indice. Lo schema definisce i campi dell'indice, i relativi tipi di dati e la modalità d'uso, ad esempio ricerche full-text, filtri, ordinamento o uso di facet. Definisce anche i profili di punteggio, i componenti per il suggerimento e altri attributi per configurare il comportamento dell'indice.

L'esempio seguente illustra uno schema usato per la ricerca di informazioni sugli hotel, con il campo relativo alla descrizione definito in due lingue. Si noti come gli attributi controllano la modalità d'uso del campo. Ad esempio, `hotelId` viene usato come chiave del documento (`"key": true`) ed è escluso dalle ricerche full-text (`"searchable": false`).

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
	  {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
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
## Creare un indice

Un indice è il mezzo principale per organizzare documenti ed eseguirvi ricerche con Ricerca di Azure, così come una tabella organizza i record in un database. Ogni indice include una raccolta di documenti che sono tutti conformi al relativo schema (nomi di campi, tipi di dati e proprietà), ma gli indici specificano anche costrutti aggiuntivi (componenti per il suggerimento, profili di punteggio e opzioni CORS) che definiscono altri comportamenti di ricerca.

È possibile creare un nuovo indice in Ricerca di Azure mediante una richiesta HTTP POST o PUT. Il corpo della richiesta è uno schema JSON che specifica le informazioni di indice e configurazione.

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

In alternativa, è possibile usare PUT e specificare il nome dell'indice nell'URI. Se l'indice non esiste, verrà creato.

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

La creazione di un indice determina la struttura dei documenti archiviati e usati nelle operazioni di ricerca. Il popolamento dell'indice è un'operazione separata. Per questo passaggio, è possibile usare un [indicizzatore](https://msdn.microsoft.com/library/azure/mt183328.aspx) (disponibile per le origini dati supportate) o un'operazione di [aggiunta, aggiornamento o eliminazione di documenti](https://msdn.microsoft.com/library/azure/dn798930.aspx). L'indice invertito viene generato quando i documenti vengono pubblicati.

**Nota**: il numero massimo di indici consentiti varia in base al livello di prezzo. Nel servizio gratuito sono consentiti fino a 3 indici. Nel servizio standard sono consentiti 50 indici per servizio di ricerca. Per dettagli, vedere [Limitazioni e vincoli](http://msdn.microsoft.com/library/azure/dn798934.aspx).

**Richiesta**

Per tutte le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta di **creazione di un indice** può essere creata mediante il metodo POST o PUT. Se si usa il metodo POST, è necessario specificare nel corpo della richiesta il nome di un indice e la definizione del relativo schema. Nel caso di PUT, il nome dell'indice fa parte dell'URL. Se l'indice non esiste, viene creato. Se esiste già, viene aggiornato in base alla nuova definizione.

Il nome dell'indice deve essere scritto in caratteri minuscoli, deve iniziare con una lettera o un numero, non deve contenere barre o punti e deve avere una lunghezza inferiore ai 128 caratteri. Dopo l'iniziale costituita da una lettera o un numero, il resto del nome può contenere lettere, numeri e trattini, purché i trattini non siano consecutivi.

L'elemento `api-version` è obbligatorio. Per un elenco delle versioni disponibili, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `Content-Type`: elemento obbligatorio. Impostare il valore su `application/json`.
- `api-key`: elemento obbligatorio. L'elemento `api-key` viene usato per
- per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta di **creazione dell'indice** deve includere un'intestazione `api-key` impostata sulla chiave amministratore, anziché su una chiave di query.

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio e `api-key` dal dashboard servizi nel portale di Azure. Per informazioni, vedere [Creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md).

<a name="RequestData"></a> **Sintassi del corpo della richiesta**

Il corpo della richiesta contiene una definizione di schema che include l'elenco dei campi dati all'interno dei documenti che verranno inseriti nell'indice, i tipi di dati, gli attributi e un elenco facoltativo dei profili di punteggio che vengono usati per assegnare un punteggio ai documenti corrispondenti quando si esegue la query.

Si noti che per una richiesta POST, è necessario specificare il nome dell'indice nel corpo della richiesta.

Può essere presente un solo campo chiave nell'indice e tale campo deve essere di tipo stringa. Il campo rappresenta l'identificatore univoco per ogni documento archiviato all'interno dell'indice.

Le parti principali di un indice includono quanto segue:

- `name`
- `fields`: elementi che verranno inseriti nell'indice, tra cui il nome, il tipo di dati e le proprietà che definiscono le azioni consentite.
- `suggesters`: elementi usati per le query con completamento automatico.
- `scoringProfiles`:elementi usati per la classificazione personalizzata dei punteggi di ricerca. Per informazioni dettagliate, vedere [Aggiungere profili di punteggio a un indice di ricerca](https://msdn.microsoft.com/library/azure/dn798928.aspx).
- `defaultScoringProfile`: elemento usato per sovrascrivere i comportamenti relativi ai punteggi predefiniti.
- `corsOptions`: elemento usato per consentire query nell'indice basate su più origini.

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
              "searchable_field_name": relative_weight_value (positive numbers),
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

**Attributi dell'indice**

Quando si crea un indice, è possibile impostare gli attributi seguenti. Per informazioni dettagliate sull'assegnazione dei punteggi e sui profili di punteggio, vedere [Aggiungere profili di punteggio a un indice di ricerca](https://msdn.microsoft.com/library/azure/dn798928.aspx).

`name`: imposta il nome del campo.

`type`: imposta il tipo di dati per il campo. Per un elenco di tipi supportati, vedere [Tipi di dati supportati](#DataTypes).

`searchable`: contrassegna il campo come disponibile per la ricerca full-text. Ciò significa che verrà sottoposto ad analisi, ad esempio la suddivisione in parole durante l'indicizzazione. Se si imposta un campo `searchable` su un valore come "sunny day", questo viene suddiviso internamente nei singoli token "sunny" e "day". È così possibile eseguire ricerche full-text di questi termini. I campi di tipo `Edm.String` o `Collection(Edm.String)` sono `searchable` per impostazione predefinita. I campi di altri tipi non possono essere `searchable`.

  - **Nota**: i campi `searchable` utilizzano spazio aggiuntivo nell'indice perché Ricerca di Azure archivia una versione supplementare in formato token del valore del campo per le ricerche full-text. Se si vuole risparmiare spazio nell'indice e non è necessario includere un campo nelle ricerche, impostare `searchable` su `false`.

`filterable`: consente il riferimento al campo nelle query `$filter`. La differenza tra `filterable` e `searchable` consiste nel modo in cui vengono gestite le stringhe. I campi di tipo `Edm.String` o `Collection(Edm.String)` che sono `filterable` non sono sottoposti a suddivisione delle parole e quindi i confronti riguardano solo le corrispondenze esatte. Se ad esempio si imposta un campo `f` su "sunny day", `$filter=f eq 'sunny'` non troverà corrispondenze, mentre `$filter=f eq 'sunny day'` ne troverà. Tutti i campi sono `filterable` per impostazione predefinita.

`sortable`: per impostazione predefinita, il sistema ordina i risultati in base al punteggio, ma in molti casi gli utenti preferiscono eseguire l'ordinamento in base ai campi nei documenti. I campi di tipo `Collection(Edm.String)` non possono essere `sortable`. Tutti gli altri campi sono `sortable` per impostazione predefinita.

`facetable`: usato generalmente in una presentazione dei risultati di ricerca che include il numero di risultati per categoria (ad esempio, per cercare fotocamere digitali e visualizzare i risultati in base alla marca, ai megapixel, al prezzo e così via). Questa opzione non può essere usata con i campi di tipo `Edm.GeographyPoint`. Tutti gli altri campi sono `facetable` per impostazione predefinita.

  - **Nota**: i campi di tipo `Edm.String` che sono `filterable`, `sortable` o `facetable` possono avere al massimo una lunghezza di 32 KB. Questa limitazione è dovuta al fatto che questi campi sono considerati un unico termine di ricerca e la lunghezza massima di un termine in Ricerca di Azure è 32 KB. Se è necessario archiviare più testo in un campo a stringa singola, impostare esplicitamente `filterable`, `sortable` e `facetable` su `false` nella definizione dell'indice.

  - **Nota**: se per un campo nessuno degli attributi elencati è impostato su `true` (`searchable`, `filterable`, `sortable` o `facetable`), il campo viene effettivamente escluso dall'indice invertito. Questa opzione è utile per i campi che non vengono usati nelle query, ma che sono necessari nei risultati della ricerca. L'esclusione di tali campi dall'indice consente di ottenere migliori prestazioni.

`suggestions`: nelle versioni precedenti dell'API è inclusa una proprietà `suggestions`. Questa proprietà booleana è obsoleta e non è più disponibile in `2015-02-28` o `2015-02-28-Preview`. In alternativa, usare l'[API per i suggerimenti](#Suggesters). Nella versione `2014-07-31`, per specificare se un campo può essere usato per il completamento automatico durante la digitazione, viene usata la proprietà `suggestions` per i campi di tipo `Edm.String` o `Collection(Edm.String)`. La proprietà `suggestions` è `false` per impostazione predefinita perché richiede spazio aggiuntivo nell'indice, ma se si è scelto di abilitarla, vedere [Transizione dalla versione API di anteprima 2014* alla versione API 2015*](search-transition-from-preview.md) per istruzioni sul passaggio alla nuova API.

`key`: contrassegna il campo come contenente identificatori univoci per i documenti all'interno dell'indice. È necessario scegliere un singolo campo come `key` e questo deve essere di tipo `Edm.String`. I campi chiave possono essere usati per la ricerca diretta di documenti tramite l'[API di ricerca](#LookupAPI).

`retrievable`: specifica se il campo può essere restituito nel risultato di una ricerca. Questo attributo è utile quando si vuole usare un campo, ad esempio quello relativo al margine, come meccanismo di filtro, ordinamento o punteggio ma si preferisce che il campo non sia visibile all'utente finale. L'attributo deve essere `true` per i campi `key`.

`analyzer`: imposta il nome dell'analizzatore di testo da usare per il campo. Per il set di valori consentito, vedere [Supporto per le lingue](#LanguageSupport). Questa opzione può essere usata solo con i campi `searchable`. Una volta scelto, l'analizzatore non può essere cambiato per il campo.

`suggesters`: imposta la modalità di ricerca e i campi che sono l'origine del contenuto per i suggerimenti. Per informazioni dettagliate, vedere [Componenti per il suggerimento](#Suggesters).

`scoringProfiles`: definisce i comportamenti di punteggio personalizzati che consentono di determinare quali elementi verranno visualizzati più in alto nei risultati della ricerca. I profili di punteggio sono costituiti da funzioni e campi ponderati. Per altre informazioni sugli attributi usati in un profilo di punteggio, vedere [Aggiungere profili di punteggio a un indice di ricerca](https://msdn.microsoft.com/library/azure/dn798928.aspx).

<!-- This is a standalone topic in MSDN -->
<a name="LanguageSupport"></a> **Supporto per le lingue**

I campi disponibili per la ricerca vengono sottoposti a un processo di analisi, che spesso comporta la suddivisione in parole, la normalizzazione del testo e l'esclusione di termini tramite filtro. Per impostazione predefinita, i campi disponibili per la ricerca in Ricerca di Azure vengono analizzati con l'[analizzatore Apache Lucene Standard](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html), che suddivide il testo in elementi sulla base delle regole di [segmentazione del testo Unicode](http://unicode.org/reports/tr29/). L'analizzatore standard, inoltre, converte tutti i caratteri nel rispettivo formato minuscolo. I documenti indicizzati e i termini di ricerca vengono sottoposti ad analisi durante l'indicizzazione e l'elaborazione delle query.

Ricerca di Azure supporta una varietà di linguaggi. Ogni lingua richiede un analizzatore di testo non standard che tenga conto delle caratteristiche specifiche della lingua. Ricerca di Azure offre due tipi di analizzatori:

- 35 sono basati sulla tecnologia Lucene.
- 50 sono basati sulla tecnologia proprietaria di Microsoft per l'elaborazione del linguaggio naturale usata in Office e Bing.

Alcuni sviluppatori potrebbero preferire la soluzione open source di Lucene, più semplice e familiare. Gli analizzatori Lucene sono più veloci, ma gli analizzatori Microsoft dispongono di funzionalità avanzate, ad esempio lemmatizzazione, scomposizione delle parole (in lingue come tedesco, danese, olandese, svedese, norvegese, estone, finlandese, ungherese, slovacco) e riconoscimento di entità (URL, messaggi di posta elettronica, date, numeri). Si consiglia di confrontare, se possibile, gli analizzatori Microsoft e Lucene per scegliere la soluzione più adatta al proprio caso.

***Analizzatori a confronto***

L'analizzatore Lucene per la lingua inglese estende l'analizzatore standard. Rimuove il genitivo sassone (la 's finale) dalle parole, applica lo stemming in base all'[algoritmo Porter Stemming](http://tartarus.org/~martin/PorterStemmer/) e rimuove le [parole non significative](http://en.wikipedia.org/wiki/Stop_words) per la lingua inglese.

In confronto, l'analizzatore Microsoft esegue la lemmatizzazione anziché lo stemming. Significa che è possibile gestire le forme lessicali flesse e irregolari molto meglio, producendo risultati delle ricerche più rilevanti (esaminare il modulo 7 della [presentazione di Ricerca di Azure MVA](http://www.microsoftvirtualacademy.com/training-courses/adding-microsoft-azure-search-to-your-websites-and-apps) per ulteriori dettagli).

L'indicizzazione con gli analizzatori Microsoft è in media da due a tre volte più lenta rispetto agli analizzatori Lucene corrispondenti, a seconda della lingua. Le prestazioni della ricerca non devono essere influenzate in modo significativo per le query di dimensioni medie.

***Configurazione***

Per ogni campo nella definizione dell'indice, è possibile impostare la proprietà `analyzer` su un nome di analizzatore che specifica la lingua e il fornitore. Lo stesso analizzatore verrà applicato per l'indicizzazione e la ricerca di tale campo. Ad esempio, nello stesso indice possono essere presenti campi separati per le descrizioni di hotel in lingua inglese, francese e spagnola. Utilizzare il [parametro di query 'searchFields'](#SearchQueryParameters) per specificare il campo specifico della lingua da ricercare nelle query. È possibile esaminare gli esempi di query che includono la proprietà `analyzer` nella sezione [Eseguire ricerche nei documenti](#SearchDocs).

***Elenco di analizzatori***

Di seguito è riportato l'elenco delle lingue supportate con i nomi degli analizzatori Lucene e Microsoft.

<table style="font-size:12">
    <tr>
		<th>Linguaggio</th>
		<th>Nome analizzatore Microsoft</th>
		<th>Nome analizzatore Lucene</th>
	</tr>
    <tr>
		<td>Arabo</td>
		<td>ar.microsoft</td>
		<td>ar.lucene</td>		
	</tr>
    <tr>
    	<td>Armeno</td>
		<td></td>
    	<td>hy.lucene</td>
  	</tr>
    <tr>
		<td>Bengalese</td>
		<td>bn.microsoft</td>
		<td></td>
	</tr>
  	<tr>
    	<td>Basco</td>
		<td></td>
    	<td>eu.lucene</td>
    </tr>
  	<tr>
 		<td>Bulgaro</td>
		<td>bg.microsoft</td>
    	<td>bg.lucene</td>
  	</tr>
  	<tr>
    	<td>Catalano</td>
    	<td>ca.microsoft</td>
		<td>ca.lucene</td>  		
  	</tr>
    <tr>
		<td>Cinese semplificato</td>
		<td>zh-Hans.microsoft</td>
		<td>zh-Hans.lucene</td>		
	</tr>
    <tr>
		<td>Cinese tradizionale</td>
		<td>zh-Hant.microsoft</td>
		<td>zh-Hant.lucene</td>		
	<tr>
    <tr>
		<td>Croato</td>
		<td>hr.microsoft</td>
		<td/></td>
	</tr>
    <tr>
		<td>Ceco</td>
		<td>cs.microsoft</td>
		<td>cs.lucene</td>		
	</tr>    
    <tr>
		<td>Danese</td>
		<td>da.microsoft</td>
		<td>da.lucene</td>		
	</tr>    
    <tr>
		<td>Olandese</td>
		<td>nl.microsoft</td>
		<td>nl.lucene</td>	
	</tr>    
    <tr>
		<td>Inglese</td>		
		<td>en.microsoft</td>
		<td>en.lucene</td>		
	</tr>
    <tr>
		<td>Estone</td>
		<td>et.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Finlandese</td>
		<td>fi.microsoft</td>
		<td>fi.lucene</td>		
	</tr>    
    <tr>
		<td>Francese</td>
		<td>fr.microsoft</td>
		<td>fr.lucene</td>		
	</tr>
    <tr>
    	<td>Gallego</td>
	    <td></td>
		<td>gl.lucene</td>    	
  	</tr>
    <tr>
		<td>Tedesco</td>
		<td>de.microsoft</td>
		<td>de.lucene</td>		
	</tr>
    <tr>
		<td>Greco</td>
		<td>el.microsoft</td>
		<td>el.lucene</td>		
	</tr>
    <tr>
		<td>Gujarati</td>
		<td>gu.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Ebraico</td>
		<td>he.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Hindi</td>
		<td>hi.microsoft</td>
		<td>hi.lucene</td>		
	</tr>
    <tr>
		<td>Ungherese</td>		
		<td>hu.microsoft</td>
		<td>hu.lucene</td>
	</tr>
    <tr>
		<td>Islandese</td>
		<td>is.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Indonesiano (Bahasa)</td>
		<td>id.microsoft</td>
		<td>id.lucene</td>		
	</tr>
    <tr>
    	<td>Irlandese</td>
		<td></td>
      	<td>ga.lucene</td>
    </tr>
    <tr>
		<td>Italiano</td>
		<td>it.microsoft</td>
		<td>it.lucene</td>		
	</tr>
    <tr>
		<td>Giapponese</td>
		<td>ja.microsoft</td>
		<td>ja.lucene</td>
		
	</tr>
    <tr>
		<td>Kannada</td>
		<td>ka.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Coreano</td>
		<td></td>
		<td>ko.lucene</td>
	</tr>
    <tr>
		<td>Lettone</td>		
		<td>lv.microsoft</td>
		<td>lv.lucene</td>	
	</tr>
    <tr>
		<td>Lituano</td>
		<td>lt.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Malayalam</td>
		<td>ml.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Malese (alfabeto latino)</td>
		<td>ms.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Marathi</td>
		<td>mr.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Norvegese</td>
		<td>nb.microsoft</td>
		<td>no.lucene</td>		
	</tr>
  	<tr>
    	<td>Persiano</td>
		<td></td>
		<td>fa.lucene</td>    	
  	</tr>
    <tr>
		<td>Polacco</td>
		<td>pl.microsoft</td>
		<td>pl.lucene</td>		
	</tr>
    <tr>
		<td>Portoghese (Brasile)</td>
		<td>pt-Br.microsoft</td>
		<td>pt-Br.lucene</td>		
	</tr>
    <tr>
		<td>Portoghese (Portogallo)</td>
		<td>pt-Pt.microsoft</td>		
		<td>pt-Pt.lucene</td>
	</tr>
    <tr>
		<td>Punjabi</td>
		<td>pa.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Romeno</td>
		<td>ro.microsoft</td>
		<td>ro.lucene</td>
	</tr>
    <tr>
		<td>Russo</td>
		<td>ru.microsoft</td>
		<td>ru.lucene</td>	
	</tr>
    <tr>
		<td>Serbo (alfabeto cirillico)</td>
		<td>sr-cyrillic.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Serbo (alfabeto latino)</td>
		<td>sr-latin.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Slovacco</td>
		<td>sk.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Sloveno</td>
		<td>sl.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Spagnolo</td>
		<td>es.microsoft</td>
		<td>es.lucene</td>
	</tr>
    <tr>
		<td>Svedese</td>
		<td>sv.microsoft</td>
		<td>sv.lucene</td>
	</tr>

    <tr>
		<td>Tamil</td>
		<td>ta.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Telugu</td>
		<td>te.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Thai</td>
		<td>th.microsoft</td>
		<td>th.lucene</td>
	</tr>
    <tr>
		<td>Turco</td>
		<td>tr.microsoft</td>
		<td>tr.lucene</td>		
	</tr>
    <tr>
		<td>Ucraino</td>
		<td>uk.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Urdu</td>
		<td>ur.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Vietnamita</td>
		<td>vi.microsoft</td>
		<td></td>
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

**Componenti per il suggerimento**

Un `suggester` definisce i campi di un indice che vengono utilizzati come supporto per il completamento automatico nelle ricerche. In genere le stringhe di ricerca parziali vengono inviate all'[API per i suggerimenti](#Suggestions) mentre l'utente digita la query di ricerca e l'API restituisce un set di espressioni suggerite. Un componente per il suggerimento definito dall'utente nell'indice consente di determinare i campi utilizzati per creare i termini di ricerca a completamento automatico. Per visualizzare i dettagli sulla configurazione, consultare [Componenti per il suggerimento](#Suggesters).

**Profili di punteggio**

Un `scoringProfile` definisce i comportamenti di punteggio personalizzati che consentono di determinare quali elementi verranno visualizzati più in alto nei risultati della ricerca. I profili di punteggio sono costituiti da funzioni e campi ponderati. Per utilizzarli, è necessario specificare il nome di un profilo nella stringa di query.

Un profilo di punteggio predefinito viene eseguito in background al fine di calcolare un punteggio di ricerca per ogni elemento visualizzato in un set di risultati. È possibile utilizzare un profilo di punteggio interno, senza nome. In alternativa, è possibile impostare il `defaultScoringProfile` affinché usi un profilo personalizzato come predefinito. Tale profilo può essere richiamato ogni volta in cui un profilo personalizzato non viene specificato nella stringa di query.

Per ulteriori dettagli, vedere [Aggiungere profili di punteggio a un indice di ricerca (API REST di Ricerca di Azure)](search-api-scoring-profiles-2015-02-28.md).

**Opzioni CORS**

JavaScript sul lato client non può chiamare API per impostazione predefinita perché il browser impedisce tutte le richieste con origini diverse. Per consentire query con origini diverse nell'indice, abilitare CORS (Cross-Origin Resource Sharing) impostando l'attributo `corsOptions`. Si noti che, per motivi di sicurezza, solo le API di query supportano CORS. Per CORS è possibile impostare le opzioni seguenti:

- `allowedOrigins` (obbligatoria): si tratta di un elenco di origini a cui verrà concesso l'accesso all'indice. Questo significa che al codice JavaScript servito da queste origini sarà consentito eseguire query sull'indice, purché fornisca la chiave API corretta. Ogni origine è in genere nel formato `protocol://fully-qualified-domain-name:port` anche se spesso la porta viene omessa. Per altri dettagli, vedere [questo articolo](http://go.microsoft.com/fwlink/?LinkId=330822).
 - Per consentire l'accesso a tutte le origini, includere `*` come unico elemento nella matrice `allowedOrigins`. Tenere presente che **questa non è la procedura consigliata per i servizi di ricerca di produzione**. Può comunque essere utile per finalità di sviluppo o debug.
- `maxAgeInSeconds` (facoltativa): i browser usano questo valore per determinare la durata (in secondi) di memorizzazione nella cache delle risposte preliminari CORS. Questo valore deve essere un intero non negativo. A un valore più grande corrispondono prestazioni migliori, ma deve trascorrere più tempo prima che le modifiche dei criteri CORS diventino effettive. Se questo valore non è impostato, viene usata una durata predefinita di 5 minuti.

<a name="CreateUpdateIndexExample"></a> **Esempio di corpo della richiesta**

    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
	    {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer"="fr.lucene"},
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

**Osservazioni**

Attualmente è previsto un supporto limitato per gli aggiornamenti dello schema di indice. Gli aggiornamenti dello schema che richiedono la reindicizzazione, ad esempio la modifica dei tipi di campo, non sono attualmente supportati. È possibile aggiungere nuovi campi a un indice esistente in qualsiasi momento, anche se i campi esistenti non possono essere modificati o eliminati. Quando si aggiunge un nuovo campo, tutti i documenti esistenti nell'indice avranno automaticamente un valore Null per tale campo. Non verrà utilizzato ulteriore spazio di archiviazione finché non verranno aggiunti nuovi documenti all'indice.

<a name="Suggesters"></a>
##Componenti per il suggerimento

Il suggerimento di Ricerca di Azure rappresenta una funzionalità di query a completamento automatico o a digitazione automatica, che offre un elenco di possibili termini di ricerca in risposta a input della stringa parziali e immessi nella casella di ricerca. I suggerimenti di query sono quelli visualizzati quando si usano i motori di ricerca commerciali: se si digita ".NET" in Bing, viene visualizzato un elenco di termini quali ".NET 4.5", ".NET Framework 3.5" e così via. Quando si utilizza l'API REST di Ricerca di Azure, l'implementazione dei suggerimenti in un'applicazione di Ricerca di Azure richiede che vengano eseguite le seguenti operazioni:

- Abilitare i suggerimenti aggiungendo una costruzione **suggester** nell'indice, assegnandole un nome, una modalità di ricerca e un elenco di campi nei quali richiamare la digitazione automatica. Ad esempio, se si specifica "cityName" come campo di origine e si digita la stringa di ricerca parziale "Sea", verranno visualizzati nomi di città quali "Seattle", "Seaside" e "Seatac". Questi tre nomi rappresentano i suggerimenti di query offerti all'utente.

- Richiamare i suggerimenti utilizzando l'[API per i suggerimenti](#Suggestions) nel codice dell'applicazione. In genere, le stringhe di ricerca parziali vengono inviate al servizio mentre l'utente digita la query di ricerca e tale l'API restituisce un set di espressioni suggerite.

In questo articolo viene spiegato come configurare un **componente per il suggerimento**. Inoltre, è necessario consultare l'articolo [API per i suggerimenti](#Suggestions) per informazioni dettagliate sulla modalità di utilizzo di un componente per il suggerimento.

**Utilizzo**

I `Suggesters` vengono creati nell'indice e funzionano in modo ottimale quando vengono usati per suggerire documenti specifici anziché espressioni o termini separati. I campi che rappresentano i candidati migliori sono i titoli, i nomi e le altre frasi relativamente brevi che possono identificare un elemento. I campi meno efficaci sono quelli ripetitivi, come le categorie e i tag, o quelli molto lunghi, come le descrizioni o i commenti.

Come parte della definizione dell'indice è possibile aggiungere un solo componente per il suggerimento alla raccolta `suggesters`. Un componente per il suggerimento è definito dalle proprietà seguenti:

- `name`: nome del componente. Questo nome viene usato nelle chiamate all'API `suggest`.
- `searchMode`: strategia usata per la ricerca di espressioni candidate. L'unica modalità attualmente supportata è `analyzingInfixMatching`, che ricerca una corrispondenza flessibile di espressioni all'inizio o all'interno di frasi.
- `sourceFields`: elenco di uno o più campi che sono l'origine del contenuto per i suggerimenti. Solo i campi di tipo `Edm.String` e `Collection(Edm.String)` possono essere origini per i suggerimenti. È possibile usare solo campi per i quali non è impostato un analizzatore di lingua personalizzato.

**Esempio di componente di suggerimento**

Un componente di suggerimento fa parte dell'indice. Nella versione attuale della raccolta `suggesters` può essere presente un solo componente per il suggerimento, insieme alla raccolta dei campi e dei `scoringProfiles`.

		{
		  "name": "hotels",
		  "fields": [
		     . . .
		   ],
		  "suggesters": [
		    {
		    "name": "sg",
		    "searchMode": "analyzingInfixMatching",
		    "sourceFields: ["hotelName", "category"]
		    }
		  ],
		  "scoringProfiles": [
		     . . .
		  ]
		}

> [AZURE.NOTE]Se è stata usata la versione di anteprima pubblica di Ricerca di Azure, `suggesters` sostituisce una proprietà booleana precedente (`"suggestions": false`) che supporta solo suggerimenti di tipo prefisso per le stringhe brevi (da 3 a 25 caratteri). Il relativo sostituto, `suggesters`, supporta la corrispondenza di infissi che trova termini corrispondenti all'inizio o all'interno del contenuto del campo, con una migliore tolleranza per gli errori nelle stringhe di ricerca. A partire dalla versione disponibile al pubblico, questa è la sola implementazione dell'API Suggestions. La proprietà `suggestions` precedente introdotta in `api-version=2014-07-31-Preview` continua a funzionare in tale versione, ma non è operativa nella versione `2015-02-28` o successiva di Ricerca di Azure.

<a name="UpdateIndex"></a>
## Aggiornare un indice

È possibile aggiornare un indice esistente in Ricerca di Azure usando una richiesta HTTP PUT. Le operazioni di aggiornamento valide includono l'aggiunta di nuovi campi allo schema esistente, la modifica delle opzioni CORS e la modifica dei profili di punteggio. Per altre informazioni, vedere [Aggiungere profili di punteggio a un indice di ricerca](https://msdn.microsoft.com/library/azure/dn798928.aspx). È necessario specificare il nome dell'indice da aggiornare nell'URI della richiesta:

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Importante**: il supporto per gli aggiornamenti dello schema di indice è limitato alle operazioni che non richiedono la ricompilazione dell'indice di ricerca. Gli aggiornamenti dello schema che richiedono la reindicizzazione, ad esempio la modifica dei tipi di campo, non sono attualmente supportati. È possibile aggiungere nuovi campi in qualsiasi momento, anche se i campi esistenti non possono essere modificati o eliminati. Lo stesso vale per `suggesters`. È possibile aggiungere nuovi campi a un componente di questo tipo, ma non è possibile rimuoverli da `suggesters` né aggiungere campi esistenti a `suggesters`.

Quando si aggiunge un nuovo campo a un indice, tutti i documenti esistenti nell'indice avranno automaticamente un valore Null per tale campo. Non verrà utilizzato ulteriore spazio di archiviazione finché non verranno aggiunti nuovi documenti all'indice.

**Richiesta**

Per tutte le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta di **aggiornamento di un indice** viene creata mediante HTTP PUT. Nel caso di PUT, il nome dell'indice fa parte dell'URL. Se l'indice non esiste, viene creato. Se esiste già, viene aggiornato in base alla nuova definizione.

Il nome dell'indice deve essere scritto in caratteri minuscoli, deve iniziare con una lettera o un numero, non deve contenere barre o punti e deve avere una lunghezza inferiore ai 128 caratteri. Dopo l'iniziale costituita da una lettera o un numero, il resto del nome può contenere lettere, numeri e trattini, purché i trattini non siano consecutivi.

`api-version=[string]` (elemento obbligatorio). La versione di anteprima è `api-version=2015-02-28-Preview`. Per informazioni dettagliate sulle altre versioni, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `Content-Type`: elemento obbligatorio. Impostare il valore su `application/json`.
- `api-key`: elemento obbligatorio. L'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta di **aggiornamento dell'indice** deve includere un'intestazione `api-key` impostata sulla chiave amministratore, anziché su una chiave di query.

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio e `api-key` dal dashboard servizi nel portale di Azure. Per informazioni, vedere [Creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md).

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
              "searchable_field_name": relative_weight_value (positive numbers),
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


**Risposta**

Per una richiesta con esito positivo: "204 Nessun contenuto".

Per impostazione predefinita, il corpo della risposta è vuoto. Se invece l'intestazione della richiesta `Prefer` è impostata su `return=representation`, il corpo della risposta conterrà il codice JSON per la definizione di indice aggiornata. In questo caso, il codice di stato di esito positivo sarà "200 OK".

<a name="ListIndexes"></a>
## Elencare gli indici

L'operazione per **elencare gli indici** restituisce un elenco degli indici attualmente disponibili in Ricerca di Azure.

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**Richiesta**

Per tutte le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta per **elencare gli indici** può essere creata mediante il metodo GET.

`api-version=[string]` (elemento obbligatorio). La versione di anteprima è `api-version=2015-02-28-Preview`. Per informazioni dettagliate sulle altre versioni, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `api-key`: elemento obbligatorio. L'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta per **elencare gli indici** deve includere un'intestazione `api-key` impostata su una chiave amministratore, anziché su una chiave di query.

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio e `api-key` dal dashboard servizi nel portale di Azure. Per informazioni, vedere [Creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md).

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

    GET /indexes?api-version=2015-02-28-Preview&$select=name

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
## Ottenere un indice

L'operazione per **ottenere un indice** recupera la definizione dell'indice da Ricerca di Azure.

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Richiesta**

Per le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta per **ottenere un indice** può essere creata mediante il metodo GET.

Il valore [index name] nell'URI della richiesta specifica l'indice da restituire dalla raccolta di indici.

`api-version=[string]` (obbligatorio). La versione di anteprima è `api-version=2015-02-28-Preview`. Per informazioni dettagliate sulle altre versioni, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `api-key`: l'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta per **ottenere un indice** deve includere un'intestazione `api-key` impostata su una chiave amministratore, anziché su una chiave di query.

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio e `api-key` dal dashboard servizi nel portale di Azure. Per informazioni, vedere [Creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md).

**Corpo della richiesta**

Nessuno.

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 200 OK.

Per un esempio di payload di risposta, vedere l'esempio JSON nella sezione relativa alla [creazione di un indice](#CreateUpdateIndexExample).

<a name="DeleteIndex"></a>
## Eliminare un indice

L'operazione di **eliminazione di un indice** rimuove un indice e i documenti associati da Ricerca di Azure. È possibile ottenere il nome dell'indice dal dashboard servizi nel portale di Azure oppure dall'API. Per informazioni dettagliate, vedere [Elencare gli indici](#ListIndexes).

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Richiesta**

Per le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta di **eliminazione di un indice** può essere creata mediante il metodo DELETE.

Il valore [index name] nell'URI della richiesta specifica l'indice da eliminare dalla raccolta di indici.

`api-version=[string]` (obbligatorio). La versione di anteprima è `api-version=2015-02-28-Preview`. Per informazioni dettagliate sulle altre versioni, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `api-key`: elemento obbligatorio. L'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per l'URL del servizio. La richiesta di **eliminazione di un indice** deve includere un'intestazione `api-key` impostata sulla chiave amministratore, anziché su una chiave di query.

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio e `api-key` dal dashboard servizi nel portale di Azure. Per informazioni, vedere [Creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md).

**Corpo della richiesta**

Nessuno.

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 204 Nessun contenuto.

<a name="GetIndexStats"></a>
## Ottenere le statistiche di un indice

L'operazione per **ottenere le statistiche di un indice** restituisce il numero dei documenti per l'indice corrente da Ricerca di Azure, oltre all'utilizzo dello spazio di archiviazione.

	GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

**Richiesta**

Per tutte le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta per **ottenere le statistiche di un indice** può essere creata mediante il metodo GET.

Il valore [index name] nell'URI della richiesta indica al servizio di restituire le statistiche per l'indice specificato.

`api-version=[string]` (obbligatorio). La versione di anteprima è `api-version=2015-02-28-Preview`. Per informazioni dettagliate sulle altre versioni, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).


**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `api-key`: l'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta per **ottenere le statistiche di un indice** deve includere un'intestazione `api-key` impostata su una chiave amministratore, anziché su una chiave di query.

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio e `api-key` dal dashboard servizi nel portale di Azure. Per informazioni, vedere [Creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md).

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

Il codice dell'applicazione deve generare documenti JSON da caricare in Ricerca di Azure oppure è possibile usare un [indicizzatore](https://msdn.microsoft.com/library/dn946891.aspx) per caricare documenti se l'origine dati è il database SQL di Azure o DocumentDB. In genere, gli indici vengono popolati da un singolo set di dati specificato dall'utente.

È consigliabile avere a disposizione un documento per ogni elemento da cercare. Un'applicazione per il noleggio di film può avere un documento per ogni film, un'applicazione di tipo vetrina può avere un documento per ogni SKU, un'applicazione per corsi online può avere un documento per ogni corso oppure una società di ricerche può avere un documento per ogni articolo accademico disponibile nel proprio archivio e così via.

I documenti sono costituiti da uno o più campi. I campi possono contenere testo suddiviso da Ricerca di Azure in token corrispondenti a termini di ricerca, oltre a valori senza token o non di testo che possono essere usati nei filtri o nei profili di punteggio. I nomi, i tipi di dati e le funzionalità di ricerca supportati per ogni campo sono determinati dallo schema dell'indice. Uno dei campi in ogni schema di indice deve essere designato come ID e ogni documento deve avere un valore per il campo ID che identifica in modo univoco tale documento nell'indice. Tutti gli altri campi dei documenti sono facoltativi e, se non è specificato alcun valore, viene usato il valore predefinito Null. Si noti che i valori Null non occupano spazio nell'indice di ricerca.

Prima di poter caricare documenti, è necessario aver creato l'indice nel servizio. Per informazioni dettagliate su questo primo passaggio, vedere [Creare un indice](#CreateIndex).

<a name="AddOrUpdateDocuments"></a>
## Aggiungere, aggiornare o eliminare documenti

È possibile caricare, unire o eliminare documenti da un indice specificato usando HTTP POST. Per un numero elevato di aggiornamenti, è consigliabile creare batch di documenti (fino a 1000 documenti per batch o circa 16 MB per batch).

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Richiesta**

Per tutte le richieste del servizio, è necessario usare il protocollo HTTPS. È possibile caricare, unire o eliminare documenti da un indice specificato usando HTTP POST.

L'URI della richiesta include [index name], che specifica l'indice su cui eseguire l'operazione POST sui documenti. Questa operazione può essere eseguita su un solo indice alla volta.

`api-version=[string]` (elemento obbligatorio). La versione di anteprima è `api-version=2015-02-28-Preview`. Per informazioni dettagliate sulle altre versioni, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `Content-Type`: elemento obbligatorio. Impostare il valore su `application/json`.
- `api-key`: elemento obbligatorio. L'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per il servizio. La richiesta di **aggiunta di documenti** deve includere un'intestazione `api-key` impostata sulla chiave amministratore, anziché su una chiave di query.

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio e `api-key` dal dashboard servizi nel portale di Azure. Per informazioni, vedere [Creare un servizio di Ricerca di Azure nel portale](.search-create-service-portal.md).

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
## Eseguire ricerche nei documenti

Un'operazione **Search** viene generata come richiesta GET o POST e specifica i parametri che forniscono i criteri per la selezione dei documenti corrispondenti.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Quando usare POST invece di GET**

Quando si usa HTTP GET per chiamare l'API di **Ricerca**, è necessario tenere presente che la lunghezza dell'URL della richiesta non può superare 8 KB. Di solito è sufficiente per la maggior parte delle applicazioni. Alcune applicazioni generano tuttavia query di dimensioni molto grandi, specialmente le espressioni di filtro OData. Per queste applicazioni l'uso di HTTP POST è una scelta migliore. Le dimensioni massime della richiesta POST sono di circa 17 MB, sufficienti anche per le query più complesse.

**Richiesta**

Per le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta **Search** può essere creata con il metodo GET.

L'URI della richiesta specifica l'indice in cui eseguire la query per tutti i documenti corrispondenti ai parametri. I parametri vengono specificati nella stringa di query per le richieste GET e nel corpo della richiesta nel caso di richieste POST.

Come procedura consigliata per la creazione di richieste GET, usare la [codifica dell'URL](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) per i parametri della query specifici quando si chiama direttamente l'API REST. Per le operazioni **Search** sono inclusi i parametri seguenti:

- `$filter`
- `facet`
- `highlightPreTag`
- `highlightPostTag`
- `search`
- `moreLikeThis`

La codifica con URL è consigliata solo per questi parametri. Se inavvertitamente si codifica con URL l'intera stringa della query (ossia tutti gli elementi che seguono il punto interrogativo), le richieste verranno interrotte.

La codifica dell'URL è necessaria solo quando si chiama direttamente l'API REST con GET. La codifica dell'URL non è invece necessaria quando si chiama **Search** con POST o quando si usa la [libreria client .NET](https://msdn.microsoft.com/library/dn951165.aspx) che gestisce automaticamente la codifica dell'URL.

<a name="SearchQueryParameters"></a> **Parametri della query**

**Search** accetta diversi parametri che forniscono i criteri di query e specificano anche il comportamento di ricerca. Specificare questi parametri nella stringa di query dell'URL quando si chiama **Search** con GET e come proprietà JSON nel corpo della richiesta quando si chiama **Search** con POST. La sintassi per alcuni parametri è leggermente diversa tra GET e POST. Queste differenze sono riportate di seguito:

`search=[string]` (facoltativo): specifica il testo da cercare. Per impostazione predefinita, la ricerca viene eseguita in tutti i campi `searchable` a meno che non sia specificato `searchFields`. Quando si esegue la ricerca nei campi `searchable`, il testo della ricerca viene suddiviso in token. In questo modo, più termini possono essere separati con uno spazio vuoto, ad esempio `search=hello world`. Per trovare corrispondenze con qualsiasi termine, usare `*`, che può essere utile per le query con filtro booleano. L'omissione di questo parametro equivale a impostarlo su `*`. Per le specifiche della sintassi di ricerca, vedere [Semplice sintassi di query nella Ricerca di Azure](https://msdn.microsoft.com/library/dn798920.aspx).

  - **Nota**: quando si eseguono query sui campi `searchable`, talvolta si possono ottenere risultati imprevisti. Il tokenizer include logica per la gestione di casi comuni nel testo inglese, ad esempio apostrofi, virgole nei numeri e così via. Ad esempio, `search=123,456` corrisponde a un singolo termine 123,456 invece che ai termini separati 123 e 456, poiché in inglese le virgole vengono usate come separatore delle migliaia per i numeri di grandi dimensioni. È quindi consigliabile usare uno spazio vuoto in sostituzione dei segni di punteggiatura per separare i termini nel parametro `search`.

`searchMode=any|all` (facoltativo, il valore predefinito è `any`): specifica se è necessario trovare una corrispondenza con tutti i termini di ricerca o con uno qualsiasi per includere il documento nelle corrispondenze.

`searchFields=[string]` (facoltativo): specifica l'elenco di nomi di campo delimitati da virgole in cui cercare il testo specificato. I campi di destinazione devono essere contrassegnati come `searchable`.

`moreLikeThis=[key]` (facoltativo) **Importante**: questa funzionalità è disponibile solo in `2015-02-28-Preview`. Non è possibile usare questa opzione in una query che contiene il parametro di ricerca di testo `search=[string]`. Il parametro `moreLikeThis` trova documenti che sono simili al documento specificato dalla chiave del documento. Quando viene effettuata una richiesta di ricerca con `moreLikeThis`, viene generato un elenco di termini di ricerca in base alla frequenza e alla rarità dei termini nel documento di origine. Questi termini vengono quindi usati per effettuare la richiesta. Per impostazione predefinita, viene considerato il contenuto di tutti i campi `searchable` a meno che non si usi `searchFields` per limitare i campi in cui eseguire la ricerca.

`$skip=#` (facoltativo): specifica il numero di risultati della ricerca da ignorare. Non può essere maggiore di 100.000. Se è necessario analizzare i documenti in sequenza, ma non è possibile usare `$skip` a causa di questa limitazione, prendere in considerazione l'uso di `$orderby` su una chiave totalmente ordinata e `$filter` con una query di intervallo.

> [AZURE.NOTE]Quando si chiama **Search** con POST, questo parametro è denominato `skip` invece di `$skip`.

`$top=#` (facoltativo): specifica il numero di risultati della ricerca da recuperare. Può essere usato insieme a `$skip` per implementare il paging sul lato client dei risultati della ricerca.

> [AZURE.NOTE]Ricerca di Azure usa il ***paging sul lato server*** per evitare che le query recuperino molti documenti contemporaneamente. Le dimensioni predefinite della pagina sono pari a 50, mentre le dimensioni massime della pagina sono pari a 1000. Ciò significa che per impostazione predefinita **Search** restituisce al massimo 50 risultati se non si specifica `$top`. Se sono presenti più di 50 risultati, la risposta include informazioni per recuperare la pagina successiva con almeno 50 risultati (vedere `@odata.nextLink` e `@search.nextPageParameters` nell'[esempio sottostante](#SearchResponse)). In modo analogo, se si specifica un valore maggiore di 1000 per `$top` e sono presenti più di 1000 risultati, verranno restituiti solo i primi 1000 insieme a informazioni per recuperare la pagina successiva con al massimo 1000 risultati.

> [AZURE.NOTE]Quando si chiama **Search** con POST, questo parametro è denominato `top` invece di `$top`.

`$count=true|false` (facoltativo, il valore predefinito è `false`): specifica se recuperare il conteggio totale dei risultati. L'impostazione di questo valore su `true` può influire negativamente sulle prestazioni. Si noti che il conteggio restituito è un'approssimazione.

> [AZURE.NOTE]Quando si chiama **Search** con POST, questo parametro è denominato `count` invece di `$count`.

`$orderby=[string]` (facoltativo): specifica un elenco di espressioni delimitate da virgole in base alle quali ordinare i risultati. Ogni espressione può essere un nome campo o una chiamata alla funzione `geo.distance()`. Ogni espressione può essere seguita da `asc` per indicare l'ordine crescente e da `desc` per indicare l'ordine decrescente. Per impostazione predefinita, l'ordinamento è crescente. Le situazioni di parità di priorità vengono risolte in base ai punteggi di corrispondenza dei documenti. Se `$orderby` non è specificato, l'ordine predefinito è decrescente in base al punteggio di corrispondenza dei documenti. È previsto un limite di 32 clausole per `$orderby`.

> [AZURE.NOTE]Quando si chiama **Search** con POST, questo parametro è denominato `orderby` invece di `$orderby`.

`$select=[string]` (facoltativo): specifica un elenco di campi delimitati da virgole da recuperare. Se non è specificato, vengono inclusi tutti i campi contrassegnati come recuperabili nello schema. È anche possibile richiedere in modo esplicito tutti i campi impostando questo parametro su `*`.

> [AZURE.NOTE]Quando si chiama **Search** con POST, questo parametro è denominato `select` invece di `$select`.

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

> [AZURE.NOTE]Quando si chiama **Search** con POST, questo parametro è denominato `facets` invece di `facet`. Viene specificato anche come matrice di stringhe JSON, dove ogni stringa è un'espressione facet distinta.

`$filter=[string]` (facoltativo): specifica un'espressione di ricerca strutturata nella sintassi standard di OData. Per informazioni dettagliate sul sottoinsieme della grammatica delle espressioni OData supportato da Ricerca di Azure, vedere l'articolo relativo alla [sintassi delle espressioni OData](#ODataExpressionSyntax).

> [AZURE.NOTE]Quando si chiama **Search** con POST, questo parametro è denominato `filter` invece di `$filter`.

`highlight=[string]` (facoltativo): specifica un set di nomi di campo delimitati da virgole usati per evidenziare i risultati. Per l'evidenziazione dei risultati è possibile usare solo i campi `searchable`.

`highlightPreTag=[string]` (facoltativo, il valore predefinito è `<em>`): specifica un tag di stringa che viene aggiunto all'inizio delle evidenziazioni dei risultati. Deve essere impostato con `highlightPostTag`.

> [AZURE.NOTE]Quando si chiama **Search** con GET, i caratteri riservati nell'URL devono essere codificati in percentuale (ad esempio, %23 invece di #).

`highlightPostTag=[string]` (facoltativo, il valore predefinito è `</em>`): specifica un tag di stringa che viene aggiunto alla fine delle evidenziazioni dei risultati. Deve essere impostato con `highlightPreTag`.

> [AZURE.NOTE]Quando si chiama **Search** con GET, i caratteri riservati nell'URL devono essere codificati in percentuale (ad esempio, %23 invece di #).

`scoringProfile=[string]` (facoltativo): specifica il nome di un profilo di punteggio da usare per valutare i punteggi di corrispondenza per i documenti trovati, in modo da ordinare i risultati.

`scoringParameter=[string]` (zero o più): indica il valore per ogni parametro definito in una funzione di assegnazione di punteggio (ad esempio, `referencePointParameter`) usando il formato nome:valore. Ad esempio, se il profilo di punteggio definisce una funzione con un parametro denominato "mylocation", l'opzione della stringa di query sarà &scoringParameter=mylocation:-122.2,44.8

> [AZURE.NOTE]Quando si chiama **Search** con POST, questo parametro è denominato `scoringParameters` invece di `scoringParameter`. Viene specificato anche come matrice di stringhe JSON, dove ogni stringa è una coppia nome:valore distinta.

`minimumCoverage` (facoltativo, valore predefinito pari a 100): un numero compreso tra 0 e 100 che indica la percentuale di indice che deve essere inclusa nella query di ricerca in modo che l'esecuzione di quest'ultima venga segnalata come corretta. Per impostazione predefinita, deve essere disponibile l'intero indice. In caso contrario, `Search` restituirà il codice di stato HTTP 503. Se si imposta `minimumCoverage` e `Search` ha esito positivo, verrà restituito HTTP 200 che include un valore `@search.coverage` nella risposta; quest'ultimo indica la percentuale dell'indice che è stata inclusa nella query.

> [AZURE.NOTE]L'impostazione di questo parametro su un valore inferiore a 100 può essere utile per garantire la disponibilità di ricerca anche per i servizi che dispongono di una sola replica. Tuttavia, non si assicura che tutti i documenti corrispondenti siano presenti nei risultati di ricerca. Se la funzionalità di richiamo della ricerca è più importante rispetto a quella di disponibilità, mantenere il valore predefinito di 100 per `minimumCoverage`.

`api-version=[string]` (obbligatorio). La versione di anteprima è `api-version=2015-02-28-Preview`. Per informazioni dettagliate sulle altre versioni, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).

Nota: per questa operazione il valore `api-version` viene specificato come parametro di query nell'URL, indipendentemente dal fatto che si chiami **Search** con GET o con POST.

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `api-key`: l'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per l'URL del servizio. La richiesta di **ricerca** può specificare una chiave amministratore o una chiave di query per `api-key`.

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio e `api-key` dal dashboard servizi nel portale di Azure. Per informazioni, vedere [Creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md).

**Corpo della richiesta**

Per GET: nessuno.

Per POST:

    {
      "count": true | false (default),
      "facets": [ "facet_expression_1", "facet_expression_2", ... ],
      "filter": "odata_filter_expression",
      "highlight": "highlight_field_1, highlight_field_2, ...",
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 100),
      "moreLikeThis": "document_key" (mutually exclusive with "search" parameter),
      "orderby": "orderby_expression",
      "scoringParameters": [ "scoring_parameter_1", "scoring_parameter_2", ... ],
      "scoringProfile": "scoring_profile_name",
      "search": "simple_query_expression",
      "searchFields": "field_name_1, field_name_2, ...",
      "searchMode": "any" (default) | "all",
      "select": "field_name_1, field_name_2, ...",
      "skip": # (default 0),
      "top": #
    }

<a name="SearchResponse"></a> **Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 200 OK.

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.coverage": # (if minimumCoverage was provided in the query),
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
      "@search.nextPageParameters": { (request body to fetch the next page of results if result count exceeds page size and Search was called with POST)
        "count": ... (value from request body if present),
        "facets": ... (value from request body if present),
        "filter": ... (value from request body if present),
        "highlight": ... (value from request body if present),
        "highlightPreTag": ... (value from request body if present),
        "highlightPostTag": ... (value from request body if present),
        "minimumCoverage": ... (value from request body if present),
        "moreLikeThis": ... (value from request body if present),
        "orderby": ... (value from request body if present),
        "scoringParameters": ... (value from request body if present),
        "scoringProfile": ... (value from request body if present),
        "search": ... (value from request body if present),
        "searchFields": ... (value from request body if present),
        "searchMode": ... (value from request body if present),
        "select": ... (value from request body if present),
        "skip": ... (page size plus value from request body if present),
        "top": ... (value from request body if present minus page size),
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
      "@odata.nextLink": (URL to fetch the next page of results if result count exceeds page size; Applies to both GET and POST)
    }

**Esempi:**

È possibile trovare altri esempi nella pagina relativa alla [sintassi delle espressioni OData per Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798921.aspx).

1) Eseguire una ricerca nell'indice ordinato in modo decrescente in base alla data.

    GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "*",
      "orderby": [ "lastRenovationDate desc" ]
    }

2) In una ricerca con facet, eseguire una ricerca nell'indice e recuperare i facet per categorie, classificazioni, tag, oltre a elementi con baseRate in intervalli specifici:

    GET /indexes/hotels/docs?search=test&facet=category&facet=rating&facet=tags&facet=baseRate,values:80|150|220&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "test",
      "facets": [ "category", "rating", "tags", "baseRate,values:80|150|220" ]
    }

3) Usando un filtro, limitare i risultati della precedente query con facet dopo che l'utente ha fatto clic su Rating 3 e sulla categoria "Motel":

    GET /indexes/hotels/docs?search=test&facet=tags&facet=baseRate,values:80|150|220&$filter=rating eq 3 and category eq 'Motel'&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "test",
      "facets": [ "tags", "baseRate,values:80|150|220" ],
      "filter": "rating eq 3 and category eq 'Motel'"
    }

4) In una ricerca con facet, impostare un limite massimo per i termini univoci restituiti in una query. Il valore predefinito è 10, ma è possibile aumentare o ridurre questo valore usando il parametro `count` nell'attributo `facet`:

    GET /indexes/hotels/docs?search=test&facet=city,count:5&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "test",
      "facets": [ "city,count:5" ]
    }

5) Eseguire una ricerca nell'indice entro campi specifici, ad esempio un campo relativo alla lingua:

    GET /indexes/hotels/docs?search=hôtel&searchFields=description_fr&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "hôtel",
      "searchFields": [ "description_fr" ]
    }

6) Eseguire una ricerca nell'indice in più campi. Ad esempio, è possibile archiviare ed eseguire query nei campi disponibili per la ricerca in più lingue, all'interno dello stesso indice. Se nello stesso documento coesistono descrizioni in inglese e in francese, sarà possibile restituirle tutte, o solo alcune, nei risultati della query:

	GET /indexes/hotels/docs?search=hotel&searchFields=description,description_fr&api-version=2015-02-28-Preview

	POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "hotel",
      "searchFields": [ "description", "description_fr" ]
    }

Si noti che è possibile eseguire query su un solo indice alla volta. Non creare più indici per una lingua a meno che non si preveda di eseguire query su un indice alla volta.

7) Paging: ottenere la prima pagina degli elementi (la dimensione di pagina è 10):

    GET /indexes/hotels/docs?search=*&$skip=0&$top=10&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "*",
      "skip": 0,
      "top": 10
    }

8) Paging: ottenere la seconda pagina degli elementi (la dimensione di pagina è 10):

    GET /indexes/hotels/docs?search=*&$skip=10&$top=10&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "*",
      "skip": 10,
      "top": 10
    }

9) Recuperare un set specifico di campi:

    GET /indexes/hotels/docs?search=*&$select=hotelName,description&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "*",
      "select": [ "hotelName", "description" ]
    }

10) Recuperare i documenti corrispondenti a un'espressione di filtro specifica:

    GET /indexes/hotels/docs?$filter=(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "filter": "(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'"
    }

11) Eseguire una ricerca nell'indice e restituire frammenti con evidenziazioni dei risultati:

    GET /indexes/hotels/docs?search=something&highlight=description&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "something",
      "highlight": "description"
    }

12) Eseguire una ricerca nell'indice e restituire documenti ordinati dal più vicino al più lontano rispetto a una posizione di riferimento:

    GET /indexes/hotels/docs?search=something&$orderby=geo.distance(location, geography'POINT(-122.12315 47.88121)')&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "something",
      "orderby": [ "geo.distance(location, geography'POINT(-122.12315 47.88121)')" ]
    }

13) Eseguire una ricerca nell'indice presupponendo che sia disponibile un profilo di punteggio denominato "geo" con due funzioni di assegnazione di punteggio in base alla distanza, che definiscono rispettivamente i parametri "currentLocation" e "lastLocation":

    GET /indexes/hotels/docs?search=something&scoringProfile=geo&scoringParameter=currentLocation:-122.123,44.77233&scoringParameter=lastLocation:-121.499,44.2113&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "something",
      "scoringProfile": "geo",
      "scoringParameters": [ "currentLocation:-122.123,44.77233", "lastLocation:-121.499,44.2113" ]
    }

14) Trovare documenti nell'indice usando una [semplice sintassi di query](https://msdn.microsoft.com/library/dn798920.aspx). Questa query restituisce gli hotel i cui campi disponibili per la ricerca contengono i termini "comfort" e "location" ma non "motel":

    GET /indexes/hotels/docs?search=comfort +location -motel&searchMode=all&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "comfort +location -motel",
      "searchMode": "all"
    }

Si noti l'uso di `searchMode=all` nell'esempio precedente. Se si include questo parametro, viene eseguito l'override del valore predefinito `searchMode=any`. In questo modo, si ha la sicurezza che `-motel` significa "AND NOT" anziché "OR NOT". Senza `searchMode=all`, si ottiene "OR NOT" che espande i risultati della ricerca anziché limitarli e questo può essere poco intuitivo per alcuni utenti.


<a name="LookupAPI"></a>
##Cercare un documento

L'operazione di **ricerca di un documento** recupera un documento da Ricerca di Azure. È utile quando un utente fa clic su un risultato della ricerca e si desidera cercare dettagli specifici su tale documento.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters]
    api-key: [admin or query key]

**Richiesta**

Per le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta di **ricerca di un documento** può essere creata nel modo seguente.

    GET /indexes/[index name]/docs/key?[query parameters]

In alternativa, è possibile usare la sintassi tradizionale di OData per la ricerca delle chiavi:

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

L'URI della richiesta include i valori [index name] e [key], che specificano il documento da recuperare da un determinato indice. È possibile recuperare un solo documento alla volta. Per ottenere più documenti con una singola richiesta, eseguire l'operazione di **ricerca nei documenti**.

**Parametri della query**

`$select=[string]` (facoltativo): specifica un elenco di campi delimitati da virgole da recuperare. Se non è specificato o se è impostato su `*`, nella proiezione vengono inclusi tutti i campi contrassegnati come recuperabili nello schema.

`api-version=[string]` (obbligatorio). La versione di anteprima è `api-version=2015-02-28-Preview`. Per informazioni dettagliate sulle altre versioni, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).

Nota: per questa operazione, l'elemento `api-version` è specificato come parametro di query.

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `api-key`: l'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per l'URL del servizio. La richiesta di **ricerca di un documento** può specificare una chiave amministratore o una chiave di query per `api-key`.

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio e `api-key` dal dashboard servizi nel portale di Azure. Per informazioni, vedere [Creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md).

**Corpo della richiesta**

Nessuno.

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 200 OK.

    {
      field_name: field_value (fields matching the default or specified projection)
    }

**Esempio**

Cercare il documento con chiave '2':

    GET /indexes/hotels/docs/2?api-version=2015-02-28-Preview

Cercare il documento con chiave '3' usando la sintassi di OData:

    GET /indexes('hotels')/docs('3')?api-version=2015-02-28-Preview

<a name="CountDocs"></a>
##Conteggio documenti

L'operazione di **conteggio dei documenti** recupera un conteggio del numero di documenti in un indice di ricerca. La sintassi `$count` fa parte del protocollo OData.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin or query key]

**Richiesta**

Per le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta di **conteggio dei documenti** può essere creata mediante il metodo GET.

Il valore [index name] nell'URI della richiesta indica al servizio di restituire un conteggio di tutti gli elementi disponibili nella raccolta di documenti dell'indice specificato.

`api-version=[string]` (obbligatorio). La versione di anteprima è `api-version=2015-02-28-Preview`. Per informazioni dettagliate sulle altre versioni, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `Accept`: questo valore deve essere impostato su `text/plain`.
- `api-key`: l'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per l'URL del servizio. La richiesta di **conteggio dei documenti** può specificare una chiave amministratore o una chiave di query per `api-key`.

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio e `api-key` dal dashboard servizi nel portale di Azure. Per informazioni, vedere [Creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md).

**Corpo della richiesta**

Nessuno.

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 200 OK.

Il corpo della risposta include il valore count sotto forma di Integer formattato in testo normale.

<a name="Suggestions"></a>
##Suggerimenti

L'operazione di recupero dei **suggerimenti** ottiene i suggerimenti in base a un input di ricerca parziale. In genere, viene usata nelle caselle di ricerca per fornire suggerimenti durante la digitazione quando gli utenti immettono i termini di ricerca.

Le richieste di suggerimenti hanno lo scopo di suggerire documenti di destinazione, pertanto il testo suggerito può essere ripetuto se più documenti corrispondono allo stesso input di ricerca. È possibile usare `$select` per recuperare altri campi di documento (inclusa la chiave) in modo da poter determinare quale documento è l'origine di ogni suggerimento.

Un'operazione **Suggestions** viene generata come richiesta GET.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/suggest?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Quando usare POST invece di GET**

Quando si usa HTTP GET per chiamare l'API **Suggestions**, è necessario tenere presente che la lunghezza dell'URL della richiesta non può superare 8 KB. Di solito è sufficiente per la maggior parte delle applicazioni. Alcune applicazioni generano tuttavia query di dimensioni molto grandi, specialmente le espressioni di filtro OData. Per queste applicazioni l'uso di HTTP POST è una scelta migliore. Le dimensioni massime della richiesta POST sono di circa 17 MB, sufficienti anche per le query più complesse.

**Richiesta**

Per le richieste del servizio, è necessario usare il protocollo HTTPS. La richiesta **Suggestions** può essere creata con il metodo GET o POST.

L'URI della richiesta specifica il nome dell'indice su cui eseguire la query. I parametri, come il termine di ricerca immesso parzialmente, vengono specificati nella stringa di query per le richieste GET e nel corpo della richiesta nel caso di richieste POST.

Come procedura consigliata per la creazione di richieste GET, usare la [codifica dell'URL](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) per i parametri della query specifici quando si chiama direttamente l'API REST. Per le operazioni **Suggestions** sono inclusi i parametri seguenti:

- `$filter`
- `highlightPreTag`
- `highlightPostTag`
- `search`

La codifica con URL è consigliata solo per questi parametri. Se inavvertitamente si codifica con URL l'intera stringa della query (ossia tutti gli elementi che seguono il punto interrogativo), le richieste verranno interrotte.

La codifica dell'URL è necessaria solo quando si chiama direttamente l'API REST con GET. La codifica dell'URL non è invece necessaria quando si chiama **Suggestions** con POST o quando si usa la [libreria client .NET](https://msdn.microsoft.com/library/dn951165.aspx) che gestisce automaticamente la codifica dell'URL.

**Parametri della query**

**Suggestions** accetta diversi parametri che forniscono i criteri di query e specificano anche il comportamento di ricerca. Specificare questi parametri nella stringa di query dell'URL quando si chiama **Suggestions** con GET e come proprietà JSON nel corpo della richiesta quando si chiama **Suggestions** con POST. La sintassi per alcuni parametri è leggermente diversa tra GET e POST. Queste differenze sono riportate di seguito:

`search=[string]`: specifica il testo della ricerca da usare per i suggerimenti per le query. Deve essere composto da un minimo di 1 carattere e da un massimo di 100 caratteri.

`highlightPreTag=[string]` (facoltativo): specifica un tag di stringa che viene aggiunto all'inizio dei risultati della ricerca. Deve essere impostato con `highlightPostTag`.

> [AZURE.NOTE]Quando si chiama **Suggestions** con GET, i caratteri riservati nell'URL devono essere codificati in percentuale (ad esempio, %23 invece di #).

`highlightPostTag=[string]` (facoltativo): specifica un tag di stringa che viene aggiunto alla fine dei risultati della ricerca. Deve essere impostato con `highlightPreTag`.

> [AZURE.NOTE]Quando si chiama **Suggestions** con GET, i caratteri riservati nell'URL devono essere codificati in percentuale (ad esempio, %23 invece di #).

`suggesterName=[string]`: specifica il nome del componente per il suggerimento, come definito nella raccolta `suggesters` che fa parte della definizione di indice. Un elemento `suggester` determina i campi analizzati per i termini di query suggeriti. Per informazioni dettagliate, vedere [Componenti per il suggerimento](#Suggesters).

`fuzzy=[boolean]` (facoltativo, il valore predefinito è false): quando il valore è true, questa API trova i suggerimenti anche in caso di carattere sostituito o mancante nel testo della ricerca. Anche se offre un'esperienza migliore in alcuni scenari, influisce negativamente sulle prestazioni poiché le ricerche con suggerimenti fuzzy sono più lente e utilizzano più risorse.

`searchFields=[string]` (facoltativo): specifica l'elenco di nomi di campo delimitati da virgole in cui cercare il testo specificato. I campi di destinazione devono essere abilitati per suggerimenti.

`$top=#` (facoltativo, il valore predefinito è 5): specifica il numero di suggerimenti da recuperare. Deve essere un numero compreso tra 1 e 100.

> [AZURE.NOTE]Quando si chiama **Suggestions** con POST, questo parametro è denominato `top` invece di `$top`.

`$filter=[string]` (facoltativo): specifica un'espressione che filtra i documenti considerati per i suggerimenti.

> [AZURE.NOTE]Quando si chiama **Suggestions** con POST, questo parametro è denominato `filter` invece di `$filter`.

`$orderby=[string]` (facoltativo): specifica un elenco di espressioni delimitate da virgole in base alle quali ordinare i risultati. Ogni espressione può essere un nome campo o una chiamata alla funzione `geo.distance()`. Ogni espressione può essere seguita da `asc` per indicare l'ordine crescente e da `desc` per indicare l'ordine decrescente. Per impostazione predefinita, l'ordinamento è crescente. È previsto un limite di 32 clausole per `$orderby`.

> [AZURE.NOTE]Quando si chiama **Suggestions** con POST, questo parametro è denominato `orderby` invece di `$orderby`.

`$select=[string]` (facoltativo): specifica un elenco di campi delimitati da virgole da recuperare. Se non è specificato, vengono restituiti solo la chiave del documento e il testo del suggerimento.

> [AZURE.NOTE]Quando si chiama **Suggestions** con POST, questo parametro è denominato `select` invece di `$select`.

`minimumCoverage` (facoltativo, valore predefinito pari a 80): un numero compreso tra 0 e 100 che indica la percentuale di indice che deve essere inclusa nella query di suggerimento in modo che l'esecuzione di quest'ultima venga segnalata come corretta. Per impostazione predefinita, deve essere disponibile almeno l'80% del codice. In caso contrario, `Suggest` restituirà il codice di stato HTTP 503. Se si imposta `minimumCoverage` e `Suggest` ha esito positivo, verrà restituito HTTP 200 che include un valore `@search.coverage` nella risposta; quest'ultimo indica la percentuale dell'indice che è stata inclusa nella query.

> [AZURE.NOTE]L'impostazione di questo parametro su un valore inferiore a 100 può essere utile per garantire la disponibilità di ricerca anche per i servizi che dispongono di una sola replica. Tuttavia, non si assicura che tutti i suggerimenti corrispondenti siano presenti nei risultati. Se la funzionalità di richiamo della ricerca è più importante rispetto a quella di disponibilità, non impostare un valore predefinito inferiore a 80 per `minimumCoverage`.

`api-version=[string]` (obbligatorio). La versione di anteprima è `api-version=2015-02-28-Preview`. Per informazioni dettagliate sulle altre versioni, vedere [Controllo delle versioni di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn864560.aspx).

Nota: per questa operazione il valore `api-version` viene specificato come parametro di query nell'URL, indipendentemente dal fatto che si chiami **Suggestions** con GET o con POST.

**Intestazioni della richiesta**

L'elenco seguente descrive le intestazioni della richiesta obbligatorie e facoltative.

- `api-key`: l'elemento `api-key` viene usato per autenticare la richiesta nel servizio di ricerca. È un valore stringa univoco per l'URL del servizio. La richiesta di recupero dei **suggerimenti** può specificare una chiave amministratore o una chiave di query per `api-key`.

Per creare l'URL della richiesta, è necessario anche il nome del servizio. È possibile ottenere il nome del servizio e `api-key` dal dashboard servizi nel portale di Azure. Per informazioni, vedere [Creare un servizio di Ricerca di Azure nel portale](search-create-service-portal.md).

**Corpo della richiesta**

Per GET: nessuno.

Per POST:

    {
      "filter": "odata_filter_expression",
	  "fuzzy": true | false (default),
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 80),
      "orderby": "orderby_expression",
      "search": "partial_search_input",
      "searchFields": "field_name_1, field_name_2, ...",
      "select": "field_name_1, field_name_2, ...",
	  "suggesterName": "suggester_name",
      "top": # (default 5)
    }

**Risposta**

Se la risposta ha esito positivo, viene restituito il codice di stato 200 OK.

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
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
      "@search.coverage": # (if minimumCoverage was provided in the query),
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

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&suggesterName=sg&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/suggest?api-version=2015-02-28-Preview
    {
      "search": "lux",
      "top": 5,
      "suggesterName": "sg"
    }

<!---HONumber=August15_HO9-->