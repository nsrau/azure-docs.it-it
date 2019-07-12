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
ms.date: 06/18/2019
ms.author: cephalin;dariagrigoriu
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 3a207f5513c24f2c33571df929f7490ec7f2eba5
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836760"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Distribuzione continua nel servizio app di Azure

[Servizio App di Azure](overview.md) abilita la distribuzione continua da GitHub, BitBucket, e [repository Azure](https://azure.microsoft.com/services/devops/repos/) repository eseguendo il pull negli aggiornamenti più recenti. Questo articolo illustra come usare il portale di Azure per distribuire continuamente l'app tramite il servizio di compilazione Kudu o [pipeline di Azure](https://azure.microsoft.com/services/devops/pipelines/). 

Per altre informazioni sui servizi di controllo di origine, vedere [creazione di repository (GitHub)], [creazione di repository (BitBucket)], o [creare un nuovo repository Git (repository di Azure)].

Per configurare manualmente la distribuzione continua da un repository cloud che il portale non supportava direttamente, ad esempio [GitLab](https://gitlab.com/), vedere [impostare la distribuzione continua usando i passaggi manuali](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Autorizzare il servizio App di Azure 

Per usare i repository di Azure, assicurarsi che l'organizzazione di Azure DevOps è collegato alla sottoscrizione di Azure. Per altre informazioni, vedere [configurare un account di servizi di Azure DevOps in modo da distribuirlo in un'app web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Per Bitbucket o GitHub, autorizzare il servizio App di Azure per la connessione al repository. È sufficiente per l'autorizzazione una sola volta con un servizio di controllo del codice sorgente. 

1. Selezionare **servizi App** nel [portale di Azure](https://portal.azure.com) riquadro di spostamento sinistro e quindi selezionare l'app web da distribuire. 
   
1. Nella pagina dell'app, selezionare **Deployment Center** nel menu a sinistra.
   
1. Nel **Deployment Center** pagina, selezionare **GitHub** oppure **Bitbucket**e quindi selezionare **Authorize**. 
   
   ![Selezionare il servizio di controllo di origine, quindi scegliere autorizza.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Accedere al servizio, se necessario e seguire le istruzioni di autorizzazione. 

## <a name="enable-continuous-deployment"></a>adsa 

Dopo che si autorizza un servizio di controllo del codice sorgente, configurare l'app per la distribuzione continua tramite l'oggetto incorporato [server di compilazione di servizio App di Kudu](#option-1-use-the-app-service-build-service), o tramite [le pipeline di Azure](#option-2-use-azure-pipelines). 

### <a name="option-1-use-the-app-service-build-service"></a>Opzione 1: Usare il servizio di compilazione del servizio App

È possibile usare l'oggetto incorporato di servizio App di Kudu creare server per la distribuzione continua da GitHub, Bitbucket o archivi di Azure. 

1. Selezionare **servizi App** nel [portale di Azure](https://portal.azure.com) riquadro di spostamento sinistro e quindi selezionare l'app web da distribuire. 
   
1. Nella pagina dell'app, selezionare **Deployment Center** nel menu a sinistra.
   
1. Selezionare il provider del controllo origine autorizzati nella **Deployment Center** pagina, quindi selezionare **continua**. Per GitHub o Bitbucket, è anche possibile selezionare **cambiare account** per modificare l'account autorizzato. 
   
   > [!NOTE]
   > Per usare i repository di Azure, assicurarsi che l'organizzazione di servizi di Azure DevOps è collegato alla sottoscrizione di Azure. Per altre informazioni, vedere [configurare un account di servizi di Azure DevOps in modo da distribuirlo in un'app web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).
   
1. Per GitHub o archivi di Azure, nel **provider di compilazione** pagina, selezionare **servizio App di servizio di compilazione**, quindi selezionare **continua**. Bitbucket Usa sempre il servizio di compilazione del servizio App.
   
   ![Selezionare il servizio di compilazione di servizio App, quindi selezionare continua.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Nel **configura** pagina:
   
   - Per GitHub, elenco a discesa e selezionare il **organizzazione**, **Repository**, e **ramo** si vuole eseguire la distribuzione continua.
     
     > [!NOTE]
     > Se non vengono visualizzati tutti i repository, occorre autorizzare il servizio App di Azure in GitHub. Passare al repository GitHub e andare al **le impostazioni** > **applicazioni** > **autorizzate OAuth app**. Selezionare **servizio App di Azure**, quindi selezionare **Concedi**.
     
   - Per Bitbucket, selezionare il Bitbucket **Team**, **Repository**, e **ramo** si vuole eseguire la distribuzione continua.
     
   - Per i repository di Azure, selezionare la **organizzazione di Azure DevOps**, **Project**, **Repository**, e **ramo** si vuole eseguire la distribuzione continua.
     
     > [!NOTE]
     > Se l'organizzazione di DevOps di Azure non è elencato, verificare che è collegato alla sottoscrizione di Azure. Per altre informazioni, vedere [configurare un account di servizi di Azure DevOps in modo da distribuirlo in un'app web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)...
     
1. Selezionare **continuare**.
   
   ![Immettere le informazioni del repository, quindi selezionare continua.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Dopo aver configurato il provider di compilazione, verificare le impostazioni nella **Summary** pagina e quindi selezionare **fine**.
   
   Nuovi commit nel repository selezionato e branch a questo punto eseguire la distribuzione continua nelle App del servizio app. È possibile monitorare le distribuzioni e i commit nel **Deployment Center** pagina.
   
   ![Tenere traccia delle distribuzioni nel centro di distribuzione e commit](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>Opzione 2: Usa Azure Pipelines 

Se l'account ha le autorizzazioni necessarie, è possibile impostare le pipeline di Azure per distribuire in modo continuo dal repository GitHub o archivi di Azure. Per altre informazioni sulla distribuzione tramite la pipeline di Azure, vedere [distribuire un'app web nel servizio App di Azure](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

Per il servizio App di Azure creare il recapito continuo le pipeline di Azure dell'organizzazione di Azure DevOps: 

- L'account di Azure deve avere le autorizzazioni di scrittura di Azure Active Directory e creare un servizio. 
  
- L'account di Azure deve avere il **proprietario** ruolo nella sottoscrizione di Azure.

- È necessario essere un amministratore del progetto DevOps di Azure da usare.

Per configurare le pipeline di Azure (anteprima):

1. Selezionare **servizi App** nel [portale di Azure](https://portal.azure.com) riquadro di spostamento sinistro e quindi selezionare l'app web da distribuire. 
   
1. Nella pagina dell'app, selezionare **Deployment Center** nel menu a sinistra.
   
1. Nel **provider di compilazione** pagina, selezionare **pipeline di Azure (anteprima)** e quindi selezionare **continua**. 
   
1. Nel **configura** pagina il **codice** sezione:
   
   - Per GitHub, elenco a discesa e selezionare il **organizzazione**, **Repository**, e **ramo** si vuole eseguire la distribuzione continua.
     
     > [!NOTE]
     > Se non vengono visualizzati tutti i repository, occorre autorizzare il servizio App di Azure in GitHub. Passare al repository GitHub e andare al **le impostazioni** > **applicazioni** > **autorizzate OAuth app**. Selezionare **servizio App di Azure**, quindi selezionare **Concedi**.
     
   - Per i repository di Azure, selezionare il **organizzazione di Azure DevOps**, **Project**, **Repository**, e **ramo** si vuole eseguire la distribuzione continua, oppure configurare una nuova organizzazione di Azure DevOps.
     
     > [!NOTE]
     > Se l'organizzazione di Azure DevOps esistente non è elencato, devi collegarla alla sottoscrizione di Azure. Per altre informazioni, vedere [definiscono la pipeline di versione CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
     
1. Selezionare **continuare**.
   
1. Per i repository di Azure, nelle **compilare** , quindi specificare il framework di linguaggio che le pipeline di Azure devono usare per eseguire le attività di compilazione e quindi selezionare **continua**.
   
1. Nel **Test** pagina, scegliere se abilitare i test di carico e quindi selezionare **continua**.
   
1. A seconda del piano di servizio App [piano tariffario](https://azure.microsoft.com/pricing/details/app-service/plans/), potrebbe essere visualizzato un **Distribuisci in staging** pagina. Scegliere se si desidera [Abilita slot di distribuzione](deploy-staging-slots.md), quindi selezionare **continua**.
   
   > [!NOTE]
   > Le pipeline di Azure non consente il recapito continuo in slot di produzione. Questa restrizione impedisce di distribuzioni accidentali nell'ambiente di produzione. Configurare il recapito continuo in uno slot di staging, verificare le modifiche richieste e quindi scambiare gli slot di quando si è pronti.
   
1. Dopo aver configurato il provider di compilazione, verificare le impostazioni nella **Summary** pagina e quindi selezionare **fine**.
   
   Nuovi commit nel repository selezionato e branch a questo punto eseguire la distribuzione continua nelle App del servizio app. È possibile monitorare le distribuzioni e i commit nel **Deployment Center** pagina.
   
   ![Tenere traccia delle distribuzioni nel centro di distribuzione e commit](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>Disabilitare la distribuzione continua

Per disabilitare la distribuzione continua, selezionare **Disconnect** nella parte superiore dell'app **Deployment Center** pagina.

![Disabilitare la distribuzione continua](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

* [Esaminare i problemi comuni con la distribuzione continua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Uso di Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Documentazione di Git](https://git-scm.com/documentation)
* [Progetto Kudu](https://github.com/projectkudu/kudu/wiki)

[creazione di repository (GitHub)]: https://help.github.com/articles/create-a-repo
[creazione di repository (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Creare un nuovo repository Git (repository di Azure)]: /azure/devops/repos/git/creatingrepo
