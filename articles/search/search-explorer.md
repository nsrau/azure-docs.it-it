---
title: Strumento di query di Esplora ricerche in portale di Azure
titleSuffix: Azure Cognitive Search
description: Esplora ricerche è integrato nella portale di Azure, utile per l'esplorazione del contenuto e la convalida delle query in Azure ricerca cognitiva. Immettere stringhe per la ricerca di termini o frasi o espressioni di ricerca complete con sintassi avanzata.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ff2986c4e90cb997df250d647bdfbd068d70e51f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112106"
---
# <a name="use-search-explorer-in-the-azure-portal-for-querying-documents-in-azure-cognitive-search"></a>Usare Esplora ricerche nella portale di Azure per eseguire query sui documenti in Azure ricerca cognitiva 

Questo articolo illustra come eseguire una query su un indice di Azure ricerca cognitiva esistente usando **Esplora ricerche** nel portale di Azure. È possibile avviare Esplora ricerche dalla barra dei comandi per inviare espressioni di query Lucene semplici o complete a qualsiasi indice esistente nel servizio. 

   ![Comando Esplora ricerche nel portale](./media/search-explorer/search-explorer-cmd2.png "Comando Esplora ricerche nel portale")

## <a name="basic-search-strings"></a>Stringhe di ricerca di base

Gli esempi seguenti presuppongono l'indice di esempio Real Immobiliare incorporato. È possibile creare questo indice usando la procedura guidata Importa dati nel portale, scegliendo **esempi** come origine dati.

### <a name="example-1---empty-search"></a>Esempio 1: ricerca vuota

Per un primo esame del contenuto, eseguire una ricerca vuota facendo clic su **Cerca** senza specificare termini. Una ricerca vuota è utile come prima query perché restituisce interi documenti ed è quindi possibile esaminarne la composizione. In una ricerca vuota non è previsto alcun ordine di classificazione e i documenti vengono restituiti in ordine arbitrario (`"@search.score": 1` per tutti i documenti). Per impostazione predefinita, in una richiesta di ricerca vengono restituiti 50 documenti.

La sintassi equivalente per una ricerca vuota è `*` o `search=*`.

   ```Input
   search=*
   ```

   **Risultati**
   
   ![Esempio di query vuota](./media/search-explorer/search-explorer-example-empty.png "Esempio di query non qualificata o vuota")

### <a name="example-2---free-text-search"></a>Esempio 2: ricerca di testo libero

Le query in formato libero, con o senza operatori, sono utili per simulare query definite dall'utente inviate da un'app personalizzata ad Azure ricerca cognitiva. Si noti che quando si specificano i termini o le espressioni di query, entra in gioco la classificazione della ricerca. L'esempio seguente illustra una ricerca di testo libero.

   ```Input
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Risultati**

   È possibile usare CTRL-F per cercare nei risultati determinati termini a cui si è interessati.

   ![Esempio di query di testo libero](./media/search-explorer/search-explorer-example-freetext.png "Esempio di query di testo libero")

### <a name="example-3---count-of-matching-documents"></a>Esempio 3: numero di documenti corrispondenti 

Aggiungere **$count** per ottenere il numero di corrispondenze trovate in un indice. Nel caso di una ricerca vuota, il conteggio corrisponde al numero totale di documenti nell'indice. Nel caso di una ricerca qualificata, è il numero di documenti corrispondenti all'input della query.

   ```Input1
   $count=true
   ```
   **Risultati**

   ![Esempio di conteggio dei documenti](./media/search-explorer/search-explorer-example-count.png "Conteggio dei documenti corrispondenti nell'indice")

### <a name="example-4---restrict-fields-in-search-results"></a>Esempio 4: limitazione dei campi nei risultati della ricerca

Aggiungere **$select** per limitare i risultati ai campi denominati in modo esplicito per migliorare la leggibilità dell'output in **Esplora ricerche**. Per mantenere la stringa di ricerca e **$count=true**, anteporre il prefisso **&** agli argomenti. 

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Risultati**

   ![Esempio di campi limite](./media/search-explorer/search-explorer-example-selectfield.png "Limitare i campi nei risultati della ricerca")

### <a name="example-5---return-next-batch-of-results"></a>Esempio 5: restituzione del batch di risultati successivo

Azure ricerca cognitiva restituisce le prime 50 corrispondenze in base al rango di ricerca. Per ottenere il set successivo di documenti corrispondenti, aggiungere **$top=100,&$skip=50** per portare il set di risultati a 100 documenti (il valore predefinito è 50 e il valore massimo è 1000), ignorando i primi 50 documenti. Ricordare che è necessario specificare i criteri di ricerca, ad esempio un termine o un'espressione di query, per ottenere risultati classificati. Si noti che i punteggi di ricerca diminuiscono man mano che si scorre l'elenco dei risultati della ricerca.

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Risultati**

   ![Risultati della ricerca batch](./media/search-explorer/search-explorer-example-topskip.png "Restituisce il batch successivo di risultati della ricerca")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Espressioni di filtro (maggiore di, minore di, uguale a)

Usare il parametro **$filter** quando si vogliono specificare criteri precisi anziché eseguire una ricerca di testo libero. Questo esempio cerca le camere maggiori di 3:

   ```Input
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Risultati**

   ![Espressione filtro](./media/search-explorer/search-explorer-example-filter.png "Filtra per criterio")

## <a name="order-by-expressions"></a>Espressioni order-by

Aggiungere **$orderby** per ordinare i risultati in base a un campo in più rispetto al punteggio di ricerca. Un'espressione di esempio che è possibile usare per testarla è la seguente:

   ```Input
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Risultati**

   ![Espressione OrderBy](./media/search-explorer/search-explorer-example-ordery.png "Modificare il tipo di ordinamento")

Le espressioni **$filter** e **$orderby** sono entrambe strutture OData. Per altre informazioni, vedere l'articolo relativo alla [sintassi OData per i filtri](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="how-to-start-search-explorer"></a>Come avviare Esplora ricerche

1. Nel [portale di Azure](https://portal.azure.com) aprire la pagina per la ricerca dei servizi dal dashboard o [trovare il servizio](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nell'elenco.

2. Nella pagina di panoramica del servizio fare clic su **Esplora ricerche**.

   ![Comando Esplora ricerche nel portale](./media/search-explorer/search-explorer-cmd2.png "Comando Esplora ricerche nel portale")

3. Selezionare l'indice su cui eseguire una query.

   ![Selezionare l'indice su cui eseguire la query](./media/search-explorer/search-explorer-changeindex-se2.png "Selezionare l'indice")

4. Facoltativamente, impostare la versione dell'API. Per impostazione predefinita, è selezionata la versione corrente dell'API disponibile a livello generale, ma è possibile scegliere un'anteprima o un'API precedente se la sintassi da usare dipende dalla versione.

5. Dopo aver selezionato l'indice e la versione dell'API, immettere termini di ricerca o espressioni di query complete nella barra di ricerca e fare clic su **Cerca** per eseguire la query.

   ![Immettere i termini di ricerca e fare clic su Cerca](./media/search-explorer/search-explorer-query-string-example.png "Immettere i termini di ricerca e fare clic su Cerca")

Suggerimenti per la ricerca in **Esplora ricerche**:

+ I risultati vengono restituiti come documenti JSON dettagliati, in modo che sia possibile visualizzare la struttura e il contenuto del documenti nel complesso. È possibile usare espressioni di query, come illustrato negli esempi, per limitare i campi restituiti.

+ I documenti sono costituiti da tutti i campi contrassegnati come **recuperabili** nell'indice. Per visualizzare gli attributi dell'indice nel portale, fare clic su *realestate-us-sample* nell'elenco **Indici** nella pagina di panoramica della ricerca.

+ Le query in formato libero, simili alle stringhe che è possibile immettere in un Web browser commerciale, sono utili per testare un'esperienza utente finale. Si supponga ad esempio di usare l'indice realestate predefinito. È possibile immettere "Seattle apartment lake washington" e quindi premere CTRL-F per trovare i termini nei risultati della ricerca. 

+ Le espressioni di query e di filtro devono essere articolate in una sintassi supportata da Azure ricerca cognitiva. Per impostazione predefinita viene usata una [sintassi semplice](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), ma è possibile usare la sintassi [Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) per eseguire query più avanzate. Le [espressioni di filtro](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) sono scritte usando la sintassi OData.


## <a name="next-steps"></a>Passaggi successivi

Le risorse seguenti offrono altri esempi e informazioni relativi alla sintassi di query.

 + [Sintassi di query semplice](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Sintassi di query Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Esempi di query Lucene](search-query-lucene-examples.md) 
 + [Sintassi delle espressioni filtro OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 
