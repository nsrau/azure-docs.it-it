---
title: Creare un indice di Ricerca di Azure con .NET SDK | Documentazione Microsoft
description: Creare un indice nel codice tramite Azure Search .NET SDK.
services: search
documentationcenter: 
author: brjohnstmsft
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 3a851647-fc7b-4fb6-8506-6aaa519e77cd
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/22/2017
ms.author: brjohnst
ms.translationtype: Human Translation
ms.sourcegitcommit: 125f05f5dce5a0e4127348de5b280f06c3491d84
ms.openlocfilehash: 0531b5c3b63a3fa54bb331f3d8d09c8119e789ea
ms.contentlocale: it-it
ms.lasthandoff: 05/22/2017


---
# <a name="create-an-azure-search-index-using-the-net-sdk"></a>Creare un indice di Ricerca di Azure con .NET SDK
> [!div class="op_single_selector"]
> * [Panoramica](search-what-is-an-index.md)
> * [Portale](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Questo articolo illustra il processo di creazione di un [indice](https://docs.microsoft.com/rest/api/searchservice/Create-Index) di Ricerca di Azure mediante [Azure Search .NET SDK](https://aka.ms/search-sdk).

Prima di seguire le indicazioni di questa guida e creare un indice, è necessario [creare un servizio Ricerca di Azure](search-create-service-portal.md).

> [!NOTE]
> Tutto il codice di esempio in questo articolo è scritto in C#. Il codice sorgente completo è disponibile su [GitHub](http://aka.ms/search-dotnet-howto). Per una descrizione più dettagliata del codice di esempio, vedere le informazioni relative a [Azure Search .NET SDK](search-howto-dotnet-sdk.md).
>
>

## <a name="identify-your-azure-search-services-admin-api-key"></a>Identificare la chiave API amministratore del servizio Ricerca di Azure
Dopo avere effettuato il provisioning di un servizio di Ricerca di Azure, è possibile inviare richieste rispetto all'endpoint di servizio usando .NET SDK. Innanzitutto, è necessario ottenere una delle chiavi API amministratore generate per il servizio di ricerca di cui è stato effettuato il provisioning. .NET SDK invierà questa chiave API a ogni richiesta del servizio. La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

1. Per trovare le chiavi API del servizio, accedere al [portale di Azure](https://portal.azure.com/)
2. Passare al pannello del servizio Ricerca di Azure.
3. Fare clic sull'icona "Chiavi".

Il servizio avrà *chiavi amministratore* e *chiavi di query*.

* Le *chiavi amministratore* primarie e secondarie concedono diritti completi a tutte le operazioni, inclusa la possibilità di gestire il servizio, creare ed eliminare indici, indicizzatori e origini dati. Sono disponibili due chiavi, quindi è possibile continuare a usare la chiave secondaria se si decide di rigenerare la chiave primaria e viceversa.
* Le *chiavi di query* concedono l'accesso in sola lettura agli indici e ai documenti e vengono in genere distribuite alle applicazioni client che inviano richieste di ricerca.

Per la creazione di un indice è possibile usare la chiave primaria o secondaria.

<a name="CreateSearchServiceClient"></a>

## <a name="create-an-instance-of-the-searchserviceclient-class"></a>Creare un'istanza della classe SearchServiceClient
Per iniziare a usare Azure Search .NET SDK, è necessario creare un'istanza della classe `SearchServiceClient` . Questa classe ha diversi costruttori. Il costruttore in uso accetta il nome del servizio di ricerca e un oggetto `SearchCredentials` come parametri. `SearchCredentials` esegue il wrapping della chiave API.

Il codice seguente crea un nuovo oggetto `SearchServiceClient` usando i valori del nome del servizio di ricerca e della chiave API archiviati nel file di configurazione dell'applicazione, `appsettings.json` nel caso dell'[applicazione di esempio](http://aka.ms/search-dotnet-howto):

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

## <a name="define-your-azure-search-index"></a>Definire l'indice di ricerca di Azure
Una singola chiamata al metodo `Indexes.Create` consente di creare l'indice. Questo metodo accetta come parametro un oggetto `Index` che definisce l'indice di Ricerca di Azure. È necessario creare un oggetto `Index` e inizializzarlo come indicato di seguito:

1. Impostare la proprietà `Name` dell'oggetto `Index` sul nome dell'indice.
2. Impostare la proprietà `Fields` dell'oggetto `Index` su un array di oggetti `Field`. Il modo più semplice per creare gli oggetti `Field` consiste nel chiamare il metodo `FieldBuilder.BuildForType` passando una classe modello per il parametro di tipo. Una classe modello dispone di proprietà corrispondenti ai campi dell'indice. Questo consente di associare documenti dell'indice di ricerca a istanze della classe modello.

> [!NOTE]
> Se non si prevede di usare una classe modello, è comunque possibile definire l'indice mediante la creazione diretta di oggetti `Field`. È possibile fornire il nome del campo al costruttore, insieme al tipo di dati (o all'analizzatore per i campi stringa). È inoltre possibile impostare altre proprietà come `IsSearchable`, `IsFilterable` e così via.
>
>

Quando si progetta l'indice è importante tenere in considerazione l'esperienza di ricerca dell'utente e le esigenze aziendali, perché a ogni campo devono essere assegnate le [proprietà opportune](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Queste proprietà controllano le funzionalità di ricerca (filtro, facet, ordinamento, ricerca full-text e così via) che vengono applicate ai campi. Per tutte le proprietà non esplicitamente impostate, la classe `Field` disabilita per impostazione predefinita la funzionalità di ricerca corrispondente, a meno che non venga abilitata specificamente.

All'indice di esempio è stato assegnato il nome "hotels" e i campi sono stati definiti usando una classe modello. Ogni proprietà della classe modello contiene attributi che determinano i comportamenti correlati alla ricerca del campo di indice corrispondente. La classe modello viene definita come segue:

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

La definizione di indice precedente usa un analizzatore della lingua per il campo `description_fr` perché quest'ultimo viene usato per archiviare testo in francese. Per altre informazioni sugli analizzatori di lingue, vedere l'[articolo sul supporto per le lingue](https://docs.microsoft.com/rest/api/searchservice/Language-support) e il [post di blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) corrispondente.

> [!NOTE]
> Per impostazione predefinita, il nome di ogni proprietà della classe modello viene usato come nome del corrispondente campo dell'indice. Se si intendono mappare tutti i nomi di proprietà ai nomi di campo secondo la convenzione camel, contrassegnare la classe con l'attributo `SerializePropertyNamesAsCamelCase`. Se si intende eseguire il mapping a un nome differente, è possibile usare l'attributo `JsonProperty` come la proprietà `DescriptionFr` precedente. L'attributo `JsonProperty` ha la precedenza sull'attributo `SerializePropertyNamesAsCamelCase`.
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

## <a name="create-the-index"></a>Creare l'indice
Dopo aver creato un oggetto `Index` inizializzato, è possibile creare l'indice chiamando `Indexes.Create` sull'oggetto `SearchServiceClient`.

```csharp
serviceClient.Indexes.Create(definition);
```

Se la richiesta è corretta, il metodo restituirà un valore regolare. Se si verifica un problema con la richiesta, ad esempio un parametro non valido, il metodo genererà `CloudException`.

Dopo avere usato un indice, se si vuole eliminarlo è sufficiente chiamare il metodo `Indexes.Delete` in `SearchServiceClient`. Ad esempio, per eliminare l'indice "hotels":

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> Il codice di esempio riportato in questo articolo utilizza i metodi sincroni di .NET SDK di Ricerca di Azure per motivi di semplicità. È consigliabile utilizzare i metodi asincroni nelle proprie applicazioni per mantenerle scalabili e reattive. Ad esempio, negli esempi precedenti è possibile usare `CreateAsync` e `DeleteAsync` anziché `Create` e `Delete`.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Dopo avere creato un indice di Ricerca di Azure, sarà possibile [caricare il contenuto nell'indice](search-what-is-data-import.md) , in modo che si possa iniziare a eseguire ricerche nei dati.


