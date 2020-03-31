---
title: Modellare i dati relazionali SQL per l'importazione e l'indicizzazioneModel SQL relational data for import and indexing
titleSuffix: Azure Cognitive Search
description: Informazioni su come modellare i dati relazionali, denormalizzati in un set di risultati flat, per l'indicizzazione e la ricerca full-text in Ricerca cognitiva di Azure.Learn how to model relational data, de-normalized into a flat result set, for indexing and full text search in Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3b973dd05d23d190c77986ca9bf6d39656739cd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72790085"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>Come modellare i dati SQL relazionali per l'importazione e l'indicizzazione in Ricerca cognitiva di AzureHow to model relational SQL data for import and indexing in Azure Cognitive Search

Ricerca cognitiva di Azure accetta un set di righe flat come input per la [pipeline di indicizzazione.](search-what-is-an-index.md) Se i dati di origine hanno origine da tabelle unite in join in un database relazionale di SQL Server, in questo articolo viene illustrato come creare il set di risultati e come modellare una relazione padre-figlio in un indice di Ricerca cognitiva di Azure.If your source data originates from joined tables in a SQL Server relational database, this article explains how to construct the result set, and how to model a parent-child relationship in an Azure Cognitive Search index.

A dire il vero, ci riferiremo a un ipotetico database degli hotel, basato sui [dati dimostrativi.](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels) Si supponga che il database sia costituito da una tabella Hotels con 50 hotel e da un tavolo Rooms con camere di vario tipo, tariffe e servizi, per un totale di 750 camere. Esiste una relazione uno-a-molti tra le tabelle. Nel nostro approccio, una vista fornirà la query che restituisce 50 righe, una riga per hotel, con i dettagli della stanza associati incorporati in ogni riga.

   ![Tabelle e viste nel database Hotels](media/index-sql-relational-data/hotels-database-tables-view.png "Tabelle e viste nel database Hotels")


## <a name="the-problem-of-denormalized-data"></a>Il problema dei dati denormalizzati

Una delle sfide nell'uso di relazioni uno-a-molti è che le query standard basate su tabelle unite in join restituiranno dati denormalizzati, che non funzionano bene in uno scenario di Ricerca cognitiva di Azure.One of the challenges in working with one-to-many relationships is that standard queries built on joined tables will return denormalized data, which doesn't work well in an Azure Cognitive Search scenario. Si consideri l'esempio seguente che unisce hotel e camere.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
I risultati di questa query restituiscono tutti i campi dell'Hotel, seguiti da tutti i campi della camera, con le informazioni preliminari sull'hotel ripetute per ogni valore della camera.

   ![Dati denormalizzati, dati ridondanti dell'hotel quando vengono aggiunti i campi della stanza](media/index-sql-relational-data/denormalize-data-query.png "Dati denormalizzati, dati ridondanti dell'hotel quando vengono aggiunti i campi della stanza")


Anche se la query ha esito positivo sulla superficie (fornendo tutti i dati in un set di righe semplice), non riesce a fornire la struttura di documento corretta per l'esperienza di ricerca prevista. Durante l'indicizzazione, Ricerca cognitiva di Azure creerà un documento di ricerca per ogni riga ingerita. Se i documenti di ricerca sembravano i risultati di cui sopra, si sarebbe percepito duplicati - sette documenti separati per l'hotel Twin Dome da solo. Una query su "hotel in Florida" restituirebbe sette risultati solo per l'hotel Twin Dome, spingendo altri alberghi rilevanti in profondità nei risultati di ricerca.

Per ottenere l'esperienza prevista di un documento per hotel, è necessario fornire un set di righe alla granularità corretta, ma con informazioni complete. Fortunatamente, è possibile farlo facilmente adottando le tecniche in questo articolo.

## <a name="define-a-query-that-returns-embedded-json"></a>Definire una query che restituisce JSON incorporatoDefine a query that returns embedded JSON

Per offrire l'esperienza di ricerca prevista, il set di dati deve essere costituito da una riga per ogni documento di ricerca in Ricerca cognitiva di Azure.To deliver the expected search experience, your data set should consist of one row for each search document in Azure Cognitive Search. Nel nostro esempio, vogliamo una riga per ogni hotel, ma vogliamo anche che i nostri utenti siano in grado di cercare in altri campi relativi alla stanza a cui tengono, come la tariffa notturna, le dimensioni e il numero di letti o una vista sulla spiaggia, tutti parte dei dettagli di una stanza.

La soluzione consiste nell'acquisire i dettagli della room come JSON annidato e quindi inserire la struttura JSON in un campo in una visualizzazione, come illustrato nel secondo passaggio. 

1. Si supponga di disporre di due tabelle unite, Hotels e Rooms, che contengono dettagli per 50 hotel e 750 camere, e sono uniti nel campo HotelID. Individualmente, questi tavoli contengono 50 hotel e 750 camere correlate.

    ```sql
    CREATE TABLE [dbo].[Hotels$](
      [HotelID] [nchar](10) NOT NULL,
      [HotelName] [nvarchar](255) NULL,
      [Description] [nvarchar](max) NULL,
      [Description_fr] [nvarchar](max) NULL,
      [Category] [nvarchar](255) NULL,
      [Tags] [nvarchar](255) NULL,
      [ParkingIncluded] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [LastRenovationDate] [smalldatetime] NULL,
      [Rating] [float] NULL,
      [StreetAddress] [nvarchar](255) NULL,
      [City] [nvarchar](255) NULL,
      [State] [nvarchar](255) NULL,
      [ZipCode] [nvarchar](255) NULL,
      [GeoCoordinates] [nvarchar](255) NULL
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
    GO

    CREATE TABLE [dbo].[Rooms$](
      [HotelID] [nchar](10) NULL,
      [Description] [nvarchar](255) NULL,
      [Description_fr] [nvarchar](255) NULL,
      [Type] [nvarchar](255) NULL,
      [BaseRate] [float] NULL,
      [BedOptions] [nvarchar](255) NULL,
      [SleepsCount] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [Tags] [nvarchar](255) NULL
    ) ON [PRIMARY]
    GO
    ```

2. Creare una visualizzazione composta da tutti`SELECT * from dbo.Hotels$`i campi della tabella padre ( ), con l'aggiunta di un nuovo campo *Stanze* contenente l'output di una query nidificata. Una clausola FOR `SELECT * from dbo.Rooms$` JSON **AUTO** nelle strutture dell'output come JSON. 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   La schermata seguente mostra la visualizzazione risultante, con *il* campo Rooms nvarchar nella parte inferiore. Il campo *Camere* esiste solo nella vista HotelRooms.

   ![Vista HotelRooms](media/index-sql-relational-data/hotelsrooms-view.png "Visualizzazione HoteRooms")

1. Eseguire `SELECT * FROM dbo.HotelRooms` per recuperare il set di righe. Questa query restituisce 50 righe, una per hotel, con informazioni sulla stanza associate come raccolta JSON. 

   ![Set di righe dalla visualizzazione HotelRoomsRowset from HotelRooms view](media/index-sql-relational-data/hotelrooms-rowset.png "Set di righe dalla visualizzazione HotelRoomsRowset from HotelRooms view")

Questo set di righe è ora pronto per l'importazione in Ricerca cognitiva di Azure.This rowset is now ready for import into Azure Cognitive Search.

> [!NOTE]
> Questo approccio presuppone che JSON incorporato sia inferiore ai [limiti di dimensione massima delle colonne di SQL Server.](https://docs.microsoft.com/sql/sql-server/maximum-capacity-specifications-for-sql-server) Se i dati non rientrano, è possibile provare un approccio a livello di codice, come illustrato in [Esempio: modellare il database adventureWorks Inventory per Ricerca cognitiva di Azure](search-example-adventureworks-modeling.md).

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>Utilizzare una raccolta complessa per il lato "molti" di una relazione uno-a-molti

Sul lato Ricerca cognitiva di Azure creare uno schema di indice che modelli la relazione uno-a-molti usando JSON annidato. Il set di risultati creato nella sezione precedente corrisponde in genere allo schema dell'indice fornito di seguito (alcuni campi sono stati tagliati per brevità).

L'esempio seguente è simile all'esempio in [Come modellare tipi di dati complessi.](search-howto-complex-data-types.md#creating-complex-fields) La struttura *Rooms,* che è stata al centro di questo articolo, è nella raccolta di campi di un indice denominato *hotels*. In questo esempio viene inoltre illustrato un tipo complesso per *Address*, che differisce da *Rooms* in quanto è composto da un set fisso di elementi, anziché il numero multiplo arbitrario di elementi consentiti in una raccolta.

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
        { "name": "BedOptions", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true },
        { "name": "SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
        { "name": "SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
        { "name": "Tags", "type": "Edm.Collection", "searchable": true }
      ]
    }
  ]
}
```

Dato il set di risultati precedente e lo schema dell'indice precedente, si dispone di tutti i componenti necessari per una corretta operazione di indicizzazione. Il set di dati appiattito soddisfa i requisiti di indicizzazione ma conserva le informazioni di dettaglio. Nell'indice di Ricerca cognitiva di Azure, i risultati della ricerca cadranno facilmente nelle entità basate su hotel, preservando il contesto delle singole chat room e dei relativi attributi.

## <a name="next-steps"></a>Passaggi successivi

Utilizzando il proprio set di dati, è possibile utilizzare [l'Importazione guidata dati](search-import-data-portal.md) per creare e caricare l'indice. La procedura guidata rileva l'insieme JSON incorporato, ad esempio quello contenuto in *Rooms*, e deduce uno schema di indice che include una raccolta di tipi complessi. 

  ![Indicizzazione dedotto dall'Importazione guidata dati](media/index-sql-relational-data/search-index-rooms-complex-collection.png "Indicizzazione dedotto dall'Importazione guidata dati")

Provare la guida introduttiva seguente per informazioni sui passaggi di base dell'Importazione guidata dati.

> [!div class="nextstepaction"]
> [Guida introduttiva: Creare un indice di ricerca usando il portale di AzureQuickstart: Create a search index using Azure portal](search-get-started-portal.md)