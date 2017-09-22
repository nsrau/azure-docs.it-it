---
title: Introduzione ad App Web di Azure per contenitori | Microsoft Docs
description: Informazioni su App Web di Azure per contenitori.
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
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: f46f59a69d13a4e702eccded810cf3c7a8b30904
ms.contentlocale: it-it
ms.lasthandoff: 09/07/2017

---
# <a name="introduction-to-azure-web-apps-for-containers"></a>Introduzione ad App Web di Azure per contenitori

[App Web](../../app-service-web/app-service-web-overview.md) è una piattaforma di calcolo completamente gestita, ottimizzata per l'hosting di siti Web e applicazioni Web. I clienti possono usare App Web per contenitori per ospitare app Web in modo nativo in Linux per stack di applicazioni supportate. Le sezioni seguenti elencano gli stack di applicazioni attualmente supportati.

## <a name="languages"></a>Lingue

|Node.js|PHP|.NET Core|Ruby|
|:------------------:|:---:|:---------:|:----:|
|4.4, 4.5|5.6|1.0-1.1|2.3|
|6.2, 6.6, 6.9-6.11|7.0|||
|8.0-8.1||||

## <a name="deployments"></a>Deployments

* FTP
* Repository Git locale
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Ambienti di staging
* [Registro contenitori di Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro) e DockerHub CI/CD

## <a name="console-publishing-and-debugging"></a>Console, pubblicazione e debug

* Ambienti
* Deployments
* Console di base
* SSH

## <a name="scaling"></a>Ridimensionamento

* I clienti possono aumentare e ridurre le prestazioni delle app Web modificando il livello nel [piano di servizio app](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json)

## <a name="locations"></a>Località

Controllare il [dashboard dello stato di Azure](https://azure.microsoft.com/status).

## <a name="limitations"></a>Limitazioni

Il portale di Azure mostra solo le funzionalità che possono essere usate attualmente per App Web per contenitori. Man mano che verranno abilitate nuove funzionalità, queste diventeranno visibili sul portale.

Alcune funzionalità, quali l'integrazione delle reti virtuali, l'autenticazione di Azure Active Directory o di terze parti o le estensioni del sito Kudu, non sono ancora disponibili. Man mano che queste funzionalità dinvetano disponibili, la documentazione verrà aggiornata e le modifiche pubblicate nei blog.

App Web per contenitori è supportato solo nei piani di servizio app [Basic e Standard](https://azure.microsoft.com/pricing/details/app-service/plans/) e non ha un livello [Gratuito o Condiviso](https://azure.microsoft.com/pricing/details/app-service/plans/). Di seguito sono riportate altre importanti limitazioni di App Web per contenitori:

* In Linux, non è possibile creare App Web per contenitori in un piano di servizio app che ospita già App Web non Linux.
* Durante la creazione di App Web per contenitori in un gruppo di risorse contenente App Web non Linux, è necessario creare un piano di servizio app in un'area diversa rispetto al piano di servizio app esistente.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Quando non è possibile avviare l'applicazione o si desidera controllare il log dall'app, controllare i log di Docker nella directory LogFiles. È possibile accedere a questa directory tramite il sito SCM o tramite FTP.
Per registrare `stdout` e `stderr` dal contenitore, è necessario abilitare **Registrazione del contenitore Docker** in **Log di diagnostica**.

![Abilitazione della registrazione][2]

![Uso di Kudu per visualizzare i log di Docker][1]

È possibile accedere al sito SCM da **Strumenti avanzati** nel menu **Strumenti di sviluppo**.

## <a name="next-steps"></a>Passaggi successivi

Vedere i collegamenti seguenti per iniziare a usare il servizio app in Linux. È possibile pubblicare domande e dubbi nel [forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Come usare un'immagine Docker personalizzata per App Web di Azure per contenitori](quickstart-custom-docker-image.md)
* [Uso di .NET Core nelle app Web del servizio App di Azure per contenitori](quickstart-dotnetcore.md)
* [Uso di Ruby nelle app Web del servizio App di Azure per contenitori](quickstart-ruby.md)
* [Domande frequenti sulle app Web per contenitori del servizio app di Azure](app-service-linux-faq.md)
* [Supporto SSH per App Web di Azure per contenitori](app-service-linux-ssh-support.md)
* [Configurare gli ambienti di gestione temporanea nel Servizio app di Azure](../../app-service-web/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Docker Hub Continuous Deployment with Azure Web Apps for Containers](./app-service-linux-ci-cd.md) (Distribuzione continua dell'hub Docker con App Web di Azure per contenitori)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png

