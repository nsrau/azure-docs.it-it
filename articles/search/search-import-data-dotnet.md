<properties
    pageTitle="Caricamento di dati in Ricerca di Azure tramite .NET SDK | Microsoft Azure | Servizio di ricerca cloud ospitato"
    description="Informazioni su come caricare dati in un indice di Ricerca di Azure tramite .NET SDK."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager=""
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/09/2016"
    ms.author="brjohnst"/>

# Caricare dati in Ricerca di Azure tramite .NET SDK
> [AZURE.SELECTOR]
- [Panoramica](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)

Questo articolo illustra come usare [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) per importare dati in un indice di Ricerca di Azure.

Prima di iniziare questa procedura dettagliata, è necessario avere [creato un indice di Ricerca di Azure](search-what-is-an-index.md). Questo articolo presuppone anche che sia già stato creato un oggetto `SearchServiceClient`, come illustrato nell'articolo relativo alla [creazione di un indice di Ricerca di Azure con .NET SDK](search-create-index-dotnet.md#CreateSearchServiceClient).

Si noti che tutto il codice di esempio in questo articolo è scritto in C#. Il codice sorgente completo è disponibile [in GitHub](http://aka.ms/search-dotnet-howto).

Per eseguire il push di documenti nell'indice usando .NET SDK, è necessario:

  1. Creare un oggetto `SearchIndexClient` da connettere all'indice di ricerca.
  2. Creare un oggetto `IndexBatch` contenente i documenti da aggiungere, modificare o eliminare.
  3. Chiamare il metodo `Documents.Index` dell'oggetto `SearchIndexClient` per inviare `IndexBatch` all'indice di ricerca.

## I. Creare un'istanza della classe SearchIndexClient
Per importare dati nell'indice usando Azure Search .NET SDK, è necessario creare un'istanza della classe `SearchIndexClient`. È possibile creare manualmente questa istanza, ma è più semplice se si ha già un'istanza di `SearchServiceClient` per chiamare il relativo metodo `Indexes.GetClient`. Ad esempio, ecco come ottenere un oggetto `SearchIndexClient` per l'indice denominato "hotels" da un oggetto `SearchServiceClient` denominato `serviceClient`:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [AZURE.NOTE] In un'applicazione di ricerca tipica, il popolamento e la gestione degli indici viene gestita da un componente separato dalle query di ricerca. `Indexes.GetClient` è utile per il popolamento di un indice perché evita la necessità di fornire un altro `SearchCredentials`. Questa operazione viene eseguita passando la chiave di amministrazione utilizzata per creare il `SearchServiceClient` al nuovo `SearchIndexClient`. Tuttavia, nella parte dell'applicazione che esegue le query, è preferibile creare il `SearchIndexClient` direttamente in modo che sia possibile passare una chiave di query anziché una chiave di amministrazione. Questo procedimento è coerente con il [principio del privilegio minimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege) e consente di rendere più sicura l'applicazione. Altre informazioni sulle chiavi di amministrazione e sulle chiavi di query sono disponibili nella [documentazione di riferimento relativa all'API REST di Ricerca di Azure su MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

`SearchIndexClient` include una proprietà `Documents`. Questa proprietà fornisce tutti i metodi necessari per aggiungere, modificare, eliminare o eseguire query sui documenti nell'indice.

## II. Decidere quale azione di indicizzazione usare
Per importare i dati usando .NET SDK, è necessario inserirli in un pacchetto in un oggetto `IndexBatch`. Un oggetto `IndexBatch` incapsula una raccolta di oggetti `IndexAction`, ognuno dei quali contiene un documento e una proprietà che indica a Ricerca di Azure quale azione eseguire sul documento (caricamento, unione, eliminazione e così via). A seconda delle azioni scelte tra le seguenti, per ogni documento devono essere inclusi solo campi specifici:

Azione | Descrizione | Campi necessari per ogni documento | Note
--- | --- | --- | ---
`Upload` | L'azione `Upload` è simile a "upsert", in cui il documento viene inserito se è nuovo e aggiornato o sostituito se esiste già. | chiave, oltre a tutti gli altri campi da definire | Quando si aggiorna o si sostituisce un documento esistente, qualsiasi campo non specificato nella richiesta avrà il campo impostato su `null`. Ciò si verifica anche quando il campo è stato precedentemente impostato su un valore diverso da null.
`Merge` | Aggiorna un documento esistente con i campi specificati. Se il documento non esiste nell'indice, l'unione non riuscirà. | chiave, oltre a tutti gli altri campi da definire | I campi specificati in un'azione di unione sostituiscono i campi esistenti nel documento. Sono inclusi anche i campi di tipo `DataType.Collection(DataType.String)`. Ad esempio, se il documento contiene un campo `tags` con valore `["budget"]` e si esegue un'unione con valore `["economy", "pool"]` per `tags`, il valore finale del campo `tags` sarà `["economy", "pool"]` e non `["budget", "economy", "pool"]`.
`MergeOrUpload` | Questa azione si comporta come `Merge` se nell'indice esiste già un documento con la chiave specificata. Se il documento non esiste, si comporta come `Upload` con un nuovo documento. | chiave, oltre a tutti gli altri campi da definire |-
`Delete` | Rimuove il documento specificato dall'indice. | solo chiave | Tutti i campi specificati oltre al campo della chiave verranno ignorati. Se si vuole rimuovere un singolo campo da un documento, usare invece `Merge` e impostare il campo su Null in modo esplicito.

È possibile specificare l'azione da usare con i vari metodi statici delle classi `IndexBatch` e `IndexAction`, come mostrato nella sezione successiva.

## III. Costruire IndexBatch
Dopo aver appreso quali azioni eseguire sui documenti, è possibile costruire `IndexBatch`. L'esempio seguente illustra come creare un batch con alcune azioni. Si noti che questo esempio usa una classe personalizzata denominata `Hotel` che esegue il mapping a un documento nell'indice "hotels".

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

In questo caso vengono usate `Upload`, `MergeOrUpload` e `Delete` come azioni di ricerca, come specificato dai metodi chiamati nella classe `IndexAction`.

Si supponga che l'indice "hotels" di esempio sia già popolato con alcuni documenti. Si noti che non è stato necessario specificare tutti i possibili campi dei documenti quando si usa `MergeOrUpload` e come viene specificata solo la chiave del documento (`HotelId`) quando si usa `Delete`.

Si noti anche che è possibile includere solo fino a 1000 documenti in una singola richiesta di indicizzazione.

> [AZURE.NOTE] In questo esempio vengono applicate azioni diverse a documenti diversi. Per eseguire le stesse azioni in tutti i documenti nel batch, invece di chiamare `IndexBatch.New` si possono usare gli altri metodi statici di `IndexBatch`. Ad esempio, è possibile creare batch chiamando `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` o `IndexBatch.Delete`. Questi metodi accettano una raccolta di documenti (oggetti di tipo `Hotel` in questo esempio) invece di oggetti `IndexAction`.

## IV. Importare dati nell'indice
Dopo aver creato un oggetto `IndexBatch` inizializzato, è possibile inviarlo all'indice chiamando `Documents.Index` sull'oggetto `SearchIndexClient`. L'esempio seguente mostra come chiamare `Index` e illustra alcune operazioni aggiuntive che è necessario eseguire:

```csharp
try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

Si noti la presenza di `try`/`catch` attorno alla chiamata al metodo `Index`. Il blocco catch gestisce un caso di errore importante per l'indicizzazione. Se il servizio Ricerca di Azure non riesce a indicizzare alcuni dei documenti nel batch, viene generato un `IndexBatchException` da `Documents.Index`. Questa situazione può verificarsi se l'indicizzazione dei documenti avviene mentre il servizio è sovraccarico. **Si consiglia di gestire in modo esplicito questo caso nel codice.** È possibile ritardare e quindi ritentare l'indicizzazione di documenti, accedere e continuare come nell'esempio, oppure eseguire altre attività a seconda dei requisiti di coerenza di dati dell'applicazione.

Infine, il codice nell'esempio precedente prevede un ritardo di due secondi. L'indicizzazione avviene in modo asincrono nel servizio Ricerca di Azure, pertanto l'applicazione di esempio deve attendere un breve periodo per garantire che i documenti siano disponibili per la ricerca. Ritardi come questi in genere sono necessari solo in applicazioni di esempio, test e demo.

<a name="HotelClass"></a>
### Modalità di gestione dei documenti in .NET SDK

Per capire in che modo .NET SDK di Ricerca di Azure è in grado di caricare le istanze di una classe definita dall'utente come `Hotel` nell'indice, si osservi la classe `Hotel`, che esegue il mapping allo schema di indice definito in [Creare un indice di Ricerca di Azure con .NET SDK](search-create-index-dotnet.md#DefineIndex):

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    public string HotelId { get; set; }

    public double? BaseRate { get; set; }

    public string Description { get; set; }

    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    public string HotelName { get; set; }

    public string Category { get; set; }

    public string[] Tags { get; set; }

    public bool? ParkingIncluded { get; set; }

    public bool? SmokingAllowed { get; set; }

    public DateTimeOffset? LastRenovationDate { get; set; }

    public int? Rating { get; set; }

    public GeographyPoint Location { get; set; }

    // ToString() method omitted for brevity...
}
```

La prima cosa da notare è che ogni proprietà pubblica di `Hotel` corrisponde a un campo nella definizione dell'indice, ma con una differenza fondamentale: il nome di ogni campo inizia con una lettera minuscola ("convenzione camel"), mentre il nome di ogni proprietà pubblica di `Hotel` inizia con una lettera maiuscola ("convenzione Pascal"). Si tratta di uno scenario comune in applicazioni .NET che consentono di eseguire l'associazione dati in cui lo schema di destinazione è fuori dal controllo dello sviluppatore dell'applicazione. Anziché dover violare linee guida sulla denominazione di .NET seguendo la convenzione camel per i nomi delle proprietà, è possibile indicare a SDK di eseguire automaticamente il mapping dei nomi delle proprietà alla convenzione camel con l’attributo `[SerializePropertyNamesAsCamelCase]`.

> [AZURE.NOTE] Azure Search .NET SDK usa la libreria [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) per serializzare e deserializzare gli oggetti modello personalizzati in e da JSON. Se necessario, è possibile personalizzare questa serializzazione. Per informazioni più dettagliate, vedere [Aggiornamento ad Azure Search .NET SDK versione 1.1](search-dotnet-sdk-migration.md#WhatsNew). Un esempio è rappresentato dall'uso dell'attributo `[JsonProperty]` sulla proprietà `DescriptionFr` nell'esempio di codice riportato sopra.

Il secondo aspetto importante della classe `Hotel` è costituita dai tipi di dati delle proprietà pubbliche. Viene eseguito il mapping dei tipi .NET di queste proprietà ai tipi di campi equivalenti nella definizione dell'indice. Ad esempio, viene eseguito il mapping della proprietà stringa `Category` al campo `category`, che è di tipo `DataType.String`. Esistono mapping di tipi simili tra `bool?` e `DataType.Boolean`, `DateTimeOffset?` e `DataType.DateTimeOffset` ecc. Le regole specifiche per il mapping dei tipi sono documentate con il metodo `Documents.Get` su [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx).

La possibilità di usare classi personalizzate come documenti funziona in entrambe le direzioni. È anche possibile recuperare i risultati della ricerca e fare in modo che l'SDK li deserializzi automaticamente nel tipo che si preferisce, come illustrato nell'[articolo seguente](search-query-dotnet.md).

> [AZURE.NOTE] .NET SDK di Ricerca di Azure supporta anche documenti tipizzati in modo dinamico utilizzando la classe `Document`, un mapping chiave/valore dei campi e valori dei campi. Ciò è utile negli scenari in cui non si conosce lo schema di indice in fase di progettazione o quando l’associazione a classi di modello specifico non sarebbe conveniente. Tutti i metodi in SDK che gestiscono documenti dispongono di overload che funzionano con la classe `Document`, nonché overload fortemente tipizzati che accettano un parametro di tipo generico. Nel codice di esempio in questo articolo vengono usati solo questi ultimi. Altre informazioni sulla classe `Document` sono disponibili [su MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx).

**Nota importante sui tipi di dati**

Quando si progettano classi di modello personalizzate per eseguire il mapping a un indice di Ricerca di Azure, è consigliabile dichiarare le proprietà dei tipi di valore, ad esempio `bool` e `int` da rendere nullable (ad esempio, `bool?` invece di `bool`). Se si usa una proprietà che non ammette i valori Null, è necessario **garantire** che nessun documento nell'indice contenga un valore Null per il campo corrispondente. Né l'SDK né il servizio di Ricerca di Azure consentiranno di applicare questo valore.

Non è solo un problema ipotetico: si pensi a uno scenario in cui si aggiunge un nuovo campo a un indice esistente di tipo `DataType.Int32`. Dopo l'aggiornamento della definizione dell'indice, tutti i documenti avranno un valore Null per il nuovo campo (perché tutti i tipi sono nullable in Ricerca di Azure). Se quindi si usa una classe di modelli con una proprietà `int` che non ammette i valori Null per tale campo, verrà restituita un'eccezione `JsonSerializationException`, come questa, quando si cercherà di recuperare i documenti:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Per questo motivo, è consigliabile usare tipi nullable nelle classi di modelli.

## Avanti
Dopo il popolamento dell'indice di Ricerca di Azure, si potrà iniziare a eseguire una query per la ricerca di documenti. Per informazioni dettagliate, vedere [Eseguire query su un indice di Ricerca di Azure](search-query-overview.md).

<!----HONumber=AcomDC_0316_2016-->