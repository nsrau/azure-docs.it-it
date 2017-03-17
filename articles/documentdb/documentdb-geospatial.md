---
title: Uso dei dati geospaziali in Azure DocumentDB | Microsoft Docs
description: Informazioni su come creare, indice ed eseguire query su oggetti spaziali con Azure DocumentDB.
services: documentdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 82ce2898-a9f9-4acf-af4d-8ca4ba9c7b8f
ms.service: documentdb
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 11/16/2016
ms.author: arramac
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 60219a9c78884e01fcf46e18ac03e2a955787bc5
ms.lasthandoff: 03/07/2017


---
# <a name="working-with-geospatial-and-geojson-location-data-in-documentdb"></a>Uso dei dati geospaziali e dei dati GeoJSON sulla posizione in DocumentDB
In questo articolo viene fornita un'introduzione alla funzionalità geospaziale in [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/). Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:

* Come è possibile archiviare i dati spaziali in Azure DocumentDB?
* Come è possibile eseguire una query su dati geospaziali in Azure DocumentDB in SQL e LINQ?
* Come è possibile abilitare o disabilitare l'indicizzazione spaziale in DocumentDB?

Consultare il [progetto Github](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) per esempi di codice.

## <a name="introduction-to-spatial-data"></a>Introduzione ai dati spaziali
I dati spaziali descrivono la posizione e la forma degli oggetti nello spazio. Nella maggior parte delle applicazioni, questi corrispondono a oggetti sulla terra, vale a dire dati geospaziali. I dati spaziali possono essere utilizzati per rappresentare la posizione di una persona, un luogo di interesse o i confini di una città o di un lago. Casi di utilizzo comuni includono spesso query di prossimità, ad esempio, "trova tutti i negozi vicino alla mia posizione attuale". 

### <a name="geojson"></a>GeoJSON
DocumentDB supporta l'indicizzazione e le query dei dati di punti geo-spaziali che ha rappresentato mediante la [specifica GeoJSON](http://geojson.org/geojson-spec.html). Le strutture di dati GeoJSON sono sempre oggetti JSON validi, quindi possono essere archiviate e interrogate utilizzando DocumentDB senza librerie o strumenti specializzati. Gli SDK di DocumentDB forniscono classi helper e metodi che semplificano il lavoro con i dati spaziali. 

### <a name="points-linestrings-and-polygons"></a>Punti, oggetti linestring e poligoni
Un **punto** indica una posizione singola nello spazio. Nei dati geospaziali, un punto rappresenta la posizione esatta, che può essere un indirizzo di un negozio, un chiosco, un'automobile o una città.  Un punto viene rappresentato in GeoJSON (e DocumentDB) mediante la sua coppia di coordinate o longitudine e latitudine. Di seguito è riportato un esempio JSON per un punto.

**Punti in DocumentDB**

    {
       "type":"Point",
       "coordinates":[ 31.9, -4.8 ]
    }

> [!NOTE]
> GeoJSON specifica la longitudine prima e la latitudine dopo. Come in altre applicazioni di mapping, longitudine e latitudine sono angoli e sono espresse in gradi. I valori della longitudine vengono misurati dal meridiano principale e sono compresi tra -180° e 180,0° gradi, mentre i valori della latitudine sono misurati dall'equatore e sono compresi tra -90,0° e 90,0°. 
> 
> DocumentDB interpreta le coordinate come rappresentate secondo il sistema di riferimento WGS-84. Per ulteriori informazioni sui sistemi di coordinate di riferimento, vedere di seguito.
> 
> 

Questo può essere incorporato in un documento di DocumentDB come illustrato in questo esempio di un profilo utente contenente i dati sulla posizione:

**Utilizzare un profilo con posizione archiviata in DocumentDB**

    {
       "id":"documentdb-profile",
       "screen_name":"@DocumentDB",
       "city":"Redmond",
       "topics":[ "NoSQL", "Javascript" ],
       "location":{
          "type":"Point",
          "coordinates":[ 31.9, -4.8 ]
       }
    }

Oltre ai punti, GeoJSON supporta oggetti linestring e poligoni. **LineStrings** rappresentano una serie di due o più punti nello spazio e i segmenti lineari che li connettono. Nei dati geospaziali, gli oggetti linestring di solito vengono usati per rappresentare autostrade o fiumi. Un oggetto **poligono** è un perimetro costituito da punti connessi che forma un elemento LineString chiuso. I poligoni vengono comunemente utilizzati per rappresentare formazioni naturali come laghi o giurisdizioni politiche come città e stati. Di seguito è riportato un esempio di poligono in DocumentDB. 

**Poligoni in DocumentDB**

    {
       "type":"Polygon",
       "coordinates":[
           [ 31.8, -5 ],
           [ 31.8, -4.7 ],
           [ 32, -4.7 ],
           [ 32, -5 ],
           [ 31.8, -5 ]
       ]
    }

> [!NOTE]
> La specifica GeoJSON richiede che per i poligoni validi l'ultima coppia di coordinate indicata corrisponda alla prima, in modo da creare una forma chiusa.
> 
> I punti all'interno di un poligono devono essere specificati in senso antiorario. Un poligono specificato in senso orario rappresenta l'inverso dell'area al suo interno.
> 
> 

Oltre a punto, LineString e poligono, GeoJSON specifica inoltre la rappresentazione della modalità di raggruppamento di più posizioni geospaziali, nonché come associare proprietà arbitrarie alla georilevazione come **Caratteristica**. Poiché questi sono oggetti JSON validi, essi possono essere memorizzati ed elaborati tutti in DocumentDB. Tuttavia DocumentDB supporta solo l'indicizzazione automatica dei punti.

### <a name="coordinate-reference-systems"></a>Sistemi di riferimento delle coordinate
Poiché la forma della terra è irregolare, le coordinate dei dati geospaziali sono rappresentate in molti sistemi di coordinate di riferimento (CRS), ognuno con la propria struttura di riferimento e le proprie unità di misura. Ad esempio, il "National Grid of Britain" è un sistema di riferimento molto accurato per il Regno Unito, ma non al suo esterno. 

Il più diffuso CRS attualmente in uso è il World Geodetic System [WGS-84](http://earth-info.nga.mil/GandG/wgs84/). Dispositivi GPS e molti servizi di mapping, tra cui Google Maps e le API di Bing Maps, utilizzano WGS-84. DocumentDB supporta l'indicizzazione e l'esecuzione di query di dati geospaziali utilizzando solo il CRS WGS-84. 

## <a name="creating-documents-with-spatial-data"></a>Creazione di documenti con dati spaziali
Quando si creano documenti che contengono valori GeoJSON, essi vengono indicizzati automaticamente con un indice spaziale in base al criterio di indicizzazione della raccolta. Se si utilizza un SDK DocumentDB in un linguaggio tipizzato in modo dinamico come Python o Node.js, è necessario creare un GeoJSON valido.

**Creare documenti con i dati geospaziali in Node.js**

    var userProfileDocument = {
       "name":"documentdb",
       "location":{
          "type":"Point",
          "coordinates":[ -122.12, 47.66 ]
       }
    };

    client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
        // additional code within the callback
    });

Se si lavora con gli SDK .NET (o Java), è possibile utilizzare le nuove classi, Point e Polygon, all'interno dello spazio dei nomi Microsoft.Azure.Documents.Spatial per incorporare le informazioni sulla posizione all'interno di oggetti dell'applicazione. Queste classi consentono di semplificare la serializzazione e la deserializzazione dei dati spaziali in GeoJSON.

**Creare documenti con i dati geospaziali in .NET**

    using Microsoft.Azure.Documents.Spatial;

    public class UserProfile
    {
        [JsonProperty("name")]
        public string Name { get; set; }

        [JsonProperty("location")]
        public Point Location { get; set; }

        // More properties
    }

    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
        new UserProfile 
        { 
            Name = "documentdb", 
            Location = new Point (-122.12, 47.66) 
        });

Se non si dispone delle informazioni di latitudine e longitudine, ma si dispone di indirizzi fisici o del nome della posizione come la città o il paese, è possibile cercare le coordinate effettive tramite un servizio di geocodifica come i servizi REST di Bing Maps. Ulteriori informazioni sulla geocodifica di Bing Maps sono disponibili [qui](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Query sui tipi spaziali
Ora che abbiamo esaminato come inserire i dati geospaziali, diamo un'occhiata a come eseguire query sui dati utilizzando DocumentDB, SQL e LINQ.

### <a name="spatial-sql-built-in-functions"></a>Funzioni predefinite spaziali di SQL
DocumentDB supporta le seguenti funzioni predefinite di Open Geospatial Consortium (OGC) per l'esecuzione di query geospaziali. Per ulteriori informazioni sul set completo di funzioni integrate nel linguaggio SQL, vedere [Query di DocumentDB](documentdb-sql-query.md).

<table>
<tr>
  <td><strong>Utilizzo</strong></td>
  <td><strong>Descrizione</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (spatial_expr, spatial_expr)</td>
  <td>Restituisce la distanza tra le due espressioni GeoJSON punto, poligono o LineString.</td>
</tr>
<tr>
  <td>ST_WITHIN (spatial_expr, spatial_expr)</td>
  <td>Restituisce un'espressione booleana che indica se il primo oggetto GeoJSON (punto, poligono o LineString) è all'interno del secondo oggetto GeoJSON (punto, poligono o LineString).</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>Restituisce un'espressione booleana che indica se i due oggetti GeoJSON specificati (punto, poligono o LineString) si intersecano.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Restituisce un valore booleano che indica se l'espressione GeoJSON punto, poligono o LineString specificata è valida.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Restituisce un valore JSON che contiene un valore booleano valore se l'espressione GeoJSON punto, poligono o LineString specificata è valida e, se non valida, anche il motivo come valore stringa.</td>
</tr>
</table>

Le funzioni spaziali possono essere utilizzate per eseguire query di prossimità rispetto ai dati spaziali. Ad esempio, di seguito è riportata una query che restituisce tutti i documenti della famiglia entro 30 km della posizione specificata utilizzando la funzione predefinita ST_DISTANCE. 

**Query**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Risultati**

    [{
      "id": "WakefieldFamily"
    }]

Se si include l'indicizzazione spaziale nel criterio di indicizzazione, le "query distance" verranno servite in modo efficiente tramite l'indice. Per altre informazioni sull'indicizzazione spaziale, vedere la sezione seguente. Se non si dispone di un indice spaziale per i percorsi specificati, è comunque possibile eseguire query spaziali specificando `x-ms-documentdb-query-enable-scan` intestazione della richiesta con il valore impostato su "true". In .NET, questa operazione può essere eseguita passando l’argomento facoltativo **FeedOptions** alle query con [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) impostato su true. 

Usare ST_WITHIN per verificare se un punto si trova all'interno di un poligono. I poligoni vengono comunemente usati per rappresentare limiti come codici postali, confini di stato o formazioni naturali. Ancora una volta, se si include l'indicizzazione spaziale nel criterio di indicizzazione, le query "within" verranno servite in modo efficiente tramite l'indice. 

Gli argomenti Polygon in ST_WITHIN possono contenere solo un anello singolo, ad esempio i poligoni non devono contenere aree vuote. 

**Query**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Risultati**

    [{
      "id": "WakefieldFamily",
    }]

> [!NOTE]
> Come per il funzionamento di tipi non corrispondenti nella query di DocumentDB, se il valore del percorso specificato nell'argomento è non corretto o non valido, verrà valutato **undefined** e il documento valutato verrà ignorato dai risultati della query. Se la query non restituisce alcun risultato, eseguire ST_ISVALIDDETAILED per eseguire il debug del tipo spatail non valido.     
> 
> 

DocumentDB supporta anche l'esecuzione di query inverse, ovvero è possibile indicizzare poligoni o linee in DocumentDB, quindi eseguire una query per le aree che contengono un punto specificato. Questo modello viene comunemente usato nella logistica per identificare, ad esempio, quando un furgone entra o esce da un'area designata. 

**Query**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**Risultati**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID e ST_ISVALIDDETAILED possono essere utilizzati per verificare la validità di un oggetto spaziale. Ad esempio, la seguente query controlla la validità di un punto con un valore di latitudine fuori scala (-132,8). ST_ISVALID restituisce solo un valore booleano e ST_ISVALIDDETAILED restituisce il valore booleano e una stringa contenente il motivo per cui è considerato non valido.

** Query **

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Risultati**

    [{
      "$1": false
    }]

Queste funzioni possono essere usate anche per convalidare i poligoni. Ad esempio, qui viene usata la funzione ST_ISVALIDDETAILED per convalidare un poligono che non è chiuso. 

**Query**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Risultati**

    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]

### <a name="linq-querying-in-the-net-sdk"></a>Query di LINQ in .NET SDK
Il .NET SDK di DocumentDB fornisce inoltre metodi stub `Distance()` e `Within()` per l'utilizzo all'interno di espressioni LINQ. Il provider LINQ di DocumentDB converte le chiamate di questi metodi nelle chiamate della funzione predefinita di SQL equivalente (ST_DISTANCE e ST_WITHIN rispettivamente). 

Di seguito è riportato un esempio di una query LINQ che consente di trovare tutti i documenti nella raccolta di DocumentDB il cui valore "location" è entro un raggio di 30 km dal punto specificato utilizzando LINQ.

**Query LINQ per Distance**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

Analogamente, di seguito è riportata una query per trovare tutti i documenti il cui valore "location" è all'interno della casella o del poligono specificato. 

**Query LINQ per Within**

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

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


Ora che abbiamo dato un'occhiata a come eseguire una query dei documenti utilizzando LINQ ed SQL, esaminiamo come configurare DocumentDB per l'indicizzazione spaziale.

## <a name="indexing"></a>Indicizzazione
Come illustrato nell’articolo [Indicizzazione indipendente dallo schema con Azure DocumentDB](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) , abbiamo progettato il motore del database DocumentDB per essere realmente indipendente dallo schema e fornire supporto avanzato per JSON. Il motore di database ottimizzato per la scrittura di DocumentDB riconosce a livello nativo i dati spaziali (punti, poligoni e linee) rappresentati nello standard GeoJSON.

In breve, la geometria è proiettata dalle coordinate geodetiche su un piano 2D, quindi suddivisa progressivamente in celle utilizzando un **quadtree**. Queste celle vengono mappate in 1D in base alla posizione della cella all'interno di una **curva di riempimento dello spazio di Hilbert**, che consente di mantenere la posizione dei punti. Inoltre, quando i dati sulla località sono indicizzati, passano attraverso un processo noto come **tassellatura**, vale a dire tutte le celle che intersecano una posizione vengono identificate e archiviate come chiavi dell’indice di DocumentDB. In fase di query, anche argomenti come punti e poligoni sono tassellati per estrarre gli intervalli degli ID delle celle pertinenti e quindi usati per recuperare dati dall'indice.

Se si specifica un criterio di indicizzazione che include un indice spaziale per /* (tutti i percorsi), tutti i punti trovati all'interno dell'insieme vengono indicizzati per query spaziali efficienti (ST_WITHIN e ST_DISTANCE). Gli indici spaziali non hanno un valore di precisione e utilizzano sempre un valore di precisione predefinito.

> [!NOTE]
> DocumentDB supporta l'indicizzazione automatica di tipi di dati Points, Polygons e LineStrings.
> 
> 

Il seguente frammento JSON mostra un criterio di indicizzazione con indicizzazione spaziale abilitata, ossia indicizza qualsiasi punto GeoJSON trovato all'interno di documenti per le query spaziali. Se si modifica il criterio di indicizzazione tramite il portale di Azure, è possibile specificare il seguente JSON come criterio di indicizzazione per abilitare l’indicizzazione spaziale nella raccolta.

**JSON criterio di indicizzazione della raccolta con Spatial abilitato per punti e poligoni**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Ecco un frammento di codice in .NET che illustra come creare una raccolta con indicizzazione spaziale attivata per tutti i percorsi contenenti punti. 

**Creare una raccolta con indicizzazione spaziale**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

Di seguito viene descritto come modificare una raccolta esistente per sfruttare i vantaggi dell'indicizzazione spaziale su tutti i punti archiviati all'interno di documenti.

**Modificare una raccolta esistente con l'indicizzazione spaziale**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [!NOTE]
> Se il valore GeoJSON della posizione all'interno del documento è non corretto o non valido, non verrà indicizzato per le query spaziali. È possibile convalidare valori della posizione utilizzando ST_ISVALID e ST_ISVALIDDETAILED.
> 
> Se la definizione della raccolta include una chiave di partizione, non viene segnalato lo stato di avanzamento della trasformazione dell'indicizzazione. 
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso come iniziare a utilizzare il supporto geospaziale in DocumentDB, è possibile:

* Iniziare a codificare con gli [esempi di codice .NET geospaziale in Github](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
* Utilizzare le query geospaziali nel [Query Playground di DocumentDB](http://www.documentdb.com/sql/demo#geospatial)
* Altre informazioni sulle [query di DocumentDB](documentdb-sql-query.md)
* Ulteriori informazioni sui [criteri di indicizzazione di DocumentDB](documentdb-indexing-policies.md)


