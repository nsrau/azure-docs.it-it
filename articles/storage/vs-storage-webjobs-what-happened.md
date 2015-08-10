<properties 
	pageTitle="Introduzione all'Archiviazione di Azure" 
	description="Descrive i risultati ottenuti durante la creazione di una risorsa di archiviazione di Azure in un progetto Azure Webjob di Visual Studio" 
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
	ms.date="07/13/2015" 
	ms.author="patshea123"/>

# Che cosa è successo al progetto?

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-webjobs-getting-started-blobs.md)
> - [What Happened](vs-storage-webjobs-what-happened.md)

###<span id="whathappened">Che cosa è successo al progetto?</span>

##### Aggiunta di riferimenti

Il pacchetto NuGet per l'Archiviazione di Azure è stato aggiunto o aggiornato nel progetto di Visual Studio. Il pacchetto aggiunge i riferimenti a .NET seguenti:

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.ConfigurationManager`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

#####Aggiunta di stringa di connessione per l'Archiviazione di Azure 
Nel file App.config del progetto gli elementi `AzureWebJobsStorage` e `AzureWebJobsDashboard` sono stati aggiornati con la stringa di connessione e la chiave dell'account di archiviazione selezionato.

Per ulteriori informazioni, vedere l'articolo relativo alle [risorse consigliate per i processi Web di Azure](http://go.microsoft.com/fwlink/?linkid=390226).

<!---HONumber=July15_HO5-->