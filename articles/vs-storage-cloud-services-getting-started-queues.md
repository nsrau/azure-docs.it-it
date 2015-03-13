<properties 
	pageTitle="Introduzione all'Archiviazione di Azure" 
	description="Informazioni su come iniziare a usare l'archiviazione di accodamento di Azure in un progetto di servizio cloud in Visual Studio." 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Per iniziare](/documentation/articles/vs-storage-cloud-services-getting-started-queues/)
> - [Risultati](/documentation/articles/vs-storage-cloud-services-what-happened/)

## Introduzione all'Archiviazione di Azure (progetti del servizio cloud)

> [AZURE.SELECTOR]
> - [BLOB](/documentation/articles/vs-storage-cloud-services-getting-started-blobs)
> - [Queues](/documentation/articles/vs-storage-cloud-services-getting-started-queues/)
> - [Tabelle](/documentation/articles/vs-storage-cloud-services-getting-started-tables/)

Il servizio di archiviazione di accodamento di Azure permette di archiviare grandi quantità di messaggi ai quali è possibile accedere da qualsiasi parte del mondo mediante chiamate autenticate tramite HTTP o HTTPS. La dimensione massima di un singolo messaggio della coda è di 64 KB e una coda può contenere milioni di messaggi, nei limiti della capacità complessiva di un account di archiviazione. Per altre informazioni, vedere [Come usare l'archiviazione di accodamento](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-queues/ "How to use Queue Storage from .NET") da .NET.

Aggiungere le seguenti dichiarazioni dello spazio dei nomi del codice all'inizio del file C# in cui si vuole accedere ad Archiviazione di Azure a livello di codice:

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

#####Ottenere la stringa di connessione di archiviazione
Prima di eseguire operazioni relative a una coda, è necessario ottenere la stringa di connessione per l'account di archiviazione in cui risiederà la coda. È possibile usare il tipo **CloudStorageAccount** per rappresentare le informazioni sull'account di archiviazione. Per i progetti di servizi cloud è possibile usare il tipo **CloudConfigurationManager** per recuperare la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure, come mostrato nel codice seguente.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]


<!--HONumber=46--> 
