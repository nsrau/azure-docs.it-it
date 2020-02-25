---
title: Esecuzione di query su dati geospaziali con Azure Cosmos DB
description: Esecuzione di query sui dati spaziali con Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 08b12bd9d35aaa61c79d35a55068983cdc0f1b83
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566322"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Esecuzione di query su dati geospaziali con Azure Cosmos DB

In questo articolo viene illustrato come eseguire query su dati geospaziali in Azure Cosmos DB tramite SQL e LINQ. L'archiviazione e l'accesso ai dati geospaziali attualmente sono supportati solo da account Azure Cosmos DB API SQL. Azure Cosmos DB supporta le seguenti funzioni predefinite di Open Geospatial Consortium (OGC) per l'esecuzione di query geospaziali. Per ulteriori informazioni sul set completo di funzioni predefinite nel linguaggio SQL, vedere [query System functions in Azure Cosmos DB](sql-query-system-functions.md).

## <a name="spatial-sql-built-in-functions"></a>Funzioni predefinite spaziali di SQL

Di seguito è riportato un elenco di funzioni di sistema geospaziale utili per l'esecuzione di query in Azure Cosmos DB:

|**Utilizzo**|**Descrizione**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Restituisce la distanza tra le due espressioni GeoJSON punto, poligono o LineString.|
|ST_WITHIN (spatial_expr, spatial_expr) | Restituisce un'espressione booleana che indica se il primo oggetto GeoJSON (punto, poligono o LineString) è all'interno del secondo oggetto GeoJSON (punto, poligono o LineString).|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Restituisce un'espressione booleana che indica se i due oggetti GeoJSON specificati (punto, poligono o LineString) si intersecano.|
|ST_ISVALID| Restituisce un valore booleano che indica se l'espressione GeoJSON punto, poligono o LineString specificata è valida.|
| ST_ISVALIDDETAILED| Restituisce un valore JSON che contiene un valore booleano se l'espressione GEOJSON punto, poligono o LineString specificata è valida. Se non è valido, restituisce il motivo come valore stringa.|

Le funzioni spaziali possono essere utilizzate per eseguire query di prossimità rispetto ai dati spaziali. Ecco, ad esempio, una query che restituisce tutti i documenti della famiglia che rientrano in 30 km della località specificata usando la `ST_DISTANCE` funzione incorporata.

**Query**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

**Risultati**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Se si include l'indicizzazione spaziale nel criterio di indicizzazione, le "query distance" verranno servite in modo efficiente tramite l'indice. Per ulteriori informazioni sull'indicizzazione spaziale, vedere [indicizzazione geospaziale](sql-query-geospatial-index.md). Se non si dispone di un indice spaziale per i percorsi specificati, la query eseguirà un'analisi del contenitore.

`ST_WITHIN` può essere usato per verificare se un punto si trova all'interno di un poligono. I poligoni vengono comunemente usati per rappresentare limiti come codici postali, confini di stato o formazioni naturali. Ancora una volta, se si include l'indicizzazione spaziale nel criterio di indicizzazione, le query "within" verranno servite in modo efficiente tramite l'indice.

Gli argomenti Polygon in `ST_WITHIN` possono contenere solo un anello singolo, ovvero i poligoni non devono contenere buchi.

**Query**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon',
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**Risultati**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> Come per il funzionamento di tipi non corrispondenti nella query di Azure Cosmos DB, se il valore di località specificato in un argomento non è corretto o non è valido, viene restituito **undefined** e il documento valutato viene omesso nei risultati della query. Se la query non restituisce alcun risultato, eseguire `ST_ISVALIDDETAILED` per eseguire il debug del tipo spaziale non valido.
>
>

Azure Cosmos DB supporta anche l'esecuzione di query inverse. In altre parole, è possibile indicizzare poligoni o linee in Azure Cosmos DB e quindi eseguire una query per le aree che contengono un punto specificato. Questo modello viene comunemente usato nella logistica per identificare, ad esempio, quando un autocarro entra o esce da un'area designata.

**Query**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)
```

**Risultati**

```json
    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]
```

è possibile utilizzare `ST_ISVALID` e `ST_ISVALIDDETAILED` per verificare se un oggetto spaziale è valido. Ad esempio, la seguente query controlla la validità di un punto con un valore di latitudine fuori scala (-132,8). `ST_ISVALID` restituisce solo un valore booleano e `ST_ISVALIDDETAILED` restituisce il valore booleano e una stringa contenente il motivo per cui viene considerato non valido.

**Query**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**Risultati**

```json
    [{
      "$1": false
    }]
```

Queste funzioni possono essere usate anche per convalidare i poligoni. Qui, ad esempio, si usa `ST_ISVALIDDETAILED` per convalidare un poligono che non è chiuso.

**Query**

```sql
    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})
```

**Risultati**

```json
    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]
```

## <a name="linq-querying-in-the-net-sdk"></a>Esecuzione di query LINQ in .NET SDK

SQL .NET SDK è anche dotato di metodi stub `Distance()` e `Within()` per l'uso all'interno di espressioni LINQ. Il provider LINQ SQL converte le chiamate di questo metodo nelle chiamate delle funzioni SQL predefinite equivalenti (ST_DISTANCE e ST_WITHIN rispettivamente).

Di seguito è riportato un esempio di query LINQ che consente di trovare tutti i documenti nel contenitore Azure Cosmos il cui valore `location` è entro un raggio di 30 km dal punto specificato con LINQ.

**Query LINQ per Distance**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

Analogamente, ecco una query per trovare tutti i documenti il cui `location` si trova all'interno della casella o del poligono specificato.

**Query LINQ per Within**

```csharp
    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
         .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come iniziare a usare il supporto geospaziale in Azure Cosmos DB, ecco le attività successive possibili:

* Altre informazioni sulle [query di Azure Cosmos DB](sql-query-getting-started.md)
* Scopri di più sui [dati della località geospaziale e GeoJSON in Azure Cosmos DB](sql-query-geospatial-intro.md)
* Altre informazioni sui [dati spaziali dell'indice con Azure Cosmos DB](sql-query-geospatial-index.md)
