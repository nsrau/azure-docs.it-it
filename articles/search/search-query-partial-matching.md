---
title: Termini parziali, modelli e caratteri speciali
titleSuffix: Azure Cognitive Search
description: Usare query con caratteri jolly, Regex e prefisso per trovare una corrispondenza con termini interi o parziali in una richiesta di query di Azure ricerca cognitiva. I modelli difficili da confrontare che includono caratteri speciali possono essere risolti usando la sintassi di query completa e gli analizzatori personalizzati.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 357f44149cb17976556c1e4609f6f2af531b80ee
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935773"
---
# <a name="partial-term-search-and-patterns-with-special-characters-wildcard-regex-patterns"></a>Ricerca dei termini parziali e modelli con caratteri speciali (carattere jolly, Regex, Patterns)

Una *ricerca con termini parziali* si riferisce a query costituite da frammenti di termini, in cui invece di un termine intero può essere presente solo l'inizio, il centro o la fine del termine (talvolta definito query di prefisso, infisso o suffisso). Una ricerca con termini parziali può includere una combinazione di frammenti, spesso con caratteri speciali, ad esempio trattini o barre che fanno parte della stringa di query. Casi d'uso comuni includono parti di un numero di telefono, un URL, codici o parole composte con trattino.

La ricerca a termini parziali e le stringhe di query che includono caratteri speciali possono essere problematiche se l'indice non ha token nel formato previsto. Durante la [fase di analisi lessicale](search-lucene-query-architecture.md#stage-2-lexical-analysis) dell'indicizzazione (presupponendo l'analizzatore standard predefinito), i caratteri speciali vengono ignorati, le parole composte vengono suddivise e lo spazio vuoto viene eliminato. che può causare l'esito negativo delle query quando non viene trovata alcuna corrispondenza. Ad esempio, un numero di telefono come `+1 (425) 703-6214` (in formato token `"1"` , `"425"` , `"703"` , `"6214"` ) non verrà visualizzato in una `"3-62"` query perché tale contenuto non esiste effettivamente nell'indice. 

La soluzione consiste nel richiamare un analizzatore durante l'indicizzazione che conserva una stringa completa, inclusi spazi e caratteri speciali, se necessario, in modo che sia possibile includere gli spazi e i caratteri nella stringa di query. Analogamente, la presenza di una stringa completa non in formato token in parti più piccole consente la corrispondenza dei criteri per le query "inizia con" o "termina con", in cui il criterio fornito può essere valutato in base a un termine non trasformato dall'analisi lessicale. La creazione di un campo aggiuntivo per una stringa intatta, oltre a usare un analizzatore di mantenimento del contenuto che emette token a lungo termine, è la soluzione per la corrispondenza dei modelli e per la corrispondenza con stringhe di query che includono caratteri speciali.

> [!TIP]
> Se si ha familiarità con le API postazione e REST, [scaricare la raccolta degli esempi di query](https://github.com/Azure-Samples/azure-search-postman-samples/) per eseguire una query su termini parziali e caratteri speciali descritti in questo articolo.

## <a name="what-is-partial-term-search-in-azure-cognitive-search"></a>Che cos'è la ricerca a termini parziali in Azure ricerca cognitiva

Azure ricerca cognitiva analizza tutti i termini in formato token nell'indice e non trova una corrispondenza in un termine parziale a meno che non si includano operatori di segnaposto con caratteri jolly ( `*` e `?` ) oppure si formatti la query come espressione regolare. I termini parziali vengono specificati utilizzando le tecniche seguenti:

+ Le [query di espressioni regolari](query-lucene-syntax.md#bkmk_regex) possono essere qualsiasi espressione regolare valida in Apache Lucene. 

+ [Gli operatori con caratteri jolly con corrispondenza dei prefissi](query-simple-syntax.md#prefix-search) fanno riferimento a un modello generalmente riconosciuto che include l'inizio di un termine, seguito da `*` `?` operatori di suffisso o, come la `search=cap*` corrispondenza in "Cap ' s Waterfront Inn" o "GACC Capital". La corrispondenza con prefisso è supportata nella sintassi di query Lucene semplice e completa.

+ Il [carattere jolly con corrispondenza degli infissi e dei suffissi](query-lucene-syntax.md#bkmk_wildcard) posiziona gli `*` `?` operatori e all'interno o all'inizio di un termine e richiede la sintassi delle espressioni regolari (in cui l'espressione è racchiusa tra barre). Ad esempio, la stringa di query ( `search=/.*numeric*./` ) restituisce i risultati su "alfanumerico" e "alfanumerico" come suffissi e corrispondenze infissi.

Per i termini parziali o i criteri di ricerca e alcuni altri moduli di query come la ricerca fuzzy, gli analizzatori non vengono usati in fase di query. Per questi moduli di query, che il parser rileva dalla presenza di operatori e delimitatori, la stringa di query viene passata al motore senza analisi lessicale. Per questi moduli di query, l'analizzatore specificato nel campo viene ignorato.

> [!NOTE]
> Quando una stringa di query parziale include caratteri, ad esempio le barre in un frammento di URL, potrebbe essere necessario aggiungere caratteri di escape. In JSON, una barra è preceduta da un carattere di `/` escape con una barra rovesciata `\` . Di conseguenza, `search=/.*microsoft.com\/azure\/.*/` è la sintassi per il frammento di URL "Microsoft.com/Azure/".

## <a name="solving-partialpattern-search-problems"></a>Risoluzione dei problemi di ricerca parziali/criteri

Quando è necessario eseguire la ricerca su frammenti o modelli o caratteri speciali, è possibile eseguire l'override dell'analizzatore predefinito con un analizzatore personalizzato che opera con regole di token più semplici, mantenendo l'intera stringa nell'indice. Eseguendo un nuovo passaggio, l'approccio è simile al seguente:

+ Definire un campo per archiviare una versione intatta della stringa (presupponendo che il testo analizzato e non analizzato venga utilizzato in fase di query)
+ Valutare e scegliere tra i vari analizzatori che emettono token al livello di granularità corretto
+ Assegnare l'analizzatore al campo
+ Compilare e testare l'indice

> [!TIP]
> La valutazione degli analizzatori è un processo iterativo che richiede ricompilazioni frequenti degli indici. Per semplificare questo passaggio, è possibile usare il post, le API REST per [create index](/rest/api/searchservice/create-index), [delete index](/rest/api/searchservice/delete-index),[Load Documents](/rest/api/searchservice/addupdate-or-delete-documents)e [Search Documents](/rest/api/searchservice/search-documents). Per i documenti di caricamento, il corpo della richiesta deve contenere un set di dati di piccole dimensioni che si desidera testare, ad esempio un campo con numeri di telefono o codici prodotto. Con queste API nella stessa raccolta di messaggi, è possibile scorrere rapidamente questi passaggi.

## <a name="duplicate-fields-for-different-scenarios"></a>Campi duplicati per diversi scenari

Gli analizzatori determinano il modo in cui i termini sono in formato token in un indice. Poiché gli analizzatori vengono assegnati in base ai singoli campi, è possibile creare campi nell'indice per ottimizzarli per diversi scenari. Ad esempio, è possibile definire "featureCode" e "featureCodeRegex" per supportare la ricerca full-text regolare sul primo e i criteri di ricerca avanzati nel secondo. Gli analizzatori assegnati a ogni campo determinano il modo in cui il contenuto di ogni campo viene suddiviso in token nell'indice.  

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
| [Whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Separa solo gli spazi vuoti. I termini che includono trattini o altri caratteri vengono considerati come un singolo token. |
| [analizzatore personalizzato](index-add-custom-analyzers.md) | consigliabile La creazione di un analizzatore personalizzato consente di specificare il filtro Tokenizer e token. Gli analizzatori precedenti devono essere usati così come sono. Un analizzatore personalizzato consente di selezionare i filtri Tokenizer e token da usare. <br><br>Una combinazione consigliata è la [parola chiave Tokenizer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) con un [filtro token in minuscolo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). Di per sé, l' [analizzatore di parole chiave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) predefinito non esegue la riduzione del testo maiuscolo e minuscolo, che può causare errori nelle query. Un analizzatore personalizzato fornisce un meccanismo per l'aggiunta del filtro del token in minuscolo. |

Se si usa uno strumento di test dell'API Web come postazione, è possibile aggiungere la [chiamata Rest dell'analizzatore test](/rest/api/searchservice/test-analyzer) per esaminare l'output in formato token.

È necessario disporre di un indice popolato da usare. Dato un indice esistente e un campo contenente trattini o termini parziali, è possibile provare diversi analizzatori su termini specifici per visualizzare i token emessi.  

1. Prima di tutto, controllare l'analizzatore standard per vedere come vengono suddivisi in token i termini per impostazione predefinita.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Valutare la risposta per vedere come il testo viene suddiviso in token all'interno dell'indice. Si noti il modo in cui ogni termine viene suddiviso in lettere maiuscole e minuscole. Solo le query che corrispondono a questi token restituiranno questo documento nei risultati. Una query che include "10-NOR" avrà esito negativo.

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
1. Modificare ora la richiesta di utilizzo dell' `whitespace` `keyword` analizzatore o:

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. A questo punto la risposta è costituita da un singolo token, in maiuscolo, con trattini conservati come parte della stringa. Se è necessario eseguire una ricerca in base a un modello o a un termine parziale, ad esempio "10-NOR", il motore di query ha ora la base per trovare una corrispondenza.


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
> Tenere presente che i parser di query spesso sono minuscoli in un'espressione di ricerca durante la compilazione dell'albero della query. Questo potrebbe essere il motivo per cui si usa un analizzatore che non inserisce input di testo in lettere minuscole durante l'indicizzazione e non si ricevono risultati previsti. La soluzione consiste nell'aggiungere un filtro token in minuscolo, come descritto nella sezione "usare gli analizzatori personalizzati" di seguito.

## <a name="configure-an-analyzer"></a>Configurare un analizzatore
 
Se si stanno valutando gli analizzatori o procedendo con una configurazione specifica, è necessario specificare l'analizzatore nella definizione del campo e possibilmente configurare l'analizzatore se non si usa un analizzatore incorporato. Quando si scambiano gli analizzatori, in genere è necessario ricompilare l'indice (drop, recreate e reload). 

### <a name="use-built-in-analyzers"></a>Usare gli analizzatori predefiniti

Gli analizzatori predefiniti o predefiniti possono essere specificati in base al nome in una `analyzer` proprietà di una definizione di campo, senza che sia necessaria alcuna configurazione aggiuntiva nell'indice. Nell'esempio seguente viene illustrato come impostare l' `whitespace` analizzatore in un campo. 

Per altri scenari e per altre informazioni su altri analizzatori incorporati, vedere l' [elenco degli analizzatori](/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)predefiniti. 

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
> Il `keyword_v2` Tokenizer e il `lowercase` filtro token sono noti al sistema e usano le configurazioni predefinite, motivo per cui è possibile fare riferimento a essi in base al nome senza doverli definire prima.

## <a name="build-and-test"></a>Compilare e testare

Dopo aver definito un indice con gli analizzatori e le definizioni di campo che supportano lo scenario, caricare i documenti con stringhe rappresentative per poter testare le query di stringa parziali. 

Nella sezione precedente è stata illustrata la logica. Questa sezione illustra ogni API da chiamare durante il test della soluzione. Come indicato in precedenza, se si usa uno strumento di test Web interattivo, ad esempio postazione, è possibile eseguire rapidamente queste attività.

+ [Delete index](/rest/api/searchservice/delete-index) rimuove un indice esistente con lo stesso nome in modo che sia possibile ricrearlo.

+ [Create index](/rest/api/searchservice/create-index) crea la struttura dell'indice nel servizio di ricerca, inclusi i campi e le definizioni dell'analizzatore con una specifica dell'analizzatore.

+ [Load Documents](/rest/api/searchservice/addupdate-or-delete-documents) importa i documenti con la stessa struttura dell'indice, nonché il contenuto ricercabile. Dopo questo passaggio, l'indice è pronto per eseguire query o test.

+ [Test Analyzer](/rest/api/searchservice/test-analyzer) è stato introdotto in [scegliere un analizzatore](#choose-an-analyzer). Testare alcune stringhe nell'indice usando un'ampia gamma di analizzatori per comprendere in che modo i termini vengono suddivisi in token.

+ Nei [documenti di ricerca](/rest/api/searchservice/search-documents) viene illustrato come creare una richiesta di query, utilizzando una sintassi [semplice](query-simple-syntax.md) o una [sintassi Lucene completa](query-lucene-syntax.md) per le espressioni regolari e con caratteri jolly.

  Per le query a termini parziali, ad esempio l'esecuzione di query su "3-6214" per trovare una corrispondenza in "+ 1 (425) 703-6214", è possibile usare la sintassi semplice: `search=3-6214&queryType=simple` .

  Per le query infissi e dei suffissi, ad esempio l'esecuzione di query su "num" o "NUMERIC" per trovare una corrispondenza in "alfanumerico", usare la sintassi Lucene completa e un'espressione regolare: `search=/.*num.*/&queryType=full`

## <a name="tune-query-performance"></a>Ottimizzare le prestazioni delle query

Se si implementa la configurazione consigliata che include il keyword_v2 Tokenizer e il filtro dei token in minuscolo, è possibile notare una riduzione delle prestazioni delle query a causa dell'elaborazione di un filtro token aggiuntivo per i token esistenti nell'indice. 

Nell'esempio seguente viene aggiunto un [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) per fare in modo che il prefisso corrisponda più velocemente. Vengono generati token aggiuntivi per le combinazioni di caratteri 2-25 che includono i caratteri: (non solo MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/SQ, MSFT/SQL). 

Come si può immaginare, l'ulteriore tokening genera un indice più grande. Se si dispone di capacità sufficiente per contenere l'indice più ampio, questo approccio con il tempo di risposta più veloce potrebbe essere una soluzione migliore.

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

## <a name="next-steps"></a>Passaggi successivi

Questo articolo spiega in che modo gli analizzatori contribuiscono a risolvere i problemi di query e risolvere i problemi di query. Come passaggio successivo, esaminare più in dettaglio l'effetto dell'analizzatore sull'indicizzazione e l'elaborazione delle query. In particolare, provare a usare l'API di analisi del testo per restituire l'output in formato token in modo che sia possibile vedere esattamente cosa sta creando un analizzatore per l'indice.

+ [Analizzatori di linguaggi](search-language-support.md)
+ [Analizzatori per l'elaborazione del testo in Azure ricerca cognitiva](search-analyzers.md)
+ [Analizza API testo (REST)](/rest/api/searchservice/test-analyzer)
+ [Funzionamento della ricerca full-text (architettura delle query)](search-lucene-query-architecture.md)