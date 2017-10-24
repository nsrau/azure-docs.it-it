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
ms.date: 09/11/2017
ms.author: heidist
ms.openlocfilehash: f9e456a57bae4aab25ef85c93132308f2c442c0b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="analyzers-in-azure-search"></a>Analizzatori in Ricerca di Azure

Un *analizzatore* è un componente della [ricerca full-text](search-lucene-query-architecture.md) responsabile dell'elaborazione di testo in stringhe di query e documenti indicizzati. Durante l'indicizzazione, un analizzatore trasforma il testo in *token*, che vengono scritti come *termini in formato token* nell'indice. Durante la ricerca, un analizzatore esegue le stesse trasformazioni sui *termini di query*, offrendo la base per i termini corrispondenti nell'indice.

Le trasformazioni seguenti sono tipiche durante l'analisi:

+ Parole non essenziali (parole non significative) e punteggiatura vengono rimosse.
+ Frasi e parole sillabate vengono divise in parti diverse.
+ Le parole maiuscole vengono modificate in parole minuscole.
+ Le parole vengono ridotte a una radice, in modo tale da poter trovare una corrispondenza indipendentemente dai tempi verbali.

Ricerca di Azure usa l'[analizzatore Lucene standard](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) come impostazione predefinita. È possibile eseguire l'override del valore predefinito nei singoli campi. Questo articolo illustra le varie opzioni disponibili e le procedure consigliate per l'analisi personalizzata. Fornisce anche alcune configurazioni di esempio per i principali scenari.

## <a name="supported-analyzers"></a>Analizzatori supportati

Nell'elenco seguente sono descritti gli analizzatori supportati in Ricerca di Azure.

| Categoria | Descrizione |
|----------|-------------|
| [Analizzatore Lucene standard](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Default. Non è necessaria alcun specifica o configurazione. Questo uso generale funziona correttamente per la maggior parte dei linguaggi e degli scenari.|
| Analizzatori predefiniti | Offerto come prodotto finito, da usare così com'è, con personalizzazione limitata. <br/>Ne esistono due tipi: specializzato e del linguaggio. Ciò che li rende "predefiniti" è il fatto di farvi riferimento in base al nome, senza alcuna personalizzazione. <br/><br/>Gli [analizzatori specializzati (indipendenti dal linguaggio)](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable) vengono usati quando gli input di testo richiedono un'elaborazione minima o specializzata. Gli analizzatori predefiniti indipendenti dal linguaggio includono **Asciifolding**, **Keyword**, **Pattern**, **Simple**, **Stop** e **Whitespace**.<br/><br/>Gli [analizzatori del linguaggio](https://docs.microsoft.com/rest/api/searchservice/language-support) vengono usati quando è necessario un supporto linguistico avanzato per i singoli linguaggi. Ricerca di Azure supporta 35 analizzatori di linguaggi Lucene e 50 analizzatori di elaborazione del linguaggio naturale Microsoft. |
|[Analizzatori personalizzati](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Configurazione definita dall'utente di una combinazione di elementi esistenti, costituiti da un tokenizer (obbligatorio) e da filtri facoltativi (char o token).|

È possibile personalizzare un analizzatore predefinito, ad esempio **Pattern** o **Stop**, per l'uso di opzioni alternative documentate nella [documentazione di riferimento per gli analizzatori predefiniti](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable). Solo alcuni analizzatori predefiniti prevedono opzioni alternative che è possibile impostare. Come per qualsiasi personalizzazione, attribuire un nome alla nuova configurazione, ad esempio *myPatternAnalyzer*, per distinguerla dall'analizzatore Pattern Lucene.

## <a name="how-to-specify-analyzers"></a>Come specificare gli analizzatori

1. (solo per gli analizzatori personalizzati) Creare una sezione **analyzer** nella definizione di indice. Per altre informazioni, vedere [Creare un indice](https://docs.microsoft.com/rest/api/searchservice/create-index) e [Analizzatori personalizzati > Crea](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer).

2. In una [definizione di campo](https://docs.microsoft.com/rest/api/searchservice/create-index) nell'indice impostare la proprietà **analyzer** sul nome di un analizzatore di destinazione (ad esempio, `"analyzer" = "keyword"`). I valori validi includono il nome di un analizzatore predefinito, un analizzatore del linguaggio o un analizzatore personalizzato definito anche nello schema di indice.

3. Invece di una proprietà **analyzer**, è anche possibile impostare diversi analizzatori per l'indicizzazione e l'esecuzione di query usando i parametri di campo **indexAnalyzer** e **searchAnalyzer`**. 

3. L'aggiunta di un analizzatore a una definizione di campo comporta un'operazione di scrittura nell'indice. Se si aggiunge una proprietà **analyzer** a un indice esistente, considerare i passaggi seguenti:
 
 | Scenario | Passi |
 |----------|-------|
 | Aggiungere un nuovo campo | Se il campo non esiste ancora nello schema, non è necessario eseguire revisioni del campo. L'analisi del testo viene eseguita quando si aggiungono o aggiornano documenti che forniscono contenuto per il nuovo campo. Usare la richiesta di [aggiornamento dell'indice](https://docs.microsoft.com/rest/api/searchservice/update-index) e [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) per questa attività.|
 | Aggiungere un analizzatore a un campo indicizzato esistente. | L'indice invertito per il campo deve essere ricreato da zero e il contenuto del documento per questi campi deve essere reindicizzato. <br/> <br/>Per gli indici in fase di sviluppo, [eliminare](https://docs.microsoft.com/rest/api/searchservice/delete-index) e [creare](https://docs.microsoft.com/rest/api/searchservice/create-index) l'indice per selezionare la nuova definizione di campo. <br/> <br/>Per gli indici nell'ambiente di produzione, è necessario creare un nuovo campo per fornire la definizione modificata e iniziare a usarla. Usare la richiesta di [aggiornamento dell'indice](https://docs.microsoft.com/rest/api/searchservice/update-index) e [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) per incorporare il nuovo campo. In un secondo momento, nell'ambito della manutenzione pianificata dell'indice, sarà possibile pulire l'indice per rimuovere i campi obsoleti. |

## <a name="best-practices"></a>Procedure consigliate

Questa sezione offre consigli su come usare gli analizzatori.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Usare un unico analizzatore in lettura-scrittura, a meno che non si disponga di requisiti specifici

Ricerca di Azure consente di specificare diversi analizzatori per l'indicizzazione e la ricerca tramite parametri di campo aggiuntivi `indexAnalyzer` e `searchAnalyzer`. Se non è specificato, l'analizzatore impostato con la proprietà `analyzer` viene usato per l'indicizzazione e la ricerca. Se il parametro `analyzer` non è specificato, verrà usato l'analizzatore Lucene standard.

Una regola generale consiste nell'usare lo stesso analizzatore sia per l'indicizzazione che per l'esecuzione di query, salvo diversamente indicato da requisiti specifici. Assicurarsi di eseguire test approfonditi. Quando l'elaborazione del testo è diversa in fase di indicizzazione e ricerca, si corre il rischio che i termini di query e i termini indicizzati non corrispondano quando le configurazioni degli analizzatori di ricerca e indicizzazione non sono allineate.

### <a name="test-during-active-development"></a>Test durante lo sviluppo attivo

L'override dell'analizzatore standard richiede una ricompilazione dell'indice. Se possibile, stabilire quali analizzatori usare durante la fase di sviluppo attivo, prima di passare un indice nell'ambiente di produzione.

### <a name="compare-analyzers-side-by-side"></a>Confronto degli analizzatori side-by-side

Si consiglia di usare il comando [Analyze API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) (Analizza API). La risposta è costituita da token, generati da un analizzatore specifico per il testo fornito. 

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

Le API includono attributi di indice aggiuntivi per specificare analizzatori diversi per l'indicizzazione e la ricerca. Gli attributi `searchAnalyzer` e `indexAnalyzer` devono essere specificati come coppia, sostituendo l'attributo singolo `analyzer`.


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
