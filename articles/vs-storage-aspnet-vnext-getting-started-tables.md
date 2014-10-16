<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-aspnet-vnext-getting-started-intro][vs-storage-aspnet-vnext-getting-started-intro]]

# Introduzione all'Archiviazione di Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs" title="Blobs" class="current">BLOB</a><a href="/it-it/documentation/articles/vs-storage-aspnet-vnext-getting-started-queues" title="Queues">Code</a><a href="/it-it/documentation/articles/vs-storage-aspnet-vnext-getting-started-tables" title="Tables">Tabelle</a></div>

Il servizio di archiviazione tabelle di Azure permette di archiviare grandi quantità di dati strutturati. Il servizio è un datastore NoSQL che accetta chiamate autenticate dall'interno e dall'esterno del cloud di Azure. Le tabelle di Azure sono ideali per l'archiviazione di dati strutturati non relazionali. Per altre informazioni, vedere [Come utilizzare l'archiviazione tabelle da .NET][Come utilizzare l'archiviazione tabelle da .NET].

Per accedere alle tabelle a livello di codice in progetti vNext ASP.NET, è necessario eseguire le attività seguenti.

1.  Ottenere l'assembly Microsoft.WindowsAzure.Storage.dll. A tale scopo, è possibile usare NuGet. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere Gestisci pacchetti NuGet. Cercare online "WindowsAzure.Storage" e fare clic su Installa per installare il pacchetto Archiviazione di Azure e le dipendenze. Aggiungere al progetto un riferimento a questo assembly.
2.  Installare il pacchetto NuGet Microsoft.Framework.ConfigurationModel.Json.
3.  Aggiungere la seguente dichiarazione dello spazio dei nomi del codice all'inizio del file C# in cui si vuole accedere ad Archiviazione di Azure a livello di programmazione:

        using Microsoft.Framework.ConfigurationModel;

4.  Usare il codice seguente per ottenere l'impostazione di configurazione.

        var config = new Configuration();
        config.AddJsonFile("config.json");

### Ottenere la stringa di connessione di archiviazione

Prima di eseguire operazioni relative a una tabella, è necessario ottenere la stringa di connessione per l'account di archiviazione in cui risiederanno le tabelle. È possibile usare il tipo **CloudStorageAccount** per rappresentare le informazioni sull'account di archiviazione. Se si usa un progetto vNext ASP.NET, sarà possibile chiamare il metodo Get dell'oggetto Configuration per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure, come mostrato nel codice seguente.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include][vs-storage-getting-started-tables-include]]

  [vs-storage-aspnet-vnext-getting-started-intro]: ../includes/vs-storage-aspnet-vnext-getting-started-intro.md
  [BLOB]: /it-it/documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs "Blobs"
  [Code]: /it-it/documentation/articles/vs-storage-aspnet-vnext-getting-started-queues "Queues"
  [Tabelle]: /it-it/documentation/articles/vs-storage-aspnet-vnext-getting-started-tables "Tables"
  [Come utilizzare l'archiviazione tabelle da .NET]: http://azure.microsoft.com/it-it/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "How to use Table Storage from .NET"
  [vs-storage-getting-started-tables-include]: ../includes/vs-storage-getting-started-tables-include.md
