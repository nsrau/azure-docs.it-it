<properties
	pageTitle="Metadati delle app per le API del servizio app per l'individuazione di API e la generazione di codice | Microsoft Azure"
	description="Informazioni sulle modalità con cui le app per le API del servizio app di Azure usano i metadati di Swagger per facilitare l'individuazione di API e la generazione di codice."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/25/2015"
	ms.author="tdykstra"/>

# Metadati delle app per le API del servizio app per l'individuazione di API e la generazione di codice 

Il supporto per i metadati dell'API [Swagger 2.0](http://swagger.io/) è incorporato nelle app per le API del servizio app. Non è necessario usare Swagger, ma se lo si usa, è possibile sfruttare le funzionalità delle app per le API che semplificano le procedure di individuazione e utilizzo.

## Endpoint Swagger

È possibile specificare un endpoint che fornisca i metadati JSON di Swagger 2.0 per un'app per le API in una proprietà dell'app. L'endpoint può essere relativo all'URL di base dell'app per le API oppure un URL assoluto, che può indirizzare anche all'esterno dell'app per le API.

L'URL deve essere accessibile pubblicamente (non protetto da un'autenticazione utente o del servizio).

Nel [portale di Azure](https://portal.azure.com/) l'URL dell'endpoint può essere visualizzato e modificato nel pannello **Definizione API**.

![](./media/app-service-api-metadata/apidefblade.png)

Quando si usa Visual Studio per creare un'app per le API, l'endpoint della definizione dell'API viene automaticamente impostato sull'URL di base dell'app per le API, più `/swagger/docs/v1`. Questo è l'URL predefinito che il pacchetto NuGet [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) usa per gestire i metadati di Swagger generati dinamicamente per un progetto API Web ASP.NET.

## Generazione del codice

Uno dei vantaggi dell'integrazione di Swagger nelle app per le API di Azure è la generazione automatica del codice. Le classi client generate semplificano la scrittura del codice che chiama un'app per le API.

È possibile generare il codice client per un'app per le API tramite Visual Studio o dalla riga di comando. Per informazioni su come generare classi client in Visual Studio per un progetto API Web ASP.NET, vedere [Introduzione alle app per le API e ad ASP.NET](app-service-api-dotnet-get-started.md#codegen). Per informazioni su come eseguire questa operazione dalla riga di comando per tutte le lingue supportate, vedere il file Leggimi del repository [Azure/autorest](https://github.com/azure/autorest) in GitHub.com.
 
## Passaggi successivi

Per un'esercitazione dettagliata sulle procedure di creazione, distribuzione e utilizzo di un'app per le API, vedere [Introduzione alle app per le API nel servizio app di Azure](app-service-api-dotnet-get-started.md).

<!---HONumber=AcomDC_1203_2015-->