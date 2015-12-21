<properties
    pageTitle="Creare un'app Android in App per dispositivi mobili del servizio app di Azure | Microsoft Azure"
    description="Seguire questa esercitazione per iniziare a usare i back-end dell'app per dispositivi mobili per lo sviluppo di Android"
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="11/20/2015"
    ms.author="yuaxu"/>

#Creare un'app per Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Panoramica

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per dispositivi mobili Android mediante un back-end per app per dispositivi mobili di Azure. Verranno creati un nuovo back-end di app per dispositivi mobili e una semplice app Android di tipo _Todo list_ che archivia dati delle app in Azure.

Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni Android relative all'uso della funzionalità delle app per dispositivi mobili in Servizio app di Azure.

## Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* [Android Developer Tools](https://developer.android.com/sdk/index.html), che include l'ambiente di sviluppo integrato di Android Studio e la piattaforma Android più recente.
* L’SDK Android di Azure Mobile, a cui si fa automaticamente riferimento come parte del progetto della guida introduttiva da scaricare.
* Un PC in cui sia installato [Visual Studio Community 2013] o versioni successive (non necessario per il back-end Node.js).
* Un [account Azure attivo](http://azure.microsoft.com/pricing/free-trial/).

## Creare un nuovo back-end dell'app per dispositivi mobili di Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Configurare il progetto server

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Scaricare ed eseguire l'app Android

[AZURE.INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_1210_2015--->