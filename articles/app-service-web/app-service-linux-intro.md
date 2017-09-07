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
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: bbe2e0bafe48b39f12c5a8a46511e9275926d4b6
ms.contentlocale: it-it
ms.lasthandoff: 08/30/2017

---
# <a name="introduction-to-azure-web-app-on-linux"></a>Introduzione ad App Web di Azure in Linux

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>Panoramica
[App Web](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-overview) è una piattaforma di calcolo completamente gestita, ottimizzata per l'hosting di siti Web e applicazioni Web. I clienti possono usare App Web in Linux per ospitare app Web in modo nativo in Linux per stack di applicazioni supportate. Le sezioni seguenti elencano gli stack di applicazioni attualmente supportati. 

### <a name="languages"></a>Lingue

|Node.js|PHP|.NET Core|Ruby|
|:------------------:|:---:|:---------:|:----:|
|4.4, 4.5|5.6|1.0-1.1|2.3|
|6.2, 6.6, 6.9-6.11|7.0|||
|8.0-8.1||||

### <a name="deployments"></a>Deployments

* FTP
* Repository Git locale
* GitHub
* Bitbucket

### <a name="devops"></a>DevOps

* Ambienti di staging
* [Registro contenitori di Azure](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-intro) e DockerHub CI/CD

### <a name="console-publishing-and-debugging"></a>Console, pubblicazione e debug

* Ambienti
* Deployments
* Console di base
* SSH

### <a name="scaling"></a>Ridimensionamento

* I clienti possono aumentare e ridurre le prestazioni delle app Web modificando il livello nel [piano di servizio app](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json)

## <a name="limitations"></a>Limitazioni
Il portale di Azure mostra solo le funzionalità che possono essere usate attualmente per App Web in Linux. Man mano che verranno abilitate nuove funzionalità, queste diventeranno visibili sul portale.

Alcune funzionalità, quali l'integrazione delle reti virtuali, l'autenticazione di Azure Active Directory o di terze parti o le estensioni del sito Kudu, non sono ancora disponibili. Man mano che queste funzionalità dinvetano disponibili, la documentazione verrà aggiornata e le modifiche pubblicate nei blog.

Questa anteprima pubblica è attualmente disponibile solo nelle aree seguenti:

|Americhe|Asia/Pacifico|Europa|
|:----------------:|:--------------:|:------------:|
|Brasile meridionale|Australia orientale|Europa settentrionale|
|Stati Uniti Orientali|Asia orientale|Europa occidentale|
|Stati Uniti centro-settentrionali|Giappone orientale||
|Stati Uniti centro-meridionali|India meridionale||
|Stati Uniti occidentali|Asia sudorientale||

Per verificare la disponibilità delle aree aggiuntive, controllare il [Dashboard dello stato di Azure](https://azure.microsoft.com/status).

App Web in Linux è supportato solo nei piani di servizio app [Basic, Standard e Premium](https://azure.microsoft.com/en-us/pricing/details/app-service/plans/) e non ha un livello [Gratuito o Condiviso](https://azure.microsoft.com/en-us/pricing/details/app-service/plans/). Di seguito sono riportate altre importanti limitazioni per App Web in Linux:

* In Linux, non è possibile creare App Web in un piano di servizio app che ospita già App Web non Linux.
* Durante la creazione di App Web in Linux in un gruppo di risorse contenente App Web non Linux, è necessario creare un piano di servizio app in un'area diversa rispetto al piano di servizio app esistente.

## <a name="troubleshooting"></a>Risoluzione dei problemi ##

Quando non è possibile avviare l'applicazione o si desidera controllare il log dall'app, controllare i log di Docker nella directory LogFiles. È possibile accedere a questa directory tramite il sito SCM o tramite FTP.
Per registrare `stdout` e `stderr` dal contenitore, è necessario abilitare **Registrazione del contenitore Docker** in **Log di diagnostica**.

![Abilitazione della registrazione][2]

![Uso di Kudu per visualizzare i log di Docker][1]

È possibile accedere al sito SCM da **Strumenti avanzati** nel menu **Strumenti di sviluppo**.

## <a name="next-steps"></a>Passaggi successivi
Vedere i collegamenti seguenti per iniziare a usare il servizio app in Linux. È possibile pubblicare domande e dubbi nel [forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Come usare un'immagine Docker personalizzata per App Web di Azure in Linux](app-service-linux-using-custom-docker-image.md)
* [Uso della configurazione PM2 per Node.js in App Web su Linux](app-service-linux-using-nodejs-pm2.md)
* [Uso di .NET Core in App Web del Servizio app di Azure in Linux](app-service-linux-using-dotnetcore.md)
* [Uso di Ruby in App Web del Servizio app di Azure in Linux](app-service-linux-ruby-get-started.md)
* [Domande frequenti su App Web del Servizio app di Azure su Linux](app-service-linux-faq.md)
* [SSH support for Azure Web App on Linux](./app-service-linux-ssh-support.md) (Supporto SSH per App Web di Azure in Linux)
* [Configurare gli ambienti di gestione temporanea nel Servizio app di Azure](./web-sites-staged-publishing.md)
* [Docker Hub Continuous Deployment with Azure Web App on Linux](./app-service-linux-ci-cd.md) (Distribuzione continua dell'hub Docker con App Web di Azure in Linux)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png

