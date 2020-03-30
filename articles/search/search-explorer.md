---
title: Strumento di query esplora ricerche nel portale di AzureSearch explorer query tool in Azure portal
titleSuffix: Azure Cognitive Search
description: In questa guida introduttiva del portale di Azure usare Esplora ricerche per apprendere la sintassi delle query, testare le espressioni di query o esaminare un documento di ricerca. Esplora ricerche esegue query sugli indici in Ricerca cognitiva di Azure.Search explorer queries indexes in Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/27/2020
ms.openlocfilehash: 9fb34141d19866a2f49ac164e0d89802cf7818c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369678"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Guida introduttiva: Usare Esplora ricerche per eseguire query nel portale

**Esplora ricerche** è uno strumento di query predefinito usato per l'esecuzione di query su un indice di ricerca in Ricerca cognitiva di Azure.Search explorer is a built-in query tool used for running queries against a search index in Azure Cognitive Search. Questo strumento semplifica l'apprendimento della sintassi delle query, il test di una query o di un'espressione di filtro o la conferma dei risultati di un aggiornamento dell'indice verificando l'esistente di contenuto più recente.

Questa guida introduttiva usa **l'indice realestate-us-sample** per illustrare Esplora ricerche. Le richieste vengono formulate utilizzando [l'API REST di ricerca](https://docs.microsoft.com/rest/api/searchservice/), con le risposte restituite come documenti JSON.

## <a name="prerequisites"></a>Prerequisiti

+ [Creare un servizio di Ricerca cognitiva di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. È possibile usare un servizio gratuito per questo avvio rapido.

+ **realestate-us-sample-index** viene utilizzato per questa guida introduttiva. Eseguire [**l'Importazione**](search-import-data-portal.md) guidata dati per generare l'indice dall'origine dati samples predefinita.

## <a name="start-search-explorer"></a>Avviare Esplora ricerche

1. Nel [portale di Azure](https://portal.azure.com)aprire la pagina del servizio di ricerca dal dashboard o [trovare il servizio.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)

1. Aprire Esplora ricerche dalla barra dei comandi:

   ![Comando Esplora ricerche nel portale](./media/search-explorer/search-explorer-cmd2.png "Comando Esplora ricerche nel portale")

    In alternativa, usare la scheda **Esplora ricerche** incorporata in un indice aperto:

   ![Scheda Esplora ricerca](./media/search-explorer/search-explorer-tab.png "Scheda Esplora ricerca")

## <a name="unspecified-query"></a>Query non specificata

Per una prima occhiata al contenuto, eseguire una ricerca vuota facendo clic su **Cerca** senza termini forniti. Una ricerca vuota è utile come prima query perché restituisce interi documenti ed è quindi possibile esaminarne la composizione. In una ricerca vuota non è previsto alcun ordine di classificazione e i documenti vengono restituiti in ordine arbitrario (`"@search.score": 1` per tutti i documenti). Per impostazione predefinita, in una richiesta di ricerca vengono restituiti 50 documenti.

La sintassi equivalente per una ricerca vuota è `*` o `search=*`.
   
   ```http
   search=*
   ```

   **Risultati**
   
   ![Esempio di query vuota](./media/search-explorer/search-explorer-example-empty.png "Esempio di query non qualificato o vuoto")

## <a name="free-text-search"></a>Ricerca a testo libero

Le query in formato libero, con o senza operatori, sono utili per simulare query definite dall'utente inviate da un'app personalizzata a Ricerca cognitiva di Azure.Free-form queries, with or without operators, are useful to simulating user-defined queries sent from a custom app to Azure Cognitive Search. Solo i campi attribuiti come **Ricercabili** nella definizione dell'indice vengono analizzati per individuare corrispondenze. 

Si noti che quando si forniscono criteri di ricerca, ad esempio termini di query o espressioni, entra in gioco il rango di ricerca. L'esempio seguente illustra una ricerca di testo libero.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Risultati**

   È possibile usare CTRL-F per cercare nei risultati determinati termini a cui si è interessati.

   ![Esempio di query a testo libero](./media/search-explorer/search-explorer-example-freetext.png "Esempio di query a testo libero")

## <a name="count-of-matching-documents"></a>Conteggio dei documenti corrispondenti 

Aggiungere **$count, true** per ottenere il numero di corrispondenze trovate in un indice. Nel caso di una ricerca vuota, il conteggio corrisponde al numero totale di documenti nell'indice. Nel caso di una ricerca qualificata, è il numero di documenti corrispondenti all'input della query.

   ```http
   $count=true
   ```

   **Risultati**

   ![Esempio di conteggio dei documenti](./media/search-explorer/search-explorer-example-count.png "Conteggio dei documenti corrispondenti nell'indice")

## <a name="limit-fields-in-search-results"></a>Limitare i campi nei risultati della ricerca

Aggiungere [**$select**](search-query-odata-select.md) per limitare i risultati ai campi denominati in modo esplicito per un output più leggibile in **Esplora ricerche**. Per mantenere la stringa di ricerca e **$count=true**, anteporre il prefisso **&** agli argomenti. 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Risultati**

   ![Esempio di campi limite](./media/search-explorer/search-explorer-example-selectfield.png "Limitare i campi nei risultati della ricerca")

## <a name="return-next-batch-of-results"></a>Restituisci il batch successivo di risultati

Ricerca cognitiva di Azure restituisce le prime 50 corrispondenze in base al rango di ricerca. Per ottenere il set successivo di documenti corrispondenti, aggiungere **$top=100,&$skip=50** per portare il set di risultati a 100 documenti (il valore predefinito è 50 e il valore massimo è 1000), ignorando i primi 50 documenti. Ricordare che è necessario specificare i criteri di ricerca, ad esempio un termine o un'espressione di query, per ottenere risultati classificati. Si noti che i punteggi di ricerca diminuiscono man mano che si scorre l'elenco dei risultati della ricerca.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Risultati**

   ![Risultati della ricerca batch](./media/search-explorer/search-explorer-example-topskip.png "Restituisci il batch successivo dei risultati della ricerca")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Espressioni di filtro (maggiore di, minore di, uguale a)

Utilizzare il parametro [**$filter**](search-query-odata-filter.md) quando si desidera specificare criteri precisi anziché la ricerca a testo libero. Il campo deve essere attribuito come **Filtrabile** nell'indice. Questo esempio cerca gli appartamenti con più di tre camere da letto: 

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Risultati**

   ![Espressione di filtro](./media/search-explorer/search-explorer-example-filter.png "Filtra per criterio")

## <a name="order-by-expressions"></a>Espressioni order-by

Aggiungere [**$orderby**](search-query-odata-orderby.md) per ordinare i risultati in base a un altro campo oltre al punteggio di ricerca. Il campo deve essere attribuito come **Ordinabile** nell'indice. Un'espressione di esempio che è possibile usare per testare questa situazione è:An example expression you can use to test this out is:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Risultati**

   ![Espressione Orderby](./media/search-explorer/search-explorer-example-ordery.png "Modificare l'ordinamento")

Le espressioni **$filter** e **$orderby** sono entrambe strutture OData. Per altre informazioni, vedere l'articolo relativo alla [sintassi OData per i filtri](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Risultati

In questa guida introduttiva è stato usato **Esplora ricerche** per eseguire query su un indice usando l'API REST.

+ I risultati vengono restituiti come documenti JSON dettagliati, in modo che sia possibile visualizzare la struttura e il contenuto del documenti nel complesso. È possibile usare espressioni di query, come illustrato negli esempi, per limitare i campi restituiti.

+ I documenti sono composti da tutti i campi contrassegnati come **Recuperabili** nell'indice. Per visualizzare gli attributi dell'indice nel portale, fare clic su *realestate-us-sample* nell'elenco **Indici** nella pagina di panoramica della ricerca.

+ Le query in formato libero, simili alle stringhe che è possibile immettere in un Web browser commerciale, sono utili per testare un'esperienza utente finale. Si supponga ad esempio di usare l'indice realestate predefinito. È possibile immettere "Seattle apartment lake washington" e quindi premere CTRL-F per trovare i termini nei risultati della ricerca. 

+ Query and filter expressions are articulated in a syntax supported by Azure Cognitive Search. Per impostazione predefinita viene usata una [sintassi semplice](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), ma è possibile usare la sintassi [Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) per eseguire query più avanzate. Le [espressioni di filtro](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) sono scritte usando la sintassi OData.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno verificare se le risorse create sono ancora necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento **Tutte le risorse** o **Gruppi di risorse** nel riquadro di spostamento a sinistra.

Se si usa un servizio gratuito, tenere presente che il numero di indicizzatori e origini dati è limitato a tre. Per non superare il limite, è possibile eliminare i singoli elementi nel portale. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle strutture di query e sulla sintassi, usare Postman o uno strumento equivalente per creare espressioni di query che sfruttano più parti dell'API. [L'API REST di ricerca](https://docs.microsoft.com/rest/api/searchservice/) è particolarmente utile per l'apprendimento e l'esplorazione.

> [!div class="nextstepaction"]
> [Creare una query di base in Postman](search-query-simple-examples.md)