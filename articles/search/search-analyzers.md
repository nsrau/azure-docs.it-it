---
title: Analizzatori per elaborazione linguistica e del testo - Ricerca di Azure
description: Assegnare degli analizzatori ai campi di testo di ricerca in un indice per sostituire lo standard Lucene predefinito con alternative specifiche per lingua, predefinite o personalizzate.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: heidist
manager: nitinme
author: HeidiSteen
ms.openlocfilehash: 85ebc75a22a4b27803df758d3f411a46a6206eb7
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72987628"
---
# <a name="analyzers-for-text-processing-in-azure-search"></a>Analizzatori per elaborazione del testo in Ricerca di Azure

Un *analizzatore* è un componente del [motore di ricerca full-text](search-lucene-query-architecture.md) responsabile dell'elaborazione di testo in stringhe di query e documenti indicizzati. Analizzatori diversi modificano il testo in modi diversi a seconda dello scenario. Gli analizzatori del linguaggio elaborano il testo usando le regole linguistiche per migliorare la qualità della ricerca, mentre altri analizzatori eseguono attività più semplici, ad esempio la conversione dei caratteri in lettere minuscole. 

Gli analizzatori del linguaggio sono quelli usati più frequentemente; vi è un analizzatore del linguaggio predefinito assegnato a ogni campo ricercabile in un indice di Ricerca di Azure. Le trasformazioni del linguaggio seguenti sono tipiche durante l'analisi del testo:

+ Parole non essenziali (parole non significative) e punteggiatura vengono rimosse.
+ Frasi e parole sillabate vengono divise in parti diverse.
+ Le parole maiuscole vengono modificate in parole minuscole.
+ Le parole vengono ridotte a una radice, in modo tale da poter trovare una corrispondenza indipendentemente dai tempi verbali.

Gli analizzatori linguistici convertono un input di testo nella forma primitiva o radice, efficace per l'archiviazione e il recupero delle informazioni. La conversione avviene durante l'indicizzazione, in fase di compilazione dell'indice, e di nuovo durante la ricerca in fase di lettura dell'indice. È molto più probabile riuscire a ottenere i risultati di ricerca previsti usando lo stesso analizzatore di testo per entrambe le operazioni.

## <a name="default-analyzer"></a>Analizzatore predefinito  

Ricerca di Azure usa l'[analizzatore Apache Lucene Standard (lucene standard)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html), come impostazione predefinita, che suddivide il testo in elementi sulla base delle regole di ["segmentazione del testo Unicode"](https://unicode.org/reports/tr29/). L'analizzatore standard, inoltre, converte tutti i caratteri nel rispettivo formato minuscolo. I documenti indicizzati e i termini di ricerca vengono sottoposti ad analisi durante l'indicizzazione e l'elaborazione delle query.  

Viene usato automaticamente in ogni campo ricercabile. È possibile eseguire l'override del valore predefinito nei singoli campi. In alternativa, si può usare un [analizzatore del linguaggio](index-add-language-analyzers.md), un [analizzatore personalizzato](index-add-custom-analyzers.md) o un analizzatore predefinito incluso nell'[elenco di analizzatori disponibili](index-add-custom-analyzers.md#AnalyzerTable).


## <a name="types-of-analyzers"></a>Tipi di analizzatori

Nell'elenco seguente sono descritti gli analizzatori disponibili in Ricerca di Azure.

| Categoria | Description |
|----------|-------------|
| [Analizzatore Lucene standard](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Default. Non è necessaria alcun specifica o configurazione. Questo uso generale funziona correttamente per la maggior parte dei linguaggi e degli scenari.|
| Analizzatori predefiniti | Offerto come prodotto finito, da usare così com'è. <br/>Ne esistono due tipi: specializzato e del linguaggio. Ciò che li rende "predefiniti" è il fatto di farvi riferimento in base al nome, senza alcuna configurazione né personalizzazione. <br/><br/>Gli [analizzatori specializzati (indipendenti dal linguaggio)](index-add-custom-analyzers.md#AnalyzerTable) vengono usati quando gli input di testo richiedono un'elaborazione minima o specializzata. Gli analizzatori predefiniti indipendenti dal linguaggio includono **Asciifolding**, **Keyword**, **Pattern**, **Simple**, **Stop** e **Whitespace**.<br/><br/>Gli [analizzatori del linguaggio](index-add-language-analyzers.md) vengono usati quando è necessario un supporto linguistico avanzato per i singoli linguaggi. Ricerca di Azure supporta 35 analizzatori di linguaggi Lucene e 50 analizzatori di elaborazione del linguaggio naturale Microsoft. |
|[Analizzatori personalizzati](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Si riferisce a una configurazione definita dall'utente di una combinazione di elementi esistenti, costituiti da un tokenizer (obbligatorio) e da filtri facoltativi (char o token).|

Alcuni analizzatori predefiniti, ad esempio, **Pattern** o **Stop**, supportano un insieme limitato di opzioni di configurazione. Per impostare queste opzioni, creare un analizzatore personalizzato, costituito dall'analizzatore predefinito e una delle opzioni alternative documentate nelle [Informazioni di riferimento sugli analizzatori predefiniti](index-add-custom-analyzers.md#AnalyzerTable). Come per qualsiasi configurazione personalizzata, attribuire un nome alla nuova configurazione, ad esempio *myPatternAnalyzer*, per distinguerla dall'analizzatore Pattern Lucene.

## <a name="how-to-specify-analyzers"></a>Come specificare gli analizzatori

1. (solo per gli analizzatori personalizzati) Creare una sezione denominata **analizzatore** nella definizione di indice. Per altre informazioni, vedere [Creare un indice](https://docs.microsoft.com/rest/api/searchservice/create-index) e [Aggiungere analizzatori personalizzati](index-add-custom-analyzers.md).

2. In una [definizione di campo](https://docs.microsoft.com/rest/api/searchservice/create-index) nell'indice impostare il campo della proprietà **analizzatore** sul nome di un analizzatore di destinazione (ad esempio, `"analyzer" = "keyword"`. I valori validi includono il nome di un analizzatore predefinito, un analizzatore del linguaggio o un analizzatore personalizzato definito anche nello schema di indice. Pianificare l'assegnazione dell'analizzatore in fase di definizione dell'indice prima che l'indice venga creato nel servizio.

3. Invece di una proprietà **analizzatore**, è anche possibile impostare diversi analizzatori per l'indicizzazione e l'esecuzione di query usando i parametri di campo **indexAnalyzer** e **searchAnalyzer**. Usare analizzatori diversi per il recupero e la preparazione dei dati se una di tali attività necessitasse di una specifica trasformazione non richiesta dall'altro.

Non è consentita l'assegnazione di **analizzatore** oppure **indexAnalyzer** a un campo che è già stato creato fisicamente. Per eventuali chiarimenti, esaminare la tabella seguente per una suddivisione di quali azioni richiedono la ricompilazione e perché.
 
 | Scenario | Impatto | Procedure |
 |----------|--------|-------|
 | Aggiungere un nuovo campo | Minimo | Se il campo non esiste ancora nello schema, non occorre alcuna revisione perché il campo non è ancora presente fisicamente nell'indice. È possibile usare [Aggiornare un indice](https://docs.microsoft.com/rest/api/searchservice/update-index) per aggiungere un nuovo campo a un indice esistente, e [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) per compilarlo.|
 | Aggiungere un **analizzatore** o **indexAnalyzer** a un campo indicizzato esistente. | [rebuild](search-howto-reindex.md) | L'indice invertito per il campo deve essere ricreato da zero e il contenuto per questi campi deve essere reindicizzato. <br/> <br/>Per gli indici in fase di sviluppo, [eliminare](https://docs.microsoft.com/rest/api/searchservice/delete-index) e [creare](https://docs.microsoft.com/rest/api/searchservice/create-index) l'indice per selezionare la nuova definizione di campo. <br/> <br/>Per gli indici nell'ambiente di produzione, è possibile posticipare la ricompilazione mediante la creazione di un nuovo campo per fornire la definizione modificata e iniziare a usarla al posto di quella precedente. Usare [Aggiornare un indice](https://docs.microsoft.com/rest/api/searchservice/update-index) per incorporare il nuovo campo e [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) per compilarlo. In un secondo momento, nell'ambito della manutenzione pianificata dell'indice, sarà possibile pulire l'indice per rimuovere i campi obsoleti. |

## <a name="when-to-add-analyzers"></a>Quando aggiungere analizzatori

Il momento migliore per aggiungere e assegnare gli analizzatori è durante lo sviluppo attivo, quando eliminare e ricreare gli indici è routine.

Durante la solidificazione di una definizione di indice, è possibile aggiungere nuovi costrutti di analisi a un indice, ma è necessario passare il flag **allowIndexDowntime** a [Aggiornare un indice](https://docs.microsoft.com/rest/api/searchservice/update-index) se si desidera evitare questo errore:

*"L'aggiornamento dell'indice non è consentito perché potrebbe causare tempi di inattività. Per aggiungere nuovi analizzatori, tokenizer, filtri di token o filtri di tipo carattere a un indice esistente, impostare il parametro di query ' allowIndexDowntime ' su' true ' nella richiesta di aggiornamento dell'indice. Si noti che questa operazione inserirà l'indice in modalità offline per almeno alcuni secondi, causando un errore di indicizzazione e richieste di query. Le prestazioni e la disponibilità di scrittura dell'indice possono essere compromesse per alcuni minuti dopo l'aggiornamento dell'indice o più a lungo per gli indici di grandi dimensioni.*

Lo stesso vale quando si assegna un analizzatore a un campo. Un analizzatore è parte integrante della definizione del campo, pertanto è possibile aggiungerlo solo quando viene creato il campo. Se si desidera aggiungere gli analizzatori ai campi esistenti, si dovrà [rilasciare e ricompilare](search-howto-reindex.md) l'indice, oppure aggiungere un nuovo campo con l'analizzatore da usare.

Come accennato, fa eccezione la variante **searchAnalyzer**. Dei tre modi per specificare analizzatori (**analizzatore**, **indexAnalyzer**, **searchAnalyzer**), solo l'attributo **searchAnalyzer** può essere modificato in un campo esistente.

## <a name="recommendations-for-working-with-analyzers"></a>Consigli per l'utilizzo degli analizzatori

Questa sezione offre consigli su come usare gli analizzatori.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Usare un unico analizzatore in lettura-scrittura, a meno che non si disponga di requisiti specifici

Ricerca di Azure consente di specificare diversi analizzatori per l'indicizzazione e la ricerca tramite i parametri di campo aggiuntivi **indexAnalyzer** e **searchAnalyzer**. Se non è specificato, l'analizzatore impostato con la proprietà **analizzatore** viene usato per l'indicizzazione e la ricerca. Se il parametro `analyzer` non è specificato, verrà usato l'analizzatore Lucene standard.

Una regola generale consiste nell'usare lo stesso analizzatore sia per l'indicizzazione che per l'esecuzione di query, salvo diversamente indicato da requisiti specifici. Assicurarsi di eseguire test approfonditi. Quando l'elaborazione del testo è diversa in fase di indicizzazione e ricerca, si corre il rischio che i termini di query e i termini indicizzati non corrispondano quando le configurazioni degli analizzatori di ricerca e indicizzazione non sono allineate.

### <a name="test-during-active-development"></a>Test durante lo sviluppo attivo

L'override dell'analizzatore standard richiede una ricompilazione dell'indice. Se possibile, stabilire quali analizzatori usare durante la fase di sviluppo attivo, prima di passare un indice nell'ambiente di produzione.

### <a name="inspect-tokenized-terms"></a>Controllare i termini in formato token

Se la ricerca non restituisce i risultati previsti, è molto probabile che esistano discrepanze a livello di token tra i termini di input nella query e i termini in formato token nell'indice. Se il token non corrispondono, non vengono individuate corrispondenze. Per esaminare l'output del tokenizer, è consigliabile usare l'[API di analisi](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) come strumento di indagine. La risposta è costituita da token, generati da un analizzatore specifico.

<a name="examples"></a>

## <a name="rest-examples"></a>Esempi REST

Gli esempi seguenti mostrano le definizioni degli analizzatori per alcuni scenari chiave.

+ [Esempio di analizzatore personalizzato](#Custom-analyzer-example)
+ [Esempio dell'assegnazione degli analizzatori a un campo](#Per-field-analyzer-assignment-example)
+ [Combinazioni di analizzatori per indicizzazione e ricerca](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Esempio di analizzatore del linguaggio](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Esempio di analizzatore personalizzato

Questo esempio illustra una definizione di analizzatore con opzioni personalizzate. Le opzioni personalizzate per filtri char, tokenizer e filtri token vengono specificate separatamente come costrutti denominati a cui si fa riferimento nella definizione dell'analizzatore. Gli elementi predefiniti vengono usati as-is, facendo riferimento a essi semplicemente in base al nome.

Passaggi di questo esempio:

* Gli analizzatori sono una proprietà della classe di campo per un campo ricercabile.
* Un analizzatore personalizzato fa parte della definizione di un indice. Esso può essere parzialmente personalizzato (ad esempio, tramite la personalizzazione di una singola opzione in un filtro) o personalizzato in più posizioni.
* In questo caso, l'analizzatore personalizzato è "my_analyzer", che a sua volta usa il tokenizer standard personalizzato "my_standard_tokenizer" e due filtri token: un filtro asciifolding minuscolo e uno personalizzato "my_asciifolding".
* Definisce inoltre due filtri di caratteri personalizzati: "map_dash" e "remove_whitespace". Il primo sostituisce tutti i trattini con caratteri di sottolineatura, mentre il secondo rimuove tutti gli spazi. Nelle regole di mapping gli spazi devono essere codificati con UTF-8. I filtri di caratteri vengono applicati prima della tokenizzazione e influiranno sui token risultanti (le interruzioni standard dei tokenizer sui trattini e gli spazi, ma non sui caratteri di sottolineatura).

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
~~~~

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>Esempio di assegnazione dell'analizzatore in base al campo

L'analizzatore Standard è l'impostazione predefinita. Si supponga di volere sostituire l'elemento predefinito con un analizzatore predefinito diverso, ad esempio l'analizzatore Pattern. Se non si impostano delle opzioni personalizzate, occorre specificarlo in base al nome nella definizione del campo.

L'elemento "analizzatore" sostituisce l'analizzatore Standard campo per campo. Non vi è alcun override globale. In questo esempio, `text1` usa l'analizzatore Pattern, mentre `text2`, che non specifica un analizzatore, usa l'elemento predefinito.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Combinazioni di analizzatori per le operazioni di indicizzazione e ricerca

Le API includono attributi di indice aggiuntivi per specificare analizzatori diversi per l'indicizzazione e la ricerca. Gli attributi **searchAnalyzer** e **indexAnalyzer** devono essere specificati come coppia, sostituendo il singolo attributo **analizzatore**.


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>Esempio di analizzatore del linguaggio

I campi contenenti stringhe in diverse lingue possono utsare un analizzatore del linguaggio, mentre altri campi mantengono il valore predefinito (o usano altri analizzatori predefiniti o personalizzati). Se si usa un analizzatore del linguaggio, questo deve essere usato per le operazioni di indicizzazione e ricerca. I campi che usano un analizzatore del linguaggio non possono disporre di analizzatori diversi per indicizzazione e ricerca.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="c-examples"></a>C#esempi

Se si usano gli esempi di codice di .NET SDK, è possibile aggiungere questi esempi per usare o configurare gli analizzatori.

+ [Assegnare un analizzatore incorporato](#Assign-a-language-analyzer)
+ [Configurare un analizzatore](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Assegnare un analizzatore del linguaggio

Qualsiasi analizzatore usato così com'è, senza configurazione, viene specificato in una definizione di campo. Non è necessario creare un costrutto dell'analizzatore. 

Questo esempio Mostra come assegnare gli analizzatori di Microsoft inglese e francese ai campi di descrizione. Si tratta di un frammento di codice tratto da una definizione più ampia dell'indice degli alberghi, creando usando la classe dell'hotel nel file hotels.cs dell'esempio [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) .

Call [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet), che specifica il tipo [analyzername](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) che fornisce un analizzatore di testo supportato in ricerca di Azure.

```csharp
    public partial class Hotel
    {
       . . . 

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        [JsonProperty("description")]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("description_fr")]
        public string DescriptionFr { get; set; }

      . . .
    }
```
<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>Definire un analizzatore personalizzato

Quando è necessaria la personalizzazione o la configurazione, sarà necessario aggiungere un costrutto dell'analizzatore a un indice. Una volta definita, è possibile aggiungerla alla definizione del campo, come illustrato nell'esempio precedente.

Creare un oggetto [CustomAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) . Per altri esempi, vedere [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs).

```csharp
{
   var definition = new Index()
   {
         Name = "hotels",
         Fields = FieldBuilder.BuildForType<Hotel>(),
         Analyzers = new[]
            {
               new CustomAnalyzer()
               {
                     Name = "url-analyze",
                     Tokenizer = TokenizerName.UaxUrlEmail,
                     TokenFilters = new[] { TokenFilterName.Lowercase }
               }
            },
   };

   serviceClient.Indexes.Create(definition);
```

## <a name="next-steps"></a>Passaggi successivi

+ Esaminare la descrizione dettagliata contenuta in [Funzionamento della ricerca full-text in Ricerca di Azure](search-lucene-query-architecture.md). Questo articolo usa alcuni esempi per illustrare i comportamenti che potrebbero sembrare poco plausibili in un primo momento.

+ Provare la sintassi di query aggiuntiva dalla sezione di esempio [Search Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) (Ricerca di documenti) o da [Sintassi di query semplice](query-simple-syntax.md) in Esplora ricerche nel portale.

+ Informazioni su come applicare [analizzatori lessicali specifici della lingua](index-add-language-analyzers.md).

+ [Configurare gli analizzatori personalizzati](index-add-custom-analyzers.md) per un'elaborazione minima o specializzata per settori specifici.

## <a name="see-also"></a>Vedi anche

 [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) (API REST di Ricerca di documenti) 

 [Sintassi di query semplice](query-simple-syntax.md) 

 [Full Lucene query syntax](query-lucene-syntax.md) (Sintassi di query completa Lucene) 
 
 [Gestire i risultati della ricerca](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
