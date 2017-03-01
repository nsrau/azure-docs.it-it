---
title: Processi Web nel servizio app di Azure
description: "Informazioni su come creare processi Web per eseguire test in background, interagire con servizi quali archiviazione e bus di servizio e creare attività pianificate."
services: app-service
documentationcenter: 
author: christopheranderson
manager: erikre
editor: mollybos
ms.assetid: 85975432-04c9-4b83-b937-b30c082d52a1
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2015
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 45016e09e981bab6e4413048838c66f1108dc663
ms.lasthandoff: 11/17/2016


---
# <a name="using-webjobs-in-azure-app-service"></a>Uso di Processi Web nel servizio app di Azure
Questo articolo include i collegamenti a risorse della documentazione relative all'uso di Processi Web di Azure e di Azure WebJobs SDK. Processi Web di Azure costituisce una soluzione semplice e rapida per eseguire script e programmi sotto forma di processi in background in [App Web del servizio app](http://go.microsoft.com/fwlink/?LinkId=529714). È infatti possibile caricare ed eseguire un file eseguibile in formato cmd, bat, exe (.NET), ps1, sh, php, py, js e jar, che verrà eseguito come processo Web in base a una pianificazione (cron) o senza interruzioni.

WebJobs SDK semplifica l'uso dell'archiviazione di Azure. Include infatti un sistema di binding e attivazione che interagisce con BLOB, code e tabelle di archiviazione di Microsoft Azure, oltre che con le code del bus di servizio.

Grazie agli strumenti integrati in Visual Studio, è inoltre semplice creare, distribuire e gestire processi Web. È possibile creare processi Web da modelli, nonché pubblicarli e gestirli (eseguirli/arrestarli/monitorarli/eseguirne il debug).

Il dashboard Processi Web nel portale di Azure include efficaci funzionalità di gestione per controllare completamente l'esecuzione di processi Web, tra cui la possibilità di richiamare singole funzioni nei processi Web. Il dashboard visualizza inoltre i runtime delle funzioni e l'output delle registrazioni.

[!INCLUDE [app-service-blueprint-webjobs](../../includes/app-service-blueprint-webjobs.md)]


