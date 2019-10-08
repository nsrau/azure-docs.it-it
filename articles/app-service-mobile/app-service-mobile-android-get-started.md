---
title: Creare un'app Android in App per dispositivi mobili del servizio app di Azure | Microsoft Docs
description: Seguire questa esercitazione per iniziare a usare i back-end dell'app per dispositivi mobili per lo sviluppo di Android
services: app-service\mobile
documentationcenter: android
author: elamalani
manager: crdun
editor: ''
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34a9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: conceptual
ms.date: 5/6/2019
ms.author: emalani
ms.openlocfilehash: 0f79d2e7112847dbc5553e60f12bc872a5c0b5d5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027645"
---
# <a name="create-an-android-app"></a>Creare un'app per Android
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center supporta end-to-end e servizi integrati centrali per lo sviluppo di app per dispositivi mobili. Gli sviluppatori possono utilizzare i servizi di **compilazione**, **test** e **distribuzione** per configurare la pipeline di integrazione e recapito continua. Una volta distribuita l'app, gli sviluppatori possono monitorare lo stato e l'utilizzo dell'app usando i servizi di **analisi** e **diagnostica** e coinvolgere gli utenti che usano il servizio di **push** . Gli sviluppatori possono inoltre sfruttare l' **autenticazione** per autenticare gli utenti e il servizio **dati** per salvare in modo permanente e sincronizzare i dati delle app nel cloud.
> Se si intende integrare servizi cloud nell'applicazione per dispositivi mobili, iscriversi con App Center [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) oggi stesso.

## <a name="overview"></a>Panoramica
Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per dispositivi mobili Android mediante un back-end per app per dispositivi mobili di Azure.  Verranno creati un nuovo back-end di app per dispositivi mobili e una semplice app Android di tipo *Todo list* che archivia dati delle app in Azure.

Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni Android relative all'uso della funzionalità delle app per dispositivi mobili in Servizio app di Azure.

## <a name="prerequisites"></a>Prerequisiti
Per completare l'esercitazione, sono necessari gli elementi seguenti:

* [Android Developer Tools](https://developer.android.com/sdk/index.html), che include l'ambiente di sviluppo integrato di Android Studio e la piattaforma Android più recente.
* Android SDK per dispositivi mobili di Azure.
* Un [account Azure attivo](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-new-azure-mobile-app-backend"></a>Creare un nuovo back-end dell'app per dispositivi mobili di Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Creare una connessione al database e configurare il progetto client e server
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-android-app"></a>Eseguire l'app Android
[!INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
