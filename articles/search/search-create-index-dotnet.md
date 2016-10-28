<properties
    pageTitle="Creare un indice di Ricerca di Azure con .NET SDK | Microsoft Azure | Servizio di ricerca cloud ospitato"
    description="Creare un indice nel codice tramite Azure Search .NET SDK."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager=""
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# Creare un indice di Ricerca di Azure con .NET SDK
> [AZURE.SELECTOR]
- [Panoramica](search-what-is-an-index.md)
- [Portale](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)


Questo articolo illustra il processo di creazione di un [indice](https://msdn.microsoft.com/library/azure/dn798941.aspx) di Ricerca di Azure mediante [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Prima di seguire le indicazioni di questa guida e creare un indice, è necessario [creare un servizio Ricerca di Azure](search-create-service-portal.md).

Si noti che tutto il codice di esempio in questo articolo è scritto in C#. Il codice sorgente completo è disponibile su [GitHub](http://aka.ms/search-dotnet-howto).

## I. Identificare la chiave API amministratore del servizio Ricerca di Azure
Dopo avere effettuato il provisioning di un servizio di Ricerca di Azure, è possibile inviare richieste rispetto all'endpoint di servizio usando .NET SDK. Innanzitutto, è necessario ottenere una delle chiavi API amministratore generate per il servizio di ricerca di cui è stato effettuato il provisioning. .NET SDK invierà questa chiave API a ogni richiesta del servizio. La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

1. Per trovare le chiavi API del servizio, è necessario accedere al [portale di Azure](https://portal.azure.com/).
2. Passare al pannello del servizio Ricerca di Azure.
3. Fare clic sull'icona "Chiavi".

Il servizio avrà *chiavi amministratore* e *chiavi di query*.

  - Le *chiavi amministratore* primarie e secondarie concedono diritti completi a tutte le operazioni, inclusa la possibilità di gestire il servizio, creare ed eliminare indici, indicizzatori e origini dati. Sono disponibili due chiavi, quindi è possibile continuare a usare la chiave secondaria se si decide di rigenerare la chiave primaria e viceversa.
  - Le *chiavi di query* concedono l'accesso in sola lettura agli indici e ai documenti e vengono in genere distribuite alle applicazioni client che inviano richieste di ricerca.

Per la creazione di un indice è possibile usare la chiave primaria o secondaria.

<a name="CreateSearchServiceClient"></a>
## II. Creare un'istanza della classe SearchServiceClient
Per iniziare a usare Azure Search .NET SDK, è necessario creare un'istanza della classe `SearchServiceClient`. Questa classe ha diversi costruttori. Il costruttore in uso accetta il nome del servizio di ricerca e un oggetto `SearchCredentials` come parametri. `SearchCredentials` include la chiave API.

Il codice seguente crea un nuovo oggetto `SearchServiceClient` usando i valori del nome del servizio di ricerca e la chiave API archiviati nel file di configurazione dell'applicazione (`app.config` o `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient` ha una proprietà `Indexes`. Questa proprietà fornisce tutti i metodi necessari per creare, elencare, aggiornare o eliminare gli indici di Ricerca di Azure.

> [AZURE.NOTE] La classe `SearchServiceClient` gestisce le connessioni al servizio di ricerca. Per evitare l'apertura di un numero eccessivo di connessioni, se possibile è consigliabile provare a condividere una singola istanza di `SearchServiceClient` nell'applicazione. I metodi per abilitare tale condivisione sono thread-safe.

<a name="DefineIndex"></a>
## III. Definire l'indice di Ricerca di Azure usando la classe `Index`
Una singola chiamata al metodo `Indexes.Create` consente di creare l'indice. Questo metodo accetta come parametro un oggetto `Index` che definisce l'indice di Ricerca di Azure. È necessario creare un oggetto `Index` e inizializzarlo come indicato di seguito:

1. Impostare la proprietà `Name` dell'oggetto `Index` sul nome dell'indice.
2. Impostare la proprietà `Fields` dell'oggetto `Index` su un array di oggetti `Field`. Ogni oggetto `Field` definisce il comportamento di un campo nell'indice. È possibile fornire il nome del campo al costruttore, insieme al tipo di dati (o all'analizzatore per i campi stringa). È inoltre possibile impostare altre proprietà come `IsSearchable`, `IsFilterable` e così via.

È importante tenere in considerazione l'esperienza di ricerca dell'utente e le esigenze aziendali quando si progetta l'indice, perché a ogni `Field` devono essere assegnate le [proprietà opportune](https://msdn.microsoft.com/library/azure/dn798941.aspx). Queste proprietà controllano le funzionalità di ricerca (filtro, facet, ordinamento, ricerca full-text e così via) che vengono applicate ai campi. Per tutte le proprietà non esplicitamente impostate, la classe `Field` disabilita per impostazione predefinita la funzionalità di ricerca corrispondente, a meno che non venga abilitata specificamente.

All'indice di esempio è stato assegnato il nome "hotels" e i campi sono stati definiti come segue:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = new[]
    {
        new Field("hotelId", DataType.String)                       { IsKey = true, IsFilterable = true },
        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("description", DataType.String)                   { IsSearchable = true },
        new Field("description_fr", AnalyzerName.FrLucene),
        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true, IsSortable = true },
        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
        new Field("smokingAllowed", DataType.Boolean)               { IsFilterable = true, IsFacetable = true },
        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
    }
};
```

I valori delle proprietà per ogni `Field` sono stati scelti con attenzione in base al modo in cui si pensa che verranno usati nell'applicazione. Ad esempio, è probabile che gli utenti che cercano degli hotel siano interessati alle corrispondenze delle parole chiave del campo `description`, viene quindi abilitata la ricerca full-text per il campo impostando `IsSearchable` su `true`.

Si noti che esattamente un campo di tipo `DataType.String` nell'indice deve essere il campo designato come _key_ impostando `IsKey` su `true`. Vedere `hotelId` nell'esempio precedente.

La definizione di indice precedente usa un analizzatore personalizzato della lingua per il campo `description_fr`, perché viene usato per archiviare testo in francese. Per altre informazioni sugli analizzatori di lingue, vedere l'[articolo sul supporto per le lingue in MSDN](https://msdn.microsoft.com/library/azure/dn879793.aspx) e il [post di blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) corrispondente.

> [AZURE.NOTE]  Si noti che passando `AnalyzerName.FrLucene` nel costruttore, `Field` automaticamente sarà di tipo `DataType.String` e `IsSearchable` sarà impostato su `true`.

## IV. Creare l'indice
Dopo aver creato un oggetto `Index` inizializzato, è possibile creare l'indice chiamando `Indexes.Create` sull'oggetto `SearchServiceClient`.

```csharp
serviceClient.Indexes.Create(definition);
```

Se la richiesta è corretta, il metodo restituirà un valore regolare. Se si verifica un problema con la richiesta, ad esempio un parametro non valido, il metodo genererà `CloudException`.

Dopo avere usato un indice, se si vuole eliminarlo è sufficiente chiamare il metodo `Indexes.Delete` in `SearchServiceClient`. Ad esempio, per eliminare l'indice "hotels":

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [AZURE.NOTE] Il codice di esempio riportato in questo articolo utilizza i metodi sincroni di .NET SDK di Ricerca di Azure per motivi di semplicità. È consigliabile utilizzare i metodi asincroni nelle proprie applicazioni per mantenerle scalabili e reattive. Ad esempio, negli esempi precedenti è possibile usare `CreateAsync` e `DeleteAsync` anziché `Create` e `Delete`.

## Avanti
Dopo avere creato un indice di Ricerca di Azure, sarà possibile [caricare il contenuto nell'indice](search-what-is-data-import.md), in modo che si possa iniziare a eseguire ricerche nei dati.

<!---HONumber=AcomDC_0831_2016-->