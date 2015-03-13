<properties 
	pageTitle="Introduzione all'Archiviazione di Azure" 
	description="" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Per iniziare](/documentation/articles/vs-storage-aspnet-getting-started-blobs/)
> - [Risultati](/documentation/articles/vs-storage-aspnet-what-happened/)

## Introduzione all'Archiviazione di Azure (progetti ASP.NET)

> [AZURE.SELECTOR]
> - [BLOB](/documentation/articles/vs-storage-aspnet-getting-started-blobs/)
> - [Code](/documentation/articles/vs-storage-aspnet-getting-started-queues/)
> - [Tabelle](/documentation/articles/vs-storage-aspnet-getting-started-tables/)

L'archiviazione BLOB di Azure è un servizio per l'archiviazione di quantità elevate di dati non strutturati a cui è possibile accedere da qualsiasi parte del mondo tramite HTTP o HTTPS. Un singolo BLOB può avere qualsiasi dimensione. I BLOB possono essere costituiti da immagini, file audio e video, dati non elaborati e file di documento.

Per iniziare, è necessario creare un account di archiviazione di Azure e quindi creare uno o più contenitori nell'archiviazione. Ad esempio, è possibile creare una risorsa di archiviazione denominata "Scrapbook", quindi creare contenitori nella risorsa di archiviazione denominati "images" per archiviare immagini e un altro contenitore denominato "audio" per archiviare file audio. Dopo la creazione dei contenitori, sarà possibile caricarvi singoli file BLOB. Per altre informazioni sulla modifica dei BLOB a livello di codice, vedere [Come usare l'archiviazione BLOB da .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/ "How to use Blob Storage from .NET").

Aggiungere le seguenti dichiarazioni dello spazio dei nomi del codice all'inizio del file C# in cui si vuole accedere ad Archiviazione di Azure a livello di codice:
	
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

##### Ottenere la stringa di connessione di archiviazione
Prima di eseguire operazioni relative a un BLOB, è necessario ottenere la stringa di connessione per l'account di archiviazione in cui risiederà il BLOB. È possibile usare il tipo **CloudStorageAccount** per rappresentare le informazioni sull'account di archiviazione. Per i progetti ASP.NET è possibile usare il tipo **ConfigurationManager** per recuperare la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure, come mostrato nel codice seguente.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[AZURE.INCLUDE [vs-storage-getting-started-blobs-include](../includes/vs-storage-getting-started-blobs-include.md)]

Per altre informazioni, vedere [ASP.NET](http://www.asp.net).
<!--HONumber=42-->
