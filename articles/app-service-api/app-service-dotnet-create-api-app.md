<properties
	pageTitle="Creare un'app per le API ASP.NET in Azure App Service | Microsoft Azure"
	description="Informazioni su come creare un'app per le API ASP.NET in Azure App Service usando Visual Studio 2013."
	services="app-service\api"
	documentationCenter=".net"
	authors="bradygaster"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/14/2015"
	ms.author="tdykstra"/>

# Creare un'app per le API ASP.NET nel servizio app di Azure

> [AZURE.SELECTOR]
- [.NET - Visual Studio 2015](app-service-dotnet-create-api-app.md)
- [.NET - Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)
- [Node.js](app-service-api-nodejs-api-app.md)
- [Java](app-service-api-java-api-app.md)

## Panoramica

Questa esercitazione mostra come creare un progetto API Web ASP.NET configurato per la distribuzione nel cloud come [app per le API nel servizio app di Azure](app-service-api-apps-why-best-platform.md). Per informazioni su come configurare un progetto API Web per la distribuzione come un'app per le API, vedere [Configurare un progetto API Web come un'app per le API](app-service-dotnet-create-api-app-visual-studio.md).

Le esercitazione successive della serie illustrano come [distribuire](app-service-dotnet-deploy-api-app.md) ed [eseguire il debug](../app-service-dotnet-remotely-debug-api-app.md) del progetto app per le API creato in questa esercitazione.

[AZURE.INCLUDE [installare-sdk-2015 2013](../../includes/install-sdk-2015-2013.md)]

Per eseguire questa esercitazione, è necessario installare Azure SDK per .NET versione 2.6 o successive.

## Creare un progetto app per le API

Quando le istruzioni richiedono l'immissione di un nome per il progetto, specificare **ContactsList**.

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## Passaggi successivi

L'app per le API è ora pronta per essere distribuita e a questo scopo è possibile seguire l'esercitazione [Distribuire un'app per le API](app-service-dotnet-deploy-api-app.md).

<!---HONumber=Oct15_HO1-->