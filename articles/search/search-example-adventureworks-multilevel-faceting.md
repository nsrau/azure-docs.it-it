---
title: 'Esempio: facet a più livelli'
titleSuffix: Azure Cognitive Search
description: Informazioni su come creare strutture di facet per tassonomie multilivello, realizzando così una struttura di esplorazione nidificata che è possibile includere nelle pagine delle applicazioni.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 8672fa0911d1a031205bb3340fa0c03ab9492a28
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792949"
---
# <a name="example-multi-level-facets-in-azure-cognitive-search"></a>Esempio: facet a più livelli in Azure ricerca cognitiva

Gli schemi di ricerca cognitiva di Azure non supportano in modo esplicito le categorie di tassonomia a più livelli, ma è possibile approssimarli modificando il contenuto prima dell'indicizzazione e quindi applicando una gestione speciale ai risultati. 

## <a name="start-with-the-data"></a>Partire dai dati

L'esempio in questo articolo si basa su un esempio precedente, [modellare il database di inventario AdventureWorks](search-example-adventureworks-modeling.md), per illustrare il facet multilivello in Azure ricerca cognitiva.

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

  ![Risultati query](./media/search-example-adventureworks/prod-query-results.png "Risultati query")

## <a name="indexing-to-a-collection-field"></a>Indicizzazione in base a un campo Collection

Nell'indice che contiene questa struttura creare un campo **Collection (EDM. String)** nello schema del ricerca cognitiva di Azure per archiviare questi dati, assicurandosi che gli attributi di campo includano ricercabile, filtrabile, facet e recuperabile.

A questo punto, quando si indicizza contenuto che fa riferimento a una specifica categoria tassonomica, presentare la tassonomia sotto forma di matrice contenente testo da ogni livello della tassonomia. Ad esempio, per un'entità con `ProductCategoryId = 5 (Mountain Bikes)`, presentare il campo come `[ "Bikes", "Bikes|Mountain Bikes"]`

Si noti l'inclusione della categoria padre "Bikes" nel valore della categoria figlio "Mountain Bikes". Ogni sottocategoria deve incorporare l'intero percorso rispetto all'elemento radice. L'uso della barra verticale come separatore è arbitrario, ma deve essere coerente e questo carattere non deve essere presente nel testo di origine. Il carattere separatore verrà usato nel codice dell'applicazione per ricostruire l'albero della tassonomia dai risultati dei facet.

## <a name="construct-the-query"></a>Creare la query

Quando si eseguono query, includere la specifica di facet seguente (dove taxonomy è il campo della tassonomia abilitato per l'esplorazione in base a facet): `facet = taxonomy,count:50,sort:value`

Il valore di count deve essere sufficientemente elevato da restituire tutti i valori possibili della tassonomia. I dati di AdventureWorks contengono 41 valori di tassonomia distinti e pertanto `count:50` è sufficiente.

  ![Filtro sfaccettato](./media/search-example-adventureworks/facet-filter.png "Filtro sfaccettato")

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

  ![filtro sfaccettato a più livelli](./media/search-example-adventureworks/multi-level-facet.png "filtro sfaccettato a più livelli")

 
Ogni collegamento nell'albero deve applicare il filtro correlato. ad esempio:

+ **taxonomy/any**`(x:x eq 'Accessories')` restituisce tutti i documenti nel ramo Accessories
+ **taxonomy/any**`(x:x eq 'Accessories|Bike Racks')` restituisce solo i documenti con sottocategoria Bike Racks nel ramo Accessories.

Questa tecnica può essere adattata per gestire scenari più complessi come alberi di tassonomia con più livelli e sottocategorie duplicate incluse in categorie padre differenti (ad esempio, `Bike Components|Forks` e `Camping Equipment|Forks`).

> [!TIP]
> La velocità della query dipende dal numero di facet restituiti. Per supportare set di tassonomia molto grandi, è consigliabile aggiungere un campo **Edm.String** abilitato per l'esplorazione in base a facet che contenga il valore di tassonomia di primo livello per ogni documento. Applicare quindi la stessa tecnica descritta in precedenza, ma eseguire solo la query in base a facet di tipo raccolta (ovvero filtrata in base al campo radice della tassonomia) quando l'utente espande un nodo di primo livello. In alternativa, se non è necessaria una percentuale di richiamo del 100%, è sufficiente ridurre il totale dei facet a un numero ragionevole e assicurarsi che le voci dei facet siano ordinate in base al totale.

## <a name="see-also"></a>Vedi anche

[Esempio: modellare il database di inventario AdventureWorks per Azure ricerca cognitiva](search-example-adventureworks-modeling.md)