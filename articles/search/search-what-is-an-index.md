---
title: Creare un indice di ricerca
titleSuffix: Azure Cognitive Search
description: Introduce concetti e strumenti di indicizzazione in ricerca cognitiva di Azure, incluse le definizioni dello schema e la struttura dei dati fisici.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: 3aa4a1917711f8997c282ba577c33e7a7f94472b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88932883"
---
# <a name="create-a-basic-search-index-in-azure-cognitive-search"></a>Creare un indice di ricerca di base in Azure ricerca cognitiva

In ricerca cognitiva di Azure, un *indice di ricerca* archivia contenuto ricercabile usato per le query full-text e filtrate. Un indice viene definito da uno schema e salvato nel servizio, con l'importazione dei dati successiva come secondo passaggio. 

Gli indici contengono *documenti*. A livello concettuale, un documento è un singola unità di dati ricercabili nell'indice. Un rivenditore potrebbe avere un documento per ogni prodotto, un'organizzazione di notizie potrebbe avere un documento per ogni articolo e così via. Mapping di questi concetti a più familiari equivalenti di database: un *indice di ricerca* equivale a una *tabella*e i *documenti* sono approssimativamente equivalenti alle *righe* di una tabella.

La struttura fisica di un indice è determinata dallo schema, con campi contrassegnati come "ricercabili", ottenendo un indice invertito creato per quel campo. 

È possibile creare un indice con gli strumenti e le API seguenti:

* Nella portale di Azure usare **Aggiungi indice** o importazione guidata **dati**
* Uso di [create index (API REST)](/rest/api/searchservice/create-index)
* Uso di [.NET SDK](./search-get-started-dotnet.md)

È più facile da imparare con uno strumento del portale. Il portale applica i requisiti e le regole dello schema per tipi di dati specifici, ad esempio la disabilitazione delle funzionalità di ricerca full-text nei campi numerici. Quando si dispone di un indice praticabile, è possibile passare al codice recuperando la definizione JSON dal servizio usando [Get index (API REST)](/rest/api/searchservice/get-index) e aggiungendola alla soluzione.

## <a name="recommended-workflow"></a>Flusso di lavoro consigliato

L'arrivo a un progetto di indice finale è un processo iterativo. È comune iniziare con il portale per creare l'indice iniziale e quindi passare al codice per inserire l'indice nel controllo del codice sorgente.

1. Determinare se è possibile utilizzare l' [**importazione dati**](search-import-data-portal.md). La procedura guidata esegue l'indicizzazione basata su indicizzatori all-in-One se i dati di origine sono da un [tipo di origine dati supportato in Azure](search-indexer-overview.md#supported-data-sources).

1. Se non è possibile utilizzare **Importa dati**, iniziare con **Aggiungi indice** per definire lo schema.

   ![Comando Aggiungi indice](media/search-what-is-an-index/add-index.png "Comando Aggiungi indice")

1. Specificare un nome e una chiave usati per identificare in modo univoco ogni documento di ricerca nell'indice. La chiave è obbligatoria e deve essere di tipo EDM. String. Durante l'importazione, è consigliabile pianificare il mapping di un campo univoco nei dati di origine a questo campo. 

   Il portale fornisce un `id` campo per la chiave. Per eseguire l'override dell'impostazione predefinita `id` , creare un nuovo campo, ad esempio una nuova definizione di campo denominata, `HotelId` quindi selezionarlo nella **chiave**.

   ![Specificare le proprietà obbligatorie](media/search-what-is-an-index//field-attributes.png "Specificare le proprietà obbligatorie")

1. Aggiungere altri campi. Il portale Mostra [gli attributi di campo](#index-attributes) disponibili per i diversi tipi di dati. Ciò è utile se non si ha familiarità con la progettazione di indici.

   Se i dati in ingresso sono di natura gerarchica, assegnare il tipo di dati del [tipo complesso](search-howto-complex-data-types.md) per rappresentare le strutture nidificate. Il set di dati di esempio predefinito, Hotels, illustra i tipi complessi usando un indirizzo (contiene più sottocampi) che presenta una relazione uno-a-uno con ogni hotel e una raccolta complessa di chat, in cui più stanze sono associate a ogni hotel. 

1. Assegnare tutti gli [analizzatori](#analyzers) ai campi stringa prima della creazione dell'indice. Eseguire la stessa operazione per i [suggerimenti](#suggesters) se si vuole abilitare il completamento automatico in campi specifici.

1. Fare clic su **Crea** per compilare le strutture fisiche nel servizio di ricerca.

1. Dopo la creazione di un indice, utilizzare comandi aggiuntivi per esaminare le definizioni o aggiungere altri elementi.

   ![Pagina Aggiungi indice con attributi per tipo di dati](media/search-what-is-an-index//field-definitions.png "Pagina Aggiungi indice con attributi per tipo di dati")

1. Scaricare lo schema dell'indice usando [Get index (API REST)](/rest/api/searchservice/get-index) e uno strumento di test Web come il [post](search-get-started-postman.md). Si dispone ora di una rappresentazione JSON dell'indice che è possibile adattare per il codice.

1. [Caricare l'indice con i dati](search-what-is-data-import.md). Azure ricerca cognitiva accetta documenti JSON. Per caricare i dati a livello di codice, è possibile usare Postman con i documenti JSON nel payload della richiesta. Se i dati non possono essere facilmente espressi in JSON, questo passaggio sarà più laborioso. 

    Una volta caricato un indice con i dati, la maggior parte delle modifiche apportate ai campi esistenti richiede l'eliminazione e la ricompilazione di un indice.

1. Eseguire query sull'indice, esaminare i risultati ed eseguire ulteriormente l'iterazione sullo schema dell'indice fino a quando non si visualizzano i risultati previsti. È possibile usare [**Esplora ricerche**](search-explorer.md) o Postman per eseguire query sull'indice.

Durante lo sviluppo, pianificare le frequenti ricompilazioni. Poiché le strutture fisiche vengono create nel servizio, è necessario [eliminare e ricreare gli indici](search-howto-reindex.md) per la maggior parte delle modifiche apportate a una definizione di campo esistente. È possibile prendere in considerazione l'uso di un subset di dati per velocizzare le ricompilazioni. 

> [!Tip]
> Il codice, anziché un approccio basato su un portale, è consigliato per lavorare simultaneamente sulla progettazione di indici e sull'importazione di dati. In alternativa, strumenti come [Postman e API REST](search-get-started-postman.md) sono utili per eseguire test dei modelli di verifica quando i progetti di sviluppo sono ancora in fase iniziale. È possibile apportare modifiche incrementali a una definizione di indice nel corpo della richiesta e quindi inviare la richiesta al servizio per ricreare un indice usando uno schema aggiornato.

## <a name="index-schema"></a>Schema dell'indice

È necessario un indice per avere un nome e un campo chiave designato (of EDM. String) nella raccolta Fields. La [*raccolta campi*](#fields-collection) in genere costituisce la maggior parte di un indice, in cui ogni campo è denominato, tipizzato e attribuito con comportamenti consentiti che determinano la modalità di utilizzo. 

Altri elementi includono [suggerimenti](#suggesters), [profili di Punteggio](#scoringprofiles), [analizzatori](#analyzers) usati per elaborare le stringhe in token in base a regole linguistiche o altre caratteristiche supportate dall'analizzatore e impostazioni di [Scripting remoto tra le origini (CORS)](#corsoptions) .

```json
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
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [
    {
      "name": "name of suggester",
      "searchMode": "analyzingInfixMatching",
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
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
          },
          "tag": {
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)
          }
        }
      ],
      "functionAggregation": (optional, applies only when functions are specified) 
        "sum (default) | average | minimum | maximum | firstMatching"
    }
  ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) {
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
  },
  "encryptionKey":(optional){
    "keyVaultUri": "azure_key_vault_uri",
    "keyVaultKeyName": "name_of_azure_key_vault_key",
    "keyVaultKeyVersion": "version_of_azure_key_vault_key",
    "accessCredentials":(optional){
      "applicationId": "azure_active_directory_application_id",
      "applicationSecret": "azure_active_directory_application_authentication_key"
    }
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>Raccolta di campi e attributi di campi

I campi hanno un nome, un tipo che classifica i dati archiviati e gli attributi che specificano come viene usato il campo.

### <a name="data-types"></a>Tipi di dati

| Type | Descrizione |
|------|-------------|
| Edm.String |Testo facoltativamente soggetto a tokenizzazione per la ricerca full-text (suddivisione delle parole, stemming e così via). |
| Collection(Edm.String) |Elenco di stringhe facoltativamente soggette a tokenizzazione per la ricerca full-text. Non esiste alcun limite superiore teorico al numero di elementi in una raccolta, ma alle raccolte si applica il limite massimo di 16 MB di dimensioni del payload. |
| Edm.Boolean |Contiene valori true/false. |
| Edm.Int32 |Valori integer a 32 bit. |
| Edm.Int64 |Valori integer a 64 bit. |
| Edm.Double |Dati numerici a precisione doppia. |
| Edm.DateTimeOffset |Valori di ora rappresentati in formato OData V4 (ad esempio `yyyy-MM-ddTHH:mm:ss.fffZ` o `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| Edm.GeographyPoint |Punto che rappresenta una località geografica del mondo. |

Per ulteriori informazioni, vedere [tipi di dati supportati](/rest/api/searchservice/Supported-data-types).

<a name="index-attributes"></a>

### <a name="attributes"></a>Attributi

Gli attributi del campo determinano le modalità in cui un campo viene usato, ad esempio se viene usato nella ricerca full-text, nella navigazione con facet, nelle operazioni di ordinamento e così via. 

I campi stringa sono spesso contrassegnati come "ricercabili" e "recuperabili". I campi utilizzati per limitare i risultati della ricerca includono "ordinabile", "filtrabile" e "facet".

|Attributo|Descrizione|  
|---------------|-----------------|  
|ricercabile |Ricercabile full-text, soggetto ad analisi lessicali, ad esempio alla scomposizione delle parole durante l'indicizzazione. Se si imposta un campo ricercabile su un valore come "sunny day", questo viene suddiviso internamente nei singoli token "sunny" e "day". Per informazioni vedere [Funzionamento della ricerca full-text](search-lucene-query-architecture.md).|  
|filtrabili |A cui si fa riferimento nelle query $filter. I campi filtrabili di tipo `Edm.String` o `Collection(Edm.String)` non sono sottoposti a suddivisione delle parole e quindi i confronti riguardano solo le corrispondenze esatte. Se ad esempio si imposta un campo su "sunny day", `$filter=f eq 'sunny'` non troverà corrispondenze, mentre `$filter=f eq 'sunny day'` ne troverà. |  
|ordinabile |Per impostazione predefinita il sistema ordina i risultati in base al punteggio, ma è possibile configurare l'ordine in base ai campi nei documenti. I campi di tipo `Collection(Edm.String)` non possono essere "ordinabili". |  
|con facet |In genere usato in una presentazione dei risultati della ricerca che include un numero di passaggi per categoria, ad esempio, gli hotel in una specifica città. Questa opzione non può essere usata con i campi di tipo `Edm.GeographyPoint`. I campi di tipo `Edm.String` filtrabile, "ordinabile" o "facet" possono avere una lunghezza massima di 32 KB. Per altri dettagli, vedere [Create Index (REST API)](/rest/api/searchservice/create-index)(Creare un indice: API REST).|  
|chiave |Identificatore univoco per i documenti all'interno dell'indice. È necessario scegliere un singolo campo come campo chiave e questo deve essere di tipo `Edm.String`.|  
|recuperabile |Specifica se il campo può essere restituito nel risultato di una ricerca. Questo attributo è utile quando si vuole usare un campo, ad esempio *margine di profitto*, come meccanismo di filtro, ordinamento o punteggio ma si preferisce che il campo non sia visibile all'utente finale. L'attributo deve essere `true` for `key` .|  

Sebbene sia possibile aggiungere nuovi campi in qualsiasi momento, le definizioni del campo esistente vengono bloccate per la durata dell'indice. Per questo motivo, gli sviluppatori in genere usano il portale per la creazione di indici semplici, idee di test o usano le pagine del portale per cercare un'impostazione. L'iterazione frequente su una progettazione degli indici è più efficiente se si segue un approccio basato sul codice in modo che sia possibile ricompilare l'indice con facilità.

> [!NOTE]
> Le API usate per compilare un indice hanno comportamenti predefiniti variabili. Per le [API REST](/rest/api/searchservice/Create-Index), la maggior parte degli attributi è abilitata per impostazione predefinita (ad esempio, "Searchable" e "Retrievable" sono true per i campi di stringa) ed è spesso necessario impostarli solo se si desidera disattivarli. Per .NET SDK, il valore opposto è true. Per le proprietà non impostate in modo esplicito, per impostazione predefinita viene disabilitato il comportamento di ricerca corrispondente, a meno che non venga abilitato in modo specifico.

## `analyzers`

L'elemento analizzatori imposta il nome dell'analizzatore di lingua da usare per il campo. Per altre informazioni sull'intervallo di analizzatori disponibili, vedere [aggiunta di analizzatori a un indice di ricerca cognitiva di Azure](search-analyzers.md). Gli analizzatori possono essere usati solo con campi ricercabili. Dopo aver assegnato l'analizzatore a un campo sarà possibile modificarlo solo ricompilando l'indice.

## `suggesters`

Un componente di suggerimento è una sezione dello schema che definisce quali campi in un indice vengono utilizzati per supportare le query con completamento automatico nelle ricerche. In genere, le stringhe di ricerca parziali vengono inviate ai [Suggerimenti (API REST)](/rest/api/searchservice/suggestions) mentre l'utente digita una query di ricerca e l'API restituisce un set di documenti o frasi suggerite. 

I campi aggiunti a uno strumento suggerimenti vengono usati per compilare i termini di ricerca con completamento automatico. Tutti i termini di ricerca vengono creati durante l'indicizzazione e archiviati separatamente. Per altre informazioni sulla creazione di una struttura per uno strumento suggerimenti, vedere [Aggiungere strumenti suggerimenti](index-add-suggesters.md).

## `corsOptions`

JavaScript sul lato client non può chiamare API per impostazione predefinita perché il browser impedisce tutte le richieste con origini diverse. Per consentire query con origini diverse nell'indice, abilitare CORS (Cross-Origin Resource Sharing) impostando l'attributo **corsOptions**. Per motivi di sicurezza, solo le API di query supportano CORS. 

Per CORS è possibile impostare le opzioni seguenti:

+ **allowedOrigins** (obbligatorio): si tratta di un elenco di origini a cui verrà concesso l'accesso all'indice. Questo significa che al codice JavaScript servito da queste origini sarà consentito eseguire query sull'indice, purché fornisca la chiave API corretta. Ogni origine è in genere nel formato `protocol://<fully-qualified-domain-name>:<port>` anche se `<port>` spesso viene omessa. Vedere [Utilizzare la condivisione di risorse tra origini (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) per altre informazioni.

  Per consentire l'accesso a tutte le origini, includere `*` come unico elemento nella matrice**allowedOrigins**. *Non si consiglia questa pratica per i servizi di ricerca della produzione* ma spesso è utile per lo sviluppo e il debug.

+ **maxAgeInSeconds** (facoltativo): i browser usano questo valore per determinare la durata (in secondi) di memorizzazione nella cache delle risposte preliminari CORS. Questo valore deve essere un intero non negativo. A un valore più grande corrispondono prestazioni migliori, ma deve trascorrere più tempo prima che le modifiche dei criteri CORS diventino effettive. Se questo valore non è impostato, viene usata una durata predefinita di 5 minuti.

## `scoringProfiles`

Un [profilo di Punteggio](index-add-scoring-profiles.md) è una sezione dello schema che definisce i comportamenti di Punteggio personalizzati che consentono di influenzare gli elementi che vengono visualizzati più in alto nei risultati della ricerca. I profili di punteggio sono costituiti da funzioni e campi ponderati. Per utilizzarli, è necessario specificare il nome di un profilo nella stringa di query.

Un profilo di punteggio predefinito viene eseguito in background al fine di calcolare un punteggio di ricerca per ogni elemento visualizzato in un set di risultati. È possibile utilizzare un profilo di punteggio interno, senza nome. In alternativa, impostare **defaultScoringProfile** per usare un profilo personalizzato come predefinito, richiamato ogni volta che nella stringa di query non viene specificato un profilo personalizzato.

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>Attributi e dimensioni degli indici (implicazioni dell'archiviazione)

Le dimensioni di un indice sono determinate dalla dimensione dei documenti caricati, oltre alla configurazione dell'indice, ad esempio se si includono i suggerimenti e come si impostano gli attributi nei singoli campi. 

Lo screenshot seguente illustra i modelli di archiviazione dell'indice derivanti da diverse combinazioni di attributi. L'indice è basato sull' **indice di esempio Real Immobiliare**, che è possibile creare facilmente tramite la procedura guidata Importa dati. Anche se non vengono visualizzati gli schemi dell'indice, è possibile dedurre gli attributi in base al nome dell'indice. Ad esempio, è stato selezionato l'attributo "ricercabile" nell'indice di *ricerca* per *indicizzazione* e non è possibile selezionare l'attributo "recuperabile" con l'attributo "recuperabile" e così via.

![Dimensioni dell'indice basate sulla selezione degli attributi](./media/search-what-is-an-index/realestate-index-size.png "Dimensioni dell'indice basate sulla selezione degli attributi")

Sebbene queste varianti di indice siano artificiali, è possibile farvi riferimento per una considerazione generale sul modo in cui gli attributi influiscono sull'archiviazione. L'impostazione "recuperabile" aumenta le dimensioni dell'indice? No. L'aggiunta di campi a un **Suggerimento** aumenta le dimensioni dell'indice? Sì.

Gli indici che supportano filtro e ordinamento sono proporzionalmente più grandi rispetto agli indici che supportano solo la ricerca full-text. Ciò è dovuto al fatto che le operazioni di filtro e ordinamento analizzano le corrispondenze esatte, richiedendo la presenza di stringhe di testo Verbatim. Al contrario, i campi ricercabili che supportano le query full-text utilizzano indici invertiti, che vengono popolati con termini in formato token che utilizzano meno spazio rispetto ai documenti interi. 

> [!Note]
> L'architettura di archiviazione è considerata un dettaglio di implementazione di Azure ricerca cognitiva e può cambiare senza preavviso. Non è garantito che il comportamento attuale verrà mantenuto in futuro.

## <a name="next-steps"></a>Passaggi successivi

Conoscendo la composizione dell'indice, è possibile continuare nel portale per creare il primo indice. Si consiglia di iniziare con l' **importazione guidata dati** , scegliendo le origini dati ospitate da *immobiliare-US-sample* o *Hotels-Sample* .

> [!div class="nextstepaction"]
> [Importazione guidata dati (portale)](search-get-started-portal.md)

Per entrambi i set di dati, la procedura guidata può dedurre uno schema di indice, importare i dati e restituire un indice ricercabile su cui è possibile eseguire query tramite Esplora ricerche. Trovare queste origini dati nella pagina **connessione ai dati** dell' **importazione guidata dei dati** .

   ![Creare un indice di esempio](media/search-what-is-an-index//import-wizard-sample-data.png "Creare un indice di esempio")