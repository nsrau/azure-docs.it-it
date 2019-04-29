---
title: Esplora ricerche nel portale di Azure per le query dei dati - Ricerca di Azure
description: Usare gli strumenti del portale di Azure come Esplora ricerche per eseguire query negli indici in Ricerca di Azure. Immettere termini di ricerca o stringhe di ricerca complete con sintassi avanzata.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 85e574a56380384b10d0916385a8816fd26c2eeb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61292397"
---
# <a name="search-explorer-for-querying-data-in-azure-search"></a>Esplora ricerche nel portale di Azure per le query dei dati in Ricerca di Azure 

Questo articolo illustra come eseguire query in un indice di Ricerca di Azure esistente usando **Esplora ricerche** nel portale di Azure. È possibile usare Esplora ricerche per inviare stringhe di query Lucene semplici o complete a qualsiasi indice nel servizio. 

   ![Comando Esplora ricerche nel portale](./media/search-explorer/search-explorer-cmd2.png "Comando Esplora ricerche nel portale")


Per informazioni su come iniziare, vedere [Avviare Esplora ricerche](#start-search-explorer).

## <a name="basic-search-strings"></a>Stringhe di ricerca di base

Gli esempi seguenti presuppongono che venga usato l'indice di esempio realestate predefinito. Per informazioni sulla creazione dell'indice, vedere [Avvio rapido: Importare, indicizzare ed eseguire query nel portale di Azure](search-get-started-portal.md).

### <a name="example-1---empty-search"></a>Esempio 1: ricerca vuota

Per un primo esame del contenuto, eseguire una ricerca vuota facendo clic su **Cerca** senza specificare termini. Una ricerca vuota è utile come prima query perché restituisce interi documenti ed è quindi possibile esaminarne la composizione. In una ricerca vuota non è previsto alcun ordine di classificazione e i documenti vengono restituiti in ordine arbitrario (`"@search.score": 1` per tutti i documenti). Per impostazione predefinita, in una richiesta di ricerca vengono restituiti 50 documenti.

La sintassi equivalente per una ricerca vuota è `*` o `search=*`.

   ```Input
   search=*
   ```

   **Risultati**
   
   ![Esempio di query vuota](./media/search-explorer/search-explorer-example-empty.png "Esempio di query vuota o non qualificata")

### <a name="example-2---free-text-search"></a>Esempio 2: ricerca di testo libero

Le query in formato libero, con o senza operatori, sono utili per la simulazione di query definite dall'utente inviate da un'app personalizzata a Ricerca di Azure. Si noti che quando si specificano i termini o le espressioni di query, entra in gioco la classificazione della ricerca. L'esempio seguente illustra una ricerca di testo libero.

   ```Input
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Risultati**

   È possibile usare CTRL-F per cercare nei risultati determinati termini a cui si è interessati.

   ![Esempio di query in formato testo libero](./media/search-explorer/search-explorer-example-freetext.png "Esempio di query in formato testo libero")

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

   ![Esempio di limitazione dei campi](./media/search-explorer/search-explorer-example-selectfield.png "Limitazione dei campi nei risultati della ricerca")

### <a name="example-5---return-next-batch-of-results"></a>Esempio 5: restituzione del batch di risultati successivo

Ricerca di Azure restituisce le prime 50 corrispondenze in base ai criteri di classificazione della ricerca. Per ottenere il set successivo di documenti corrispondenti, aggiungere **$top=100,&$skip=50** per portare il set di risultati a 100 documenti (il valore predefinito è 50 e il valore massimo è 1000), ignorando i primi 50 documenti. Ricordare che è necessario specificare i criteri di ricerca, ad esempio un termine o un'espressione di query, per ottenere risultati classificati. Si noti che i punteggi di ricerca diminuiscono man mano che si scorre l'elenco dei risultati della ricerca.

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100,&$skip=50
   ```

   **Risultati**

   ![Batch di risultati della ricerca](./media/search-explorer/search-explorer-example-topskip.png "Successivo batch di risultati della ricerca")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Espressioni di filtro (maggiore di, minore di, uguale a)

Usare il parametro **$filter** quando si vogliono specificare criteri precisi anziché eseguire una ricerca di testo libero. Questo esempio cerca gli appartamenti con più di tre camere da letto: `search=seattle condo&$filter=beds gt 3&$count=true`

   ![Espressione di filtro](./media/search-explorer/search-explorer-example-filter.png "Criteri in base ai quali applicare il filtro")

## <a name="order-by-expressions"></a>Espressioni order-by

Aggiungere **$orderby** per ordinare i risultati in base a un campo in più rispetto al punteggio di ricerca. Un'espressione di esempio che può essere usata per testare questo comportamento è `search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc`

   ![Espressione order-by](./media/search-explorer/search-explorer-example-ordery.png "Modificare l'ordinamento")

Le espressioni **$filter** e **$orderby** sono entrambe strutture OData. Per altre informazioni, vedere l'articolo relativo alla [sintassi OData per i filtri](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="how-to-start-search-explorer"></a>Come avviare Esplora ricerche

1. Nel [portale di Azure](https://portal.azure.com) aprire la pagina per la ricerca dei servizi dal dashboard o [trovare il servizio](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nell'elenco.

2. Nella pagina di panoramica del servizio fare clic su **Esplora ricerche**.

   ![Comando Esplora ricerche nel portale](./media/search-explorer/search-explorer-cmd2.png "Comando Esplora ricerche nel portale")

3. Selezionare l'indice su cui eseguire una query.

   ![Selezionare l'indice per la query](./media/search-explorer/search-explorer-changeindex-se2.png "Selezionare l'indice")

4. Facoltativamente, impostare la versione dell'API. Per impostazione predefinita, è selezionata la versione corrente dell'API disponibile a livello generale, ma è possibile scegliere un'anteprima o un'API precedente se la sintassi da usare dipende dalla versione.

5. Dopo aver selezionato l'indice e la versione dell'API, immettere termini di ricerca o espressioni di query complete nella barra di ricerca e fare clic su **Cerca** per eseguire la query.

   ![Immettere termini di ricerca e fare clic su Cerca](./media/search-explorer/search-explorer-query-string-example.png "Immettere termini di ricerca e fare clic su Cerca")

Suggerimenti per la ricerca in **Esplora ricerche**:

+ I risultati vengono restituiti come documenti JSON dettagliati, in modo che sia possibile visualizzare la struttura e il contenuto del documenti nel complesso. È possibile usare espressioni di query, come illustrato negli esempi, per limitare i campi restituiti.

+ I documenti sono costituiti da tutti i campi contrassegnati come **recuperabili** nell'indice. Per visualizzare gli attributi dell'indice nel portale, fare clic su *realestate-us-sample* nell'elenco **Indici** nella pagina di panoramica della ricerca.

+ Le query in formato libero, simili alle stringhe che è possibile immettere in un Web browser commerciale, sono utili per testare un'esperienza utente finale. Si supponga ad esempio di usare l'indice realestate predefinito. È possibile immettere "Seattle apartment lake washington" e quindi premere CTRL-F per trovare i termini nei risultati della ricerca. 

+ Le espressioni di query e filtro devono essere riportate in una sintassi supportata da Ricerca di Azure. Per impostazione predefinita viene usata una [sintassi semplice](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), ma è possibile usare la sintassi [Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) per eseguire query più avanzate. Le [espressioni di filtro](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) sono scritte usando la sintassi OData.


## <a name="next-steps"></a>Passaggi successivi

Le risorse seguenti offrono altri esempi e informazioni relativi alla sintassi di query.

 + [Sintassi di query semplice](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Sintassi di query Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Esempi di query Lucene](search-query-lucene-examples.md) 
 + [Sintassi delle espressioni filtro OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 