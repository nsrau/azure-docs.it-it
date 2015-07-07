<properties
	pageTitle="Creare un'app per le API ASP.NET nel servizio app di Azure"
	description="Informazioni su come creare un'app per le API ASP.NET nel servizio app di Azure usando Visual Studio 2013"
	services="app-service\api"
	documentationCenter=".net"
	authors="bradygaster"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="05/19/2015"
	ms.author="bradyg;tarcher"/>

# Creare un'app per le API ASP.NET nel servizio app di Azure

> [AZURE.SELECTOR]
- [Visual Studio 2013](app-service-dotnet-create-api-app.md)
- [Visual Studio 2015 RC](app-service-dotnet-create-api-app-vs2015.md)
- [Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)

## Panoramica

Questa esercitazione mostra come creare un progetto API Web ASP.NET usando un modello di Visual Studio 2013 che configura il progetto per la distribuzione nel cloud come [app per le API](app-service-api-apps-why-best-platform.md) nel [servizio app di Azure](../app-service/app-service-value-prop-what-is.md). Per informazioni su come configurare un progetto API Web per la distribuzione come un'app per le API, vedere [Configurare un progetto API Web come un'app per le API](app-service-dotnet-create-api-app-visual-studio.md).

Le esercitazionI successive della serie illustrano come [distribuire](app-service-dotnet-deploy-api-app.md) ed [eseguire il debug](../app-service-dotnet-remotely-debug-api-app.md) del progetto app per le API creato in questa esercitazione.

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

Per eseguire questa esercitazione, è necessario installare Azure SDK per .NET versione 2.5.1 o successive.

## Creare un progetto app per le API

1. Aprire Visual Studio 2013.

2. Selezionare **File > nuovo progetto**.

3. Selezionare il modello **Applicazione Web ASP.NET**.

4. Assicurarsi che la casella di controllo **Aggiungi Application Insights al progetto** sia deselezionata.

4. Assegnare al progetto il nome *ContactsList*

	![](./media/app-service-dotnet-create-api-app/01-filenew-v3.png)

5. Fare clic su **OK**.

6. Nella finestra di dialogo **Nuovo progetto ASP.NET** selezionare il modello di progetto **App per le API di Azure**.

	![](./media/app-service-dotnet-create-api-app/02-api-app-template-v3.png)

7. Fare clic su **OK** per generare il progetto.

Visual Studio create un progetto API Web configurato per la distribuzione come app per le API.

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## Passaggi successivi

L'app per le API è ora pronta per essere distribuita e a questo scopo è possibile seguire l'esercitazione [Distribuire un'app per le API](app-service-dotnet-deploy-api-app.md).
 

<!---HONumber=62-->