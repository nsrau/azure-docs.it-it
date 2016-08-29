<properties
	pageTitle="Cosa è successo al progetto Webjob (servizio connesso a Visual Studio Archiviazione di Azure)? | Microsoft Azure"
	description="Viene descritto cosa succede in un progetto Webjob di Azure dopo la connessione a un account di archiviazione utilizzando i servizi connessi a Visual Studio "
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

# Cosa è successo al progetto Webjob (servizio connesso a Visual Studio Archiviazione di Azure)?

## Aggiunta di riferimenti

Il pacchetto NuGet per l'Archiviazione di Azure è stato aggiunto o aggiornato nel progetto di Visual Studio. Il pacchetto aggiunge i riferimenti a .NET seguenti:

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.ConfigurationManager**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

## Aggiunta di stringa di connessione per l'Archiviazione di Azure
Nel file App.config del progetto gli elementi **AzureWebJobsStorage** e **AzureWebJobsDashboard** sono stati aggiornati con la stringa di connessione e la chiave dell'account di archiviazione selezionato.

Per altre informazioni, vedere [Risorse di documentazione di Processi Web di Azure](http://go.microsoft.com/fwlink/?linkid=390226).

<!---HONumber=AcomDC_0817_2016-->