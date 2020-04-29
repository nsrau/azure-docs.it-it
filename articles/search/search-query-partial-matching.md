---
title: Termini parziali, modelli e caratteri speciali
titleSuffix: Azure Cognitive Search
description: Usare query con caratteri jolly, Regex e prefisso per trovare una corrispondenza con termini interi o parziali in una richiesta di query di Azure ricerca cognitiva. I modelli difficili da confrontare che includono caratteri speciali possono essere risolti usando la sintassi di query completa e gli analizzatori personalizzati.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 5a05f2973ac17460250fb3e80eb7bc0da9849940
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81262877"
---
# <a name="partial-term-search-and-patterns-with-special-characters-wildcard-regex-patterns"></a>Ricerca dei termini parziali e modelli con caratteri speciali (carattere jolly, Regex, Patterns)

Una *ricerca con termini parziali* si riferisce a query costituite da frammenti di termini, in cui invece di un termine intero può essere presente solo l'inizio, il centro o la fine del termine (talvolta definito query di prefisso, infisso o suffisso). Un *modello* potrebbe essere una combinazione di frammenti, spesso con caratteri speciali, ad esempio trattini o barre, che fanno parte della stringa di query. Casi d'uso comuni includono l'esecuzione di query per parti di un numero di telefono, URL, persone o codici prodotto o parole composte.

La ricerca parziale e dei criteri può essere problematica se l'indice non presenta termini nel formato previsto. Durante la [fase di analisi lessicale](search-lucene-query-architecture.md#stage-2-lexical-analysis) dell'indicizzazione (presupponendo l'analizzatore standard predefinito), i caratteri speciali vengono ignorati, le stringhe composite e compound sono suddivise e lo spazio vuoto viene eliminato. tutti i quali possono causare errori nelle query del modello quando non viene trovata alcuna corrispondenza. Ad esempio, un numero di telefono `+1 (425) 703-6214` come (in formato `"1"`token `"425"`, `"703"`, `"6214"`,) non verrà visualizzato in `"3-62"` una query perché tale contenuto non esiste effettivamente nell'indice. 

La soluzione consiste nel richiamare un analizzatore che conserva una stringa completa, inclusi spazi e caratteri speciali, se necessario, in modo che sia possibile trovare una corrispondenza con termini e modelli parziali. La creazione di un campo aggiuntivo per una stringa intatta, più l'uso di un analizzatore che conserva il contenuto, costituisce la base della soluzione.

> [!TIP]
> Familiarità con le API REST e REST? [Scaricare la raccolta degli esempi di query](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/full-syntax-examples) per eseguire una query su termini parziali e caratteri speciali descritti in questo articolo.

## <a name="what-is-partial-search-in-azure-cognitive-search"></a>Che cos'è la ricerca parziale in Azure ricerca cognitiva

In ricerca cognitiva di Azure, la ricerca parziale e il modello sono disponibili nei seguenti formati:

+ [Ricerca di prefisso](query-simple-syntax.md#prefix-search), ad `search=cap*`esempio, che corrisponde a "Cap ' s Waterfront Inn" o "GACC Capital". È possibile usare la sintassi di query semplice o la sintassi di query Lucene completa per la ricerca di prefisso.

+ [Ricerca con caratteri jolly](query-lucene-syntax.md#bkmk_wildcard) o [espressioni regolari](query-lucene-syntax.md#bkmk_regex) per la ricerca di un modello o di parti di una stringa incorporata. Il carattere jolly e le espressioni regolari richiedono la sintassi Lucene completa. Le query sui suffissi e sugli indici vengono formulate come espressione regolare.

  Di seguito sono riportati alcuni esempi di ricerca con termini parziali. Per una query di suffisso, dato il termine "alfanumerico", è necessario usare una`search=/.*numeric.*/`ricerca con caratteri jolly () per trovare una corrispondenza. Per un termine parziale che include caratteri interni, ad esempio un frammento di URL, potrebbe essere necessario aggiungere caratteri di escape. In JSON, una barra `/` è preceduta da un carattere di escape `\`con una barra rovesciata. Di conseguenza, `search=/.*microsoft.com\/azure\/.*/` è la sintassi per il frammento di URL "Microsoft.com/Azure/".

Come indicato, per tutti i precedenti è necessario che l'indice contenga stringhe in un formato propizio ai criteri di ricerca, che non sono disponibili nell'analizzatore standard. Seguendo i passaggi descritti in questo articolo, è possibile verificare che il contenuto necessario esista per supportare questi scenari.

## <a name="solving-partialpattern-search-problems"></a>Risoluzione dei problemi di ricerca parziali/criteri

Quando è necessario eseguire la ricerca su frammenti o modelli o caratteri speciali, è possibile eseguire l'override dell'analizzatore predefinito con un analizzatore personalizzato che opera con regole di token più semplici, mantenendo l'intera stringa. Eseguendo un nuovo passaggio, l'approccio è simile al seguente:

+ Definire un campo per archiviare una versione intatta della stringa (presupponendo che si desideri analizzare e testo non analizzato)
+ Scegliere un analizzatore predefinito o definire un analizzatore personalizzato per restituire una stringa intatta non analizzata
+ Assegnare l'analizzatore personalizzato al campo
+ Compilare e testare l'indice

> [!TIP]
> La valutazione degli analizzatori è un processo iterativo che richiede ricompilazioni frequenti degli indici. Per semplificare questo passaggio, è possibile usare il post, le API REST per [create index](https://docs.microsoft.com/rest/api/searchservice/create-index), [delete index](https://docs.microsoft.com/rest/api/searchservice/delete-index),[Load Documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)e [Search Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents). Per i documenti di caricamento, il corpo della richiesta deve contenere un set di dati di piccole dimensioni che si desidera testare, ad esempio un campo con numeri di telefono o codici prodotto. Con queste API nella stessa raccolta di messaggi, è possibile scorrere rapidamente questi passaggi.

## <a name="duplicate-fields-for-different-scenarios"></a>Campi duplicati per diversi scenari

Gli analizzatori vengono assegnati in base ai singoli campi, il che significa che è possibile creare campi nell'indice per ottimizzare scenari diversi. In particolare, è possibile definire "featureCode" e "featureCodeRegex" per supportare la ricerca full-text regolare sul primo e il criterio avanzato corrispondente nel secondo.

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_custom_analyzer"
},
```

## <a name="choose-an-analyzer"></a>Scegliere un analizzatore

Quando si sceglie un analizzatore che produce token a lungo termine, gli analizzatori seguenti sono opzioni comuni:

| Analizzatore | Comportamenti |
|----------|-----------|
| [analizzatori del linguaggio](index-add-language-analyzers.md) | Conserva i trattini in parole o stringhe composte, mutazioni vocali e forme verbo. Se i modelli di query includono trattini, potrebbe essere sufficiente utilizzare un analizzatore di linguaggio. |
| [parola chiave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Il contenuto dell'intero campo viene suddiviso in token come un singolo termine. |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Separa solo gli spazi vuoti. I termini che includono trattini o altri caratteri vengono considerati come un singolo token. |
| [analizzatore personalizzato](index-add-custom-analyzers.md) | consigliabile La creazione di un analizzatore personalizzato consente di specificare il filtro Tokenizer e token. Gli analizzatori precedenti devono essere usati così come sono. Un analizzatore personalizzato consente di selezionare i filtri Tokenizer e token da usare. <br><br>Una combinazione consigliata è la [parola chiave Tokenizer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) con un [filtro token in minuscolo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). Di per sé, l' [analizzatore di parole chiave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) predefinito non esegue la riduzione del testo maiuscolo e minuscolo, che può causare errori nelle query. Un analizzatore personalizzato fornisce un meccanismo per l'aggiunta del filtro del token in minuscolo. |

Se si usa uno strumento di test dell'API Web come postazione, è possibile aggiungere la [chiamata Rest dell'analizzatore test](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) per esaminare l'output in formato token.

È necessario disporre di un indice esistente da usare. Dato un indice esistente e un campo contenente trattini o termini parziali, è possibile provare diversi analizzatori su termini specifici per visualizzare i token emessi.  

1. Controllare l'analizzatore standard per verificare il modo in cui i termini sono suddivisi in token per impostazione predefinita.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Valutare la risposta per vedere come il testo viene suddiviso in token all'interno dell'indice. Si noti il modo in cui ogni termine viene suddiviso in lettere maiuscole e minuscole.

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. Modificare la richiesta di utilizzo dell' `whitespace` analizzatore o `keyword` :

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. A questo punto la risposta è costituita da un singolo token, in maiuscolo, con trattini conservati come parte della stringa. Se è necessario eseguire una ricerca in base a un modello o a un termine parziale, il motore di query ha ora la base per trovare una corrispondenza.


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> Tenere presente che i parser di query spesso sono minuscoli in un'espressione di ricerca durante la compilazione dell'albero della query. Questo potrebbe essere il motivo per cui si usa un analizzatore che non consente l'immissione di testo in lettere minuscole e non si ricevono risultati previsti. La soluzione consiste nell'aggiungere un filtro token in minuscolo, come descritto nella sezione "usare gli analizzatori personalizzati" di seguito.

## <a name="configure-an-analyzer"></a>Configurare un analizzatore
 
Se si stanno valutando gli analizzatori o procedendo con una configurazione specifica, è necessario specificare l'analizzatore nella definizione del campo e possibilmente configurare l'analizzatore se non si usa un analizzatore incorporato. Quando si scambiano gli analizzatori, in genere è necessario ricompilare l'indice (drop, recreate e reload). 

### <a name="use-built-in-analyzers"></a>Usare gli analizzatori predefiniti

Gli analizzatori predefiniti o predefiniti possono essere specificati in base al nome in una `analyzer` proprietà di una definizione di campo, senza che sia necessaria alcuna configurazione aggiuntiva nell'indice. Nell'esempio seguente viene illustrato come impostare l' `whitespace` analizzatore in un campo. 

Per altri scenari e per altre informazioni su altri analizzatori incorporati, vedere l' [elenco degli analizzatori](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)predefiniti. 

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```

### <a name="use-custom-analyzers"></a>Usare gli analizzatori personalizzati

Se si usa un [analizzatore personalizzato](index-add-custom-analyzers.md), definirlo nell'indice con una combinazione definita dall'utente di Tokenizer, un filtro token, con le impostazioni di configurazione possibili. A questo punto, fare riferimento alla definizione di un campo, esattamente come si farebbe con un analizzatore incorporato.

Quando l'obiettivo è la suddivisione in token a lungo termine, è consigliabile un analizzatore personalizzato costituito da una **parola chiave Tokenizer** e da un **filtro token in minuscolo** .

+ La parola chiave Tokenizer crea un singolo token per l'intero contenuto di un campo.
+ Il filtro di token minuscolo trasforma le lettere maiuscole in testo minuscolo. I parser di query sono in genere caratteri minuscoli per gli input di testo maiuscoli. L'utilizzo di maiuscole e minuscole omogeneizza gli input con i termini in formato token.

Nell'esempio seguente viene illustrato un analizzatore personalizzato che fornisce la parola chiave Tokenizer e un filtro di token minuscolo.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> Il `keyword_v2` Tokenizer e `lowercase` il filtro token sono noti al sistema e usano le configurazioni predefinite, motivo per cui è possibile fare riferimento a essi in base al nome senza doverli definire prima.

## <a name="build-and-test"></a>Compilare e testare

Dopo aver definito un indice con gli analizzatori e le definizioni di campo che supportano lo scenario, caricare i documenti con stringhe rappresentative per poter testare le query di stringa parziali. 

Nella sezione precedente è stata illustrata la logica. Questa sezione illustra ogni API da chiamare durante il test della soluzione. Come indicato in precedenza, se si usa uno strumento di test Web interattivo, ad esempio postazione, è possibile eseguire rapidamente queste attività.

+ [Delete index](https://docs.microsoft.com/rest/api/searchservice/delete-index) rimuove un indice esistente con lo stesso nome in modo che sia possibile ricrearlo.

+ [Create index](https://docs.microsoft.com/rest/api/searchservice/create-index) crea la struttura dell'indice nel servizio di ricerca, inclusi i campi e le definizioni dell'analizzatore con una specifica dell'analizzatore.

+ [Load Documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) importa i documenti con la stessa struttura dell'indice, nonché il contenuto ricercabile. Dopo questo passaggio, l'indice è pronto per eseguire query o test.

+ [Test Analyzer](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) è stato introdotto in [scegliere un analizzatore](#choose-an-analyzer). Testare alcune stringhe nell'indice usando un'ampia gamma di analizzatori per comprendere in che modo i termini vengono suddivisi in token.

+ Nei [documenti di ricerca](https://docs.microsoft.com/rest/api/searchservice/search-documents) viene illustrato come creare una richiesta di query, utilizzando una sintassi [semplice](query-simple-syntax.md) o una [sintassi Lucene completa](query-lucene-syntax.md) per le espressioni regolari e con caratteri jolly.

  Per le query a termini parziali, ad esempio l'esecuzione di query su "3-6214" per trovare una corrispondenza in "+ 1 (425) 703-6214", è `search=3-6214&queryType=simple`possibile usare la sintassi semplice:.

  Per le query infissi e dei suffissi, ad esempio l'esecuzione di query su "num" o "NUMERIC" per trovare una corrispondenza in "alfanumerico", usare la sintassi Lucene completa e un'espressione regolare:`search=/.*num.*/&queryType=full`

## <a name="tips-and-best-practices"></a>Suggerimenti e procedure consigliate

### <a name="tune-query-performance"></a>Ottimizzare le prestazioni delle query

Se si implementa la configurazione consigliata che include il keyword_v2 Tokenizer e il filtro dei token in minuscolo, è possibile notare una riduzione delle prestazioni delle query a causa dell'elaborazione di un filtro token aggiuntivo per i token esistenti nell'indice. 

Nell'esempio seguente viene aggiunto un [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) per fare in modo che il prefisso corrisponda più velocemente. Vengono generati token aggiuntivi per le combinazioni di caratteri 2-25 che includono i caratteri: (non solo MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/SQ, MSFT/SQL). Come si può immaginare, l'ulteriore tokening genera un indice più grande.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>Usare analizzatori diversi per l'indicizzazione e l'elaborazione delle query

Gli analizzatori vengono chiamati durante l'indicizzazione e durante l'esecuzione delle query. È comune usare lo stesso analizzatore per entrambi, ma è possibile configurare analizzatori personalizzati per ogni carico di lavoro. Gli override dell'analizzatore sono specificati nella [definizione](https://docs.microsoft.com/rest/api/searchservice/create-index) dell' `analyzers` indice in una sezione e quindi fanno riferimento a campi specifici. 

Quando l'analisi personalizzata è necessaria solo durante l'indicizzazione, è possibile applicare l'analizzatore personalizzato alla semplice indicizzazione e continuare a usare l'analizzatore Lucene standard (o un altro analizzatore) per le query.

Per specificare l'analisi specifica del ruolo, è possibile impostare le proprietà del campo per ciascuna di esse `indexAnalyzer` , `searchAnalyzer` impostando e invece `analyzer` della proprietà predefinita.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo spiega in che modo gli analizzatori contribuiscono a risolvere i problemi di query e risolvere i problemi di query. Come passaggio successivo, esaminare più in dettaglio l'effetto dell'analizzatore sull'indicizzazione e l'elaborazione delle query. In particolare, provare a usare l'API di analisi del testo per restituire l'output in formato token in modo che sia possibile vedere esattamente cosa sta creando un analizzatore per l'indice.

+ [Analizzatori di linguaggi](search-language-support.md)
+ [Analizzatori per l'elaborazione del testo in Azure ricerca cognitiva](search-analyzers.md)
+ [Analizza API testo (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Funzionamento della ricerca full-text (architettura delle query)](search-lucene-query-architecture.md)