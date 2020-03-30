---
title: Distribuire dal repository GIT locale
description: Informazioni su come abilitare la distribuzione dell'archivio Git locale nel servizio app di Azure. Uno dei modi più semplici per distribuire il codice dal computer locale.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 06/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: efe4c07a6231e0b2c95b049db056a4e5d055db98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152993"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Distribuzione Git locale nel servizio app di AzureLocal Git deployment to Azure App Service

Questa guida alle procedure illustra come distribuire l'app nel servizio app di [Azure](overview.md) da un repository Git nel computer locale.

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura illustrata in questa guida dettagliata:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Installare Git](https://www.git-scm.com/downloads).
  
- Disporre di un repository Git locale con il codice che si desidera distribuire. Per scaricare un repository di esempio, eseguire il comando seguente nella finestra del terminale locale:To download a sample repository, run the following command in your local terminal window:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Eseguire la distribuzione con il server di compilazione KuduDeploy with Kudu build server

The easiest way to enable local Git deployment for your app with the Kudu App Service build server is to use Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Configurare un utente della distribuzione

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Ottenere l'URL di distribuzioneGet the deployment URL

Per ottenere l'URL per abilitare la [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) distribuzione Git locale per un'app esistente, eseguire in Cloud Shell. Sostituire \<> nome \<app e> nome-gruppo con i nomi dell'app e il relativo gruppo di risorse di Azure.Replace app-name> and group-name> with the names of your app and its Azure resource group.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```
> [!NOTE]
> Se si utilizza un piano del servizio di app linux, è necessario aggiungere questo parametro: --runtime python


In alternativa, per creare una nuova [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) app abilitata `--deployment-local-git` per Git, eseguire in Cloud Shell con il parametro . Sostituisci \<i> \<del nome dell'app, i> del nome del gruppo e \<i> nome-piano con i nomi della nuova app Git, del gruppo di risorse di Azure e del piano del servizio app di Azure.Replace app-name>, group-name>, and plan-name> with the names for your new Git app, its Azure resource group, and its Azure App Service plan.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Entrambi i comandi restituiscono un URL come: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Usa questo URL per distribuire l'app nel passaggio successivo.

Invece di usare questo URL a livello di account, puoi anche abilitare Git locale usando le credenziali a livello di app. Il servizio app di Azure genera automaticamente queste credenziali per ogni app. 

Ottenere le credenziali dell'app eseguendo il comando seguente in Cloud Shell. Sostituire \<i> \<app-name> e group-name con il nome dell'app e il nome del gruppo di risorse di Azure.Replace app-name the and group-name> with your app's name and Azure resource group name.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Usa l'URL restituito per distribuire l'app nel passaggio successivo.

### <a name="deploy-the-web-app"></a>Distribuire l'app Web

1. Aprire una finestra del terminale locale nel repository Git locale e aggiungere un server remoto di Azure.Open a local terminal window to your local Git repository, and add an Azure remote. Nel comando seguente \<sostituire url> con l'URL specifico dell'utente di distribuzione o l'URL specifico dell'app ottenuto nel passaggio precedente.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Eseguire il push `git push azure master`nel remoto di Azure con . 
   
1. Nella finestra **Gestione credenziali Git** immettere la password utente per la distribuzione, non la password di accesso di Azure.In the Git Credential Manager window, enter your deployment user [password,](#configure-a-deployment-user)not your Azure sign-in password.
   
1. Esaminare l'output. È possibile visualizzare l'automazione specifica del `npm install` runtime, ad esempio `pip install` MSBuild per ASP.NET, Node.js e Python.You may see runtime-specific automation, such as MSBuild for ASP.NET, for Node.js, and for Python. 
   
1. Passare all'app nel portale di Azure per verificare che il contenuto sia distribuito.

## <a name="deploy-with-azure-pipelines-builds"></a>Distribuzione con le build di Azure PipelinesDeploy with Azure Pipelines builds

Se l'account dispone delle autorizzazioni necessarie, è possibile configurare le pipeline di Azure (anteprima) per abilitare la distribuzione Git locale per l'app. 

- L'account Azure deve disporre delle autorizzazioni per scrivere in Azure Active Directory e creare un servizio. 
  
- L'account Azure deve avere il ruolo Proprietario nella sottoscrizione di Azure.Your Azure account must have the **Owner** role in your Azure subscription.

- È necessario essere un amministratore nel progetto DevOps di Azure che si vuole usare.

Per abilitare la distribuzione Git locale per l'app con le pipeline di Azure (anteprima):To enable local Git deployment for your app with Azure Pipelines (Preview):

1. Nel [portale](https://portal.azure.com)di Azure cercare e selezionare **App Services**. 

1. Selezionare l'app del servizio app di Azure e selezionare **Centro di distribuzione** nel menu a sinistra.
   
1. Nella pagina **Centro di distribuzione** selezionare Git **locale**e quindi **Continua**. 
   
   ![Selezionare Git locale, quindi selezionare Continua](media/app-service-deploy-local-git/portal-enable.png)
   
1. Nella pagina **Crea provider** selezionare Pipeline di **Azure (anteprima)** e quindi selezionare **Continua**. 
   
   ![Select Azure Pipelines (Preview), and then select Continue.](media/app-service-deploy-local-git/pipeline-builds.png)

1. Nella pagina **Configura** configurare una nuova organizzazione DevOps di Azure o specificare un'organizzazione esistente e quindi selezionare **Continua**.
   
   > [!NOTE]
   > Se l'organizzazione DevOps di Azure esistente non è presente nell'elenco, potrebbe essere necessario collegarla alla sottoscrizione di Azure.If your existing Azure DevOps organization isn't listed, you may need to link it to your Azure subscription. Per ulteriori informazioni, vedere Definire la pipeline di [rilascio di CD.](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)
   
1. A seconda del [piano tariffario](https://azure.microsoft.com/pricing/details/app-service/plans/)del piano di servizio app, è possibile che venga visualizzata una pagina **Distribuisci in gestione temporanea.** Scegliere se [abilitare gli slot](deploy-staging-slots.md)di distribuzione , quindi scegliere **Continua**.
   
1. Nella pagina **Riepilogo** rivedere le impostazioni e quindi selezionare **Fine**.
   
1. Quando la pipeline di Azure è pronta, copiare l'URL del repository Git dalla pagina **Centro distribuzione** da usare nel passaggio successivo. 
   
   ![Copiare l'URL del repository Git](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. Nella finestra del terminale locale aggiungere un telecomando di Azure al repository Git locale. Nel comando sostituire \<url> con l'URL del repository Git ottenuto nel passaggio precedente.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Eseguire il push `git push azure master`nel remoto di Azure con . 
   
1. Nella pagina **Gestione credenziali Git** accedere con il nome utente visualstudio.com. Per altri metodi di autenticazione, vedere [Panoramica dell'autenticazione](/vsts/git/auth-overview?view=vsts)di Azure DevOps Services.For other authentication methods, see Azure DevOps Services authentication overview .
   
1. Al termine della distribuzione, visualizzare `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`lo stato di `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`avanzamento della compilazione in e l'avanzamento della distribuzione in .
   
1. Passare all'app nel portale di Azure per verificare che il contenuto sia distribuito.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Risolvere i problemi relativi alla distribuzioneTroubleshoot deployment

Quando si usa Git per pubblicare in un'app del servizio app in Azure, è possibile che vengano visualizzati i messaggi di errore comuni seguenti:

|Message|Causa|Risoluzione
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|L'app non è in esecuzione.|avviare l'app nel portale di Azure. La distribuzione Git non è disponibile quando l'app Web viene arrestata.|
|`Couldn't resolve host 'hostname'`|Le informazioni sull'indirizzo per il telecomando 'azure' non sono corrette.|usare il comando `git remote -v` per elencare tutti i repository remoti, insieme agli URL associati. Verificare che l'URL del repository remoto 'azure' sia corretto. Se necessario, rimuovere e ricreare questo repository remoto usando l'URL corretto.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Non è stato specificato `git push`un ramo durante o `push.default` non `.gitconfig`è stato impostato il valore in .|Eseguire `git push` nuovamente, specificando il `git push azure master`ramo master: .|
|`src refspec [branchname] does not match any.`|Si è tentato di eseguire il push in un ramo diverso da master nel telecomando 'azure'.|Eseguire `git push` nuovamente, specificando il `git push azure master`ramo master: .|
|`RPC failed; result=22, HTTP code = 5xx.`|questo errore può verificarsi se si tenta di eseguire il push di un repository Git di grandi dimensioni tramite HTTPS.|Modificare la configurazione git sul computer `postBuffer` locale per ingrandire. Ad esempio `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|È stata distribuita un'app Node.js con un file _package.json_ che specifica moduli obbligatori aggiuntivi.|Esaminare `npm ERR!` i messaggi di errore prima di questo errore per ulteriori contesto sull'errore. Di seguito sono riportate le cause `npm ERR!` note di questo errore e i messaggi corrispondenti:<br /><br />**File package.json in formato malformato**:`npm ERR! Couldn't read dependencies.`<br /><br />Il modulo nativo non dispone di **una distribuzione binaria per Windows:**<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />o <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Risorse aggiuntive

- [Documentazione del progetto Kudu](https://github.com/projectkudu/kudu/wiki)
- [Distribuzione continua al servizio app di AzureContinuous deployment to Azure App Service](deploy-continuous-deployment.md)
- [Esempio: creare un'app Web e distribuire codice da un repository Git locale (CLI di Azure)Sample: Create a web app and deploy code from a local Git repository (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Esempio: creare un'app Web e distribuire codice da un repository Git locale (PowerShell)Sample: Create a web app and deploy code from a local Git repository (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
