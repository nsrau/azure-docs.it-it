<properties
    pageTitle="Creare un'app Cordova in App per dispositivi mobili del Servizio app di Azure | Microsoft Azure"
    description="Seguire questa esercitazione per iniziare a usare i back-end dell'app per dispositivi mobili per lo sviluppo di Apache Cordova"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="glenga"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-javascript"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="adrianha"/>

#Creare un'app Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Panoramica

Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per dispositivi mobili Apache Cordova mediante un back-end per app per dispositivi mobili di Azure. Verranno creati un nuovo back-end di app per dispositivi mobili e una semplice app Apache Cordova di tipo _Todo list_ che archivia dati delle app in Azure.

Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni Apache Cordova relative all'uso della funzionalità delle app per dispositivi mobili nel Servizio app di Azure.

## Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un PC in cui sia installato [Visual Studio Community 2015] o versioni successive.
* [Strumenti di Visual Studio per Apache Cordova].
* Un [account Azure attivo](https://azure.microsoft.com/pricing/free-trial/).

È anche possibile ignorare Visual Studio e usare direttamente la riga di comando di Apache Cordova. Questo è utile quando viene completata l'esercitazione in un computer Mac. La compilazione di applicazioni client di Apache Cordova dalla riga di comando non è trattata in questa esercitazione.

## Creare un nuovo back-end dell'app per dispositivi mobili di Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Configurare il progetto server

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Scaricare ed eseguire l'app Apache Cordova

[AZURE.INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Strumenti di Visual Studio per Apache Cordova]: https://www.visualstudio.com/it-IT/features/cordova-vs.aspx

<!---HONumber=AcomDC_0211_2016-->