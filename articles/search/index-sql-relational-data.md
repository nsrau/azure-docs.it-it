---
title: Modellare i dati relazionali SQL per l'importazione e l'indicizzazione
titleSuffix: Azure Cognitive Search
description: Informazioni su come modellare i dati relazionali, denormalizzati in un set di risultati Flat, per l'indicizzazione e la ricerca full-text in Azure ricerca cognitiva.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3b973dd05d23d190c77986ca9bf6d39656739cd8
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790085"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>Come modellare i dati SQL relazionali per l'importazione e l'indicizzazione in Azure ricerca cognitiva

Azure ricerca cognitiva accetta un set di righe flat come input per la [pipeline di indicizzazione](search-what-is-an-index.md). Se i dati di origine provengono da tabelle unite in join in un database relazionale SQL Server, in questo articolo viene illustrato come costruire il set di risultati e come modellare una relazione padre-figlio in un indice di ricerca cognitiva di Azure.

Come esempio, si farà riferimento a un ipotetico database di Hotel, basato sui [dati dimostrativi](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels). Si supponga che il database sia costituito da una tabella Hotels $ con 50 Hotels e da una tabella Rooms $ con camere di tipo, tariffe e servizi diversi, per un totale di 750 chat room. Esiste una relazione uno-a-molti tra le tabelle. In questo approccio, una vista fornirà la query che restituisce 50 righe, una riga per ogni albergo, con i dettagli della stanza associati incorporati in ogni riga.

   ![Tabelle e viste nel database di Hotel](media/index-sql-relational-data/hotels-database-tables-view.png "Tabelle e viste nel database di Hotel")


## <a name="the-problem-of-denormalized-data"></a>Il problema dei dati denormalizzati

Una delle complicazioni legate all'uso di relazioni uno-a-molti è che le query standard basate su tabelle unite in join restituiscono dati denormalizzati, che non funzionano correttamente in uno scenario di ricerca cognitiva di Azure. Si consideri l'esempio seguente che unisce Alberghi e chat room.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
I risultati di questa query restituiscono tutti i campi dell'oggetto, seguiti da tutti i campi della stanza, con le informazioni preliminari sugli hotel ripetute per ogni valore di room.

   ![Dati denormalizzati, dati di Hotel ridondanti quando vengono aggiunti campi room](media/index-sql-relational-data/denormalize-data-query.png "Dati denormalizzati, dati di Hotel ridondanti quando vengono aggiunti campi room")


Quando la query ha esito positivo sulla superficie (fornendo tutti i dati in un set di righe Flat), non riesce a fornire la struttura del documento corretta per l'esperienza di ricerca prevista. Durante l'indicizzazione, Azure ricerca cognitiva creerà un documento di ricerca per ogni riga inserita. Se i documenti di ricerca sono simili ai risultati precedenti, è possibile che vengano percepiti duplicati, ovvero sette documenti distinti per l'Hotel Twin Dome. Una query su "Hotels in Florida" restituisce sette risultati solo per l'Hotel Twin Dome, inserendo altri alberghi rilevanti nei risultati della ricerca.

Per ottenere l'esperienza prevista di un documento per ogni albergo, è necessario fornire un set di righe con la granularità corretta, ma con informazioni complete. Fortunatamente, è possibile eseguire questa operazione con facilità adottando le tecniche descritte in questo articolo.

## <a name="define-a-query-that-returns-embedded-json"></a>Definire una query che restituisca JSON incorporato

Per fornire l'esperienza di ricerca prevista, il set di dati deve essere costituito da una riga per ogni documento di ricerca in Azure ricerca cognitiva. In questo esempio, si vuole una riga per ogni albergo, ma si vuole che gli utenti siano in grado di eseguire ricerche in altri campi correlati alla chat, ad esempio la tariffa notturna, le dimensioni e il numero di letti, oppure una vista della spiaggia, che tutti fanno parte di un dettaglio della chat.

La soluzione consiste nell'acquisire i dettagli della stanza come JSON annidato e quindi inserire la struttura JSON in un campo in una visualizzazione, come illustrato nel secondo passaggio. 

1. Si supponga di disporre di due tabelle unite in join, Hotels $ e chats $, che contengono i dettagli per gli alberghi 50 e 750 e che vengono unite in join nel campo dei Hotel. Singolarmente, queste tabelle contengono 50 Hotels e 750 chat room correlate.

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

2. Creare una vista composta da tutti i campi della tabella padre (`SELECT * from dbo.Hotels$`), con l'aggiunta di un nuovo campo *room* che contiene l'output di una query nidificata. Una clausola **for JSON auto** in `SELECT * from dbo.Rooms$` struttura l'output come JSON. 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   Nella schermata seguente viene illustrata la visualizzazione risultante, con il campo *chats* nvarchar nella parte inferiore. Il campo *chats* esiste solo nella vista hotelrooms.

   ![Visualizzazione HotelRooms](media/index-sql-relational-data/hotelsrooms-view.png "Visualizzazione HoteRooms")

1. Eseguire `SELECT * FROM dbo.HotelRooms` per recuperare il set di righe. Questa query restituisce 50 righe, una per ogni albergo, con le informazioni sulla stanza associate come raccolta JSON. 

   ![Set di righe dalla vista HotelRooms](media/index-sql-relational-data/hotelrooms-rowset.png "Set di righe dalla vista HotelRooms")

Questo set di righe è ora pronto per l'importazione in Azure ricerca cognitiva.

> [!NOTE]
> Questo approccio presuppone che il codice JSON incorporato sia inferiore al [limite massimo di dimensioni della colonna di SQL Server](https://docs.microsoft.com/sql/sql-server/maximum-capacity-specifications-for-sql-server). Se i dati non si adattano, è possibile provare un approccio programmatico, come illustrato nell' [esempio: modellare il database di inventario AdventureWorks per Azure ricerca cognitiva](search-example-adventureworks-modeling.md).

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>Usare una raccolta complessa per il lato "molti" di una relazione uno-a-molti

Sul lato ricerca cognitiva di Azure, creare uno schema dell'indice che modella la relazione uno-a-molti usando JSON annidato. Il set di risultati creato nella sezione precedente corrisponde generalmente allo schema di indice fornito di seguito (si tagliano alcuni campi per brevità).

L'esempio seguente è simile all'esempio di [come modellare tipi di dati complessi](search-howto-complex-data-types.md#creating-complex-fields). La struttura *room* , che è stata l'area di interesse di questo articolo, si trova nella raccolta Fields di un indice denominato *Hotels*. Questo esempio mostra anche un tipo complesso per *Address*, che differisce dalle *chat room* in quanto è costituito da un set fisso di elementi, in contrapposizione al numero arbitrario multiplo di elementi consentiti in una raccolta.

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

Dato il set di risultati precedente e lo schema dell'indice precedente, sono disponibili tutti i componenti necessari per un'operazione di indicizzazione corretta. Il set di dati flat soddisfa i requisiti di indicizzazione ma conserva le informazioni dettagliate. Nell'indice del ricerca cognitiva di Azure i risultati della ricerca rientrano facilmente in entità basate su Hotel, mantenendo al contempo il contesto delle singole chat e dei rispettivi attributi.

## <a name="next-steps"></a>Passaggi successivi

Utilizzando il proprio set di dati, è possibile utilizzare la [procedura guidata Importa dati](search-import-data-portal.md) per creare e caricare l'indice. La procedura guidata rileva la raccolta JSON incorporata, ad esempio quella contenuta in *chat room*, e deduce uno schema di indice che include una raccolta di tipi complessi. 

  ![Indice derivato dall'importazione guidata dati](media/index-sql-relational-data/search-index-rooms-complex-collection.png "Indice derivato dall'importazione guidata dati")

Per informazioni sui passaggi di base della procedura guidata Importa dati, provare a eseguire la Guida introduttiva seguente.

> [!div class="nextstepaction"]
> [Guida introduttiva: creare un indice di ricerca usando portale di Azure](search-get-started-portal.md)