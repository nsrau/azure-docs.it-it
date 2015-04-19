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
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Per iniziare](vs-storage-aspnet5-getting-started-blobs.md)
> - [Risultati](vs-storage-aspnet5-what-happened.md)

### <span id="whathappened">Che cosa è successo al progetto?</span>

##### Aggiunta di riferimenti

Il pacchetto NuGet per l'Archiviazione di Azure è stato aggiunto al progetto di Visual Studio.  
Il pacchetto aggiunge i riferimenti a .NET seguenti:

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.Configuration`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

È stato anche aggiunto il pacchetto **NuGet Microsoft.Framework.ConfigurationModel.Json**.

##### Aggiunta di stringa di connessione per l'Archiviazione di Azure 
Nel file config.json del progetto è stato creato un elemento con la stringa di connessione e la chiave dell'account di archiviazione selezionato.

Per altre informazioni, vedere [ASP.NET 5](http://www.asp.net/vnext).
\<!--HONumber=42-->
