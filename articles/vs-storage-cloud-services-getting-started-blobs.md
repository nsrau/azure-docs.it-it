<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-cloud-services-getting-started-intro][vs-storage-cloud-services-getting-started-intro]]

# Introduzione all'Archiviazione di Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/vs-storage-cloud-services-getting-started-blobs" title="Blobs" class="current">BLOB</a><a href="/it-it/documentation/articles/vs-storage-cloud-services-getting-started-queues" title="Queues">Code</a><a href="/it-it/documentation/articles/vs-storage-cloud-services-getting-started-tables" title="Tables">Tabelle</a></div>

L'archiviazione BLOB di Azure è un servizio per l'archiviazione di quantità elevate di dati non strutturati a cui è possibile accedere da qualsiasi parte del mondo tramite HTTP o HTTPS. Un singolo BLOB può avere qualsiasi dimensione. I BLOB possono essere costituiti da immagini, file audio e video, dati non elaborati e file di documento.

Per iniziare, è necessario creare un account di archiviazione di Azure e quindi creare uno o più contenitori nell'archiviazione. Ad esempio, è possibile creare una risorsa di archiviazione denominata "Scrapbook", quindi creare contenitori nella risorsa di archiviazione denominati "images" per archiviare immagini e un altro contenitore denominato "audio" per archiviare file audio. Dopo la creazione dei contenitori, sarà possibile caricarvi singoli file BLOB. Per altre informazioni sulla modifica dei BLOB a livello di codice, vedere [Come utilizzare l'archiviazione BLOB da .NET][Come utilizzare l'archiviazione BLOB da .NET]s.

Per accedere ai BLOB a livello di codice in un progetto di servizio cloud, è necessario eseguire le attività seguenti.

1.  Ottenere l'assembly Microsoft.WindowsAzure.Storage.dll. A tale scopo, è possibile usare NuGet. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere Gestisci pacchetti NuGet. Cercare online "WindowsAzure.Storage" e fare clic su Installa per installare il pacchetto Archiviazione di Azure e le dipendenze. Aggiungere al progetto un riferimento a questo assembly.
2.  Aggiungere le seguenti dichiarazioni dello spazio dei nomi del codice all'inizio del file C# in cui si vuole accedere ad Archiviazione di Azure a livello di programmazione:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

### Ottenere la stringa di connessione di archiviazione

Prima di eseguire operazioni relative a un BLOB, è necessario ottenere la stringa di connessione per l'account di archiviazione in cui risiederà il BLOB. È possibile usare il tipo **CloudStorageAccount** per rappresentare le informazioni sull'account di archiviazione. Per i progetti di servizi cloud è possibile usare il tipo **CloudConfigurationManager** per recuperare la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure, come mostrato nel codice seguente.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-blobs-include][vs-storage-getting-started-blobs-include]]

  [vs-storage-cloud-services-getting-started-intro]: ../includes/vs-storage-cloud-services-getting-started-intro.md
  [BLOB]: /it-it/documentation/articles/vs-storage-cloud-services-getting-started-blobs "Blobs"
  [Code]: /it-it/documentation/articles/vs-storage-cloud-services-getting-started-queues "Queues"
  [Tabelle]: /it-it/documentation/articles/vs-storage-cloud-services-getting-started-tables "Tables"
  [Come utilizzare l'archiviazione BLOB da .NET]: http://azure.microsoft.com/it-it/documentation/articles/storage-dotnet-how-to-use-blobs/ "How to use Blob Storage from .NET"
  [vs-storage-getting-started-blobs-include]: ../includes/vs-storage-getting-started-blobs-include.md
