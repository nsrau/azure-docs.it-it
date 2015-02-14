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
> - [Per iniziare](/documentation/articles/vs-storage-aspnet-getting-started-queues/)
> - [Risultati](/documentation/articles/vs-storage-aspnet-what-happened/)

## Introduzione all'Archiviazione di Azure (progetti ASP.NET)

> [AZURE.SELECTOR]
> - [BLOB](/documentation/articles/vs-storage-aspnet-getting-started-blobs/)
> - [Code](/documentation/articles/vs-storage-aspnet-getting-started-queues/)
> - [Tabelle](/documentation/articles/vs-storage-aspnet-getting-started-tables/)

Il servizio di archiviazione di accodamento di Azure consente di archiviare grandi quantità di messaggi ai quali è possibile accedere da qualsiasi parte del mondo mediante chiamate autenticate tramite HTTP o HTTPS. La dimensione massima di un singolo messaggio della coda è di 64 KB e una coda può contenere milioni di messaggi, nei limiti della capacità complessiva di un account di archiviazione. Per altre informazioni, vedere [Come usare l'archiviazione di accodamento da .NET](http://azure.microsoft.com/it-it/documentation/articles/storage-dotnet-how-to-use-queues/).

Aggiungere le seguenti dichiarazioni dello spazio dei nomi del codice all'inizio del file C# in cui si vuole accedere ad Archiviazione di Azure a livello di codice:

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

##### Ottenere la stringa di connessione di archiviazione
Prima di eseguire operazioni relative a una coda, è necessario ottenere la stringa di connessione per l'account di archiviazione in cui risiederanno le code. È possibile usare il tipo **CloudStorageAccount** per rappresentare le informazioni sull'account di archiviazione. Per i progetti ASP.NET è possibile usare il tipo **ConfigurationManager** per recuperare la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure, come mostrato nel codice seguente.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      ConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[AZURE.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]

Per altre informazioni, vedere [ASP.NET](http://www.asp.net).<!--HONumber=42-->
