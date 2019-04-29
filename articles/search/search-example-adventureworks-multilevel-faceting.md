---
title: 'Esempio: Facet multilivello - Ricerca di Azure'
description: Informazioni su come creare strutture di facet per tassonomie multilivello, realizzando così una struttura di esplorazione nidificata che è possibile includere nelle pagine delle applicazioni.
author: cstone
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: chstone
ms.openlocfilehash: 7fa17528931be40109d81edac0f15a6a6822ec01
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61291763"
---
# <a name="example-multi-level-facets-in-azure-search"></a>Esempio: Facet multilivello in Ricerca di Azure

Gli schemi di Ricerca di Azure non supportano esplicitamente le categorie tassonomiche multilivello, ma è possibile creare un'approssimazione di tali categorie modificando il contenuto prima di eseguire l'indicizzazione e quindi applicando alcune modifiche particolari ai risultati. 

## <a name="start-with-the-data"></a>Partire dai dati

L'esempio riportato in questo articolo si basa su un esempio precedente, disponibile in [Modellazione del database di inventario di AdventureWorks](search-example-adventureworks-modeling.md), per illustrare l'uso di facet multilivello in Ricerca di Azure.

In AdventureWorks i dati sono classificati in base a una tassonomia semplice a due livelli, con una relazione di tipo padre-figlio. Per la profondità della tassonomia a lunghezza fissa di questa struttura, è possibile usare una semplice query join SQL per raggruppare la tassonomia:

```T-SQL
SELECT 
  parent.Name Parent, category.Name Category, category.ProductCategoryId
FROM 
  SalesLT.ProductCategory category
LEFT JOIN
  SalesLT.ProductCategory parent
  ON category.ParentProductCategoryId=parent.ProductCategoryId
```

  ![Risultati della query](./media/search-example-adventureworks/prod-query-results.png "Risultati della query")

## <a name="indexing-to-a-collection-field"></a>Indicizzazione in base a un campo Collection

Nell'indice contenente questa struttura, creare un campo **Collection(Edm.String)** nello schema di Ricerca di Azure per archiviare questi dati, verificando che per il campo siano definiti gli attributi searchable, filterable, facetable e retrievable.

A questo punto, quando si indicizza contenuto che fa riferimento a una specifica categoria tassonomica, presentare la tassonomia sotto forma di matrice contenente testo da ogni livello della tassonomia. Ad esempio, per un'entità con `ProductCategoryId = 5 (Mountain Bikes)`, presentare il campo come `[ "Bikes", "Bikes|Mountain Bikes"]`

Si noti l'inclusione della categoria padre "Bikes" nel valore della categoria figlio "Mountain Bikes". Ogni sottocategoria deve incorporare l'intero percorso rispetto all'elemento radice. L'uso della barra verticale come separatore è arbitrario, ma deve essere coerente e questo carattere non deve essere presente nel testo di origine. Il carattere separatore verrà usato nel codice dell'applicazione per ricostruire l'albero della tassonomia dai risultati dei facet.

## <a name="construct-the-query"></a>Creare la query

Quando si eseguono query, includere la specifica di facet seguente (dove taxonomy è il campo della tassonomia abilitato per l'esplorazione in base a facet): `facet = taxonomy,count:50,sort:value`

Il valore di count deve essere sufficientemente elevato da restituire tutti i valori possibili della tassonomia. I dati di AdventureWorks contengono 41 valori di tassonomia distinti e pertanto `count:50` è sufficiente.

  ![Filtro in base a facet](./media/search-example-adventureworks/facet-filter.png "Filtro in base a facet")

## <a name="build-the-structure-in-client-code"></a>Creare la struttura nel codice client

Nel codice dell'applicazione client, ricostruire l'albero della tassonomia suddividendo ogni valore di facet in base al carattere barra verticale.

```javascript
var sum = 0
  , categories = {children:{},length:0}
  , results = getSearchResults();
separator = separator || '|';
field = field || 'taxonomy';
results['@search.facets'][field].forEach(function(d) {
  var node = categories;
  var parts = d.value.split(separator);
  sum += d.count;
  parts.forEach(function(c, i) {
    if (!_(node.children).has(c)) {
      node.children[c] = {};
      node.children[c].count = d.count;
      node.children[c].children = {};
      node.children[c].length = 0;
      node.children[c].filter = parts.slice(0,i+1).join(separator);
      node.length++;
    }
    node = node.children[c];
  });
});
categories.count = sum;
```

È ora possibile usare l'oggetto **categories** per eseguire il rendering di un albero della tassonomia comprimibile con conteggi accurati:

  ![filtro in base a facet multilivello](./media/search-example-adventureworks/multi-level-facet.png "filtro in base a facet multilivello")

 
Ogni collegamento nell'albero deve applicare il filtro correlato. Ad esempio: 

+ **taxonomy/any**`(x:x eq 'Accessories')` restituisce tutti i documenti nel ramo Accessories
+ **taxonomy/any**`(x:x eq 'Accessories|Bike Racks')` restituisce solo i documenti con sottocategoria Bike Racks nel ramo Accessories.

Questa tecnica può essere adattata per gestire scenari più complessi come alberi di tassonomia con più livelli e sottocategorie duplicate incluse in categorie padre differenti (ad esempio, `Bike Components|Forks` e `Camping Equipment|Forks`).

> [!TIP]
> La velocità della query dipende dal numero di facet restituiti. Per supportare set di tassonomia molto grandi, è consigliabile aggiungere un campo **Edm.String** abilitato per l'esplorazione in base a facet che contenga il valore di tassonomia di primo livello per ogni documento. Applicare quindi la stessa tecnica descritta in precedenza, ma eseguire solo la query in base a facet di tipo raccolta (ovvero filtrata in base al campo radice della tassonomia) quando l'utente espande un nodo di primo livello. In alternativa, se non è necessaria una percentuale di richiamo del 100%, è sufficiente ridurre il totale dei facet a un numero ragionevole e assicurarsi che le voci dei facet siano ordinate in base al totale.

## <a name="see-also"></a>Vedere anche 

[Esempio: Modellare il database di inventario di AdventureWorks per Ricerca di Azure](search-example-adventureworks-modeling.md)