---
title: Uso di Ruby nelle app Web del servizio App di Azure in Linux | Documentazione Microsoft
description: Uso di Ruby nelle app Web del servizio app di Azure in Linux.
keywords: Servizio app di Azure, app Web, domande frequenti, Linux, OSS, Ruby
services: app-service
documentationCenter: 
authors: aelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: aelnably;wesmc
translationtype: Human Translation
ms.sourcegitcommit: bb4c7ea7adfe1326ae8259782b5de2762c8c2bf5
ms.openlocfilehash: 8427034e5229000faf134bcd5b7174b0f7046321
ms.lasthandoff: 02/17/2017


---

# <a name="using-ruby-in-web-apps-on-linux"></a>Uso di Ruby nelle app Web in Linux #

Con l'aggiornamento più recente al back-end è stato aggiunto il supporto per Ruby v.2.3. Definendo la configurazione dell'app Web Linux è possibile modificare lo stack di applicazioni.

## <a name="using-the-azure-portal"></a>Uso del portale di Azure ##

Dal menu Nuovo nel [portale di Azure](https://portal.azure.com), è possibile scegliere di creare un'app Web in Linux dall'opzione Web e dispositivi mobili come illustrato nella figura seguente:

![Avviare la creazione di un'app Web nel portale di Azure][1]

Viene quindi aperto il **pannello Crea**, come illustrato nell'immagine seguente:

![Pannello Crea][2]

1. Assegnare all'app Web un nome.
2. Scegliere un gruppo di risorse esistente oppure crearne uno nuovo. Vedere le aree disponibili nella [sezione sulle limitazioni](app-service-linux-intro.md).
3. Scegliere un piano di servizio app di Azure esistente o crearne uno nuovo. Vedere le note sul piano di servizio app nella [sezione sulle limitazioni](app-service-linux-intro.md).
4. Scegliere Ruby tra gli stack di runtime predefiniti.

Dopo aver creato un'applicazione Web Ruby, è possibile distribuirla tramite Git o FTP.

## <a name="next-steps"></a>Passaggi successivi
* [What is App Service on Linux?](app-service-linux-intro.md) (Che cos'è Servizio app in Linux?)
* [Creating Apps in an App Service on Linux](app-service-linux-how-to-create-a-web-app.md) (Creazione di app Web nel servizio app in Linux)
* [Distribuzione dell'archivio Git locale nel servizio app di Azure](app-service-deploy-local-git.md)
* [Azure App Service Web Apps on Linux FAQ](app-service-linux-faq.md) (App Web del Servizio app di Azure nelle domande frequenti su Linux)

<!--Image references-->
[1]: ./media/app-service-linux-using-ruby/New-Linux.png
[2]: ./media/app-service-linux-using-ruby/Ruby-UX.png
