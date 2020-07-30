---
title: 'Esercitazione: Creare un analizzatore personalizzato'
titleSuffix: Azure Cognitive Search
description: Informazioni su come creare un analizzatore personalizzato per migliorare la qualità dei risultati della ricerca in Ricerca cognitiva di Azure.
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/22/2020
ms.openlocfilehash: a9c2a5beae8a9206554dd6c432c1d8442b652696
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87021886"
---
# <a name="tutorial-create-a-custom-analyzer-for-phone-numbers"></a>Esercitazione: Creare un analizzatore personalizzato per i numeri di telefono

Gli [analizzatori](search-analyzers.md) sono un componente chiave delle soluzioni di ricerca. Per migliorare la qualità dei risultati della ricerca, è importante comprendere come funzionano gli analizzatori e come influiscono su questi risultati.

In alcuni casi, ad esempio con un campo di testo libero, è sufficiente selezionare l'[analizzatore del linguaggio](index-add-language-analyzers.md) corretto per migliorare i risultati della ricerca. Alcuni scenari invece, come la ricerca accurata di numeri di telefono, URL o indirizzi di posta elettronica, possono richiedere l'uso di analizzatori personalizzati.

Questa esercitazione usa Postman e le [API REST](https://docs.microsoft.com/rest/api/searchservice/) di Ricerca cognitiva di Azure per:

> [!div class="checklist"]
> * Spiegare il funzionamento degli analizzatori
> * Definire un analizzatore personalizzato per la ricerca di numeri di telefono
> * Testare il modo in cui l'analizzatore personalizzato suddivide il testo in token
> * Creare analizzatori distinti per l'indicizzazione e la ricerca per migliorare ulteriormente i risultati

## <a name="prerequisites"></a>Prerequisiti

Per questa esercitazione sono necessari i servizi e gli strumenti seguenti.

+ [App desktop Postman](https://www.getpostman.com/)
+ [Creare](search-create-service-portal.md) o [trovare un servizio di ricerca esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)

## <a name="download-files"></a>Scaricare i file

Il codice sorgente per questa esercitazione è disponibile nella cartella [custom-analyzers](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) all'interno del repository GitHub [Azure-Samples/azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples).

## <a name="1---create-azure-cognitive-search-service"></a>1 - Creare un servizio di Ricerca cognitiva di Azure

Per completare questa esercitazione sarà necessario un servizio di Ricerca cognitiva di Azure, che è possibile [creare nel portale](search-create-service-portal.md). Per completare questa procedura dettagliata, è possibile usare il livello gratuito.

Per il passaggio successivo sarà necessario conoscere il nome del servizio di ricerca e della relativa chiave API. Se non si sa come trovare questi elementi, vedere questo [argomento di avvio rapido](search-create-service-portal.md#get-a-key-and-url-endpoint).


## <a name="2---set-up-postman"></a>2 - Configurare Postman

Avviare Postman e importare la raccolta scaricata da [Azure-Samples/azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples).

Per importare la raccolta, passare a **Files** > **Import** (File > Importa), quindi selezionare il file della raccolta da importare.

Per ogni richiesta è necessario:

1. Sostituire `<YOUR-SEARCH-SERVICE>` con il nome del servizio di ricerca.

1. Sostituire `<YOUR-ADMIN-API-KEY>` con la chiave primaria o secondaria del servizio di ricerca.

  ![Intestazione e URL della richiesta Postman](media/search-get-started-postman/postman-url.png "Intestazione e URL della richiesta Postman")

Se non si ha familiarità con Postman, vedere [Esplorare le API REST di Ricerca cognitiva di Azure con Postman](search-get-started-postman.md).

## <a name="3---create-an-initial-index"></a>3 - Creare un indice iniziale

In questo passaggio si creerà un indice iniziale, si caricheranno i documenti nell'indice e quindi si eseguirà una query sui documenti per verificare le prestazioni delle ricerche iniziali.

### <a name="create-index"></a>Creare un indice

Per iniziare si creerà un indice semplice denominato `tutorial-basic-index` con due campi: `id` e `phone_number`. Non è ancora stato definito un analizzatore, quindi si userà l'analizzatore `standard.lucene` per impostazione predefinita.

Per creare l'indice, si invia la richiesta seguente:

```http
PUT https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "fields": [
      {
        "name": "id",
        "type": "Edm.String",
        "key": true,
        "searchable": true,
        "filterable": false,
        "facetable": false,
        "sortable": true
      },
      {
        "name": "phone_number",
        "type": "Edm.String",
        "sortable": false,
        "searchable": true,
        "filterable": false,
        "facetable": false
      }
    ]
  }
```

### <a name="load-data"></a>Caricare dati

A questo punto occorre caricare i dati nell'indice. In alcuni casi si potrebbe non avere il controllo sul formato dei numeri di telefono inseriti, quindi si eseguirà il test su diversi tipi di formati. Idealmente una soluzione di ricerca dovrebbe restituire tutti i numeri di telefono corrispondenti indipendentemente dal formato.

I dati vengono caricati nell'indice usando la richiesta seguente: 

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs/index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "value": [
      {
        "@search.action": "upload",  
        "id": "1",
        "phone_number": "425-555-0100"
      },
      {
        "@search.action": "upload",  
        "id": "2",
        "phone_number": "(321) 555-0199"
      },
      {  
        "@search.action": "upload",  
        "id": "3",
        "phone_number": "+1 425-555-0100"
      },
      {  
        "@search.action": "upload",  
        "id": "4",  
        "phone_number": "+1 (321) 555-0199"
      },
      {
        "@search.action": "upload",  
        "id": "5",
        "phone_number": "4255550100"
      },
      {
        "@search.action": "upload",  
        "id": "6",
        "phone_number": "13215550199"
      },
      {
        "@search.action": "upload",  
        "id": "7",
        "phone_number": "425 555 0100"
      },
      {
        "@search.action": "upload",  
        "id": "8",
        "phone_number": "321.555.0199"
      }
    ]  
  }
```

Una volta che i dati sono nell'indice, è possibile iniziare la ricerca.

### <a name="search"></a>Ricerca

Per rendere intuitiva la ricerca, è opportuno non aspettarsi che gli utenti formattino le query in un modo specifico. Un utente potrebbe cercare `(425) 555-0100` in uno qualsiasi dei formati mostrati sopra e aspettarsi comunque che i risultati vengano restituiti. In questo passaggio si testeranno un paio di query di esempio per verificarne le prestazioni.

Per iniziare si cercherà `(425) 555-0100`:

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=(425) 555-0100
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  
```

Questa query restituisce **tre dei quattro risultati previsti**, ma restituisce anche **due risultati imprevisti**:

```json
{
    "value": [
        {
            "@search.score": 0.05634898,
            "phone_number": "+1 425-555-0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425 555 0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425-555-0100"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "(321) 555-0199"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "+1 (321) 555-0199"
        }
    ]
}
```

Si cercherà quindi un numero senza formattazione: `4255550100`

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=4255550100
  api-key: <YOUR-ADMIN-API-KEY>
```

Le prestazioni di questa query sono ancora peggiori, dato che restituisce solo **una delle quattro corrispondenze corrette**.

```json
{
    "value": [
        {
            "@search.score": 0.6015292,
            "phone_number": "4255550100"
        }
    ]
}
```

Questi risultati possono creare confusione. Nella prossima sezione si esamineranno i motivi per cui si ottengono questi risultati.

## <a name="4---debug-search-results"></a>4 - Eseguire il debug dei risultati della ricerca

Per comprendere i risultati della ricerca, è importante comprendere prima di tutto il funzionamento degli analizzatori. Sarà quindi possibile testare l'analizzatore predefinito usando l'[API di analisi del testo](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) e creare un analizzatore che soddisfi le specifiche esigenze.

### <a name="how-analyzers-work"></a>Funzionamento degli analizzatori

Un [analizzatore](search-analyzers.md) è un componente del [motore di ricerca full-text](search-lucene-query-architecture.md) responsabile dell'elaborazione di testo in stringhe di query e documenti indicizzati. Analizzatori diversi modificano il testo in modi diversi a seconda dello scenario. Per questo scenario è necessario creare un analizzatore mirato per i numeri di telefono.

Gli analizzatori sono costituiti da tre componenti:

+ I [**filtri di caratteri**](#CharFilters) che consentono di rimuovere o sostituire singoli caratteri dal testo di input.
+ Un [**tokenizer**](#Tokenizers) che suddivide il testo di input in token, che diventano chiavi nell'indice di ricerca.
+ I [**filtri di token**](#TokenFilters) che modificano i token generati dal tokenizer.

Nel diagramma seguente si può vedere come questi tre componenti interagiscono tra loro per suddividere in token una frase:

  ![Diagramma del processo dell'analizzatore](media/tutorial-create-custom-analyzer/analyzers-explained.png)

Questi token vengono quindi archiviati in un indice invertito, che consente ricerche full-text rapide.  Un indice invertito abilita la ricerca full-text eseguendo il mapping di tutti i termini univoci estratti durante l'analisi lessicale ai documenti che li contengono. Il diagramma seguente ne illustra un esempio:

  ![Esempio di indice invertito](media/tutorial-create-custom-analyzer/inverted-index-explained.png)

L'intero processo di ricerca consiste nel cercare i termini archiviati nell'indice invertito. Quando un utente esegue una query:

1. La query viene analizzata, così come i termini della query.
1. Viene quindi eseguita la scansione dell'indice invertito alla ricerca di documenti contenenti termini corrispondenti.
1. Infine, i documenti recuperati vengono classificati in base all'[algoritmo di somiglianza](index-ranking-similarity.md).

  ![Diagramma del processo dell'analizzatore](media/tutorial-create-custom-analyzer/query-architecture-explained.png)

Se i termini della query non corrispondono a quelli nell'indice invertito, non vengono restituiti risultati. Per altre informazioni sul funzionamento delle query, vedere questo articolo sulla [ricerca full-text](search-lucene-query-architecture.md).

> [!Note]
> Le [query con termini parziali](search-query-partial-matching.md) sono un'importante eccezione a questa regola. Queste query (query con prefisso, query con caratteri jolly, query espressione regolare), a differenza delle query di termini normali, ignorano il processo di analisi lessicale. I termini parziali sono solo in minuscolo prima di essere confrontati con i termini nell'indice. Se un analizzatore non è configurato per supportare questi tipi di query, si riceveranno spesso risultati imprevisti perché i termini corrispondenti non esistono nell'indice.

### <a name="test-analyzer-using-the-analyze-text-api"></a>Testare l'analizzatore usando l'API di analisi del testo

Ricerca cognitiva di Azure fornisce un'[API di analisi del testo](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) che consente di testare gli analizzatori per comprendere il modo in cui elaborano il testo.

L'API viene chiamata tramite la richiesta seguente:

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
      "text": "(425) 555-0100",
      "analyzer": "standard.lucene"
  }
```

L'API restituisce quindi un elenco dei token estratti dal testo. Come si può notare, l'analizzatore Lucene standard suddivide il numero di telefono in tre token distinti:

```json
{
    "tokens": [
        {
            "token": "425",
            "startOffset": 1,
            "endOffset": 4,
            "position": 0
        },
        {
            "token": "555",
            "startOffset": 6,
            "endOffset": 9,
            "position": 1
        },
        {
            "token": "0100",
            "startOffset": 10,
            "endOffset": 14,
            "position": 2
        }
    ]
}
```

Il numero di telefono `4255550100` formattato senza punteggiatura viene invece tokenizzato in un singolo token.

```json
{
  "text": "4255550100",
  "analyzer": "standard.lucene"
}
```

```json
{
    "tokens": [
        {
            "token": "4255550100",
            "startOffset": 0,
            "endOffset": 10,
            "position": 0
        }
    ]
}
```

Tenere presente che vengono analizzati sia i termini della query che i documenti indicizzati. Ripensando ai risultati della ricerca del passaggio precedente, si può iniziare a capire perché vengono restituiti questi risultati.

Nella prima query sono stati restituiti i numeri di telefono non corretti perché uno dei termini, `555`, corrispondeva a uno dei termini cercati. Nella seconda query è stato restituito solo un numero perché era l'unico record con un termine corrispondente a `4255550100`.

## <a name="5---build-a-custom-analyzer"></a>5 - Creare un analizzatore personalizzato

Ora che i risultati restituiti non sono più un mistero, è il momento di creare un analizzatore personalizzato per migliorare la logica di tokenizzazione.

L'obiettivo è fornire una ricerca intuitiva dei numeri di telefono, indipendentemente dal formato della query o della stringa indicizzata. Per ottenere questo risultato, si specificherà un [filtro di caratteri](#CharFilters), un [tokenizer](#Tokenizers) e un [filtro di token](#TokenFilters).

<a name="CharFilters"></a>

### <a name="character-filters"></a>Filtri di caratteri

I filtri di caratteri vengono usati per elaborare il testo prima di inserirlo nel tokenizer. Gli utilizzi comuni dei filtri di caratteri includono l'esclusione di elementi HTML o la sostituzione di caratteri speciali.

Per i numeri di telefono occorre rimuovere gli spazi vuoti e i caratteri speciali perché non tutti i formati dei numeri di telefono contengono gli stessi spazi e caratteri speciali.

```json
"charFilters": [
    {
      "@odata.type": "#Microsoft.Azure.Search.MappingCharFilter",
      "name": "phone_char_mapping",
      "mappings": [
        "-=>",
        "(=>",
        ")=>",
        "+=>",
        ".=>",
        "\\u0020=>"
      ]
    }
  ]
```

Il filtro riportato sopra rimuove `-` `(` `)` `+` `.` e gli spazi dall'input.

|Input|Output|  
|-|-|  
|`(321) 555-0199`|`3215550199`|  
|`321.555.0199`|`3215550199`|

<a name="Tokenizers"></a>

### <a name="tokenizers"></a>Tokenizer

I tokenizer suddividono il testo in token eliminando al contempo alcuni caratteri, ad esempio la punteggiatura. In molti casi, l'obiettivo della tokenizzazione è suddividere una frase in singole parole.

Per questo scenario si userà un tokenizer di parole chiave, `keyword_v2`, perché si vuole acquisire il numero di telefono come singolo termine. Si noti che questo non è l'unico modo per risolvere il problema. Vedere la sezione [Approcci alternativi](#Alternate) di seguito.

I tokenizer di parole chiave generano sempre lo stesso testo fornito come singolo termine.

|Input|Output|  
|-|-|  
|`The dog swims.`|`[The dog swims.]`|  
|`3215550199`|`[3215550199]`|

<a name="TokenFilters"></a>

### <a name="token-filters"></a>Filtri di token

I filtri di token vengono usati per filtrare o modificare i token generati dal tokenizer. Vengono comunemente usati anche per convertire in minuscolo tutti i caratteri o per escludere parole non significative come `the`, `and` o `is`.

Sebbene non sia necessario usare questi filtri per questo scenario, verrà usato un filtro token nGram per consentire ricerche parziali dei numeri di telefono.

```json
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.NGramTokenFilterV2",
    "name": "custom_ngram_filter",
    "minGram": 3,
    "maxGram": 20
  }
]
```

#### <a name="ngramtokenfilterv2"></a>NGramTokenFilterV2

Il [filtro di token nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html) suddivide i token in n-grammi di una determinata dimensione in base ai parametri `minGram` e `maxGram`.

Per l'analizzatore dei numeri di telefono, `minGram` viene impostato su `3` perché è la sottostringa più corta che si prevede venga cercata dagli utenti. `maxGram` viene impostato su `20` per assicurare che tutti i numeri di telefono, anche quelli comprensivi di numero interno, rientrino in un singolo n-gramma.

 Il lato negativo degli n-grammi è che vengono restituiti anche alcuni falsi positivi. Questo problema verrà risolto nel passaggio 7 creando un analizzatore distinto per le ricerche che non includono il filtro di token di n-grammi.

|Input|Output|  
|-|-|  
|`[12345]`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`[3215550199]`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

### <a name="analyzer"></a>Analizzatore

Una volta predisposti i filtri di caratteri, il tokenizer e i filtri di token, è possibile definire l'analizzatore.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_ngram_filter"
    ],
    "charFilters": [
      "phone_char_mapping"
    ]
  }
]
```

|Input|Output|  
|-|-|  
|`12345`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`(321) 555-0199`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

Si noti che ora è possibile cercare qualsiasi token nell'output. Se la query include uno di questi token, il numero di telefono viene restituito.

Dopo aver definito l'analizzatore personalizzato, ricreare l'indice in modo che l'analizzatore personalizzato sia disponibile per il test nel passaggio successivo. Per semplicità, la raccolta di Postman crea un nuovo indice denominato `tutorial-first-analyzer` con l'analizzatore definito qui.

## <a name="6---test-the-custom-analyzer"></a>6 - Testare l'analizzatore personalizzato

Dopo aver creato l'indice, è ora possibile testare l'analizzatore creato usando la richiesta seguente:

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-first-analyzer/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  

  {
    "text": "+1 (321) 555-0199",
    "analyzer": "phone_analyzer"
  }
```

Si potrà quindi vedere la raccolta di token risultante dal numero di telefono:

```json
{
    "tokens": [
        {
            "token": "132",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "1321",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "13215",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        ...
        ...
        ...
    ]
}
```

## <a name="7---build-a-custom-analyzer-for-queries"></a>7 - Creare un analizzatore personalizzato per le query

Dopo aver eseguito alcune query di esempio sull'indice con l'analizzatore personalizzato, si noterà che il richiamo è migliorato e tutti i numeri di telefono corrispondenti vengono ora restituiti. Tuttavia, il filtro di token di n-gramma causa anche la restituzione di alcuni falsi positivi. È un effetto collaterale comune di questo tipo di filtro.

Per evitare falsi positivi, verrà creato un analizzatore distinto per l'esecuzione di query. Questo analizzatore sarà uguale all'analizzatore già creato, ma **senza** `custom_ngram_filter`.

```json
    {
      "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
      "name": "phone_analyzer_search",
      "tokenizer": "custom_tokenizer_phone",
      "tokenFilters": [],
      "charFilters": [
        "phone_char_mapping"
      ]
    }
```

Nella definizione dell'indice si specificherà quindi sia `indexAnalyzer` che `searchAnalyzer`.

```json
    {
      "name": "phone_number",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "indexAnalyzer": "phone_analyzer",
      "searchAnalyzer": "phone_analyzer_search"
    }
```

Dopo questa modifica, è tutto pronto. Ricreare l'indice, indicizzare i dati e testare di nuovo le query per verificare che la ricerca funzioni come previsto. Se si usa la raccolta di Postman, verrà creato un terzo indice denominato `tutorial-second-analyzer`.

<a name="Alternate"></a>

## <a name="alternate-approaches"></a>Approcci alternativi

L'analizzatore è stato progettato per ottimizzare la flessibilità per la ricerca. Il costo di questo risultato è però l'archiviazione di molti termini potenzialmente irrilevanti nell'indice.

L'esempio seguente illustra un analizzatore diverso che può essere ugualmente usato per questa attività. 

L'analizzatore funziona bene eccetto che per i dati di input come `14255550100`, che rendono difficile suddividere logicamente il numero di telefono. Ad esempio, l'analizzatore non sarebbe in grado di separare il prefisso internazionale, `1`, dall'indicativo di località, `425`. A causa di questa discrepanza, il numero riportato sopra non verrebbe restituito se l'utente non includesse il prefisso internazionale nella ricerca.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer_shingles",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_shingle_filter"
    ]
  }
],
"tokenizers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.StandardTokenizerV2",
    "name": "custom_tokenizer_phone",
    "maxTokenLength": 4
  }
],
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.ShingleTokenFilter",
    "name": "custom_shingle_filter",
    "minShingleSize": 2,
    "maxShingleSize": 6,
    "tokenSeparator": ""
  }
]
```

Nell'esempio seguente il numero di telefono è suddiviso nei blocchi che in genere si prevede vengano cercati dall'utente.

|Input|Output|  
|-|-|  
|`(321) 555-0199`|`[321, 555, 0199, 321555, 5550199, 3215550199]`|

A seconda delle esigenze, questo può essere un approccio più efficiente al problema.

## <a name="reset-and-rerun"></a>Reimpostare ed eseguire di nuovo

Per semplicità, in questa esercitazione sono stati creati tre nuovi indici. È tuttavia pratica comune eliminare e ricreare gli indici durante le fasi iniziali dello sviluppo. È possibile eliminare un indice nel portale di Azure o usando la chiamata API seguente:

```http
DELETE https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  api-key: <YOUR-ADMIN-API-KEY>
```

## <a name="takeaways"></a>Risultati

Questa esercitazione ha illustrato il processo di compilazione e test di un analizzatore personalizzato. È stato creato un indice, sono stati indicizzati i dati e quindi è stata eseguita una query sull'indice per controllare i risultati della ricerca restituiti. È stata quindi usata l'API di analisi del testo per vedere il processo di analisi lessicale in azione.

L'analizzatore definito in questa esercitazione offre una soluzione semplice per la ricerca di numeri di telefono e questo stesso processo può essere usato anche per creare un analizzatore di numeri di telefono personalizzato per qualsiasi scenario.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si lavora nella propria sottoscrizione, una volta terminato un progetto è opportuno rimuovere le risorse che non sono più necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento Tutte le risorse o Gruppi di risorse nel riquadro di spostamento a sinistra.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare un analizzatore personalizzato, si può passare ad esaminare tutti i diversi filtri, tokenizer e analizzatori disponibili per creare un'esperienza di ricerca avanzata.

> [!div class="nextstepaction"]
> [Analizzatori personalizzati in Ricerca cognitiva di Azure](index-add-custom-analyzers.md)
