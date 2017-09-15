---
title: Analizzatori in Ricerca di Azure | Documentazione Microsoft
description: Assegnare degli analizzatori ai campi di testo di ricerca in un indice per sostituire lo standard Lucene predefinito con alternative specifiche per lingua, predefinite o personalizzate.
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 09/03/2017
ms.author: heidist
ms.translationtype: HT
ms.sourcegitcommit: ce0189706a3493908422df948c4fe5329ea61a32
ms.openlocfilehash: 6e6c4491b8f66011340d1246495dbded7caf2903
ms.contentlocale: it-it
ms.lasthandoff: 09/05/2017

---

# <a name="analyzers-in-azure-search"></a>Analizzatori in Ricerca di Azure

Un *analizzatore* è un componente di [elaborazione della ricerca full-text](search-lucene-query-architecture.md) responsabile per le conversioni da testo a token per carichi di lavoro sia di indicizzazione che di query. Durante l'indicizzazione, un analizzatore trasforma il testo sotto forma di token, che vengono scritti in corrispondenza dell'indice. In fase di query, un analizzatore esegue le stesse trasformazioni (da testo in formato token), ma questa volta per operazioni di lettura durante le query. 

Le trasformazioni seguenti sono tipiche durante l'analisi:

+ Parole non essenziali (parole non significative) e punteggiatura vengono rimosse.
+ Frasi e termini con trattini sono suddivisi in parti di componente.
+ I termini sono in minuscolo.
+ Le parole vengono ridotte a una radice, in modo tale da poter trovare una corrispondenza indipendentemente dai tempi verbali.

Ricerca di Azure fornisce uno strumento di analisi predefinito. È possibile sostituirlo campo per campo con alcune opzioni alternative. Lo scopo di questo articolo è quello di descrivere la gamma di opzioni e le procedure consigliate per l'aggiunta di un analizzatore per le operazioni di ricerca. Vengono anche mostrate alcune configurazioni di esempio dell'analizzatore per scenari chiave.

## <a name="how-analysis-fits-into-full-text-search-processing"></a>Inserimento dell'analisi nell'elaborazione della ricerca full-text

Gli analizzatori operano su termini passati dal parser di query e restituiscono termini analizzati che vengono aggiunti a un oggetto di query ad albero.

 ![Diagramma dell'architettura della query di Lucene in Ricerca di Azure][1]

Gli analizzatori vengono usati solo per la query di un singolo termine o di una frase. Gli analizzatori non si usano per i tipi di query con termini incompleti: query di prefissi, query con caratteri jolly, query regex o query fuzzy. Per questi tipi di query, i termini vengono aggiunti direttamente alla struttura della query, ignorando la fase di analisi. L'unica trasformazione eseguita per i termini della query di queste tipologie è la conversione in lettere minuscole.

## <a name="supported-analyzers"></a>Analizzatori supportati

Nell'elenco seguente sono descritti gli analizzatori supportati in Ricerca di Azure.

| Categoria | Descrizione |
|----------|-------------|
| [Analizzatore Lucene standard](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Default. Viene usato automaticamente per l'indicizzazione e le query. Non è necessaria alcun specifica o configurazione. Questo uso generale funziona correttamente per la maggior parte dei linguaggi e degli scenari.|
| Analizzatori predefiniti | Offerto come prodotto finito, da usare così com'è, con personalizzazione limitata. <br/>Ne esistono due tipi: specializzato e del linguaggio. Ciò che li rende "predefiniti" è il fatto di farvi riferimento in base al nome, senza alcuna personalizzazione. <br/><br/>[Analizzatori specializzati (indipendenti dal linguaggio)](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable) per gli input di testo che richiedono un'elaborazione minima o specializzata. Gli analizzatori predefiniti indipendenti dal linguaggio includono **Asciifolding**, **Keyword**, **Pattern**, **Simple**, **Stop** e **Whitespace**.<br/><br/>Gli [analizzatori del linguaggio](https://docs.microsoft.com/rest/api/searchservice/language-support) forniscono un supporto linguistico avanzato per i singoli linguaggi. Ricerca di Azure supporta 35 analizzatori di linguaggi Lucene e 50 analizzatori di elaborazione del linguaggio naturale Microsoft. |
|[Analizzatori personalizzati](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Configurazione definita dall'utente di una combinazione di elementi esistenti, costituiti da un tokenizer (obbligatorio) e da filtri facoltativi (char o token).|

È possibile personalizzare un analizzatore predefinito, ad esempio **Pattern** o **Stop**, per l'uso di opzioni alternative documentate nella [documentazione di riferimento per gli analizzatori predefiniti](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable). Solo alcuni analizzatori predefiniti prevedono opzioni alternative che è possibile impostare. Come per qualsiasi personalizzazione, attribuire un nome alla nuova configurazione, ad esempio *myPatternAnalyzer*, per distinguerla dall'analizzatore Pattern Lucene.

## <a name="how-to-specify-analyzer"></a>Specificare un analizzatore

1. Per i soli analizzatori personalizzati, creare una definizione `analyzer` dell'indice. Per altre informazioni, vedere [Creare un indice](https://docs.microsoft.com/rest/api/searchservice/create-index) e [Analizzatori personalizzati > Crea](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer).

2. In ogni campo per il quale si desidera usare l'analizzatore, impostare la proprietà `analyzer` sul nome di un analizzatore di destinazione sulla [definizione di un campo dell'indice](https://docs.microsoft.com/rest/api/searchservice/create-index). I valori validi includono un analizzatore predefinito, un analizzatore del linguaggio o un analizzatore personalizzato definito in precedenza nello schema di indice.

 In alternativa, anziché una proprietà `analyzer`, è possibile impostare diversi analizzatori per l'indicizzazione e l'esecuzione di query usando i parametri di campo `indexAnalyzer` e `searchAnalyzer`. 

3. Ricompilare l'indice per richiamare i nuovi comportamenti di elaborazione del testo.

## <a name="best-practices"></a>Procedure consigliate

In questa sezione vengono forniti dei suggerimenti su come lavorare con gli analizzatori con maggiore efficienza.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Usare un unico analizzatore in lettura-scrittura, a meno che non si disponga di requisiti specifici

Ricerca di Azure consente di specificare diversi analizzatori per l'indicizzazione e la ricerca tramite parametri di campo aggiuntivi `indexAnalyzer` e `searchAnalyzer`. Se non è specificato, l'analizzatore impostato con la proprietà `analyzer` viene usato per l'indicizzazione e la ricerca. Se il parametro `analyzer` non è specificato, verrà usato l'analizzatore Lucene standard.

Una regola generale consiste nell'usare lo stesso analizzatore sia per l'indicizzazione che per l'esecuzione di query, salvo diversamente indicato da requisiti specifici. In genere, la procedura risulta più efficiente se l'analizzatore di creazione del token è lo stesso usato per trovare i token in un secondo momento durante la fase di query. 

### <a name="test-during-active-development"></a>Test durante lo sviluppo attivo

L'override dell'analizzatore standard richiede una ricompilazione dell'indice. Se possibile, stabilire quali analizzatori usare durante la fase di sviluppo attivo, prima di passare un indice nell'ambiente di produzione.

### <a name="compare-analyzers-side-by-side"></a>Confronto degli analizzatori side-by-side

Si consiglia di usare il comando [Analyze API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) (Analizza API). La risposta è costituita da termini in formato token, così come generati da un analizzatore specifico per il testo fornito. 

> [!Tip]
> Nella [Search Analyzer Demo](http://alice.unearth.ai/) viene illustrato un confronto side-by-side tra l'analizzatore Lucene standard, l'analizzatore di lingua inglese Lucene e il processore di lingua inglese di Microsoft. Per ogni input di ricerca fornito, i risultati di ogni analizzatore vengono visualizzati nei riquadri adiacenti.

## <a name="examples"></a>esempi

Gli esempi seguenti mostrano le definizioni degli analizzatori per alcuni scenari chiave.

<a name="Example1"></a>
### <a name="example-1-custom-options"></a>Esempio 1: opzioni personalizzate

Questo esempio illustra una definizione di analizzatore con opzioni personalizzate. Le opzioni personalizzate per filtri char, tokenizer e filtri token vengono specificate separatamente come costrutti denominati a cui si fa riferimento nella definizione dell'analizzatore. Gli elementi predefiniti vengono usati as-is, facendo riferimento a essi semplicemente in base al nome.

Passaggi di questo esempio:

* Gli analizzatori sono una proprietà della classe di campo per un campo ricercabile.
* Un analizzatore personalizzato fa parte della definizione di un indice. Esso può essere parzialmente personalizzato (ad esempio, tramite la personalizzazione di una singola opzione in un filtro) o personalizzato in più posizioni.
* In questo caso, l'analizzatore personalizzato è "my_analyzer", che a sua volta usa il tokenizer standard personalizzato "my_standard_tokenizer" e due filtri token: un filtro asciifolding minuscolo e uno personalizzato "my_asciifolding".
* Esso definisce anche un filtro char "map_dash" personalizzato per sostituire tutti i trattini con caratteri di sottolineatura prima della tokenizzazione (le interruzioni standard dei tokenizer sono dei trattini, ma non dei caratteri di sottolineatura).

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
              "map_dash"
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
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
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

<a name="Example2"></a>
### <a name="example-2-override-the-default-analyzer"></a>Esempio 2: eseguire l'Override dell'analizzatore predefinito

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

<a name="Example3"></a>
### <a name="example-3-different-analyzers-for-indexing-and-search-operations"></a>Esempio 3: analizzatori diversi per le operazioni di indicizzazione e ricerca

Le API di anteprima includono attributi di indice aggiuntivi per specificare analizzatori diversi per indicizzazione e ricerca. Gli attributi `searchAnalyzer` e `indexAnalyzer` devono essere specificati come coppia, sostituendo l'attributo singolo `analyzer`.


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

<a name="Example4"></a>
### <a name="example-4-language-analyzer"></a>Esempio 4: analizzatore del linguaggio

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
           "IndexAnalyzer":"whitespace",
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

## <a name="next-steps"></a>Passaggi successivi

+ Esaminare la descrizione dettagliata contenuta in [Funzionamento della ricerca full-text in Ricerca di Azure](search-lucene-query-architecture.md). Questo articolo usa alcuni esempi per illustrare i comportamenti che potrebbero sembrare poco plausibili in un primo momento.

+ Provare la sintassi di query aggiuntiva dalla sezione di esempio [Search Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) (Ricerca di documenti) o da [Sintassi di query semplice](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) in Esplora ricerche nel portale.

+ Informazioni su come applicare [analizzatori lessicali specifici della lingua](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Configurare gli analizzatori personalizzati](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) per un'elaborazione minima o specializzata per settori specifici.

+ [Fare un confronto degli analizzatori standard e in lingua inglese](http://alice.unearth.ai/) su riquadri adiacenti in questo sito Web demo. 

## <a name="see-also"></a>Vedere anche

 [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) (API REST di Ricerca di documenti) 

 [Sintassi di query semplice](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

 [Full Lucene query syntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) (Sintassi di query completa Lucene) 
 
 [Gestire i risultati della ricerca](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png

