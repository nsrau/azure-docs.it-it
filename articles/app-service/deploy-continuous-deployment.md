---
title: Configurare la distribuzione continua
description: Informazioni su come abilitare l'integrazione continua/distribuzione continua per app Azure servizio da GitHub, BitBucket, Azure Repos o altri repository. Selezionare la pipeline di compilazione che soddisfa le proprie esigenze.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/20/2020
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 847de2c2c8916558d542473d9b7c80fd5552dbf7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80437152"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Distribuzione continua nel servizio app di Azure

Il [servizio app Azure](overview.md) consente la distribuzione continua da GitHub, BitBucket e [Azure Repos](https://azure.microsoft.com/services/devops/repos/) repository eseguendo il pull degli aggiornamenti più recenti. Questo articolo illustra come usare la portale di Azure per distribuire continuamente l'app tramite il servizio di compilazione Kudu o [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/). 

Per ulteriori informazioni sui servizi di controllo del codice sorgente, vedere [creare un repository (GitHub)], [creare un repository (bitbucket)]o [creare un nuovo repository git (Azure Repos)].

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Autorizzare il servizio app Azure 

Per usare Azure Repos, assicurarsi che l'organizzazione Azure DevOps sia collegata alla sottoscrizione di Azure. Per altre informazioni, vedere [configurare un account Azure DevOps Services in modo che possa essere distribuito in un'app Web](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).

Per bitbucket o GitHub, autorizzare app Azure servizio a connettersi al repository. È sufficiente autorizzare un servizio di controllo del codice sorgente una sola volta. 

1. Nella [portale di Azure](https://portal.azure.com)cercare **Servizi app** e selezionare.

   ![Cercare i servizi app.](media/app-service-continuous-deployment/search-for-app-services.png)

1. Selezionare il servizio app che si vuole distribuire.

   ![Selezionare l'app.](media/app-service-continuous-deployment/select-your-app.png)
   
1. Nella pagina App selezionare **centro distribuzione** nel menu a sinistra.
   
1. Nella pagina **centro distribuzione** selezionare **GitHub** o **bitbucket**e quindi selezionare **autorizza**. 
   
   ![Selezionare servizio di controllo del codice sorgente, quindi selezionare autorizza.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Accedere al servizio, se necessario, e seguire le istruzioni di autorizzazione. 

## <a name="enable-continuous-deployment"></a>adsa 

Dopo aver autorizzato un servizio di controllo del codice sorgente, configurare l'app per la distribuzione continua tramite il server di compilazione del [servizio app Kudu](#option-1-kudu-app-service) incorporato o tramite [Azure Pipelines](#option-2-azure-pipelines). 

### <a name="option-1-kudu-app-service"></a>Opzione 1: servizio app Kudu

È possibile usare il server di compilazione del servizio app Kudu incorporato per eseguire la distribuzione continua da GitHub, Bitbucket o Azure Repos. 

1. Nella [portale di Azure](https://portal.azure.com)cercare **Servizi app**e quindi selezionare il servizio app che si vuole distribuire. 
   
1. Nella pagina App selezionare **centro distribuzione** nel menu a sinistra.
   
1. Selezionare il provider del controllo del codice sorgente autorizzato nella pagina **centro distribuzione** e selezionare **continua**. Per GitHub o Bitbucket, è anche possibile selezionare **Cambia account** per modificare l'account autorizzato. 
   
   > [!NOTE]
   > Per usare Azure Repos, assicurarsi che l'organizzazione Azure DevOps Services sia collegata alla sottoscrizione di Azure. Per altre informazioni, vedere [configurare un account Azure DevOps Services in modo che possa essere distribuito in un'app Web](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).
   
1. Per GitHub o Azure Repos, nella pagina **provider di compilazione** selezionare **servizio di compilazione servizio app**e quindi fare clic su **continua**. Bitbucket usa sempre il servizio di compilazione del servizio app.
   
   ![Selezionare servizio di compilazione servizio app, quindi selezionare continua.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Nella pagina **Configura** :
   
   - Per GitHub, elenco a discesa e selezionare l' **organizzazione**, il **repository**e il **ramo** che si desidera distribuire in modo continuo.
     
     > [!NOTE]
     > Se non viene visualizzato alcun repository, potrebbe essere necessario autorizzare app Azure servizio in GitHub. Passare al repository GitHub e passare a **Impostazioni** > **applicazioni** > **app OAuth autorizzate**. Selezionare **app Azure servizio**e quindi fare clic su **Concedi**. Per i repository dell'organizzazione, è necessario essere un proprietario dell'organizzazione per concedere le autorizzazioni.
     
   - Per bitbucket selezionare il **Team**, il **repository**e il **ramo** di bitbucket che si desidera distribuire in modo continuo.
     
   - Per Azure Repos selezionare l'organizzazione, il **progetto**, il **repository**e il **ramo** di **Azure DevOps**che si vuole distribuire in modo continuo.
     
     > [!NOTE]
     > Se l'organizzazione di Azure DevOps non è elencata, assicurarsi che sia collegata alla sottoscrizione di Azure. Per ulteriori informazioni, vedere la pagina relativa [alla configurazione di un account Azure DevOps Services in modo che possa essere distribuito in un'app Web](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).
     
1. Seleziona **Continua**.
   
   ![Immettere le informazioni sul repository, quindi selezionare continue (continua).](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Dopo aver configurato il provider di compilazione, rivedere le impostazioni nella pagina **Riepilogo** e quindi fare clic su **fine**.
   
1. I nuovi commit nel repository e nel ramo selezionati vengono distribuiti in modo continuo nell'app del servizio app. È possibile tenere traccia di commit e distribuzioni nella pagina **Centro distribuzione**.
   
   ![Tenere traccia di commit e distribuzioni in centro distribuzione](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-azure-pipelines"></a>Opzione 2: Azure Pipelines 

Se l'account dispone delle autorizzazioni necessarie, è possibile configurare Azure Pipelines per la distribuzione continua da GitHub o Azure Repos. Per altre informazioni sulla distribuzione tramite Azure Pipelines, vedere [distribuire un'app Web in servizi app Azure](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

#### <a name="prerequisites"></a>Prerequisiti

Per app Azure servizio per la creazione di recapito continuo con Azure Pipelines, l'organizzazione di Azure DevOps deve disporre delle autorizzazioni seguenti: 

- L'account Azure deve avere le autorizzazioni per scrivere in Azure Active Directory e creare un servizio. 
  
- L'account Azure deve avere il ruolo **proprietario** nella sottoscrizione di Azure.

- È necessario essere un amministratore nel progetto Azure DevOps che si vuole usare.

#### <a name="github--azure-pipelines"></a>GitHub + Azure Pipelines

1. Nella [portale di Azure](https://portal.azure.com)cercare **Servizi app**e quindi selezionare il servizio app che si vuole distribuire. 
   
1. Nella pagina App selezionare **centro distribuzione** nel menu a sinistra.

1. Selezionare **GitHub** come provider del controllo del codice sorgente nella pagina **centro distribuzione** e selezionare **continua**. Per **GitHub**è possibile selezionare **Cambia account** per modificare l'account autorizzato.

    ![controllo del codice sorgente](media/app-service-continuous-deployment/deployment-center-src-control.png)
   
1. Nella pagina **provider di compilazione** selezionare **Azure Pipelines (anteprima)**, quindi selezionare **continua**.

    ![provider di compilazione](media/app-service-continuous-deployment/select-build-provider.png)
   
1. Nella sezione **codice** della pagina **Configura** selezionare l' **organizzazione**, il **repository**e il **ramo** che si desidera distribuire in modo continuo e selezionare **continua**.
     
     > [!NOTE]
     > Se non viene visualizzato alcun repository, potrebbe essere necessario autorizzare app Azure servizio in GitHub. Passare al repository GitHub e passare a **Impostazioni** > **applicazioni** > **app OAuth autorizzate**. Selezionare **app Azure servizio**e quindi fare clic su **Concedi**. Per i repository dell'organizzazione, è necessario essere un proprietario dell'organizzazione per concedere le autorizzazioni.
       
    Nella sezione **Build (compila** ) specificare l'organizzazione, il progetto e il Framework del linguaggio Azure DevOps che Azure Pipelines deve usare per eseguire le attività di compilazione e quindi selezionare **continue (continua**).

   ![provider di compilazione](media/app-service-continuous-deployment/build-configure.png)

1. Dopo aver configurato il provider di compilazione, rivedere le impostazioni nella pagina **Riepilogo** e quindi fare clic su **fine**.

   ![provider di compilazione](media/app-service-continuous-deployment/summary.png)
   
1. I nuovi commit nel repository e nel ramo selezionati ora vengono distribuiti continuamente nel servizio app. È possibile tenere traccia di commit e distribuzioni nella pagina **Centro distribuzione**.
   
   ![Tenere traccia di commit e distribuzioni in centro distribuzione](media/app-service-continuous-deployment/github-finished.png)

#### <a name="azure-repos--azure-pipelines"></a>Azure Repos + Azure Pipelines

1. Nella [portale di Azure](https://portal.azure.com)cercare **Servizi app**e quindi selezionare il servizio app che si vuole distribuire. 
   
1. Nella pagina App selezionare **centro distribuzione** nel menu a sinistra.

1. Selezionare **Azure Repos** come provider del controllo del codice sorgente nella pagina **centro distribuzione** e selezionare **continua**.

    ![controllo del codice sorgente](media/app-service-continuous-deployment/deployment-center-src-control.png)

1. Nella pagina **provider di compilazione** selezionare **Azure Pipelines (anteprima)**, quindi selezionare **continua**.

    ![controllo del codice sorgente](media/app-service-continuous-deployment/azure-pipelines.png)

1. Nella sezione **codice** della pagina **Configura** selezionare l' **organizzazione**, il **repository**e il **ramo** che si desidera distribuire in modo continuo e selezionare **continua**.

   > [!NOTE]
   > Se l'organizzazione DevOps di Azure esistente non è elencata, potrebbe essere necessario collegarla alla sottoscrizione di Azure. Per altre informazioni, vedere [definire la pipeline di rilascio del CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).

   Nella sezione **Build (compila** ) specificare l'organizzazione, il progetto e il Framework del linguaggio Azure DevOps che Azure Pipelines deve usare per eseguire le attività di compilazione e quindi selezionare **continue (continua**).

   ![provider di compilazione](media/app-service-continuous-deployment/build-configure.png)

1. Dopo aver configurato il provider di compilazione, rivedere le impostazioni nella pagina **Riepilogo** e quindi fare clic su **fine**.  
     
   ![provider di compilazione](media/app-service-continuous-deployment/summary-azure-pipelines.png)

1. I nuovi commit nel repository e nel ramo selezionati ora vengono distribuiti continuamente nel servizio app. È possibile tenere traccia di commit e distribuzioni nella pagina **Centro distribuzione**.

## <a name="disable-continuous-deployment"></a>Disabilitare la distribuzione continua

Per disabilitare la distribuzione continua, selezionare **Disconnetti** nella parte superiore della pagina **centro distribuzione** dell'app.

![Disabilitare la distribuzione continua](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>USA repository non supportati

Per le app di Windows, è possibile configurare manualmente la distribuzione continua da un repository git cloud o Mercurial che il portale non supporta direttamente, ad esempio [GitLab](https://gitlab.com/). A tale scopo, selezionare la casella esterna nella pagina **centro distribuzione** . Per ulteriori informazioni, vedere la pagina relativa alla [configurazione della distribuzione continua mediante passaggi manuali](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Esaminare i problemi comuni con la distribuzione continua](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Usare Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Documentazione di Git](https://git-scm.com/documentation)
* [Progetto Kudu](https://github.com/projectkudu/kudu/wiki)

[creazione di repository (GitHub)]: https://help.github.com/articles/create-a-repo
[creazione di repository (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Creare un nuovo repository git (Azure Repos)]: /azure/devops/repos/git/creatingrepo
