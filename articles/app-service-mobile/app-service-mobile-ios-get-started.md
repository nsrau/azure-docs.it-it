<properties
	pageTitle="Creare un'app iOS in App per dispositivi mobili del servizio app di Azure | Microsoft Azure"
	description="Seguire questa esercitazione per iniziare a usare i back-end dell'app per dispositivi mobili di Azure per lo sviluppo per iOS in Objective-C o Swift"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="11/30/2015"
	ms.author="krisragh"/>

#Creare un'app iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Panoramica

Questa esercitazione illustra come usare un servizio back-end basato sul cloud ([App per dispositivi mobili di Azure](app-service-mobile-value-prop.md)) in un'app per dispositivi mobili iOS. Viene creato un nuovo back-end di app per dispositivi mobili e illustrato come usare una semplice app iOS di tipo _elenco azioni_ che archivia dati delle app in Azure.

## Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un [account Azure attivo](http://azure.microsoft.com/pricing/free-trial/)

* Un PC in cui sia installato [Visual Studio Community 2013] o versioni successive

* Un computer Mac in cui sia installato Xcode v7.0 o versioni successive

* Il [framework Azure Mobile per iOS](https://go.microsoft.com/fwLink/?LinkID=529823), che è automaticamente incluso nel progetto di guida introduttiva da scaricare

## Creare un nuovo back-end dell'app per dispositivi mobili di Azure

Per creare un nuovo back-end dell'app per dispositivi mobili, attenersi alla procedura seguente.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

È stato eseguito il provisioning di un back-end dell'app per dispositivi mobili di Azure che può essere usato dalle applicazioni client per dispositivi mobili. Successivamente, scaricare un progetto server per un semplice back-end "todo list" e pubblicarlo in Azure.

## Scaricare il progetto server

1. Nel PC passare al [portale di Azure]. Fare clic su **Esplora tutto** > **App per dispositivi mobili**, quindi fare clic sul back-end dell'app per dispositivi mobili appena creato.

2. Nel pannello App per dispositivi mobili fare clic su **Impostazioni** e in **App per dispositivi mobili** fare clic su **Avvio rapido** > **iOS (Objective-C)**. Se si preferisce Swift, fare clic su **Avvio rapido** > **iOS (Swift)**.

## Configurare il progetto server

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]


## Scaricare ed eseguire l'app iOS

[AZURE.INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[portale di Azure]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_1203_2015--->