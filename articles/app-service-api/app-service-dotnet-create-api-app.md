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
	ms.date="10/08/2015"
	ms.author="tdykstra"/>

# Creare un'app per le API ASP.NET nel servizio app di Azure

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## Panoramica

Questa esercitazione mostra come creare un progetto API Web ASP.NET configurato per la distribuzione nel cloud come [app per le API nel servizio app di Azure](app-service-api-apps-why-best-platform.md). Per informazioni su come configurare un progetto API Web per la distribuzione come un'app per le API, vedere [Configurare un progetto API Web come un'app per le API](app-service-dotnet-create-api-app-visual-studio.md).

Questa è un'esercitazione semplice e veloce che illustra solo come creare un progetto di Visual Studio usando un modello. Si tratta della prima esercitazione di una serie. Le esercitazioni successive mostrano come [distribuire](app-service-dotnet-deploy-api-app.md) ed eseguire il [debug](../app-service-dotnet-remotely-debug-api-app.md) del progetto di app per le API creato in questa esercitazione. Per altri approfondimenti su come usare le app per le API, vedere la sezione [Passaggi successivi](#next-steps) alla fine dell'esercitazione.

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

Per informazioni su come usare codice client generato automaticamente per chiamare le app per le API, vedere [Usare un'app per le API da un client .NET](app-service-api-dotnet-consume.md).

Per informazioni su come personalizzare i metadati di Swagger generati automaticamente per un'app per le API, vedere [Personalizzare le definizioni delle API generate da Swashbuckle](app-service-api-dotnet-swashbuckle-customize.md).

Per informazioni su come creare, eliminare e configurare le app per le API nel portale di anteprima di Azure, vedere [Gestire le app per le API](app-service-api-manage-in-portal.md).

Per informazioni su come autenticare gli utenti delle app per le API, vedere [Autenticazione per app per le API e per dispositivi mobili nel servizio app di Azure](../app-service/app-service-authentication-overview.md).

Per informazioni sulle funzionalità delle app per le API, vedere [Informazioni sulle app per le API](app-service-api-apps-why-best-platform.md).

<!---HONumber=AcomDC_1203_2015-->