---
title: "Avvio rapido: Creare un indice in un'applicazione console C# - Ricerca di Azure"
description: Informazioni su come creare un indice di ricerca full-text in C# usando l'SDK .NET della Ricerca di Azure.
author: heidisteen
manager: cgronlun
ms.author: heidist
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/08/2019
ms.openlocfilehash: 83842893e0ffc6bb954832cd65b6312b59bbcaa3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59269045"
---
# <a name="quickstart-1---create-an-azure-search-index-in-c"></a>Avvio rapido: 1 - Creare un indice di Ricerca di Azure in C#
> [!div class="op_single_selector"]
> * [C#](search-create-index-dotnet.md)
> * [Portale](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
> * [Postman](search-fiddler.md)
>*

Questo articolo illustra il processo di creazione di un [indice di Ricerca di Azure](search-what-is-an-index.md) mediante C# e [.NET SDK](https://aka.ms/search-sdk). Questa è la prima lezione di un esercizio in tre parti per la creazione e il caricamento di un indice e l'esecuzione di query su tale indice. Per creare un indice, è necessario eseguire queste attività:

> [!div class="checklist"]
> * Creare un oggetto [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) da connettere a un servizio di ricerca.
> * Creare un oggetto [`Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) da passare come parametro a `Indexes.Create`.
> * Chiamare il metodo `Indexes.Create` in `SearchServiceClient` per inviare `Index` a un servizio.

## <a name="prerequisites"></a>Prerequisiti

In questa guida di avvio rapido vengono usati i servizi, gli strumenti e i dati seguenti. 

[Creare un servizio Ricerca di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. È possibile usare un servizio gratuito per questo avvio rapido.

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), qualsiasi edizione. Il codice di esempio e le istruzioni sono stati testati nell'edizione Community Edition gratuita.

[DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) fornisce la soluzione di esempio, un'applicazione console .NET Core scritta in C#, disponibile nel repository GitHub di esempi di Azure. Scaricare ed estrarre la soluzione. Per impostazione predefinita, le soluzioni sono di sola lettura. Fare clic con il pulsante destro del mouse sulla soluzione e deselezionare l'attributo di sola lettura in modo che sia possibile modificare i file. I dati sono inclusi nella soluzione.

## <a name="get-a-key-and-url"></a>Ottenere una chiave e un URL

Le chiamate al servizio richiedono un URL endpoint e una chiave di accesso per ogni richiesta. Con entrambi gli elementi viene creato un servizio di ricerca, quindi se si è aggiunto Ricerca di Azure alla sottoscrizione, seguire questi passaggi per ottenere le informazioni necessarie:

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere l'URL nella pagina **Panoramica** del servizio di ricerca. Un endpoint di esempio potrebbe essere simile a `https://mydemo.search.windows.net`.

2. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per diritti completi sul servizio. Sono disponibili due chiavi amministratore interscambiabili, fornite per continuità aziendale nel caso in cui sia necessario eseguire il rollover di una di esse. È possibile usare la chiave primaria o secondaria nelle richieste per l'aggiunta, la modifica e l'eliminazione di oggetti.

![Ottenere una chiave di accesso e un endpoint HTTP](media/search-fiddler/get-url-key.png "Ottenere una chiave di accesso e un endpoint HTTP")

Per ogni richiesta inviata al servizio è necessario specificare una chiave API. La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

## <a name="1---configure-and-build"></a>1 - Configurare e compilare

1. Aprire il file **DotNetHowTo.sln** in Visual Studio.

1. In appsettings.json sostituire il contenuto predefinito con l'esempio seguente e quindi specificare il nome del servizio e la chiave API amministratore per il servizio. 


   ```json
   {
       "SearchServiceName": "Put your search service name here (not the full URL)",
       "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
    }
   ```

  Per il nome del servizio, è sufficiente il nome stesso. Se ad esempio l'URL è https://mydemo.search.windows.net, aggiungere `mydemo` al file JSON.

1. Premere F5 per compilare la soluzione ed eseguire l'app console. I passaggi rimanenti di questo esercizio e quelli che seguono illustrano il funzionamento di questo codice. 

In alternativa è possibile vedere [Come usare Ricerca di Azure da un'applicazione .NET](search-howto-dotnet-sdk.md) per informazioni sui comportamenti dell'SDK. 

<a name="CreateSearchServiceClient"></a>

## <a name="2---create-a-client"></a>2 - Creare un client

Per iniziare a usare .NET SDK per Ricerca di Azure, creare un'istanza della classe `SearchServiceClient`. Questa classe ha diversi costruttori. Il costruttore in uso accetta il nome del servizio di ricerca e un oggetto `SearchCredentials` come parametri. `SearchCredentials` esegue il wrapping della chiave API.

Il codice seguente è contenuto nel file Program.cs. Consente di creare un nuovo oggetto `SearchServiceClient` usando i valori del nome del servizio di ricerca e della chiave API archiviati nel file config dell'applicazione (appsettings.json).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

`SearchServiceClient` ha una proprietà `Indexes`. Questa proprietà fornisce tutti i metodi necessari per creare, elencare, aggiornare o eliminare gli indici di Ricerca di Azure.

> [!NOTE]
> La classe `SearchServiceClient` gestisce le connessioni al servizio di ricerca. Per evitare l'apertura di un numero eccessivo di connessioni, se possibile è consigliabile provare a condividere una singola istanza di `SearchServiceClient` nell'applicazione. I metodi per abilitare tale condivisione sono thread-safe.
> 
> 

<a name="DefineIndex"></a>

## <a name="3---construct-index"></a>3 - Costruire l'indice
Una singola chiamata al metodo `Indexes.Create` consente di creare un indice. Questo metodo accetta come parametro un oggetto `Index` che definisce un indice di Ricerca di Azure. Creare un oggetto `Index` e inizializzarlo come indicato di seguito:

1. Impostare la proprietà `Name` dell'oggetto `Index` sul nome dell'indice.

2. Impostare la proprietà `Fields` dell'oggetto `Index` su un array di oggetti `Field`. Il modo più semplice per creare gli oggetti `Field` consiste nel chiamare il metodo `FieldBuilder.BuildForType` passando una classe modello per il parametro di tipo. Una classe modello dispone di proprietà corrispondenti ai campi dell'indice. Questo consente di associare documenti dell'indice di ricerca a istanze della classe modello.

> [!NOTE]
> Se non si prevede di usare una classe modello, è comunque possibile definire l'indice mediante la creazione diretta di oggetti `Field`. È possibile fornire il nome del campo al costruttore, insieme al tipo di dati (o all'analizzatore per i campi stringa). È inoltre possibile impostare altre proprietà, come ad esempio `IsSearchable` e `IsFilterable`.
>
>

È importante tenere in considerazione l'esperienza di ricerca dell'utente e le esigenze aziendali quando si progetta un indice. A ogni campo devono essere assegnati gli [attributi](https://docs.microsoft.com/rest/api/searchservice/Create-Index) che controllano le funzionalità di ricerca (filtri, facet, ordinamento e così via) da applicare e i campi a cui applicarle. Per tutte le proprietà non esplicitamente impostate, la classe `Field` disabilita per impostazione predefinita la funzionalità di ricerca corrispondente, a meno che non venga abilitata specificamente.

In questo esempio il nome dell'indice è "hotels" e i campi vengono definiti usando una classe modello. Ogni proprietà della classe modello contiene attributi che determinano i comportamenti correlati alla ricerca del campo di indice corrispondente. La classe modello viene definita come segue:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }
}
```

Gli attributi per ogni proprietà sono stati scelti con attenzione in base al modo in cui si pensa che verranno usati nell'applicazione. Ad esempio, è probabile che gli utenti che cercano degli hotel siano interessati alle corrispondenze delle parole chiave nel campo `description`. Viene quindi abilitata la ricerca full-text per il campo mediante l'aggiunta dell'attributo `IsSearchable` alla proprietà `Description`.

Si noti che esattamente un campo di tipo `string` dell'indice deve essere il campo designato come *key* mediante l'aggiunta dell'attributo `Key`. Vedere `HotelId` nell'esempio precedente.

La definizione di indice precedente usa un analizzatore della lingua per il campo `description_fr` perché quest'ultimo viene usato per archiviare testo in francese. Per altre informazioni, vedere [Aggiungere analizzatori del linguaggio a un indice di Ricerca di Azure](index-add-language-analyzers.md).

> [!NOTE]
> Per impostazione predefinita, il nome di ogni proprietà nella classe modello corrisponde al nome del campo nell'indice. Se si intendono mappare tutti i nomi di proprietà ai nomi di campo secondo la convenzione camel, contrassegnare la classe con l'attributo `SerializePropertyNamesAsCamelCase`. Se si intende eseguire il mapping a un nome differente, è possibile usare l'attributo `JsonProperty` come la proprietà `DescriptionFr` precedente. L'attributo `JsonProperty` ha la precedenza sull'attributo `SerializePropertyNamesAsCamelCase`.
> 
> 

Dopo aver definito una classe modello, è possibile creare una definizione di indice in modo molto semplice:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = FieldBuilder.BuildForType<Hotel>()
};
```

## <a name="4---call-indexescreate"></a>4 - Chiamare Indexes.Create
Dopo aver creato un oggetto `Index` inizializzato, creare l'indice chiamando `Indexes.Create` nell'oggetto `SearchServiceClient`:

```csharp
serviceClient.Indexes.Create(definition);
```

Se la richiesta è corretta, il metodo restituirà un valore regolare. Se si verifica un problema con la richiesta, ad esempio un parametro non valido, il metodo genererà `CloudException`.

Dopo aver usato un indice, se si vuole eliminarlo chiamare il metodo `Indexes.Delete` in `SearchServiceClient`. Ad esempio: 

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> Il codice di esempio riportato in questo articolo utilizza i metodi sincroni di .NET SDK di Ricerca di Azure per motivi di semplicità. È consigliabile utilizzare i metodi asincroni nelle proprie applicazioni per mantenerle scalabili e reattive. Ad esempio, negli esempi precedenti è possibile usare `CreateAsync` e `DeleteAsync` anziché `Create` e `Delete`.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
In questo argomento di avvio rapido è stato creato un indice di Ricerca di Azure vuoto basato su uno schema che definisce i tipi e i comportamenti dei dati dei campi. L'indice consiste essenzialmente in un nome e una raccolta di campi con attributo. Un indice più realistico includerebbe altri elementi, ad esempio [profili di punteggio](index-add-scoring-profiles.md), [strumenti suggerimenti](index-add-suggesters.md) per il supporto automatico, [sinonimi](search-synonyms.md)ed eventualmente [analizzatori personalizzati](index-add-custom-analyzers.md). È consigliabile rivedere queste funzionalità dopo aver compreso il flusso di lavoro di base.

L'argomento di avvio rapido successivo di questa serie illustrerà come caricare l'indice con contenuto disponibile per la ricerca.

> [!div class="nextstepaction"]
> [Caricare dati in un indice di Ricerca di Azure tramite C#](search-import-data-dotnet.md)