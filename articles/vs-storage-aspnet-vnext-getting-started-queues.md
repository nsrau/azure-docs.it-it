<properties title="Introduzione all'Archiviazione di Azure" pageTitle="Introduzione all'Archiviazione di Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [Introduzione][Introduzione]
> -   [Risultati][Risultati]

## Introduzione all'Archiviazione di Azure (progetti ASP.NET vNext)

> [AZURE.SELECTOR]
>
> -   [BLOB][BLOB]
> -   [Code][Introduzione]
> -   [Tabelle][Tabelle]

Il servizio di archiviazione di accodamento di Azure permette di archiviare grandi quantità di messaggi ai quali è possibile accedere da qualsiasi parte del mondo mediante chiamate autenticate tramite HTTP o HTTPS. La dimensione massima di un singolo messaggio della coda è di 64 KB e una coda può contenere milioni di messaggi, nei limiti della capacità complessiva di un account di archiviazione. Per altre informazioni, vedere [Come utilizzare l'archiviazione di accodamento da .NET][Come utilizzare l'archiviazione di accodamento da .NET].

Per accedere alle code a livello di codice in progetti vNext ASP.NET, è necessario eseguire le attività seguenti.

1.  Aggiungere la seguente dichiarazione dello spazio dei nomi del codice all'inizio del file C# in cui si vuole accedere ad Archiviazione di Azure a livello di programmazione:

        using Microsoft.Framework.ConfigurationModel;

2.  Usare il codice seguente per ottenere l'impostazione di configurazione.

        var config = new Configuration();
        config.AddJsonFile("config.json");

##### Ottenere la stringa di connessione di archiviazione

Prima di eseguire operazioni relative a una coda, è necessario ottenere la stringa di connessione per l'account di archiviazione in cui risiederanno le code. È possibile usare il tipo **CloudStorageAccount** per rappresentare le informazioni sull'account di archiviazione. Se si usa un progetto vNext ASP.NET, sarà possibile chiamare il metodo Get dell'oggetto Configuration per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure, come mostrato nel codice seguente.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]

Per altre informazioni, vedere [ASP.NET vNext][ASP.NET vNext].

  [Introduzione]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-queues/
  [Risultati]: /documentation/articles/vs-storage-aspnet-vnext-what-happened/
  [BLOB]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs/
  [Tabelle]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-tables/
  [Come utilizzare l'archiviazione di accodamento da .NET]: http://azure.microsoft.com/it-it/documentation/articles/storage-dotnet-how-to-use-queues/ "How to use Queue Storage from .NET"
  [ASP.NET vNext]: http://www.asp.net/vnext
