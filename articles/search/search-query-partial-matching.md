---
title: Abbina modelli e caratteri speciali
titleSuffix: Azure Cognitive Search
description: Usare query con caratteri jolly e prefissi per trovare una corrispondenza in termini interi o parziali in una richiesta di query di Ricerca cognitiva di Azure.Use wildcard and prefix queries to match on whole or partial terms in an Azure Cognitive Search query request. I modelli di corrispondenza che includono caratteri speciali possono essere risolti utilizzando la sintassi di query completa e analizzatori personalizzati.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: f78ba5b351a3da46d7b8b3780cf00772c4f3b2ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289312"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>Corrispondenza su modelli e caratteri speciali (trattini)

Per le query che`-, *, (, ), /, \, =`includono caratteri speciali ( ) o per modelli di query basati su termini parziali all'interno di un termine più ampio, sono in genere necessari passaggi di configurazione aggiuntivi per garantire che l'indice contenga il contenuto previsto nel formato corretto. 

Per impostazione predefinita, `+1 (425) 703-6214` un numero `"1"` `"425"`di `"703"` `"6214"`telefono come è tokenizzato come , , , , . Come si può immaginare, la ricerca su `"3-62"`termini parziali che includono un trattino, avrà esito negativo perché tale contenuto non esiste effettivamente nell'indice. 

Quando è necessario cercare stringhe parziali o caratteri speciali, è possibile eseguire l'override dell'analizzatore predefinito con un analizzatore personalizzato che opera in base a regole di tokenizzazione più semplici, conservando interi termini, necessari quando le stringhe di query includono parti di un termine o di uno speciale personaggi. Facendo un passo indietro, l'approccio si presenta così:

+ Scegliere un analizzatore predefinito o definire un analizzatore personalizzato che produce l'output desiderato
+ Assegnare l'analizzatore al campo
+ Compilare l'indice e testare

In questo articolo vengono illustrate queste attività. L'approccio descritto di seguito è utile in altri scenari: anche le query con caratteri jolly e espressioni regolari richiedono termini interi come base per i criteri di ricerca. 

> [!TIP]
> La valutazione degli analizzatori è un processo iterativo che richiede frequenti ricompilazioni dell'indice. È possibile semplificare questo passaggio utilizzando Postman, le API REST per [Crea indice](https://docs.microsoft.com/rest/api/searchservice/create-index), [Elimina indice,](https://docs.microsoft.com/rest/api/searchservice/delete-index)[Carica documenti](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)e [Cerca documenti](https://docs.microsoft.com/rest/api/searchservice/search-documents). Per carica documenti, il corpo della richiesta deve contenere un piccolo set di dati rappresentativi che si desidera testare ,ad esempio un campo con numeri di telefono o codici prodotto. Con queste API nella stessa raccolta Postman, è possibile scorrere rapidamente questi passaggi.

## <a name="choosing-an-analyzer"></a>Scelta di un analizzatore

Quando si sceglie un analizzatore che produce token a termine completo, gli analizzatori seguenti sono scelte comuni:When choosing an analyzer that produces whole-term tokens, the following analyzers are common choices:

| Analizzatore | Comportamenti |
|----------|-----------|
| [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Il contenuto dell'intero campo viene tokenizzato come un singolo termine. |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Separa solo gli spazi vuoti. I termini che includono trattini o altri caratteri vengono considerati come un singolo token. |
| [analizzatore personalizzato](index-add-custom-analyzers.md) | (scelta consigliata) La creazione di un analizzatore personalizzato consente di specificare sia il tokenizer che il filtro token. Gli analizzatori precedenti devono essere utilizzati così come sono. Un analizzatore personalizzato consente di scegliere i tokenizer e i filtri token da usare. <br><br>Una combinazione consigliata è il [tokenizer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) con parole chiave con un [filtro token minuscolo.](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html) Di per sé, [l'analizzatore](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) di parole chiave predefinito non minuscola alcun testo maiuscolo, che può causare l'esito negativo delle query. Un analizzatore personalizzato fornisce un meccanismo per l'aggiunta del filtro token minuscolo. |

Se si usa uno strumento di test API Web come Postman, è possibile aggiungere la [chiamata REST di Test Analyzer](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) per esaminare l'output in formato token. Dato un indice esistente e un campo contenente trattini o termini parziali, è possibile provare vari analizzatori su termini specifici per vedere quali token vengono emessi.  

1. Controllare l'analizzatore Standard per vedere come i termini sono tokenizzati per impostazione predefinita.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Valutare la risposta per vedere come il testo viene tokenizzato all'interno dell'indice. Si noti come ogni termine è minuscolo e suddiviso.

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
1. Modificare la richiesta `whitespace` per `keyword` utilizzare l'analizzatore o :

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Ora la risposta è costituita da un singolo token, maiuscolo, con trattini conservati come parte della stringa. Se è necessario eseguire la ricerca in base a un modello o a un termine parziale, il motore di query ha ora la base per trovare una corrispondenza.


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
> Tenere presente che i parser di query spesso i termini minuscoli in un'espressione di ricerca durante la creazione dell'albero delle query. Se si utilizza un analizzatore che non immette input di testo minuscoli e non si ottengono i risultati previsti, questo potrebbe essere il motivo. La soluzione consiste nell'aggiungere un filtro token lwower-case.

## <a name="analyzer-definitions"></a>Definizioni degli analizzatori
 
Sia che si stia valutando gli analizzatori o si eseguo con una configurazione specifica, sarà necessario specificare l'analizzatore nella definizione del campo ed eventualmente configurare l'analizzatore stesso se non si utilizza un analizzatore incorporato. Quando si scambiano analizzatori, in genere è necessario ricompilare l'indice (eliminare, ricreare e ricaricare). 

### <a name="use-built-in-analyzers"></a>Usare analizzatori incorporatiUse built-in analyzers

Gli analizzatori predefiniti o incorporati possono essere `analyzer` specificati per nome in una proprietà di una definizione di campo, senza alcuna configurazione aggiuntiva nell'indice. Nell'esempio seguente viene illustrato `whitespace` come impostare l'analizzatore su un campo.

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
Per ulteriori informazioni su tutti gli analizzatori predefiniti disponibili, vedere [Elenco analizzatori predefiniti](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

### <a name="use-custom-analyzers"></a>Usare analizzatori personalizzatiUse custom analyzers

Se si utilizza un [analizzatore personalizzato,](index-add-custom-analyzers.md)definirlo nell'indice con una combinazione definita dall'utente di tokenizer, tokenfilter, con possibili impostazioni di configurazione. Successivamente, fare riferimento a esso in una definizione di campo, proprio come si farebbe con un analizzatore incorporato.

Quando l'obiettivo è la tokenizzazione a termine intero, è consigliabile un analizzatore personalizzato costituito da un **tokenizer** per parole chiave e da un filtro token **minuscolo.**

+ Il tokenizer della parola chiave crea un singolo token per l'intero contenuto di un campo.
+ Il filtro token minuscolo trasforma le lettere maiuscole in testo minuscolo. I parser di query in genere minuscolo qualsiasi input di testo in maiuscolo. La cassa inferiore omogeneizza gli input con i termini in formato token.

L'esempio seguente illustra un analizzatore personalizzato che fornisce il tokenizer con parole chiave e un filtro token minuscolo.

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
> Il `keyword_v2` tokenizer `lowercase` e il filtro token sono noti al sistema e utilizzano le configurazioni predefinite, motivo per cui è possibile farvi riferimento per nome senza doverli prima definire.

## <a name="tips-and-best-practices"></a>Suggerimenti e procedure consigliate

### <a name="tune-query-performance"></a>Ottimizzare le prestazioni delle query

Se si implementa la configurazione consigliata che include il tokenizer keyword_v2 e il filtro token minuscolo, è possibile notare una riduzione delle prestazioni delle query a causa dell'elaborazione del filtro token aggiuntivo sui token esistenti nell'indice. 

Nell'esempio seguente viene aggiunto un [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) per velocizzare le corrispondenze con prefisso. Vengono generati token aggiuntivi per in 2-25 combinazioni di caratteri che includono caratteri: (non solo MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/S, MSFT/SQ, MSFT/SQL). Come si può immaginare, la tokenizzazione aggiuntiva genera un indice più grande.

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

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>Usare analizzatori diversi per l'indicizzazione e l'elaborazione delle queryUse different analyzers for indexing and query processing

Gli analizzatori vengono chiamati durante l'indicizzazione e durante l'esecuzione della query. È comune usare lo stesso analizzatore per entrambi, ma è possibile configurare analizzatori personalizzati per ogni carico di lavoro. Le sostituzioni dell'analizzatore `analyzers` vengono specificate nella [definizione dell'indice](https://docs.microsoft.com/rest/api/searchservice/create-index) in una sezione e quindi vi viene fatto riferimento in campi specifici. 

Quando l'analisi personalizzata è necessaria solo durante l'indicizzazione, è possibile applicare l'analizzatore personalizzato solo per l'indicizzazione e continuare a utilizzare l'analizzatore Lucene standard (o un altro analizzatore) per le query.

Per specificare l'analisi specifica del ruolo, è possibile `indexAnalyzer` `searchAnalyzer` impostare le `analyzer` proprietà del campo per ciascuno di essi, impostando e anziché la proprietà predefinita.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

### <a name="duplicate-fields-for-different-scenarios"></a>Campi duplicati per scenari diversi

Un'altra opzione sfrutta l'assegnazione dell'analizzatore per campo per ottimizzare diversi scenari. In particolare, è possibile definire "featureCode" e "featureCodeRegex" per supportare la normale ricerca full-text nel primo e criteri di ricerca avanzati nel secondo.

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
  "analyzer": "my_customanalyzer"
},
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo viene illustrato in che modo gli analizzatori contribuiscono ai problemi di query e risolvono i problemi di query. Come passaggio successivo, esaminare più da vicino l'impatto dell'analizzatore sull'indicizzazione e sull'elaborazione delle query. In particolare, è consigliabile usare l'API Analizza testo per restituire l'output in formato token in modo da poter vedere esattamente ciò che un analizzatore sta creando per l'indice.

+ [Analizzatori di linguaggi](search-language-support.md)
+ [Analizzatori per l'elaborazione del testo in Ricerca cognitiva di AzureAnalyzers for text processing in Azure Cognitive Search](search-analyzers.md)
+ [Analizzare l'API di testo (REST)Analyze Text API (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Funzionamento della ricerca full-text (architettura delle query)](search-lucene-query-architecture.md)
