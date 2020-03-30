---
title: 'Esempio: modellare il database AdventureWorks Inventory'
titleSuffix: Azure Cognitive Search
description: Informazioni su come modellare i dati relazionali, trasformandoli in un set di dati appiattito, per l'indicizzazione e la ricerca full-text in Ricerca cognitiva di Azure.Learn how to model relational data, transforming it into a flattened data set, for indexing and full text search in Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: heidist
ms.openlocfilehash: edb6162724938962df8a7340afea6e930a0b1049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793004"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-cognitive-search"></a>Esempio: modellare il database AdventureWorks Inventory per Ricerca cognitiva di AzureExample: Model the AdventureWorks Inventory database for Azure Cognitive Search

Ricerca cognitiva di Azure accetta un set di righe bidimensionale come input per la pipeline di [indicizzazione (inserimento dati).](search-what-is-an-index.md) Se i dati di origine provengono da un database relazionale di SQL Server, in questo articolo viene illustrato un approccio per la creazione di un set di righe bidimensionale prima dell'indicizzazione, usando il database di esempio AdventureWorks come esempio.

## <a name="about-adventureworks"></a>Informazioni su AdventureWorks

Se si dispone di un'istanza di SQL Server, è possibile che si abbia familiarità con il [database di esempio AdventureWorks](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017). Tra le tabelle incluse in questo database sono presenti cinque tabelle che espongono le informazioni sui prodotti.

+ **ProductModel**: nome
+ **Product**: nome, colore, costo, misura, peso, immagine, categoria. Ogni riga è unita in join a un record di ProductModel
+ **ProductDescription**: descrizione
+ **ProductModelProductDescription**: impostazioni locali. Ogni riga unisce in join un record di ProductModel a un record di ProductDescription per una lingua specifica
+ **ProductCategory**: nome, categoria padre

La combinazione di tutti questi dati in un set di righe bidimensionale che può essere inserito in un indice di ricerca è l'obiettivo di questo esempio. 

## <a name="considering-our-options"></a>Opzioni possibili

L'approccio ingenuo consiste nell'indicizzazione di tutte le righe della tabella Product (con i join appropriati), dato che la tabella Product contiene le informazioni più specifiche. Tale approccio, tuttavia, espone l'indice di ricerca alla generazione di un set di risultati in cui vengono percepiti duplicati. Il modello Road-650, ad esempio, è disponibile in due colori e sei misure. I risultati della query "road bikes" sarebbero quindi dominati da 12 istanze dello stesso modello, distinte solo dalla misura e dal colore. Gli altri sei modelli che soddisfano questa query verrebbero confinati tutti nel limbo della seconda pagina della ricerca.

  ![Elenco di prodotti](./media/search-example-adventureworks/products-list.png "Elenco di prodotti")
 
Si noti che il modello Road-650 ha dodici opzioni. Nell'indice di ricerca, la rappresentazione più efficiente di righe di entità uno-a-molti è quella di campi multivalore o con valori preaggregati.

Per risolvere questo problema, non basta spostare semplicemente l'indice di destinazione alla tabella ProductModel. Con questa operazione verrebbero ignorati i dati della tabella Product, che sono importanti e devono essere rappresentati nei risultati della ricerca.

## <a name="use-a-collection-data-type"></a>Usare il tipo di dati Collection

L'"approccio corretto" consiste nell'utilizzare una funzionalità dello schema di ricerca che non dispone di un parallelo diretto nel modello di database: **Collection(Edm.String)**. Questo costrutto è definito nello schema dell'indice di Ricerca cognitiva di Azure.This construct is defined in the Azure Cognitive Search index schema. Un tipo di dati Collection viene utilizzato quando è necessario rappresentare un elenco di singole stringhe, anziché una stringa molto lunga (singola). Se sono presenti tag o parole chiave, per i campi corrispondenti si usa un tipo di dati Collection.

Se si definiscono campi di indice multivalore di tipo **Collection(Edm.String)** per colore, misura e immagine, è possibile mantenere le informazioni ausiliarie per i facet e i filtri senza inquinare l'indice con voci duplicate. Analogamente, applicare funzioni di aggregazione ai campi numerici di Product, indicizzando **minListPrice** anziché **listPrice** di ogni singolo prodotto.

Con un indice così strutturato, la ricerca "mountain bike" visualizza i modelli di bicicletta discreti, mantenendo metadati importanti quali colore, misura e prezzo più basso. Lo screenshot seguente illustra tale risultato.

  ![Esempio di ricerca di mountain bike](./media/search-example-adventureworks/mountain-bikes-visual.png "Esempio di ricerca di mountain bike")

## <a name="use-script-for-data-manipulation"></a>Usare script per la manipolazione dei dati

Questo tipo di modellazione, purtroppo, non può essere ottenuto facilmente solo tramite istruzioni SQL. Usare invece un semplice script NodeJS per caricare i dati e quindi eseguirne il mapping all'interno di entità JSON adatte alla ricerca.

Il mapping finale di ricerca nel database ha l'aspetto seguente:

+ model (Edm.String: ricercabile, filtrabile, recuperabile) da "ProductModel.Name"
+ description_en (Edm.String: ricercabile) da "ProductDescription" per modello con impostazioni cultura='en'
+ color (Collection(Edm.String): ricercabile, filtrabile, con facet, recuperabile): valori univoci da "Product.Color" per il modello
+ size (Collection(Edm.String): ricercabile, filtrabile, con facet, recuperabile): valori univoci da "Product.Size" per il modello
+ image (Collection(Edm.String): recuperabile): valori univoci da "Product.ThumbnailPhoto" per il modello
+ minStandardCost (Edm.Double: filtrabile, con facet, ordinabile, recuperabile): minimo aggregato di tutti i record "Product.StandardCost" per il modello
+ minListPrice (Edm.Double: filtrabile, con facet, ordinabile, recuperabile): minimo aggregato di tutti i record "Product.ListPrice" per il modello
+ minWeight (Edm.Double: filtrabile, con facet, ordinabile, recuperabile): minimo aggregato di tutti i record "Product.Weight" per il modello
+ products (Collection(Edm.String): ricercabile, filtrabile, recuperabile): valori univoci da "Product.Name" per il modello

Dopo aver creato un join della tabella ProductModel con Product e ProductDescription, usare [lodash](https://lodash.com/) (o Linq in C#) per trasformare rapidamente il set di risultati:

```javascript
var records = queryYourDatabase();
var models = _(records)
  .groupBy('ModelName')
  .values()
  .map(function(d) {
    return {
      model: _.first(d).ModelName,
      description: _.first(d).Description,
      colors: _(d).pluck('Color').uniq().compact().value(),
      products: _(d).pluck('ProductName').uniq().compact().value(),
      sizes: _(d).pluck('Size').uniq().compact().value(),
      images: _(d).pluck('ThumbnailPhotoFilename').uniq().compact().value(),
      minStandardCost: _(d).pluck('StandardCost').min(),
      maxStandardCost: _(d).pluck('StandardCost').max(),
      minListPrice: _(d).pluck('ListPrice').min(),
      maxListPrice: _(d).pluck('ListPrice').max(),
      minWeight: _(d).pluck('Weight').min(),
      maxWeight: _(d).pluck('Weight').max(),
    };
  })
  .value();
```

Il codice JSON risultante ha l'aspetto seguente:

```json
[
  {
    "model": "HL Road Frame",
    "colors": [
      "Black",
      "Red"
    ],
    "products": [
      "HL Road Frame - Black, 58",
      "HL Road Frame - Red, 58",
      "HL Road Frame - Red, 62",
      "HL Road Frame - Red, 44",
      "HL Road Frame - Red, 48",
      "HL Road Frame - Red, 52",
      "HL Road Frame - Red, 56",
      "HL Road Frame - Black, 62",
      "HL Road Frame - Black, 44",
      "HL Road Frame - Black, 48",
      "HL Road Frame - Black, 52"
    ],
    "sizes": [
      "58",
      "62",
      "44",
      "48",
      "52",
      "56"
    ],
    "images": [
      "no_image_available_small.gif"
    ],
    "minStandardCost": 868.6342,
    "maxStandardCost": 1059.31,
    "minListPrice": 1431.5,
    "maxListPrice": 1431.5,
    "minWeight": 961.61,
    "maxWeight": 1043.26
  }
]
```

Ecco infine la query SQL che restituisce il recordset iniziale. Per caricare i dati nell'app NodeJS, è stato usato il modulo npm [mssql](https://www.npmjs.com/package/mssql).

```T-SQL
SELECT
  m.Name as ModelName,
  d.Description,
  p.Name as ProductName,
  p.*
FROM 
  SalesLT.ProductModel m
INNER JOIN 
  SalesLT.ProductModelProductDescription md
  ON m.ProductModelId = md.ProductModelId
INNER JOIN 
  SalesLT.ProductDescription d
  ON md.ProductDescriptionId = d.ProductDescriptionId
LEFT JOIN 
  SalesLT.product p
  ON m.ProductModelId = p.ProductModelId
WHERE
  md.Culture='en'
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esempio: tassonomie di facet a più livelli in Ricerca cognitiva di AzureExample: Multi-level facet taxonomies in Azure Cognitive Search](search-example-adventureworks-multilevel-faceting.md)