<properties 
	pageTitle="Introduzione all'Archiviazione di Azure" 
	description="Spiega cosa succede quando si crea un archivio di Azure in un progetto di servizio cloud di Visual Studio." 
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
	ms.date="04/20/2015" 
	ms.author="patshea123"/>

# Che cosa è successo al progetto?

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-cloud-services-getting-started-blobs.md)
> - [What Happened](vs-storage-cloud-services-what-happened.md)

###<span id="whathappened">Che cosa è successo al progetto?</span>

###### Aggiunta di riferimenti

Il pacchetto NuGet per l'Archiviazione di Azure è stato aggiunto al progetto di Visual Studio. Il pacchetto aggiunge i riferimenti a .NET seguenti:

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.Configuration`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

######Aggiunta di stringa di connessione per l'Archiviazione di Azure 
Sono stati creati elementi con la stringa di connessione e la chiave dell'account di archiviazione selezionato. Sono state apportate modifiche ai file seguenti:

- `ServiceDefinition.csdef`
- `ServiceConfiguration.Cloud.cscfg`
- `ServiceConfiguration.Local.cscfg`

 

<!---HONumber=July15_HO2-->