---
title: Strumento di query Esplora ricerche nel portale di Azure
titleSuffix: Azure Cognitive Search
description: Questa guida di avvio rapido del portale di Azure illustra come usare Esplora ricerche per apprendere la sintassi delle query, testare le espressioni di query o esaminare un documento di ricerca. Esplora ricerche esegue query sugli indici in Ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 09/25/2020
ms.openlocfilehash: adcd07ad370ad9f1301caa41c494a33958743dd8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91398409"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Avvio rapido: Usare Esplora ricerche per eseguire query nel portale

**Esplora ricerche** è uno strumento di query predefinito che consente di eseguire query su un indice di ricerca in Ricerca cognitiva di Azure. Questo strumento semplifica l'apprendimento della sintassi di query, il test di una query o di un'espressione filtro oppure la conferma di un aggiornamento dei dati verificando la disponibilità di nuovi contenuti nell'indice.

Questo argomento di avvio rapido prevede l'uso di un indice esistente per illustrare il funzionamento di Esplora ricerche. Le richieste vengono formulate usando l'[API REST di ricerca](/rest/api/searchservice/) e le risposte vengono restituite come documenti JSON.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire le procedure descritte è necessario:

+ Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/).

+ Un'istanza del servizio Ricerca cognitiva di Azure. [Creare un servizio](search-create-service-portal.md) o [trovarne uno esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. È possibile usare un servizio gratuito per questo avvio rapido. 

+ Per questo argomento di avvio rapido si userà *realestate-us-sample-index*. Usare la procedura guidata [**Importa dati**](search-import-data-portal.md) per creare l'indice. Nel primo passaggio, quando viene chiesta l'origine dati, scegliere **Esempi** e quindi selezionare l'origine dati **realestate-us-sample**. Accettare tutte le impostazioni predefinite della procedura guidata per creare l'indice.

## <a name="start-search-explorer"></a>Avviare Esplora ricerche

1. Nel [portale di Azure](https://portal.azure.com) aprire la pagina per la ricerca dei servizi dal dashboard o [trovare il servizio](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) desiderato.

1. Aprire Esplora ricerche dalla barra dei comandi:

   :::image type="content" source="media/search-explorer/search-explorer-cmd2.png" alt-text="Comando Esplora ricerche nel portale" border="false":::

    In alternativa, usare la scheda **Esplora ricerche** incorporata in un indice aperto:

   :::image type="content" source="media/search-explorer/search-explorer-tab.png" alt-text="Comando Esplora ricerche nel portale" border="false":::

## <a name="unspecified-query"></a>Query non specificata

Per un primo esame del contenuto, eseguire una ricerca vuota facendo clic su **Cerca** senza specificare termini. Una ricerca vuota è utile come prima query perché restituisce interi documenti ed è quindi possibile esaminarne la composizione. In una ricerca vuota non è previsto alcun ordine di classificazione e i documenti vengono restituiti in ordine arbitrario (`"@search.score": 1` per tutti i documenti). Per impostazione predefinita, in una richiesta di ricerca vengono restituiti 50 documenti.

La sintassi equivalente per una ricerca vuota è `*` o `search=*`.
   
   ```http
   search=*
   ```

   **Risultati**
   
   :::image type="content" source="media/search-explorer/search-explorer-example-empty.png" alt-text="Comando Esplora ricerche nel portale" border="false":::

## <a name="free-text-search"></a>Ricerca con testo libero

Le query in formato libero, con o senza operatori, sono utili per la simulazione di query definite dall'utente inviate da un'app personalizzata a Ricerca cognitiva di Azure. Solo i campi attribuiti come **ricercabili** nella definizione dell'indice vengono analizzati per individuare le corrispondenze. 

Si noti che quando si specificano i criteri di ricerca, ad esempio i termini o le espressioni di query, entra in gioco la classificazione della ricerca. L'esempio seguente illustra una ricerca di testo libero.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Risultati**

   È possibile usare CTRL-F per cercare nei risultati determinati termini a cui si è interessati.

   :::image type="content" source="media/search-explorer/search-explorer-example-freetext.png" alt-text="Comando Esplora ricerche nel portale" border="false":::

## <a name="count-of-matching-documents"></a>Numero di documenti corrispondenti 

Aggiungere **$count=true** per ottenere il numero di corrispondenze trovate in un indice. Nel caso di una ricerca vuota, il conteggio corrisponde al numero totale di documenti nell'indice. Nel caso di una ricerca qualificata, è il numero di documenti corrispondenti all'input della query.

   ```http
   $count=true
   ```

   **Risultati**

   :::image type="content" source="media/search-explorer/search-explorer-example-count.png" alt-text="Comando Esplora ricerche nel portale" border="false":::

## <a name="limit-fields-in-search-results"></a>Limitare i campi nei risultati della ricerca

Aggiungere [ **$select**](search-query-odata-select.md) per limitare i risultati ai campi denominati in modo esplicito per migliorare la leggibilità dell'output in **Esplora ricerche**. Per mantenere la stringa di ricerca e **$count=true**, anteporre il prefisso **&** agli argomenti. 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Risultati**

   :::image type="content" source="media/search-explorer/search-explorer-example-selectfield.png" alt-text="Comando Esplora ricerche nel portale" border="false":::

## <a name="return-next-batch-of-results"></a>Restituire il batch di risultati successivo

Ricerca cognitiva di Azure restituisce le prime 50 corrispondenze in base ai criteri di classificazione della ricerca. Per ottenere il set successivo di documenti corrispondenti, aggiungere **$top=100,&$skip=50** per portare il set di risultati a 100 documenti (il valore predefinito è 50 e il valore massimo è 1000), ignorando i primi 50 documenti. Ricordare che è necessario specificare i criteri di ricerca, ad esempio un termine o un'espressione di query, per ottenere risultati classificati. Si noti che i punteggi di ricerca diminuiscono man mano che si scorre l'elenco dei risultati della ricerca.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Risultati**

   :::image type="content" source="media/search-explorer/search-explorer-example-topskip.png" alt-text="Comando Esplora ricerche nel portale" border="false":::

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Espressioni di filtro (maggiore di, minore di, uguale a)

Usare il parametro [ **$filter**](search-query-odata-filter.md) per specificare criteri precisi anziché eseguire una ricerca con testo libero. Il campo deve essere attribuito come **filtrabile** nell'indice. Questo esempio cerca gli appartamenti con più di 3 camere da letto:

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Risultati**

   :::image type="content" source="media/search-explorer/search-explorer-example-filter.png" alt-text="Comando Esplora ricerche nel portale" border="false":::

## <a name="order-by-expressions"></a>Espressioni order-by

Aggiungere [ **$orderby**](search-query-odata-orderby.md) per ordinare i risultati in base a un altro campo oltre al punteggio di ricerca. Il campo deve essere attribuito come **ordinabile** nell'indice. Per testare questo comportamento, è possibile usare questa espressione di esempio:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Risultati**

   :::image type="content" source="media/search-explorer/search-explorer-example-ordery.png" alt-text="Comando Esplora ricerche nel portale" border="false":::

Le espressioni **$filter** e **$orderby** sono entrambe strutture OData. Per altre informazioni, vedere l'articolo relativo alla [sintassi OData per i filtri](/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Risultati

In questa guida di avvio rapido è stato usato **Esplora ricerche** per eseguire query su un indice usando l'API REST.

+ I risultati vengono restituiti come documenti JSON dettagliati, in modo che sia possibile visualizzare la struttura e il contenuto del documenti nel complesso. È possibile usare espressioni di query, come illustrato negli esempi, per limitare i campi restituiti.

+ I documenti sono costituiti da tutti i campi contrassegnati come **recuperabili** nell'indice. Per visualizzare gli attributi dell'indice nel portale, fare clic su *realestate-us-sample* nell'elenco **Indici** nella pagina di panoramica della ricerca.

+ Le query in formato libero, simili alle stringhe che è possibile immettere in un Web browser commerciale, sono utili per testare un'esperienza utente finale. Si supponga ad esempio di usare l'indice realestate predefinito. È possibile immettere "Seattle apartment lake washington" e quindi premere CTRL-F per trovare i termini nei risultati della ricerca. 

+ Le espressioni di query e filtro devono essere formulate in una sintassi supportata da Ricerca cognitiva di Azure. Per impostazione predefinita viene usata una [sintassi semplice](/rest/api/searchservice/simple-query-syntax-in-azure-search), ma è possibile usare la sintassi [Lucene completa](/rest/api/searchservice/lucene-query-syntax-in-azure-search) per eseguire query più avanzate. Le [espressioni di filtro](/rest/api/searchservice/odata-expression-syntax-for-azure-search) sono scritte usando la sintassi OData.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno verificare se le risorse create sono ancora necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento **Tutte le risorse** o **Gruppi di risorse** nel riquadro di spostamento a sinistra.

Se si usa un servizio gratuito, tenere presente che il numero di indicizzatori e origini dati è limitato a tre. Per non superare il limite, è possibile eliminare i singoli elementi nel portale. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle strutture e la sintassi delle query, usare Postman o uno strumento equivalente per creare espressioni di query che sfruttano più parti dell'API. L'[API REST di ricerca](/rest/api/searchservice/) è particolarmente utile per l'apprendimento e l'esplorazione.

> [!div class="nextstepaction"]
> [Creare una query di base in Postman](search-query-simple-examples.md)