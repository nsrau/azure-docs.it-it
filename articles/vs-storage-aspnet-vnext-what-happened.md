<properties title="Introduzione all'Archiviazione di Azure" pageTitle="Introduzione all'Archiviazione di Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [Introduzione][Introduzione]
> -   [Risultati][Risultati]

### <span id="whathappened">Che cosa è successo al progetto?</span>

##### Aggiunta di riferimenti

Il pacchetto NuGet per l'Archiviazione di Azure è stato aggiunto al progetto Visual Studio.
Il pacchetto aggiunge i riferimenti a .NET seguenti:

-   `Microsoft.Data.Edm`
-   `Microsoft.Data.OData`
-   `Microsoft.Data.Services.Client`
-   `Microsoft.WindowsAzure.Configuration`
-   `Microsoft.WindowsAzure.Storage`
-   `Newtonsoft.Json`
-   `System.Data`
-   `System.Spatial`

È stato inoltre aggiunto il pacchetto NuGet **Microsoft.Framework.ConfigurationModel.Json**.

##### Aggiunta di stringa di connessione per l'Archiviazione di Azure

Nel file config.json del progetto è stato creato un elemento con la stringa di connessione e la chiave dell'account di archiviazione selezionato.

Per altre informazioni, vedere [ASP.NET vNext][ASP.NET vNext].

  [Introduzione]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs/
  [Risultati]: /documentation/articles/vs-storage-aspnet-vnext-what-happened/
  [ASP.NET vNext]: http://www.asp.net/vnext
