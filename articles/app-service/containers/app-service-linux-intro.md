---
title: Introduzione al Servizio app in Linux - Azure | Microsoft Docs
description: Informazioni sul Servizio app di Azure in Linux.
keywords: Servizio app di Azure, Linux, OSS
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu;yili
ms.custom: seodec18
ms.openlocfilehash: 2e75ff08acdda03c0080f49c6616274a4b031075
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2019
ms.locfileid: "54903724"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Introduzione al Servizio app di Azure in Linux

[Servizio app di Azure](../overview.md) è una piattaforma di calcolo completamente gestita, ottimizzata per l'hosting di siti Web e applicazioni Web. I clienti possono usare il servizio app in Linux per ospitare app Web in modo nativo in Linux per stack di applicazioni supportate. La sezione [Linguaggi](#languages) elenca gli stack di applicazioni attualmente supportati.

## <a name="languages"></a>Languages

Il Servizio app in Linux supporta diverse immagini incorporate per aumentare la produttività degli sviluppatori. Se il runtime richiesto dall'applicazione non è supportato nelle immagini incorporate, sono disponibili istruzioni su come [creare un'immagine Docker personalizzata](tutorial-custom-docker-image.md) da distribuire in app Web per contenitori.

| Linguaggio | Versioni supportate |
|---|---|
| Node.js | 4.4, 4.5, 4.8, 6.2, 6.6, 6.9, 6.10, 6.11, 8.0, 8.1, 8.2, 8.8, 8.9, 8.11, 9.4, 10.1,10.10 |
| Java * | Tomcat 8.5, 9.0, Java SE, WildFly 14 (con JRE 8 in esecuzione) |
| PHP | 5.6, 7.0, 7.2 |
| Python (anteprima) | 2.7, 3.6, 3.7 |
| .NET Core | 1.0, 1.1, 2.0, 2.1 |
| Ruby | 2.3 |

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

Alcune funzionalità, quali l'integrazione delle reti virtuali, l'autenticazione di Azure Active Directory o di terze parti o le estensioni del sito Kudu, non sono ancora disponibili. Man mano che queste funzionalità dinvetano disponibili, la documentazione verrà aggiornata e le modifiche pubblicate nei blog.

In Linux il servizio app è supportato solo nei piani di servizio app [Basic, Standard e Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) e non ha un livello [Gratuito o Condiviso](https://azure.microsoft.com/pricing/details/app-service/plans/). In Linux, non è possibile creare l'app Web per i contenitori in un piano di servizio app che ospita già App Web non Linux. 

Inoltre, in base una limitazione corrente, non combinare app di Windows e Linux nello stesso gruppo di risorse.

## <a name="troubleshooting"></a>risoluzione dei problemi

Quando non è possibile avviare l'applicazione o si desidera controllare il log dall'app, controllare i log di Docker nella directory LogFiles. È possibile accedere a questa directory tramite il sito SCM o tramite FTP.
Per registrare `stdout` e `stderr` dal contenitore, è necessario abilitare **Registrazione del contenitore Docker** in **Log di diagnostica**.

![Abilitazione della registrazione][2]

L'impostazione ha effetto immediato. Il servizio app rileva la modifica delle impostazioni e riavvia automaticamente il contenitore per l'utente.

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
