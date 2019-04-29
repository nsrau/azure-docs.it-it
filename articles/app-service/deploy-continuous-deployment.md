---
title: Distribuzione continua - Servizio app di Azure | Microsoft Docs
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
ms.date: 12/03/2018
ms.author: cephalin;dariagrigoriu
ms.custom: seodec18
ms.openlocfilehash: fcb2c270b36d5efbe7b799787cf2a123b51bea5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765711"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Distribuzione continua nel servizio app di Azure
Questo articolo illustra come configurare la distribuzione continua per il [servizio app di Azure](overview.md). Il servizio app consente la distribuzione continua da BitBucket, GitHub e [Azure DevOps Services](https://www.visualstudio.com/team-services/) effettuando il pull degli aggiornamenti più recenti dal repository esistente in uno di questi servizi.

Per informazioni su come configurare manualmente la distribuzione continua da un repository cloud non elencato nel portale di Azure (ad esempio [GitLab](https://gitlab.com/)), vedere [Setting up continuous deployment using manual steps](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps) (Configurazione manuale della distribuzione continua).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

Pubblicare il repository preparato in uno dei servizi supportati. Per altre informazioni sulla pubblicazione di progetti in questi servizi, vedere gli articoli relativi a [creazione di repository (GitHub)], [creazione di repository (BitBucket)] e [Introduzione ad Azure DevOps Services].

## <a name="deploy-continuously-from-github"></a>Eseguire la distribuzione continua da GitHub

Per abilitare la distribuzione continua con GitHub, passare alla pagina dell'app del servizio app nel [portale di Azure](https://portal.azure.com).

Nel menu a sinistra fare clic su **Centro distribuzione** > **GitHub** > **Autorizza**. Seguire le istruzioni di autorizzazione. 

![](media/app-service-continuous-deployment/github-choose-source.png)

È necessario concedere l'autorizzazione per GitHub una sola volta. Se si dispone già di autorizzazione, fare semplicemente clic su **Continua**. È possibile modificare l'account GitHub autorizzato facendo clic su **Cambia account**.

![](media/app-service-continuous-deployment/github-continue.png)

Nella pagina **Provider compilazione** scegliere il provider di compilazione e fare clic su > **Continua**.

### <a name="option-1-use-app-service-kudu-build-server"></a>Opzione 1: usare il server di compilazione Kudu del servizio app

Nella pagina **Configura** selezionare l'organizzazione, il repository e il ramo da cui si vuole eseguire la distribuzione continua. Al termine dell'operazione, fare clic su **Continua**.

Per distribuire da un repository in un'organizzazione di GitHub, passare a GitHub e andare al **le impostazioni** > **applicazioni** > **autorizzate OAuth app**. Quindi fare clic su "Servizio App di Azure".

![Impostazioni > applicazioni > OAuth le app autorizzate > servizio App di Azure](media/app-service-continuous-deployment/github-settings-navigation.png)

Nella pagina successiva, concedere l'accesso di servizio App per i repository dell'organizzazione facendo clic sul pulsante "Concedi" sul lato destro.

![Fare clic su "Concessione" per concedere l'accesso al servizio App per i repository dell'organizzazione](media/app-service-continuous-deployment/grant-access.png)

L'organizzazione ora dovrebbe essere visualizzato nell'elenco "Organizzazione" i **configura** pagina del centro di distribuzione.

### <a name="option-2-use-azure-pipelines-preview"></a>Opzione 2: usare Azure Pipelines (anteprima)

> [!NOTE]
> Per il servizio App per creare le Azure Pipelines necessarie nell'organizzazione di servizi di Azure DevOps, l'account di Azure deve avere il ruolo di **Proprietario** nella sottoscrizione di Azure.
>

Nella sezione **Codice** della pagina **Configura** selezionare l'organizzazione, il repository e il ramo da cui si vuole eseguire la distribuzione continua. Al termine dell'operazione, fare clic su **Continua**.

Nella sezione **Compila** della pagina **Configura**, configurare una nuova organizzazione di Azure DevOps Services o specificare un'organizzazione esistente. Al termine dell'operazione, fare clic su **Continua**.

> [!NOTE]
> Se si desidera usare un'organizzazione di Azure DevOps Services esistente non presente in elenco, è necessario [collegare l'organizzazione di Azure DevOps Services alla sottoscrizione di Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Nella pagina **Test** scegliere se abilitare i test di carico e fare clic su **Continua**.

In base al [piano tariffario](https://azure.microsoft.com/pricing/details/app-service/plans/) del piano di servizio app, è anche possibile visualizzare la pagina **Distribuisci nell'ambiente di staging**. Scegliere se [abilitare gli slot di distribuzione](deploy-staging-slots.md) e fare clic su **Continua**.

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

## <a name="deploy-continuously-from-azure-repos-devops-services"></a>Eseguire la distribuzione continua da Azure Repos (DevOps Services)

Per abilitare la distribuzione continua da [Azure Repos](https://docs.microsoft.com/azure/devops/repos/index), passare alla pagina dell'app del servizio app nel [portale di Azure](https://portal.azure.com).

Nel menu a sinistra fare clic su **Centro distribuzione** > **Azure Repos** > **Continua**. 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

Nella pagina **Provider compilazione** scegliere il provider di compilazione e fare clic su > **Continua**.

> [!NOTE]
> Se si desidera usare un'organizzazione di Azure DevOps Services esistente non presente in elenco, è necessario [collegare l'organizzazione di Azure DevOps Services alla sottoscrizione di Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

### <a name="option-1-use-app-service-kudu-build-server"></a>Opzione 1: usare il server di compilazione Kudu del servizio app

Nella pagina **Configura** selezionare l'organizzazione, il progetto, il repository e il ramo Azure DevOps Services da cui si vuole eseguire la distribuzione continua. Al termine dell'operazione, fare clic su **Continua**.

### <a name="option-2-use-azure-devops-services-continuous-delivery"></a>Opzione 2: usare il recapito continuo di Azure DevOps Services

> [!NOTE]
> Per il servizio app per creare le Azure Pipelines necessarie nell'organizzazione di Azure DevOps Services, l'account di Azure deve avere il ruolo del **Proprietario** nella sottoscrizione di Azure.
>

Nella sezione **Codice** della pagina **Configura**, selezionare l'organizzazione, il progetto, il repository e il ramo Azure DevOps Services da cui si vuole eseguire la distribuzione continua. Al termine dell'operazione, fare clic su **Continua**.

Nella sezione **Compila** della pagina **Configura**, specificare il framework del linguaggio che Azure DevOps Services userà per eseguire le attività di compilazione per il repository selezionato. Al termine dell'operazione, fare clic su **Continua**.

Nella pagina **Test** scegliere se abilitare i test di carico e fare clic su **Continua**.

In base al [piano tariffario](https://azure.microsoft.com/pricing/details/app-service/plans/) del piano di servizio app, è anche possibile visualizzare la pagina **Distribuisci nell'ambiente di staging**. Scegliere se [abilitare gli slot di distribuzione](deploy-staging-slots.md) e fare clic su **Continua**. 

### <a name="finish-configuration"></a>Concludere la configurazione

Nella pagina **Riepilogo** verificare le opzioni e fare clic su **Fine**.

Al termine della configurazione, i nuovi commit all'interno del repository selezionato vengono distribuiti in modo continuo nell'app del servizio app.

## <a name="disable-continuous-deployment"></a>Disabilitare la distribuzione continua

Per disabilitare la distribuzione continua, passare alla pagina dell'app del servizio app nel [portale di Azure](https://portal.azure.com).

Nel menu a sinistra fare clic su **Centro distribuzione** > **GitHub** oppure **Azure DevOps Services** oppure **BitBucket** > **Disconnetti**.

![](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

* [How to investigate common issues with continuous deployment](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Come usare PowerShell per Azure]
* [Documentazione su Git]
* [Progetto Kudu](https://github.com/projectkudu/kudu/wiki)
* [Use Azure to automatically generate a CI/CD pipeline to deploy an ASP.NET 4 app](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic) (Usare Azure per generare automaticamente una pipeline CI/CD per distribuire un'app ASP.NET 4)

[Azure portal]: https://portal.azure.com
[Azure DevOps portal]: https://azure.microsoft.com/services/devops/
[Installing Git]: https://git-scm.com/book/en/Getting-Started-Installing-Git
[Come usare PowerShell per Azure]: /powershell/azureps-cmdlets-docs
[Documentazione su Git]: https://git-scm.com/documentation

[creazione di repository (GitHub)]: https://help.github.com/articles/create-a-repo
[creazione di repository (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Introduzione ad Azure DevOps Services]: https://docs.microsoft.com/azure/devops/user-guide/devops-alm-overview
