<properties title="Introduzione all'Archiviazione di Azure" pageTitle="Introduzione all'Archiviazione di Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Introduzione][Introduzione]
> -   [Risultati][Risultati]

## Introduzione all'Archiviazione di Azure (progetti ASP.NET vNext)

> [AZURE.SELECTOR]
>
> -   [BLOB][Introduzione]
> -   [Code][Code]
> -   [Tabelle][Tabelle]

L'archiviazione BLOB di Azure è un servizio per l'archiviazione di quantità elevate di dati non strutturati a cui è possibile accedere da qualsiasi parte del mondo tramite HTTP o HTTPS. Un singolo BLOB può avere qualsiasi dimensione. I BLOB possono essere costituiti da immagini, file audio e video, dati non elaborati e file di documento.

Introduzione, è necessario creare un account di archiviazione di Azure e quindi creare uno o più contenitori nell'archiviazione. Ad esempio, è possibile creare una risorsa di archiviazione denominata "Scrapbook", quindi creare contenitori nella risorsa di archiviazione denominati "images" per archiviare immagini e un altro contenitore denominato "audio" per archiviare file audio. Dopo la creazione dei contenitori, sarà possibile caricarvi singoli file BLOB. Per altre informazioni sulla modifica dei BLOB a livello di codice, vedere [Come utilizzare l'archiviazione BLOB da .NET][Come utilizzare l'archiviazione BLOB da .NET]s.

Per accedere ai BLOB a livello di codice in progetti vNext ASP.NET, è necessario eseguire le attività seguenti.

1.  Aggiungere la seguente dichiarazione dello spazio dei nomi del codice all'inizio del file C# in cui si vuole accedere ad Archiviazione di Azure a livello di programmazione:

        using Microsoft.Framework.ConfigurationModel;

2.  Usare il codice seguente per ottenere l'impostazione di configurazione.

        var config = new Configuration();
        config.AddJsonFile("config.json");

##### Ottenere la stringa di connessione di archiviazione

Prima di eseguire operazioni relative a un BLOB, è necessario ottenere la stringa di connessione per l'account di archiviazione in cui risiederà il BLOB. È possibile usare il tipo **CloudStorageAccount** per rappresentare le informazioni sull'account di archiviazione. Se si usa un progetto vNext ASP.NET, sarà possibile chiamare il metodo Get dell'oggetto Configuration per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure, come mostrato nel codice seguente.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-blobs-include](../includes/vs-storage-getting-started-blobs-include.md)]

Per altre informazioni, vedere [ASP.NET vNext][ASP.NET vNext].

  [Introduzione]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs/
  [Risultati]: /documentation/articles/vs-storage-aspnet-vnext-what-happened/
  [Code]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-queues/
  [Tabelle]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-tables/
  [Come utilizzare l'archiviazione BLOB da .NET]: http://azure.microsoft.com/it-it/documentation/articles/storage-dotnet-how-to-use-blobs/ "How to use Blob Storage from .NET"
  [vs-storage-getting-started-blobs-include]: ../includes/vs-storage-getting-started-blobs-include.md
  [ASP.NET vNext]: http://www.asp.net/vnext
