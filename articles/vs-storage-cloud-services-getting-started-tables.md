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
	ms.date="09/17/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Per iniziare](vs-storage-cloud-services-getting-started-tables.md)
> - [Risultati](vs-storage-cloud-services-what-happened.md)

##Introduzione all'Archiviazione di Azure (progetti del servizio cloud)

> [AZURE.SELECTOR]
> - [BLOB](vs-storage-cloud-services-getting-started-blobs.md)
> - [Code](vs-storage-cloud-services-getting-started-queues.md)
> - [Tabelle](vs-storage-cloud-services-getting-started-tables.md)

Il servizio di archiviazione tabelle di Azure consente di archiviare grandi quantità di dati strutturati. Il servizio è un datastore NoSQL che accetta chiamate autenticate dall'interno e dall'esterno del cloud di Azure. Le tabelle di Azure sono ideali per l'archiviazione di dati strutturati non relazionali.  Per altre informazioni, vedere [Come usare l'archiviazione tabelle da .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "How to use Table Storage from .NET").

Aggiungere le seguenti dichiarazioni dello spazio dei nomi del codice all'inizio del file C# in cui si vuole accedere ad Archiviazione di Azure a livello di codice:

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Table;

##### Ottenere la stringa di connessione di archiviazione
Prima di eseguire operazioni relative a una tabella, è necessario ottenere la stringa di connessione per l'account di archiviazione in cui risiederanno le tabelle. È possibile usare il tipo **CloudStorageAccount** per rappresentare le informazioni sull'account di archiviazione. Per i progetti di servizi cloud è possibile usare il tipo **CloudConfigurationManager** per recuperare la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure, come mostrato nel codice seguente.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[AZURE.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]

<!--HONumber=42-->
