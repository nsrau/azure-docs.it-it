---
title: Distribuzione continua nel servizio app di Azure | Documentazione Microsoft
description: Informazioni su come abilitare la distribuzione continua nel servizio app di Azure.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: cephalin;dariagrigoriu
ms.openlocfilehash: d83d1ad74d04356f73f18a744c2d1509b5efc280
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233845"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Distribuzione continua nel servizio app di Azure
Questo articolo illustra come configurare la distribuzione continua per il [servizio app di Azure](app-service-web-overview.md). Il servizio app consente la distribuzione continua da BitBucket, GitHub e [Visual Studio Team Services (VSTS)](https://www.visualstudio.com/team-services/) effettuando il pull degli aggiornamenti più recenti dal repository esistente in uno di questi servizi.

Per informazioni su come configurare manualmente la distribuzione continua da un repository cloud non elencato nel portale di Azure (ad esempio [GitLab](https://gitlab.com/)), vedere [Setting up continuous deployment using manual steps](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps) (Configurazione manuale della distribuzione continua).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

Pubblicare il repository preparato in uno dei servizi supportati. Per altre informazioni sulla pubblicazione di progetti in questi servizi, vedere gli articoli relativi a [creazione di repository (GitHub)], [creazione di repository (BitBucket)] e [VSTS].

## <a name="deploy-continuously-from-github"></a>Eseguire la distribuzione continua da GitHub

Per abilitare la distribuzione continua con GitHub, passare alla pagina dell'app del servizio app nel [portale di Azure](https://portal.azure.com).

Nel menu a sinistra fare clic su **Centro distribuzione** > **GitHub** > **Autorizza**. Seguire le istruzioni di autorizzazione. 

![](media/app-service-continuous-deployment/github-choose-source.png)

È necessario concedere l'autorizzazione per GitHub una sola volta. Se si dispone già di autorizzazione, fare semplicemente clic su **Continua**. È possibile modificare l'account GitHub autorizzato facendo clic su **Cambia account**.

![](media/app-service-continuous-deployment/github-continue.png)

Nella pagina **Provider compilazione** scegliere il provider di compilazione e fare clic su > **Continua**.

### <a name="option-1-use-app-service-kudu-build-server"></a>Opzione 1: usare il server di compilazione Kudu del servizio app

Nella pagina **Configura** selezionare l'organizzazione, il repository e il ramo da cui si vuole eseguire la distribuzione continua. Al termine dell'operazione, fare clic su **Continua**.

### <a name="option-2-use-vsts-continuous-delivery"></a>Opzione 2: usare la distribuzione continua di VSTS

> [!NOTE]
> Perché il servizio app crei la compilazione e le definizioni di versione necessarie nell'account di VSTS, l'account di Azure deve avere il ruolo di **Proprietario** nella sottoscrizione di Azure.
>

Nella sezione **Codice** della pagina **Configura** selezionare l'organizzazione, il repository e il ramo da cui si vuole eseguire la distribuzione continua. Al termine dell'operazione, fare clic su **Continua**.

Nella sezione **Compila** della pagina **Configura** configurare un nuovo account VSTS o specificare un account esistente. Al termine dell'operazione, fare clic su **Continua**.

> [!NOTE]
> Se si vuole usare un account VSTS esistente che non è elencato, è necessario [collegare l'account VSTS alla sottoscrizione di Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Nella pagina **Test** scegliere se abilitare i test di carico e fare clic su **Continua**.

In base al [piano tariffario](/pricing/details/app-service/plans/) del piano di servizio app, è anche possibile visualizzare la pagina **Distribuisci nell'ambiente di staging**. Scegliere se [abilitare gli slot di distribuzione](web-sites-staged-publishing.md) e fare clic su **Continua**.

### <a name="finish-configuration"></a>Concludere la configurazione

Nella pagina **Riepilogo** verificare le opzioni e fare clic su **Fine**.

Al termine della configurazione, i nuovi commit all'interno del repository selezionato vengono distribuiti in modo continuo nell'app del servizio app.

![](media/app-service-continuous-deployment/github-finished.png)

## <a name="deploy-continuously-from-bitbucket"></a>Eseguire la distribuzione continua da BitBucket

Per abilitare la distribuzione continua con BitBucket, passare alla pagina dell'app del servizio app nel [portale di Azure](https://portal.azure.com).

Nel menu a sinistra fare clic su **Centro distribuzione** > **BitBucket** > **Autorizza**. Seguire le istruzioni di autorizzazione. 

![](media/app-service-continuous-deployment/bitbucket-choose-source.png)

È necessario concedere l'autorizzazione per BitBucket una sola volta. Se si dispone già di autorizzazione, fare semplicemente clic su **Continua**. È possibile modificare l'account BitBucket autorizzato facendo clic su **Cambia account**.

![](media/app-service-continuous-deployment/bitbucket-continue.png)

Nella pagina **Configura** selezionare il repository e il ramo da cui si vuole eseguire la distribuzione continua. Al termine dell'operazione, fare clic su **Continua**.

Nella pagina **Riepilogo** verificare le opzioni e fare clic su **Fine**.

Al termine della configurazione, i nuovi commit all'interno del repository selezionato vengono distribuiti in modo continuo nell'app del servizio app.

## <a name="deploy-continuously-from-vsts"></a>Eseguire la distribuzione continua da VSTS

Per abilitare la distribuzione continua con VSTS, passare alla pagina dell'app del servizio app nel [portale di Azure](https://portal.azure.com).

Nel menu a sinistra fare clic su **Centro distribuzione** > **VSTS** > **Autorizza**. 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

Nella pagina **Provider compilazione** scegliere il provider di compilazione e fare clic su > **Continua**.

### <a name="option-1-use-app-service-kudu-build-server"></a>Opzione 1: usare il server di compilazione Kudu del servizio app

Nella pagina **Configura** selezionare l'account VSTS, il progetto, il repository e il ramo da cui si vuole eseguire la distribuzione continua. Al termine dell'operazione, fare clic su **Continua**.

### <a name="option-2-use-vsts-continuous-delivery"></a>Opzione 2: usare la distribuzione continua di VSTS

> [!NOTE]
> Perché il servizio app crei la compilazione e le definizioni di versione necessarie nell'account di VSTS, l'account di Azure deve avere il ruolo di **Proprietario** nella sottoscrizione di Azure.
>

Nella sezione **Codice** della pagina **Configura** selezionare l'account VSTS, il progetto, il repository e il ramo da cui si vuole eseguire la distribuzione continua. Al termine dell'operazione, fare clic su **Continua**.

> [!NOTE]
> Se si vuole usare un account VSTS esistente che non è elencato, è necessario [collegare l'account VSTS alla sottoscrizione di Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Nella sezione **Compila** della pagina **Configura** specificare il framework del linguaggio che VSTS userà per eseguire le attività di compilazione per il repository selezionato. Al termine dell'operazione, fare clic su **Continua**.

Nella pagina **Test** scegliere se abilitare i test di carico e fare clic su **Continua**.

In base al [piano tariffario](/pricing/details/app-service/plans/) del piano di servizio app, è anche possibile visualizzare la pagina **Distribuisci nell'ambiente di staging**. Scegliere se [abilitare gli slot di distribuzione](web-sites-staged-publishing.md) e fare clic su **Continua**. 

### <a name="finish-configuration"></a>Concludere la configurazione

Nella pagina **Riepilogo** verificare le opzioni e fare clic su **Fine**.

Al termine della configurazione, i nuovi commit all'interno del repository selezionato vengono distribuiti in modo continuo nell'app del servizio app.

## <a name="disable-continuous-deployment"></a>Disabilitare la distribuzione continua

Per disabilitare la distribuzione continua, passare alla pagina dell'app del servizio app nel [portale di Azure](https://portal.azure.com).

Nel menu a sinistra fare clic su **Centro distribuzione** > **GitHub** oppure **VSTS** oppure **BitBucket** > **Disconnetti**.

![](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

* [How to investigate common issues with continuous deployment](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Come usare PowerShell per Azure]
* [Documentazione su Git]
* [Progetto Kudu](https://github.com/projectkudu/kudu/wiki)
* [Use Azure to automatically generate a CI/CD pipeline to deploy an ASP.NET 4 app](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic) (Usare Azure per generare automaticamente una pipeline CI/CD per distribuire un'app ASP.NET 4)

[Azure portal]: https://portal.azure.com
[VSTS Portal]: https://www.visualstudio.com/en-us/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Come usare PowerShell per Azure]: /powershell/azureps-cmdlets-docs
[Documentazione su Git]: http://git-scm.com/documentation

[creazione di repository (GitHub)]: https://help.github.com/articles/create-a-repo
[creazione di repository (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[VSTS]: https://www.visualstudio.com/docs/vsts-tfs-overview
