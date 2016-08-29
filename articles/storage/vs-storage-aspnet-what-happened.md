<properties
	pageTitle="Cosa è successo a un progetto ASP.NET? | Microsoft Azure | Servizi connessi a Visual Studio"
	description="Viene descritto cosa succede dopo l'aggiunta di archiviazione di Azure a un progetto ASP.NET utilizzando i servizi connessi a Visual Studio"
	services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/15/2016"
	ms.author="tarcher"/>  

# Cosa è successo a un progetto ASP.NET (servizio connesso a Visual Studio Archiviazione di Azure)?

## Aggiunta di riferimenti

Il pacchetto NuGet per l'Archiviazione di Azure è stato aggiunto al progetto di Visual Studio. Il pacchetto aggiunge i riferimenti a .NET seguenti:

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.Configuration**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

##Aggiunta di stringa di connessione per l'Archiviazione di Azure
Nel file web.config del progetto è stato creato un elemento con la stringa di connessione e la chiave dell'account di archiviazione selezionato.

Per altre informazioni, vedere [ASP.NET](http://www.asp.net).

<!---HONumber=AcomDC_0817_2016-->