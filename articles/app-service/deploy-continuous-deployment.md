---
title: Configurare la distribuzione continua
description: Informazioni su come abilitare ci/CD al servizio app di Azure da GitHub, BitBucket, Azure Repos o altri repository. Selezionare la pipeline di compilazione più adatta alle proprie esigenze.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 08/23/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: d58eb333c930d2ffac4eb57340ea776338325181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266039"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Distribuzione continua nel servizio app di Azure

[Il servizio app di Azure](overview.md) consente la distribuzione continua dai repository GitHub, BitBucket e Azure [Repos](https://azure.microsoft.com/services/devops/repos/) eseguendo gli aggiornamenti più recenti. Questo articolo illustra come usare il portale di Azure per distribuire continuamente l'app tramite il servizio di compilazione Kudu o le pipeline di Azure.This article shows you how to use the Azure portal to continuously deploy your app through the Kudu build service or [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/). 

Per altre informazioni sui servizi del controllo del codice sorgente, vedere [Creare un repository (GitHub)], [Creare un repository (BitBucket)]o [Creare un nuovo repository Git (Azure Repos).]

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Autorizzare il servizio app di AzureAuthorize Azure App Service 

Per usare Azure Repos, assicurarsi che l'organizzazione DevOps di Azure sia collegata alla sottoscrizione di Azure.To use Azure Repos, make sure your Azure DevOps organization is linked to your Azure subscription. Per altre informazioni, vedere Configurare un account di servizi DevOps di [Azure in modo che possa essere distribuito in un'app Web.](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)

Per Bitbucket o GitHub, autorizzare il servizio app di Azure a connettersi al repository. È sufficiente autorizzare con un servizio di controllo del codice sorgente una sola volta. 

1. Nel [portale](https://portal.azure.com)di Azure cercare e selezionare **App Services**. 

   ![Cercare i servizi dell'app.](media/app-service-continuous-deployment/search-for-app-services.png)

1. Selezionare l'app Web da distribuire.

   ![Seleziona la tua app.](media/app-service-continuous-deployment/select-your-app.png)
   
1. Nella pagina dell'app selezionare **Centro di distribuzione** nel menu a sinistra.
   
1. Nella pagina **Centro distribuzione** selezionare **GitHub** o **Bitbucket**, quindi scegliere **Autorizza**. 
   
   ![Select source control service, then select Authorize.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Accedere al servizio, se necessario, e seguire le istruzioni di autorizzazione. 

## <a name="enable-continuous-deployment"></a>adsa 

Dopo aver autorizzato un servizio di controllo del codice sorgente, configurare l'app per la distribuzione continua tramite il server di compilazione predefinito del [servizio app Kudu](#option-1-use-the-app-service-build-service)o tramite Pipeline di [Azure.](#option-2-use-azure-pipelines) 

### <a name="option-1-use-the-app-service-build-service"></a>Opzione 1: usare il servizio di compilazione del servizio appOption 1: Use the App Service build service

È possibile usare il server di compilazione del servizio app Kudu incorporato per distribuire continuamente da GitHub, Bitbucket o Azure Repos.You can use the built server built-in Kudu App Service to continuously deploy from GitHub, Bitbucket, or Azure Repos. 

1. Nel [portale](https://portal.azure.com)di Azure cercare e selezionare **Servizi app**e quindi selezionare l'app Web da distribuire. 
   
1. Nella pagina dell'app selezionare **Centro di distribuzione** nel menu a sinistra.
   
1. Selezionare il provider del controllo del codice sorgente autorizzato nella pagina **Centro di distribuzione** e selezionare **Continua**. Per GitHub o Bitbucket, puoi anche selezionare **Cambia account** per modificare l'account autorizzato. 
   
   > [!NOTE]
   > Per usare I repository di Azure, verificare che l'organizzazione dei servizi DevOps di Azure sia collegata alla sottoscrizione di Azure.To use Azure Repos, make sure your Azure DevOps Services organization is linked to your Azure subscription. Per altre informazioni, vedere Configurare un account di servizi DevOps di [Azure in modo che possa essere distribuito in un'app Web.](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)
   
1. Per GitHub o Azure Repos, nella pagina **Crea provider** selezionare Servizio di **compilazione del servizio app**e quindi **Continua**. Bitbucket usa sempre il servizio di compilazione del servizio app.
   
   ![Select App Service build service, then select Continue.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Nella pagina **Configura:**
   
   - Per GitHub, fare clic su e selezionare **Organizzazione**, **Repository**e **Branch** che si desidera distribuire continuamente.
     
     > [!NOTE]
     > Se non viene visualizzato alcun repository, potrebbe essere necessario autorizzare il servizio app di Azure in GitHub.If you don't see any repositories, you may need to authorize Azure App Service in GitHub. Passare al repository GitHub e passare a **Impostazioni** > **Applicazioni** > **autorizzate Applicazioni OAuth**. Selezionare **Servizio app di Azure**e quindi **Concessione**. Per i repository dell'organizzazione, è necessario essere proprietari dell'organizzazione per concedere le autorizzazioni.
     
   - Per Bitbucket, selezionare il **Team**Bitbucket , **Repository**e **Branch** che si desidera distribuire continuamente.
     
   - Per Azure Repos, selezionare **Azure DevOps Organization**, **Project**, **Repository**e **Branch** che si desidera distribuire continuamente.
     
     > [!NOTE]
     > Se l'organizzazione DevOps di Azure non è presente nell'elenco, assicurarsi che sia collegata alla sottoscrizione di Azure.If your Azure DevOps organization isn't listed, make sure it's linked to your Azure subscription. Per altre informazioni, vedere Configurare un account di servizi DevOps di [Azure in modo che possa essere distribuito in un'app Web.For](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)more information, see Set up an Azure DevOps Services account so it can deploy to a web app ..
     
1. Selezionare **Continua**.
   
   ![Inserisci le informazioni del repository, quindi seleziona Continua.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Dopo aver configurato il provider di compilazione, rivedere le impostazioni nella pagina **Riepilogo** e quindi selezionare **Fine**.
   
   I nuovi commit nel repository e nel ramo selezionati vengono ora distribuiti continuamente nell'app del servizio app. È possibile tenere traccia dei commit e delle distribuzioni nella pagina **Centro distribuzione.**
   
   ![Tenere traccia dei commit e delle distribuzioni nel Centro distribuzione](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>Opzione 2: Usare le pipeline di AzureOption 2: Use Azure Pipelines 

Se l'account dispone delle autorizzazioni necessarie, è possibile configurare le pipeline di Azure per la distribuzione continua da repository GitHub o Azure Repos.If your account has the necessary permissions, you can set up Azure Pipelines to continuously deploy from GitHub or Azure Repos repositories. Per altre informazioni sulla distribuzione tramite le pipeline di Azure, vedere [Distribuire un'app Web in Servizi app di Azure.For](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)more information about deploying through Azure Pipelines, see Deploy a web app to Azure App Services.

Per il servizio app di Azure creare pipeline di Azure a distribuzione continua nell'organizzazione DevOps di Azure:For Azure App Service to create continuous delivery Azure Pipelines in your Azure DevOps organization: 

- L'account Azure deve disporre delle autorizzazioni per scrivere in Azure Active Directory e creare un servizio. 
  
- L'account Azure deve avere il ruolo Proprietario nella sottoscrizione di Azure.Your Azure account must have the **Owner** role in your Azure subscription.

- È necessario essere un amministratore nel progetto DevOps di Azure che si vuole usare.

Per configurare le pipeline di Azure (anteprima):To configure Azure Pipelines (Preview):

1. Nel [portale](https://portal.azure.com)di Azure cercare e selezionare **Servizi app**e quindi selezionare l'app Web da distribuire. 
   
1. Nella pagina dell'app selezionare **Centro di distribuzione** nel menu a sinistra.
   
1. Nella pagina **Crea provider** selezionare Pipeline di **Azure (anteprima)** e quindi selezionare **Continua**. 
   
1. Nella sezione **Codice** della pagina **Configura:**
   
   - Per GitHub, fare clic su e selezionare **Organizzazione**, **Repository**e **Branch** che si desidera distribuire continuamente.
     
     > [!NOTE]
     > Se non viene visualizzato alcun repository, potrebbe essere necessario autorizzare il servizio app di Azure in GitHub.If you don't see any repositories, you may need to authorize Azure App Service in GitHub. Passare al repository GitHub e passare a **Impostazioni** > **Applicazioni** > **autorizzate Applicazioni OAuth**. Selezionare **Servizio app di Azure**e quindi **Concessione**. Per i repository dell'organizzazione, è necessario essere proprietari dell'organizzazione per concedere le autorizzazioni.
     
   - Per Azure Repos, selezionare **Azure DevOps Organization**, **Project**, **Repository**e **Branch** che si desidera distribuire continuamente oppure configurare una nuova organizzazione DevOps di Azure.
     
     > [!NOTE]
     > Se l'organizzazione DevOps di Azure esistente non è presente nell'elenco, potrebbe essere necessario collegarla alla sottoscrizione di Azure.If your existing Azure DevOps organization isn't listed, you may need to link it to your Azure subscription. Per ulteriori informazioni, vedere Definire la pipeline di [rilascio di CD.](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)
     
1. Selezionare **Continua**.
   
1. Per Azure Repos, nella sezione **Compilazione** specificare il framework del linguaggio che le pipeline di Azure devono usare per eseguire le attività di compilazione e quindi selezionare **Continua**.
   
1. Nella pagina **Test** scegliere se abilitare i test di carico e quindi scegliere **Continua**.
   
1. A seconda del [piano tariffario](https://azure.microsoft.com/pricing/details/app-service/plans/)del piano di servizio app, è possibile che venga visualizzata una pagina **Distribuisci in gestione temporanea.** Scegliere se [abilitare gli slot](deploy-staging-slots.md)di distribuzione , quindi scegliere **Continua**.
   
   > [!NOTE]
   > Le pipeline di Azure non consentono il recapito continuo allo slot di produzione. Questa restrizione impedisce distribuzioni accidentali nell'ambiente di produzione. Impostare la consegna continua in uno slot di gestione temporanea, verificare le modifiche e quindi scambiare gli slot quando si è pronti.
   
1. Dopo aver configurato il provider di compilazione, rivedere le impostazioni nella pagina **Riepilogo** e quindi selezionare **Fine**.
   
   I nuovi commit nel repository e nel ramo selezionati vengono ora distribuiti continuamente nell'app del servizio app. È possibile tenere traccia dei commit e delle distribuzioni nella pagina **Centro distribuzione.**
   
   ![Tenere traccia dei commit e delle distribuzioni nel Centro distribuzione](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>Disabilitare la distribuzione continua

Per disabilitare la distribuzione continua, seleziona **Disconnetti** nella parte superiore della pagina **Centro di distribuzione** dell'app.

![Disabilitare la distribuzione continua](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Usa repository non supportati

Per le app di Windows, è possibile configurare manualmente la distribuzione continua da un repository Git o Mercurial cloud non supportato direttamente dal portale, ad esempio [GitLab](https://gitlab.com/). A tale scopo, scegliere la casella Esterno nella pagina **Centro distribuzione.** Per ulteriori informazioni, vedere [Configurare la distribuzione continua tramite passaggi manuali](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Esaminare i problemi comuni relativi alla distribuzione continuaInvestigate common issues with continuous deployment](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Usare Azure PowerShellUse Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Documentazione di Git](https://git-scm.com/documentation)
* [Progetto Kudu](https://github.com/projectkudu/kudu/wiki)

[creazione di repository (GitHub)]: https://help.github.com/articles/create-a-repo
[creazione di repository (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Creare un nuovo repository Git (Azure Repos)Create a new Git repo (Azure Repos)]: /azure/devops/repos/git/creatingrepo
