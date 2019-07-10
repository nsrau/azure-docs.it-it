---
title: Creare un'app Cordova in App per dispositivi mobili del Servizio app di Azure | Microsoft Docs
description: Seguire questa esercitazione per iniziare a usare i back-end dell'app per dispositivi mobili per lo sviluppo di Apache Cordova
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
tags: ''
keywords: cordova,javascript,dispositivi mobili,client
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 8a1634cfa7b701401927859a9fbd3fb203b61a77
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445659"
---
# <a name="create-an-apache-cordova-app"></a>Creare un'app Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center investe in nuovi e integrati servizi fondamentali per lo sviluppo di app per dispositivi mobili. Gli sviluppatori possono utilizzare **compilare**, **Test** e **Distribuisci** servizi per impostare le pipeline di integrazione continua e recapito. Dopo aver distribuito l'app, gli sviluppatori possono monitorare lo stato e sull'utilizzo di app using il **Analitica** e **diagnostica** servizi e Coinvolgi gli utenti utilizzando il **Push** servizio. Gli sviluppatori possono inoltre sfruttare **Auth** di autenticare gli utenti e **dati** service per rendere persistente e sincronizzare i dati dell'app nel cloud. Consulta [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-cordova-get-started) oggi stesso.
>

## <a name="overview"></a>Panoramica
Questa esercitazione illustra come aggiungere un servizio back-end basato sul cloud a un'app per dispositivi mobili Apache Cordova mediante un back-end per app per dispositivi mobili di Azure.  Verranno creati un nuovo back-end di app per dispositivi mobili e una semplice app Apache Cordova di tipo *Todo list* che archivia i dati delle app in Azure.

Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni Apache Cordova relative all'uso della funzionalità delle app per dispositivi mobili nel Servizio app di Azure.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

* Un PC con installato [Visual Studio Community 2017] o versione successiva.
* [Strumenti di Visual Studio per Apache Cordova].
* Un [account Azure attivo](https://azure.microsoft.com/pricing/free-trial/).

È anche possibile ignorare Visual Studio e usare direttamente la riga di comando di Apache Cordova.  L'uso della riga di comando è utile quando l'esercitazione viene completata in un computer Mac.  La compilazione di applicazioni client di Apache Cordova dalla riga di comando non è trattata in questa esercitazione.

## <a name="create-an-azure-mobile-app-backend"></a>Creare un back-end dell'app per dispositivi mobili di Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Creare una connessione al database e configurare il progetto client e server
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Scaricare ed eseguire l'app Apache Cordova
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]
<!-- URLs -->
[Portale di azure]: https://portal.azure.com/

[Visual Studio Community 2017]: https://www.visualstudio.com/
[Strumenti di Visual Studio per Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx