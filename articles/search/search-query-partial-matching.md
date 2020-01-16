---
title: Criteri di corrispondenza e caratteri speciali
titleSuffix: Azure Cognitive Search
description: Usare query con caratteri jolly e prefisso per trovare una corrispondenza con termini interi o parziali in una richiesta di query di ricerca cognitiva di Azure. I modelli difficili da confrontare che includono caratteri speciali possono essere risolti usando la sintassi di query completa e gli analizzatori personalizzati.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ec1422d03cce78bdd8206f6687a78b63ddf989dc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989618"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>Corrispondenza con modelli e caratteri speciali (trattini)

Per le query che includono caratteri speciali (`-, *, (, ), /, \, =`) o per i modelli di query basati su termini parziali in un termine più ampio, in genere sono necessari passaggi di configurazione aggiuntivi per garantire che l'indice contenga il contenuto previsto, nel formato corretto. 

Per impostazione predefinita, un numero di telefono come `+1 (425) 703-6214` viene suddiviso in token come `"1"`, `"425"`, `"703"``"6214"`. Come si può immaginare, la ricerca di `"3-62"`, termini parziali che includono un trattino, avrà esito negativo perché il contenuto non esiste effettivamente nell'indice. 

Quando è necessario eseguire la ricerca di stringhe parziali o caratteri speciali, è possibile eseguire l'override dell'analizzatore predefinito con un analizzatore personalizzato che opera con regole di token più semplici, mantenendo i termini completi, necessario quando le stringhe di query includono parti di un termine o speciale caratteri. Eseguendo un nuovo passaggio, l'approccio è simile al seguente:

+ Scegliere un analizzatore predefinito o definire un analizzatore personalizzato che produce l'output desiderato
+ Assegnare l'analizzatore al campo
+ Compilare l'indice e il test

Questo articolo illustra queste attività. L'approccio descritto in questo articolo è utile in altri scenari: le query con caratteri jolly e di espressioni regolari richiedono anche termini interi come base per la corrispondenza dei criteri. 

> [!TIP]
> La valutazione di analyers è un processo iterativo che richiede ricompilazioni di indici frequenti. Per semplificare questo passaggio, è possibile usare il post, le API REST per [create index](https://docs.microsoft.com/rest/api/searchservice/create-index), [delete index](https://docs.microsoft.com/rest/api/searchservice/delete-index),[Load Documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)e [Search Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents). Per i documenti di caricamento, il corpo della richiesta deve contenere un set di dati di piccole dimensioni che si desidera testare, ad esempio un campo con numeri di telefono o codici prodotto. Con queste API nella stessa raccolta di messaggi, è possibile scorrere rapidamente questi passaggi.

## <a name="choosing-an-analyzer"></a>Scelta di un analizzatore

Quando si sceglie un analizzatore che produce token a lungo termine, gli analizzatori seguenti sono opzioni comuni:

| Analizzatore | Comportamenti |
|----------|-----------|
| [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Il contenuto dell'intero campo viene suddiviso in token come un singolo termine. |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Separa solo gli spazi vuoti. I termini che includono trattini o altri caratteri vengono considerati come un singolo token. |
| [analizzatore personalizzato](index-add-custom-analyzers.md) | consigliabile La creazione di un analizzatore personalizzato consente di specificare il filtro Tokenizer e token. Gli analizzatori precedenti devono essere usati così come sono. Un analizzatore personalizzato consente di selezionare i filtri Tokenizer e token da usare. <br><br>Una combinazione consigliata è la [parola chiave Tokenizer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) con un [filtro token in minuscolo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). Di per sé, l' [analizzatore di parole chiave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) predefinito non esegue la riduzione del testo maiuscolo e minuscolo, che può causare errori nelle query. Un analizzatore personalizzato fornisce un meccanismo per l'aggiunta del filtro del token in minuscolo. |

Se si usa uno strumento di test dell'API Web come postazione, è possibile aggiungere la [chiamata Rest dell'analizzatore test](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) per esaminare l'output in formato token. Dato un indice esistente e un campo contenente trattini o termini parziali, è possibile provare diversi analizzatori su termini specifici per visualizzare i token emessi.  

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
1. Modificare la richiesta di utilizzo del `whitespace` o dell'analizzatore `keyword`:

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
> Tenere presente che i parser di query spesso sono minuscoli in un'espressione di ricerca durante la compilazione dell'albero della query. Questo potrebbe essere il motivo per cui si usa un analizzatore che non consente l'immissione di testo in lettere minuscole e non si ricevono risultati previsti. La soluzione consiste nell'aggiungere un filtro per i token del case lwower.

## <a name="analyzer-definitions"></a>Definizioni analizzatore
 
Se si stanno valutando gli analizzatori o procedendo con una configurazione specifica, è necessario specificare l'analizzatore nella definizione del campo e possibilmente configurare l'analizzatore se non si usa un analizzatore incorporato. Quando si scambiano gli analizzatori, in genere è necessario ricompilare l'indice (drop, recreate e reload). 

### <a name="use-built-in-analyzers"></a>Usare gli analizzatori predefiniti

Gli analizzatori predefiniti o predefiniti possono essere specificati in base al nome in una proprietà `analyzer` di una definizione di campo, senza che sia necessaria alcuna configurazione aggiuntiva nell'indice. Nell'esempio seguente viene illustrato come impostare l'analizzatore di `whitespace` in un campo.

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
Per altre informazioni su tutti gli analizzatori incorporati disponibili, vedere l' [elenco degli analizzatori](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)predefiniti. 

### <a name="use-custom-analyzers"></a>Usare gli analizzatori personalizzati

Se si usa un [analizzatore personalizzato](index-add-custom-analyzers.md), definirlo nell'indice con una combinazione definita dall'utente Tokenizer, tokenfilter, con le impostazioni di configurazione possibili. A questo punto, fare riferimento alla definizione di un campo, esattamente come si farebbe con un analizzatore incorporato.

Quando l'obiettivo è la suddivisione in token a lungo termine, è consigliabile un analizzatore personalizzato costituito da una **parola chiave Tokenizer** e da un **filtro token in minuscolo** .

+ La parola chiave Tokenizer crea un singolo token per l'intero contenuto di un campo.
+ Il filtro di token minuscolo trasforma le lettere maiuscole in testo minuscolo. I parser di query sono in genere caratteri minuscoli per gli input di testo maiuscoli. Minuscole omogeneizza gli input con i termini in formato token.

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
]

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
> Il `keyword_v2` Tokenizer e il filtro del token `lowercase` sono noti al sistema e usano le configurazioni predefinite, motivo per cui è possibile fare riferimento a essi in base al nome senza doverli definire per primi.

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
]

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

Gli analizzatori vengono chiamati durante l'indicizzazione e durante l'esecuzione delle query. È comune usare lo stesso analizzatore per entrambi, ma è possibile configurare analizzatori personalizzati per ogni carico di lavoro. Gli override dell'analizzatore sono specificati nella [definizione dell'indice](https://docs.microsoft.com/rest/api/searchservice/create-index) in una `analyzers` sezione e quindi fanno riferimento a campi specifici. 

Quando l'analisi personalizzata è necessaria solo durante l'indicizzazione, è possibile applicare l'analizzatore personalizzato alla semplice indicizzazione e continuare a usare l'analizzatore Lucene standard (o un altro analizzatore) per le query.

Per specificare l'analisi specifica del ruolo, è possibile impostare le proprietà del campo per ciascuna di esse, impostando `indexAnalyzer` e `searchAnalyzer` invece della proprietà `analyzer` predefinita.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

### <a name="duplicate-fields-for-different-scenarios"></a>Campi duplicati per diversi scenari

Un'altra opzione utilizza l'assegnazione dell'analizzatore per campo per ottimizzare scenari diversi. In particolare, è possibile definire "featureCode" e "featureCodeRegex" per supportare la ricerca full-text regolare sul primo e il criterio avanzato corrispondente nel secondo.

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

Questo articolo spiega in che modo gli analizzatori contribuiscono a risolvere i problemi di query e risolvere i problemi di query. Come passaggio successivo, esaminare più in dettaglio l'effetto dell'analizzatore sull'indicizzazione e l'elaborazione delle query. In particolare, provare a usare l'API di analisi del testo per restituire l'output in formato token in modo che sia possibile vedere esattamente cosa sta creando un analizzatore per l'indice.

+ [Analizzatori di linguaggi](search-language-support.md)
+ [Analizzatori per l'elaborazione del testo in Azure ricerca cognitiva](search-analyzers.md)
+ [Analizza API testo (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Funzionamento della ricerca full-text (architettura delle query)](search-lucene-query-architecture.md)