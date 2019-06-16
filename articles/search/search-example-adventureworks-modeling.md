---
title: 'Esempio: Modellare il database di inventario di AdventureWorks - Ricerca di Azure'
description: Informazioni su come modellare dati relazionali, trasformandoli in un set di dati bidimensionale, per l'indicizzazione e la ricerca full-text in Ricerca di Azure.
author: cstone
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: chstone
ms.openlocfilehash: 6d5d01dfbbcfda56818f5c38b06117a87e021445
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61291909"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-search"></a>Esempio: Modellare il database di inventario di AdventureWorks per Ricerca di Azure

Modellare contenuto di database strutturato in un indice di ricerca efficiente è di rado un esercizio semplice. Oltre alla pianificazione e alla gestione del cambiamento, c'è la sfida della denormalizzazione delle righe di origine da righe unite in join a una tabella a entità adatte alla ricerca. Questo articolo usa i dati di esempio di AdventureWorks, disponibili online, per evidenziare esperienze comuni nella transizione da database a ricerca. 

## <a name="about-adventureworks"></a>Informazioni su AdventureWorks

Se si ha un'istanza di SQL Server, il database di esempio AdventureWorks può essere familiare. Tra le tabelle incluse in questo database sono presenti cinque tabelle che espongono le informazioni sui prodotti.

+ **ProductModel**: nome
+ **Product**: nome, colore, costo, misura, peso, immagine, categoria. Ogni riga è unita in join a un record di ProductModel
+ **ProductDescription**: descrizione
+ **ProductModelProductDescription**: impostazioni locali. Ogni riga unisce in join un record di ProductModel a un record di ProductDescription per una lingua specifica
+ **ProductCategory**: nome, categoria padre

In questo esempio, tutti questi dati verranno combinati in un set di righe bidimensionale che possa essere inserito in un indice di ricerca. 

## <a name="considering-our-options"></a>Opzioni possibili

L'approccio ingenuo consiste nell'indicizzazione di tutte le righe della tabella Product (con i join appropriati), dato che la tabella Product contiene le informazioni più specifiche. Tale approccio, tuttavia, espone l'indice di ricerca alla generazione di un set di risultati in cui vengono percepiti duplicati. Il modello Road-650, ad esempio, è disponibile in due colori e sei misure. I risultati della query "road bikes" sarebbero quindi dominati da 12 istanze dello stesso modello, distinte solo dalla misura e dal colore. Gli altri sei modelli che soddisfano questa query verrebbero confinati tutti nel limbo della seconda pagina della ricerca.

  ![Elenco di prodotti](./media/search-example-adventureworks/products-list.png "Elenco di prodotti")
 
Si noti che il modello Road-650 ha dodici opzioni. Nell'indice di ricerca, la rappresentazione più efficiente di righe di entità uno-a-molti è quella di campi multivalore o con valori preaggregati.

Per risolvere questo problema, non basta spostare semplicemente l'indice di destinazione alla tabella ProductModel. Con questa operazione verrebbero ignorati i dati della tabella Product, che sono importanti e devono essere rappresentati nei risultati della ricerca.

## <a name="use-a-collection-data-type"></a>Usare il tipo di dati Collection

L'approccio "corretto" consiste nell'usare una funzionalità di schema di ricerca che non ha una corrispondenza diretta nel modello di database: **Collection(Edm.String)** . Un tipo di dati Collection viene usato quando si ha un elenco di stringhe singole, anziché una stringa unica molto lunga. Se sono presenti tag o parole chiave, per i campi corrispondenti si usa un tipo di dati Collection.

Se si definiscono campi di indice multivalore di tipo **Collection(Edm.String)** per colore, misura e immagine, è possibile mantenere le informazioni ausiliarie per i facet e i filtri senza inquinare l'indice con voci duplicate. Analogamente, applicare funzioni di aggregazione ai campi numerici di Product, indicizzando **minListPrice** anziché **listPrice** di ogni singolo prodotto.

Con un indice così strutturato, la ricerca "mountain bike" visualizza i modelli di bicicletta discreti, mantenendo metadati importanti quali colore, misura e prezzo più basso. Lo screenshot seguente illustra tale risultato.

  ![Esempio di ricerca di mountain bike](./media/search-example-adventureworks/mountain-bikes-visual.png "Esempio di ricerca di Mountain bike")

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
> [Esempio: Tassonomie facet multilivello in Ricerca di Azure](search-example-adventureworks-multilevel-faceting.md)


