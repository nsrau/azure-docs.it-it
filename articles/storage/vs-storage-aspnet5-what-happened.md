<properties
	pageTitle="Introduzione a Archiviazione di Azure | Microsoft Azure"
	description="Descrive i risultati ottenuti durante la creazione di una risorsa di archiviazione di Azure in un progetto ASP.NET 5 di Visual Studio"
	services="storage"
	documentationCenter=""
	authors="patshea123"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2015"
	ms.author="patshea"/>

# Che cosa è successo al progetto?

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-blobs.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

### Che cosa è successo al progetto?

#### Aggiunta di riferimenti

Il pacchetto NuGet per l'Archiviazione di Azure è stato aggiunto al progetto di Visual Studio. Il pacchetto aggiunge i riferimenti a .NET seguenti:

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.Configuration`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

È stato anche aggiunto il pacchetto NuGet **Microsoft.Framework.Configuration.Json**.

#### Aggiunta di stringa di connessione per l'Archiviazione di Azure
Nel file config.json del progetto è stato creato un elemento con la stringa di connessione e la chiave dell'account di archiviazione selezionato.

Per altre informazioni, vedere [ASP.NET 5](http://www.asp.net/vnext).

<!---HONumber=September15_HO1-->