---
title: Creare un'app iOS in App per dispositivi mobili del servizio app di Azure | Microsoft Docs
description: Seguire questa esercitazione per iniziare a usare i back-end dell'app per dispositivi mobili di Azure per lo sviluppo per iOS in Objective-C o Swift
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: cdfe395bca7ac73f00506da0180146310bd71d3d
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027240"
---
# <a name="create-an-ios-app"></a>Creare un'app iOS

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center supporta end-to-end e servizi integrati centrali per lo sviluppo di app per dispositivi mobili. Gli sviluppatori possono utilizzare i servizi di **compilazione**, **test** e **distribuzione** per configurare la pipeline di integrazione e recapito continua. Una volta distribuita l'app, gli sviluppatori possono monitorare lo stato e l'utilizzo dell'app usando i servizi di **analisi** e **diagnostica** e coinvolgere gli utenti che usano il servizio di **push** . Gli sviluppatori possono inoltre sfruttare l' **autenticazione** per autenticare gli utenti e il servizio **dati** per salvare in modo permanente e sincronizzare i dati delle app nel cloud.
> Se si intende integrare servizi cloud nell'applicazione per dispositivi mobili, iscriversi con App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) oggi stesso.

## <a name="overview"></a>Panoramica

Questa esercitazione illustra come aggiungere [app per dispositivi mobili del servizio app di Azure](app-service-mobile-value-prop.md), un servizio back-end cloud, a un'app per iOS. Il primo passaggio consiste nel creare un nuovo back-end per dispositivi mobili in Azure. Scaricare una semplice app di esempio per iOS *Todo list* che archivia i dati in Azure.

Per completare l'esercitazione, è necessario un computer Mac e [un account Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="create-a-new-azure-mobile-app-backend"></a>Creare un nuovo back-end dell'app per dispositivi mobili di Azure

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Creare una connessione al database e configurare il progetto client e server
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-ios-app"></a>Eseguire l'app iOS

[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
