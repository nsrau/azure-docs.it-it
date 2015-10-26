<properties
	pageTitle="Profili di punteggio (API REST di Ricerca di Azure versione 2015-02-28-Preview) | Microsoft Azure"
	description="Profili di punteggio (API REST di Ricerca di Azure versione 2015-02-28-Preview)"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.author="heidist"
	ms.date="10/01/2015" />

# Profili di punteggio (API REST di Ricerca di Azure versione 2015-02-28-Preview)

> [AZURE.NOTE]Questo articolo descrive i profili di punteggio in [2015-02-28-Preview](search-api-2015-02-28-preview.md). Attualmente non esiste alcuna differenza tra la versione documentata `2015-02-28` in [MSDN](http://msdn.microsoft.com/library/azure/mt183328.aspx) e la versione `2015-02-28-Preview` descritta di seguito.

## Panoramica

L'assegnazione di punteggio fa riferimento al calcolo di un punteggio di ricerca per ogni elemento restituito nei risultati della ricerca. Il punteggio è un indicatore della rilevanza di un elemento nel contesto dell'operazione di ricerca attuale. Maggiore è il punteggio, più rilevante sarà l'elemento. Nei risultati della ricerca gli elementi vengono classificati dal maggiore al minore, in base ai punteggi di ricerca calcolati per ogni elemento.

Ricerca di Azure usa l'assegnazione predefinita del punteggio per calcolare un punteggio, ma è possibile personalizzare il calcolo tramite un profilo di punteggio. I profili di punteggio offrono maggiore controllo sulla classificazione degli elementi nei risultati della ricerca. Ad esempio, è possibile aumentare la priorità degli elementi in base al rispettivo potenziale di profitto, alzare di livello elementi più recenti o evidenziare elementi che sono rimasti troppo a lungo in magazzino.

Un profilo di punteggio fa parte della definizione dell'indice, costituita da campi, funzioni e parametri.

Per illustrare un profilo di punteggio, l'esempio seguente mostra un semplice profilo denominato 'geo'. Questo profilo aumenta la priorità degli elementi che includono il termine di ricerca nel campo `hotelName`. Usa anche la funzione `distance` per assegnare una preferenza maggiore agli elementi che si trovano entro dieci chilometri dalla posizione attuale. Se si cerca il termine 'inn' e 'inn' fa parte del nome di un hotel, i documenti che includono gli hotel con 'inn' verranno visualizzati più in alto nei risultati della ricerca.

    "scoringProfiles": [
      {
        "name": "geo",
        "text": {
          "weights": { "hotelName": 5 }
        },
        "functions": [
          {
            "type": "distance",
            "boost": 5,
            "fieldName": "location",
            "interpolation": "logarithmic",
            "distance": {
              "referencePointParameter": "currentLocation",
              "boostingDistance": 10
            }
          }
        ]
      }
    ]

Per usare il profilo di punteggio, la query viene formulata in modo da specificare il profilo nella stringa di query. Si noti il parametro di query `scoringProfile=geo` nella richiesta presente nella query seguente.

    GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation:-122.123,44.77233&api-version=2015-02-28-Preview

Questa query cerca il termine 'inn' e passa la posizione attuale. Si noti che questa query include altri parametri, ad esempio `scoringParameter`. I parametri della query sono illustrati in [Cercare documenti (API di Ricerca di Azure)](search-api-2015-02-28-preview/#SearchDocs.md).

Fare clic su [Esempio](#example) per esaminare un esempio più dettagliato del profilo di punteggio.

## Informazioni sull'assegnazione predefinita di punteggio

L'assegnazione di punteggio calcola un punteggio di ricerca per ogni elemento in un set di risultati ordinato in base a una classificazione. A ogni elemento nel set di risultati della ricerca viene assegnato un punteggio di ricerca e quindi gli elementi vengono classificati dal maggiore al minore. Gli elementi con i punteggi maggiori vengono restituiti all'applicazione. Per impostazione predefinita, vengono restituiti i primi 50 elementi, ma è possibile usare il parametro `$top` per restituire un numero minore o maggiore di elementi, fino a un massimo di 1000 elementi in una singola risposta.

Per impostazione predefinita, un punteggio di ricerca viene calcolato in base alle proprietà statistiche dei dati e della query. Ricerca di Azure trova documenti che includono i termini di ricerca nella stringa di query (alcuni o tutti, in base a `searchMode`), preferendo i documenti che includono più istanze del termine di ricerca. Il punteggio di ricerca aumenta ancora di più se il termine risulta raro nell'insieme di dati, ma comune all'interno del documento. I concetti di base per questo approccio alla rilevanza di calcolo sono noti come TF-IDF (Term Frequency-Inverse Document Frequency).

Se non è applicato alcun ordinamento personalizzato, i risultati vengono quindi classificati in base al punteggio di ricerca prima di essere restituiti all'applicazione chiamante. Se non viene specificato `$top`, verranno restituiti i 50 elementi con il punteggio di ricerca maggiore.

I valori dei punteggi di ricerca possono essere ripetuti in un set di risultati. Ad esempio, possono essere presenti 10 elementi con punteggio pari a 1,2, 20 elementi con punteggio pari a 1,0 e 20 elementi con punteggio pari a 0,5. Quando più riscontri hanno lo stesso punteggio di ricerca, l'ordine degli stessi elementi con punteggio non è definito e non è quindi stabile. Se si esegue di nuovo la query, è possibile che gli elementi cambino posizione. Se due elementi hanno punteggio identico, non vi è alcuna garanzia su quale elemento verrà visualizzato per primo.

## Quando usare l'assegnazione personalizzata di punteggio

È consigliabile creare uno o più profili di punteggio quando il comportamento di classificazione predefinito non permette di raggiungere gli obiettivi aziendali stabiliti. È ad esempio possibile che si voglia assegnare una rilevanza di ricerca maggiore agli elementi aggiunti di recente. Analogamente, è possibile che sia presente un campo che include i margini di profitto o un altro campo che indica il potenziale di ricavi. L'aumento della priorità dei riscontri utili per l'azienda può essere un fattore importante nella decisione di usare i profili di punteggio,

che permettono anche di implementare l'ordinamento basato sulla rilevanza. È consigliabile esaminare le pagine di risultati della ricerca usate in precedenza e che permettevano di ordinare i risultati in base a prezzo, data, classificazione o rilevanza. In Ricerca di Azure i profili di punteggio determinano l'opzione di "rilevanza". La definizione della rilevanza è controllata dall'utente, in base agli obiettivi aziendali specifici e al tipo di esperienza di ricerca che si vuole offrire.

<a name="example"></a>
## Esempio

Come indicato in precedenza, l'assegnazione personalizzata di punteggio viene implementata tramite profili di punteggio definiti in uno schema di indice.

Questo esempio illustra lo schema di un indice con due profili di punteggio (`boostGenre`, `newAndHighlyRated`). Qualsiasi query eseguita in questo indice e che include uno dei profili come parametro di query userà il profilo per assegnare un punteggio al set di risultati.

[Provare a eseguire questo esempio](search-get-started-scoring-profiles.md).

    {
      "name": "musicstoreindex",
	  "fields": [
	    { "name": "key", "type": "Edm.String", "key": true },
	    { "name": "albumTitle", "type": "Edm.String" },
	    { "name": "albumUrl", "type": "Edm.String", "filterable": false },
	    { "name": "genre", "type": "Edm.String" },
	    { "name": "genreDescription", "type": "Edm.String", "filterable": false },
	    { "name": "artistName", "type": "Edm.String" },
	    { "name": "orderableOnline", "type": "Edm.Boolean" },
	    { "name": "rating", "type": "Edm.Int32" },
	    { "name": "tags", "type": "Collection(Edm.String)" },
	    { "name": "price", "type": "Edm.Double", "filterable": false },
	    { "name": "margin", "type": "Edm.Int32", "retrievable": false },
	    { "name": "inventory", "type": "Edm.Int32" },
	    { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
	  ],
      "scoringProfiles": [
        {
	      "name": "boostGenre",
          "text": {
            "weights": {
              "albumTitle": 1,
              "genre": 5 ,
              "artistName": 2
            }
          }
	    },
        {
	      "name": "newAndHighlyRated",
          "functions": [
            {
              "type": "freshness",
              "fieldName": "lastUpdated",
              "boost": 10,
              "interpolation": "quadratic",
              "freshness": {
                "boostingDuration": "P365D"
              }
            },
            {
              "type": "magnitude",
              "fieldName": "rating",
              "boost": 10,
              "interpolation": "linear",
              "magnitude": {
                "boostingRangeStart": 1,
                "boostingRangeEnd": 5,
                "constantBoostBeyondRange": false
              }
            }
          ]
        }
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["albumTitle", "artistName"]
        }
      ]
    }


##Flusso di lavoro

Per implementare un comportamento predefinito per l'assegnazione di punteggio, aggiungere un profilo di punteggio allo schema che definisce l'indice. Un indice può includere più profili di punteggio, ma è possibile specificare solo un profilo alla volta in una determinata query.

Iniziare con il profilo [Modello](#bkmk_template) fornito in questo argomento.

Specificare un nome. I profili di punteggio sono facoltativi, ma quando se ne aggiunge uno è necessario specificare un nome. Assicurarsi di rispettare le convenzioni di denominazione per i campi (deve iniziare con una lettera ed evitare caratteri speciali e parole riservate). Per altre informazioni, vedere [Regole di denominazione](http://msdn.microsoft.com/library/azure/dn857353.aspx).

Il corpo del profilo di punteggio è costituito da campi ponderati e funzioni.

<table>
<thead>
<tr><td><b>Elemento</b></td><td><b>Descrizione</b></td></tr></thead>
  <tbody>
    <tr>
      <td>
        <b>Weights</b>
      </td>
      <td>
        Specificare coppie nome-valore che assegnano un peso relativo a un campo. In [Esempio](#example), ai campi albumTitle, genre e artistName viene applicata una priorità pari rispettivamente a 1, 5 e Null. Al campo genre viene assegnata una priorità molto più alta rispetto agli altri, poiché se la ricerca viene eseguita su dati abbastanza omogenei (come nel caso di 'genre' in `musicstoreindex`), potrebbe essere necessaria una varianza maggiore nei pesi relativi. Ad esempio, in `musicstoreindex`, 'rock' viene visualizzato sia come genere che nelle descrizioni di genere che usano lo stesso termine. Se si vuole assegnare una priorità maggiore al genere rispetto alla descrizione del genere, il campo genre dovrà avere un peso relativo decisamente maggiore.
      </td>
    </tr>
    <tr>
      <td>
        <b>Funzioni</b>
      </td>
      <td>
        Usate quando sono necessari calcoli aggiuntivi per contesti specifici. I valori validi sono `freshness`, `magnitude`, `distance` e `tag`. Ogni funzione dispone di parametri univoci.
        <p>
          È consigliabile usare `freshness`quando si desidera aumentare la priorità in base alla data di creazione più o meno recente di un elemento. Questa funzione può essere usata solo con i campi datetime (Edm.DataTimeOffset). L’attributo 'boostingDuration' viene utilizzato solo con la funzione freshness.
          </p><p>
            È consigliabile usare `magnitude`quando si desidera aumentare la priorità in base alla grandezza di un valore numerico. Gli scenari che richiedono questa funzione includono l'aumento della priorità in base a margine di profitto, prezzo massimo, prezzo minimo o conteggio di download. Questa funzione può essere usata solo con i campi di tipo Integer e Double.
          </p><p>
            Per la funzione `magnitude`, è possibile invertire l'intervallo, dal più alto al più basso, se si vuole il modello inverso. Ad esempio, per aumentare la priorità degli articoli più economici piuttosto che di quelli più cari. Dato un intervallo di prezzi da € 100 a € 1, è necessario impostare 'boostingRangeStart' su 100 e 'boostingRangeEnd' su 1 per aumentare la priorità degli articoli più economici.
          </p><p>
            È consigliabile usare `distance` quando si desidera aumentare la priorità in base alla prossimità o alla posizione geografica. Questa funzione può essere usata solo con i campi `Edm.GeographyPoint`.
          </p><p>
            È consigliabile usare `tag` quando si vuole aumentare la priorità in base ai tag in comune tra documenti e query di ricerca. Questa funzione può essere usata solo con campi `Edm.String` e `(Collection(Edm.String).
          </p>
             <p><b>Regole per l'uso delle funzioni</b>
			</p>
            Il tipo di funzione (freshness, magnitude, distance, tag) deve essere scritto in lettere minuscole.
            <br/>
            Le funzioni non possono includere valori Null o vuoti. In particolare, se si include il nome campo, sarà necessario impostare un valore.
            <br/>
             Le funzioni possono essere applicate solo ai campi filtrabili. Per altre informazioni sui campi filtrabili, vedere [Creare un indice](search-api-2015-02-28/#createindex).
             <br/>
             Le funzioni possono essere applicate solo a campi definiti nella raccolta di campi di un indice.
         </td>
</tr>
  </tbody>
</table>

Dopo la definizione dell'indice, compilarlo caricando lo schema dell'indice, seguito dai documenti. Per istruzioni relative a queste operazioni, vedere [Creare un indice](search-api-2015-02-28-preview/#createindex) e [Aggiungere o aggiornare documenti](search-api-2015-02-28-preview/#AddOrUpdateDocuments). Dopo la compilazione, dovrebbe essere disponibile un profilo di punteggio funzionale utilizzabile con i dati di ricerca.

<a name="bkmk_template"></a>
##Modello
Questa sezione illustra la sintassi e il modello per i profili di punteggio. Per descrizioni degli attributi, vedere [Riferimento agli attributi dell'indice](#bkmk_indexref) nella sezione seguente.

    ...
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
            }

            // (- or -)

            "freshness": {
              "boostingDuration": "..." (value representing timespan over which boosting occurs)
            }

            // (- or -)

            "distance": {
              "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
              "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
            }

            // (- or -)

            "tag": {
              "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field)
            }
          }
        ],
        "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
      }
    ],
    "defaultScoringProfile": (optional) "...",
    ...

<a name="bkmk_indexref"></a>
##Riferimento agli attributi dell'indice

**Nota** Una funzione di assegnazione di punteggio può essere applicata solo a campi filtrabili.

<table border="1">
<tr>
<th>Attributo</th>
<th>Descrizione</th>
</tr>
<tr>
<td>Name</td>	<td>Obbligatorio. Nome del profilo di punteggio. Segue le stesse convenzioni di denominazione di un campo. Deve iniziare con una lettera, non può contenere punti, punti e virgole o simboli @ e non può iniziare con la frase 'azureSearch' (distinzione tra maiuscole e minuscole applicata). </td>
</tr><tr>
<td>Text</td>	<td>Contiene la proprietà Weights.</td>
</tr><tr>
<td>Weights</td>	<td>Facoltativo. Coppia nome-valore che specifica un nome campo e il peso relativo. Il peso relativo deve essere un numero intero positivo. Il valore massimo è int32.MaxValue. È possibile specificare il nome campo senza un peso corrispondente. I pesi vengono usati per indicare l'importanza di un campo rispetto a un altro.</td>
<tr>
<td>Functions</td>	<td>Facoltativo. Si noti che la funzione di assegnazione di punteggio può essere applicata solo a campi filtrabili.</td>
</tr><tr>
<td>Tipo</td>	<td>Obbligatorio per le funzioni di assegnazione di punteggio. Indica il tipo di funzione da usare. I valori validi includono magnitude, freshness, distance e tag. È possibile includere più funzioni in ogni profilo di punteggio. Il nome della funzione deve essere scritto in lettere minuscole.</td>
</tr><tr>
<td>Boost</td>	<td>Obbligatorio per le funzioni di assegnazione di punteggio. Numero positivo usato come moltiplicatore per un punteggio non elaborato. Non può essere uguale a 1.</td>
</tr><tr>
<td>Fieldname</td>	<td>Obbligatorio per le funzioni di assegnazione di punteggio. Una funzione di assegnazione di punteggio può essere applicata solo a campi che fanno parte della raccolta di campi dell'indice e che sono filtrabili. Ogni tipo di funzione introduce inoltre restrizioni aggiuntive (il valore freshness viene usato con campi datetime, il valore magnitude con campi di tipo Integer o Double, il valore distance con campi location e il valore tag con campi string o string collection). È possibile specificare solo un campo per ogni definizione di funzione. Ad esempio, per usare il valore magnitude due volte nello stesso profilo, sarà necessario includere due definizioni di magnitude, una per ogni campo.</td>
</tr><tr>
<td>Interpolation</td>	<td>Obbligatorio per le funzioni di assegnazione di punteggio. Definisce il coefficiente angolare in base al quale viene incrementato l'aumento di priorità del punteggio dall'inizio alla fine dell'intervallo. I valori validi includono Linear (predefinito), Constant, Quadratic e Logarithmic. Per informazioni dettagliate, vedere [Impostare le interpolazioni](#bkmk_interpolation).</td>
</tr><tr>
<td>magnitude</td>	<td>La funzione di assegnazione di punteggio in base al valore magnitude viene usata per modificare le classificazioni in base all'intervallo di valori per un campo numerico. Alcuni degli esempi di utilizzo più comuni sono i seguenti:
<br>
Classificazioni basate su stelle: il punteggio viene modificato in base al valore disponibile nel campo "Star Rating". Quando due elementi sono rilevanti, verrà visualizzato per primo l'elemento con la classificazione superiore.
<br>
- Margine: quando due documenti sono rilevanti, è possibile che un rivenditore voglia aumentare la priorità dei documenti che presentano il margine più alto.
<br>
- Conteggio di clic: per le applicazioni che tengono traccia delle azioni di tipo clickthrough a prodotti o pagine, è possibile usare il valore magnitude per aumentare la priorità degli elementi che tendono a ottenere il traffico maggiore.
<br>
- Conteggio di download: per le applicazioni che tengono traccia dei download, la funzione magnitude permette di aumentare la priorità degli elementi più scaricati.
<tr>
<td>magnitude | boostingRangeStart</td>	<td>Imposta il valore iniziale dell'intervallo su cui è basato il calcolo della funzione magnitude. Il valore deve essere di tipo Integer o Double. Per le classificazioni a stelle da 1 a 4, corrisponde a 1. Per i margini superiori al 50%, corrisponde a 50.</td>
</tr><tr>
<td>magnitude | boostingRangeEnd</td>	<td>Imposta il valore finale dell'intervallo su cui è basato il calcolo della funzione magnitude. Il valore deve essere di tipo Integer o Double. Per le classificazioni a stelle da 1 a 4, corrisponde a 4.</td>
</tr><tr>
<td>magnitude | constantBoostBeyondRange</td>	<td>I valori validi sono true o false (predefinito). Se impostato su true, l'aumento completo della priorità continuerà a essere applicato a documenti che includono un valore per il campo di destinazione maggiore rispetto al limite superiore dell'intervallo. Se false, l'aumento di priorità di questa funzione non verrà applicato ai documenti che includono un valore per il campo di destinazione che non rientra nell'intervallo.</td>
</tr><tr>
<td>freshness</td>	<td>La funzione freshness per l'assegnazione di punteggio viene usata per modificare i punteggi di classificazione per gli elementi in base ai valori dei campi DateTimeOffset fields. Ad esempio, un elemento con una data più recente può essere classificato con una priorità maggiore rispetto agli elementi meno recenti. (Si noti che è anche possibile classificare elementi come eventi di calendario con date future in modo che gli elementi più vicini possano essere classificate in un livello superiore rispetto agli elementi più lontani nel futuro.) Nella versione attuale del servizio un'estremità dell'intervallo sarà fissata all'ora attuale. L'altra estremità è il momento al passato in base a 'boostingDuration'. Per potenziare un intervallo di tempo nel futuro utilizzare un valore negativo di 'boostingDuration'. La frequenza della modifica dell'aumento di priorità da un intervallo massimo e un intervallo minimo viene determinata dall'interpolazione applicata al profilo di punteggio (vedere la figura seguente). Per invertire il fattore di aumento di priorità applicato, scegliere un fattore di aumento di priorità inferiore a 1.</td>
</tr><tr>
<td>freshness | boostingDuration</td>	<td>Imposta un periodo di scadenza, dopo il quale l'aumento di priorità non verrà più applicato a un determinato documento. Per informazioni sulla sintassi ed esempi, vedere [Impostare boostingDuration][#bkmk_boostdur] nella sezione seguente.</td>
</tr><tr>
<td>distance</td>	<td>La funzione distance per l'assegnazione di punteggio viene usata per influire sul punteggio di documenti in base alla vicinanza o lontananza rispetto a una posizione geografica di riferimento. La posizione di riferimento viene fornita come parte della query in un parametro, usando l'opzione di stringa 'scoringParameterquery' sotto forma di argomento lon,lat.</td>
</tr><tr>
<td>distance | referencePointParameter</td>	<td>Parametro da passare nelle query e da usare come posizione di riferimento. scoringParameter è un parametro di query. Per descrizioni dei parametri di query, vedere [Eseguire ricerche nei documenti](search-api-2015-02-28-preview/#SearchDocs).</td>
</tr><tr>
<td>distance | boostingDistance</td>	<td>Numero che indica la distanza, in chilometri, dalla posizione di riferimento in cui termina l'intervallo di aumento della priorità.</td>
</tr><tr>
<td>tag</td>	<td>La funzione per l'assegnazione di punteggio viene usata per influire sul punteggio di documenti in base ai tag nei documenti e nelle query di ricerca. La priorità di documenti con tag in comune con la query di ricerca verrà aumentata. I tag per la query di ricerca vengono specificati come parametri di assegnazione dei punteggi in ciascuna richiesta di ricerca (tramite l'opzione di stringa 'scoringParameterquery').</td>
</tr><tr>
<td>tag | tagsParameter</td>	<td>Parametro da passare nelle query per specificare i tag per una particolare richiesta. scoringParameter è un parametro di query. Per descrizioni dei parametri di query, vedere [Eseguire ricerche nei documenti](search-api-2015-02-28-preview/#SearchDocs).</td>
</tr><tr>
<td>functionAggregation</td>	<td>Facoltativo. Applicabile solo se vengono specificate funzioni. I valori validi includono: sum (default), average, minimum, maximum e firstMatching. Un punteggio di ricerca è un singolo valore calcolato da più variabili, incluse le funzioni multiple. Questo attributo indica il modo in cui gli aumenti di priorità di tutte le funzioni vengono combinati in un singolo aumento aggregato della priorità, che viene quindi applicato al punteggio di base del documento. Il punteggio di base è basato sul valore tf-idf calcolato dal documento e dalla query di ricerca.</td>
</tr><tr>
<td>defaultScoringProfile</td>	<td>Quando si esegue una richiesta di ricerca, se non viene specificato alcun profilo di punteggio, verrà usato il punteggio predefinito (solo tf-idf). È possibile impostare qui un nome di profilo di punteggio predefinito, in modo che Ricerca di Azure usi tale profilo quando nella richiesta di ricerca non viene specificato alcun profilo. </td>
</tr>
</table>

<a name="bkmk_interpolation"></a>
##Impostare le interpolazioni

Le interpolazioni permettono di definire il coefficiente angolare in base al quale viene incrementato l'aumento di priorità del punteggio dall'inizio alla fine dell'intervallo. È possibile usare le interpolazioni seguenti:

- `Linear` Per gli elementi inclusi nell'intervallo max e min, l'aumento di priorità applicato all'elemento corrisponderà a un importo costantemente decrescente. L'interpolazione predefinita per un profilo di punteggio è lineare.

- `Constant` Per gli elementi inclusi nell'intervallo di inizio e di fine, un aumento di priorità costante verrà applicato ai risultati della classificazione.

- `Quadratic` Rispetto all'interpolazione lineare, che prevede un aumento di priorità costantemente decrescente, l'interpolazione quadratica presenterà una diminuzione iniziale a un ritmo più ridotto e una diminuzione con un intervallo molto più elevato quando si avvicina all'intervallo finale. Questa opzione di interpolazione non è consentita nelle funzioni di assegnazione di punteggio in base a tag.

- `Logarithmic` Rispetto all'interpolazione lineare che presenta un aumento di priorità costantemente decrescente, l'interpolazione logaritmica diminuirà inizialmente a un ritmo più elevato e diminuirà con un intervallo molto più ridotto quando si avvicina all'intervallo finale. Questa opzione di interpolazione non è consentita nelle funzioni di assegnazione di punteggio in base a tag.

<a name="Figure1"></a> ![][1]

<a name="bkmk_boostdur"></a>
##Impostare boostingDuration

`boostingDuration` è un attributo della funzione freshness. Consente di impostare un periodo di scadenza, dopo il quale l'aumento di priorità non verrà più applicato a un determinato documento. Ad esempio, per aumentare la priorità di una linea di prodotti o una marca per un periodo promozionale di 10 giorni, è necessario specificare tale periodo come "P10D" per questi documenti. O per migliorare gli eventi previsti per la settimana successiva specificare "-P7D".

Il valore `boostingDuration` deve essere formattato come valore XSD "dayTimeDuration" (un subset limitato di un valore di durata ISO 8601). Il modello è: "P[nD][T[nH][nM][nS]]".

La tabella seguente fornisce alcuni esempi.

<table>
<thead>
<tr>
<td><b>Durata</b></td> <td><b>boostingDuration</b></td>
</tr>
</thead>
<tbody>
<tr>
<td>1 giorno</td>	<td>"P1D"</td>
</tr><tr>
<td>2 giorni e 12 ore</td>	<td>"P2DT12H"</td>
</tr><tr>
<td>15 minuti</td>	<td>"PT15M"</td>
</tr><tr>
<td>30 giorni, 5 ore, 10 minuti e 6334 secondi</td>	<td>"P30DT5H10M6.334S"</td>
</tr>
</tbody>
</table>

Per altri esempi, vedere il sito Web relativo ai [tipi di dati dello schema XML (W3.org)](http://www.w3.org/TR/xmlschema11-2/).

**Vedere anche** [API REST di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn798935.aspx) su MSDN <br/> [Creare un indice (API di Ricerca di Azure)](http://msdn.microsoft.com/library/azure/dn798941.aspx) su MSDN<br/> [Aggiungere un profilo di punteggio a un indice di ricerca](http://msdn.microsoft.com/library/azure/dn798928.aspx) su MSDN<br/>

<!--Image references-->
[1]: ./media/search-api-scoring-profiles-2015-02-28-Preview/scoring_interpolations.png

<!---HONumber=Oct15_HO3-->