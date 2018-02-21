---
title: Creare un'app Cordova in App per dispositivi mobili del Servizio app di Azure | Microsoft Docs
description: Seguire questa esercitazione per iniziare a usare i back-end dell'app per dispositivi mobili per lo sviluppo di Apache Cordova
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: 
tags: 
keywords: cordova,javascript,dispositivi mobili,client
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: hero-article
ms.date: 07/07/2017
ms.author: crdun
ms.openlocfilehash: 223e9e35fcab347f9b5b8db01a9fd667b9f5d55d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="create-an-apache-cordova-app"></a>Creare un'app Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Panoramica
Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per dispositivi mobili Apache Cordova mediante un back-end per app per dispositivi mobili di Azure.  Verranno creati un nuovo back-end di app per dispositivi mobili e una semplice app Apache Cordova di tipo *Todo list* che archivia i dati delle app in Azure.

Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni Apache Cordova relative all'uso della funzionalità delle app per dispositivi mobili nel Servizio app di Azure.

## <a name="prerequisites"></a>prerequisiti
Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

* Un PC con installato [Visual Studio Community 2017] o versione successiva.
* [Visual Studio Tools per Apache Cordova].
* Un [account Azure attivo](https://azure.microsoft.com/pricing/free-trial/).

È anche possibile ignorare Visual Studio e usare direttamente la riga di comando di Apache Cordova.  L'uso della riga di comando è utile quando l'esercitazione viene completata in un computer Mac.  La compilazione di applicazioni client di Apache Cordova dalla riga di comando non è trattata in questa esercitazione.

## <a name="create-an-azure-mobile-app-backend"></a>Creare un back-end dell'app per dispositivi mobili di Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

[Guardare un video che illustra una procedura simile](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-1-Create-an-Azure-Mobile-App)

## <a name="configure-the-server-project"></a>Configurare il progetto server
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Scaricare ed eseguire l'app Apache Cordova
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## <a name="next-steps"></a>Passaggi successivi
Ora che l'esercitazione introduttiva è stata completata, passare a una delle esercitazioni seguenti:

* [Aggiungere dati offline](app-service-mobile-cordova-get-started-offline-data.md) all'app Apache Cordova.
* [Aggiungere l'autenticazione all'app Apache Cordova](app-service-mobile-cordova-get-started-users.md) .
* [Aggiungere notifiche push all'app Apache Cordova](app-service-mobile-cordova-get-started-push.md) .

Altre informazioni sui concetti chiave del servizio app di Azure.

* [Dati offline]
* [autenticazione]
* [Notifiche push]

Informazioni su come usare gli SDK.

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2017]: http://www.visualstudio.com/
[Visual Studio Tools per Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Dati offline]: app-service-mobile-offline-data-sync.md
[autenticazione]: app-service-mobile-auth.md
[Notifiche push]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
