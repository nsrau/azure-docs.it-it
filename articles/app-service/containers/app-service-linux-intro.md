---
title: Introduzione al Servizio app in Linux - Azure | Microsoft Docs
description: Informazioni sul Servizio app di Azure in Linux.
keywords: Servizio app di Azure, Linux, OSS
services: app-service
documentationcenter: ''
author: msangapu-msft
manager: gwallace
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 966eb6068ff545dde284463866bfeee3d5074069
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559015"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Introduzione al Servizio app di Azure in Linux

[Servizio app di Azure](../overview.md) è una piattaforma di calcolo completamente gestita, ottimizzata per l'hosting di siti Web e applicazioni Web. I clienti possono usare il servizio app in Linux per ospitare app Web in modo nativo in Linux per stack di applicazioni supportate.

## <a name="languages"></a>Languages

Il Servizio app in Linux supporta diverse immagini incorporate per aumentare la produttività degli sviluppatori. I linguaggi includono: Node.js, Java (JRE 8 e JRE 11), PHP, Python, .NET Core e Ruby. Eseguire [`az webapp list-runtimes --linux`](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) per visualizzare i linguaggi più recenti e le versioni supportate. Se il runtime richiesto dall'applicazione non è supportato nelle immagini incorporate, sono disponibili istruzioni su come [creare un'immagine Docker personalizzata](tutorial-custom-docker-image.md) da distribuire in app Web per contenitori.

## <a name="deployments"></a>Deployments

* FTP
* Repository Git locale
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Ambienti di staging
* [Registro Azure Container](https://docs.microsoft.com/azure/container-registry/container-registry-intro) e DockerHub CI/CD

## <a name="console-publishing-and-debugging"></a>Console, pubblicazione e debug

* Ambienti
* Deployments
* Console di base
* SSH

## <a name="scaling"></a>Ridimensionamento

* I clienti possono aumentare e ridurre le prestazioni delle app Web modificando il livello nel [piano di servizio app](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json)

## <a name="locations"></a>Località

Controllare il [dashboard dello stato di Azure](https://azure.microsoft.com/status).

## <a name="limitations"></a>Limitazioni

Il portale di Azure mostra solo le funzionalità che possono essere usate attualmente per l'app Web per i contenitori. Man mano che verranno abilitate nuove funzionalità, queste diventeranno visibili sul portale.

In Linux il servizio app è supportato solo nei piani di servizio app [Gratuito, Basic, Standard e Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) e non ha un livello [Condiviso](https://azure.microsoft.com/pricing/details/app-service/plans/). In Linux non è possibile creare app Web in un piano di servizio app che ospita già app Web non Linux.  

In base a una limitazione corrente, non è possibile combinare app di Windows e Linux per lo stesso gruppo di risorse nella stessa area.

## <a name="troubleshooting"></a>risoluzione dei problemi

> [!NOTE]
> È disponibile una nuova funzionalità di registrazione integrata con [Monitoraggio di Azure (anteprima)](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs#send-logs-to-azure-monitor-preview). 
>
>

Quando non è possibile avviare l'applicazione o si desidera controllare il log dall'app, controllare i log di Docker nella directory LogFiles. È possibile accedere a questa directory tramite il sito SCM o tramite FTP. Per registrare `stdout` e `stderr` dal contenitore, è necessario abilitare **Registrazione applicazioni** in **Log del servizio app**. L'impostazione ha effetto immediato. Il servizio app rileva la modifica e riavvia automaticamente il contenitore.

È possibile accedere al sito SCM da **Strumenti avanzati** nel menu **Strumenti di sviluppo**.

![Uso di Kudu per visualizzare i log di Docker][1]

## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti consentono di iniziare a usare il servizio app in Linux con le app Web scritte in vari tipi di linguaggio:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [App con più contenitori](quickstart-multi-container.md)

Per altre informazioni sul Servizio app in Linux, vedere:

* [Domande frequenti sul servizio app per Linux](app-service-linux-faq.md)
* [Supporto SSH per il servizio app in Linux](app-service-linux-ssh-support.md)
* [Configurare gli ambienti di gestione temporanea nel servizio app](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Abilitare la distribuzione continua di hub Docker](app-service-linux-ci-cd.md)

È possibile pubblicare domande e dubbi nel [forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
