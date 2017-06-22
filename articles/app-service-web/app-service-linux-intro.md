---
title: Introduzione ad App Web di Azure in Linux | Microsoft Docs
description: Informazioni su App Web di Azure in Linux.
keywords: Servizio app di Azure, Linux, OSS
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 036e5691ecc435da54f381563b5d798f065bfb7f
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-azure-web-app-on-linux"></a>Introduzione ad App Web di Azure in Linux

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>Panoramica
I clienti possono usare App Web in Linux per ospitare app Web in modo nativo in Linux per stack di applicazioni supportate. La sezione seguente elenca gli stack di applicazioni attualmente supportati. 

## <a name="features"></a>Funzionalità
App Web in Linux supporta attualmente gli stack di applicazioni seguenti:

* Node.js
    * 4.4
    * 4.5
    * 6.2
    * 6.6
    * 6.9
* PHP
    * 5.6
    * 7.0
* .Net Core
    * 1.0
    * 1.1
* Ruby
    * 2.3

I clienti possono distribuire le applicazioni tramite:

* FTP
* Repository Git locale
* GitHub
* Bitbucket

Per il ridimensionamento delle applicazioni:

* I clienti possono aumentare e ridurre le prestazioni delle app Web modificando il livello nel piano di servizio app
* I clienti possono scalare orizzontalmente le applicazioni ed eseguire più istanze di un'app entro i confini dello SKU

Per Kudu, alcune delle funzionalità di base:

* Ambienti
* Distribuzioni
* Console di base
* SSH

Per DevOps:

* Ambienti di staging
* DockerHub CI/CD

## <a name="limitations"></a>Limitazioni
Il portale di Azure mostra solo le funzionalità che possono essere usate attualmente per App Web in Linux e nasconde le altre. Man mano che verranno abilitate nuove funzionalità, queste diventeranno visibili nel portale.

Alcune funzionalità, quali l'integrazione delle reti virtuali, l'autenticazione di Azure Active Directory o di terze parti o le estensioni del sito Kudu, non sono ancora disponibili. Man mano che queste funzionalità dinvetano disponibili, la documentazione verrà aggiornata e le modifiche pubblicate nei blog.

Questa anteprima pubblica è attualmente disponibile solo nelle aree seguenti:

* Stati Uniti occidentali
* Europa occidentale 
* Asia sudorientale
* Australia orientale

App Web in Linux è supportato solo nei piani di servizio app dedicati e non ha un livello Gratuito o Condiviso. I piani di servizio app per le app Web normali e di Linux si escludono a vicenda, quindi non è possibile creare un'app Web di Linux in un piano di servizio app non Linux.

Le app Web in Linux devono essere create in un gruppo di risorse che non contiene app Web non Linux nella stessa area.

## <a name="next-steps"></a>Passaggi successivi
Vedere i collegamenti seguenti per iniziare a usare il servizio app in Linux. È possibile pubblicare domande e dubbi nel [forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Creazione di app Web in App Web di Azure in Linux](app-service-linux-how-to-create-web-app.md)
* [Come usare un'immagine Docker personalizzata per App Web di Azure in Linux](app-service-linux-using-custom-docker-image.md)
* [Uso della configurazione PM2 per Node.js in App Web su Linux](app-service-linux-using-nodejs-pm2.md)
* [Uso di .NET Core in App Web del Servizio app di Azure in Linux](app-service-linux-using-dotnetcore.md)
* [Uso di Ruby in App Web del Servizio app di Azure in Linux](app-service-linux-ruby-get-started.md)
* [Domande frequenti su App Web del Servizio app di Azure su Linux](app-service-linux-faq.md)
* [SSH support for Azure Web App on Linux](./app-service-linux-ssh-support.md) (Supporto SSH per App Web di Azure in Linux)
* [Configurare gli ambienti di gestione temporanea nel Servizio app di Azure](./web-sites-staged-publishing.md)
* [Docker Hub Continuous Deployment with Azure Web App on Linux](./app-service-linux-ci-cd.md) (Distribuzione continua dell'hub Docker con App Web di Azure in Linux)


