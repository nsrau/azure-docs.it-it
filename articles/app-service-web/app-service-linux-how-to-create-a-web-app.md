---
title: Creare un&quot;app Web di Azure eseguita in Linux | Documentazione Microsoft
description: Flusso di lavoro per la creazione di un&quot;app Web per il servizio app in Linux.
keywords: Servizio app di Azure, app Web, Linux, OSS
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: 3a71d10a-a0fe-4d28-af95-03b2860057d5
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
translationtype: Human Translation
ms.sourcegitcommit: bb4c7ea7adfe1326ae8259782b5de2762c8c2bf5
ms.openlocfilehash: 19a42a8dc9a4026d93c10dd209e84d4e14b8f00a
ms.lasthandoff: 02/17/2017


---
# <a name="create-an-azure-web-app-running-on-linux"></a>Creare un'app Web di Azure eseguita in Linux
## <a name="use-the-azure-portal-to-create-your-web-app"></a>Usare il portale di Azure per creare l'app Web
È possibile avviare la creazione dell'app Web in Linux dal [portale di Azure](https://portal.azure.com), come illustrato nell'immagine seguente:

![Avviare la creazione di un'app Web nel portale di Azure][1]

Viene quindi aperto il **pannello Crea**, come illustrato nell'immagine seguente:

![Pannello Crea][2]

1. Assegnare all'app Web un nome.
2. Scegliere un gruppo di risorse esistente oppure crearne uno nuovo. Vedere le aree disponibili nella [sezione sulle limitazioni](app-service-linux-intro.md).
3. Scegliere un piano di servizio app di Azure esistente o crearne uno nuovo. Vedere le note sul piano di servizio app nella [sezione sulle limitazioni](app-service-linux-intro.md).
4. Scegliere lo stack di applicazioni che si intende da usare. È possibile scegliere tra diverse versioni di Node.js, PHP, .Net Core e Ruby.

Dopo avere creato l'app, è possibile cambiare lo stack di applicazioni dalle impostazioni dell'applicazione, come illustrato nell'immagine seguente:

![Impostazioni dell'applicazione][3]

## <a name="deploy-your-web-app"></a>Distribuire l'app Web
Scegliendo le **opzioni di distribuzione** dal portale di gestione, è possibile usare un repository Git o GitHub locale per distribuire l'applicazione. Le istruzioni rimanenti sono analoghe a quelle per un'app Web non Linux. Per distribuire l'app, è possibile seguire le istruzioni indicate nella [distribuzione Git locale](app-service-deploy-local-git.md) o nella [distribuzione continua](app-service-continuous-deployment.md).

È anche possibile usare l'FTP per caricare l'applicazione nel sito. È possibile ottenere l'endpoint FTP per l'app Web dalla sezione Log di diagnostica, come illustrato nell'immagine seguente:

![Log di diagnostica][4]

## <a name="next-steps"></a>Passaggi successivi
* [What is App Service on Linux?](app-service-linux-intro.md) (Che cos'è Servizio app in Linux?)
* [Uso della configurazione PM2 per Node.js nelle app Web in Linux](app-service-linux-using-nodejs-pm2.md)
* [Uso di Ruby nelle app Web del servizio app di Azure in Linux](app-service-linux-using-ruby.md)
* [Azure App Service Web Apps on Linux FAQ](app-service-linux-faq.md) (App Web del Servizio app di Azure nelle domande frequenti su Linux)

<!--Image references-->
[1]: ./media/app-service-linux-how-to-create-a-web-app/top-level-create.png
[2]: ./media/app-service-linux-how-to-create-a-web-app/create-blade.png
[3]: ./media/app-service-linux-how-to-create-a-web-app/application-settings-change-stack.png
[4]: ./media/app-service-linux-how-to-create-a-web-app/diagnostic-logs-ftp.png

