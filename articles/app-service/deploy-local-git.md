---
title: Distribuire da repository Git locale - Servizio app di Azure
description: Informazioni su come abilitare la distribuzione dell'archivio Git locale nel servizio app di Azure.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: dariagrigoriu;cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: fa961e43408fed014be2266c14c7972d39435b97
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836868"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Distribuzione Git locale al servizio App di Azure

Questa guida illustra come distribuire l'app per [servizio App di Azure](overview.md) da un repository Git nel computer locale.

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura illustrata in questa guida dettagliata:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Installare Git](https://www.git-scm.com/downloads).
  
- Avere un repository Git locale con il codice da distribuire. Per scaricare un repository di esempio, eseguire il comando seguente nella finestra del terminale locale:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Eseguire la distribuzione con server di compilazione Kudu

Il modo più semplice per abilitare la distribuzione Git locale per l'app con il server di compilazione Kudu del servizio di App consiste nell'usare Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Configurare un utente della distribuzione

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Ottenere l'URL di distribuzione

Per ottenere l'URL per abilitare la distribuzione Git locale per un'app esistente, eseguire [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) in Cloud Shell. Sostituire \<app-name > e \<gruppo-name > con i nomi delle app e il relativo gruppo di risorse di Azure.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

In alternativa, per creare una nuova app abilitata per Git, eseguire [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) in Cloud Shell con il `--deployment-local-git` parametro. Sostituire \<app-name >, \<-nome del gruppo >, e \<piano-name > con i nomi per la nuova app per Git, il gruppo di risorse di Azure e il piano di servizio App di Azure.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Entrambi i comandi restituisce un URL come: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Usare questo URL per distribuire l'app nel passaggio successivo.

Invece di usare questo URL a livello di account, è anche possibile abilitare archivio Git locale usando le credenziali a livello di app. Servizio App di Azure genera automaticamente queste credenziali per ogni app. 

Ottenere le credenziali dell'app eseguendo il comando seguente in Cloud Shell. Sostituire \<app-name > e \<gruppo-name > con nome e il nome del gruppo di risorse di Azure dell'app.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Usare l'URL che restituisce per distribuire l'app nel passaggio successivo.

### <a name="deploy-the-web-app"></a>Distribuire l'app Web

1. Aprire una finestra del terminale locale al repository Git locale e aggiungere un'istanza remota di Azure. Nel comando seguente, sostituire \<url > con l'URL di distribuzione specifici dell'utente o un URL specifico dell'app ottenuto nel passaggio precedente.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Eseguire il push di Azure remoto con `git push azure master`. 
   
1. Nel **Git Credential Manager** finestra, immettere il [password utente distribuzione](#configure-a-deployment-user), non Azure accesso tramite password.
   
1. Esaminare l'output. Automazione specifica di runtime, ad esempio MSBuild per ASP.NET, potrebbe essere visualizzato `npm install` per Node. js, e `pip install` per Python. 
   
1. Passare all'app nel portale di Azure per verificare che il contenuto venga distribuito.

## <a name="deploy-with-azure-pipelines-builds"></a>Distribuire con le compilazioni di pipeline di Azure

Se l'account ha le autorizzazioni necessarie, è possibile impostare le pipeline di Azure (anteprima) per abilitare la distribuzione Git locale per l'app. 

- L'account di Azure deve avere le autorizzazioni di scrittura di Azure Active Directory e creare un servizio. 
  
- L'account di Azure deve avere il **proprietario** ruolo nella sottoscrizione di Azure.

- È necessario essere un amministratore del progetto DevOps di Azure da usare.

Per abilitare la distribuzione Git locale per l'app con le pipeline di Azure (anteprima):

1. Passare alla pagina dell'app di servizio App di Azure nel [portale di Azure](https://portal.azure.com)e selezionare **Deployment Center** nel menu a sinistra.
   
1. Nel **Deployment Center** pagina, selezionare **Git locale**e quindi selezionare **continua**. 
   
   ![Selezionare l'istanza Git locale, quindi continua](media/app-service-deploy-local-git/portal-enable.png)
   
1. Nel **provider di compilazione** pagina, selezionare **pipeline di Azure (anteprima)** e quindi selezionare **continua**. 
   
   ![Selezionare la pipeline di Azure (anteprima) e quindi selezionare continua.](media/app-service-deploy-local-git/pipeline-builds.png)

1. Nel **Configure** pagina, configurare una nuova organizzazione, Azure DevOps o specificare un'organizzazione esistente e quindi selezionare **continua**.
   
   > [!NOTE]
   > Se l'organizzazione di Azure DevOps esistente non è elencato, devi collegarla alla sottoscrizione di Azure. Per altre informazioni, vedere [definiscono la pipeline di versione CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
   
1. A seconda del piano di servizio App [piano tariffario](https://azure.microsoft.com/pricing/details/app-service/plans/), potrebbe essere visualizzato un **Distribuisci in staging** pagina. Scegliere se si desidera [Abilita slot di distribuzione](deploy-staging-slots.md), quindi selezionare **continua**.
   
1. Nel **Summary** pagina, esaminare le impostazioni e quindi selezionare **fine**.
   
1. Quando la Pipeline di Azure è pronta, copiare l'URL del repository Git dal **Deployment Center** pagina da usare nel passaggio successivo. 
   
   ![Copiare l'URL del repository Git](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. Nella finestra del terminale locale aggiungere un'istanza remota di Azure al repository Git locale. Nel comando sostituire \<url > con l'URL del repository Git che è stato ottenuto nel passaggio precedente.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Eseguire il push di Azure remoto con `git push azure master`. 
   
1. Nel **Git Credential Manager** pagina, accedere con il nome utente visualstudio.com. Per altri metodi di autenticazione, vedere [panoramica dell'autenticazione servizi di Azure DevOps](/vsts/git/auth-overview?view=vsts).
   
1. Una volta terminata la distribuzione, visualizzare lo stato della compilazione alla `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`e l'avanzamento della distribuzione in `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`.
   
1. Passare all'app nel portale di Azure per verificare che il contenuto venga distribuito.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Risolvere i problemi di distribuzione

Si possono vedere i seguenti messaggi di errore comuni quando si usa Git per pubblicare un'app di servizio App in Azure:

|Messaggio|Causa|Risoluzione
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|L'app non è attivo e in esecuzione.|avviare l'app nel portale di Azure. Distribuzione GIT non è disponibile quando l'app web è arrestata.|
|`Couldn't resolve host 'hostname'`|Le informazioni sull'indirizzo per il remoto 'azure' non è corretto.|usare il comando `git remote -v` per elencare tutti i repository remoti, insieme agli URL associati. Verificare che l'URL del repository remoto 'azure' sia corretto. Se necessario, rimuovere e ricreare questo repository remoto usando l'URL corretto.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Non è stato specificato un ramo durante `git push`, o non è stata impostata la `push.default` valore `.gitconfig`.|Eseguire `git push` anche in questo caso, specificando il ramo master: `git push azure master`.|
|`src refspec [branchname] does not match any.`|Si è provato a push in un ramo diverso dal master nel repository 'azure' remoto.|Eseguire `git push` anche in questo caso, specificando il ramo master: `git push azure master`.|
|`RPC failed; result=22, HTTP code = 5xx.`|questo errore può verificarsi se si tenta di eseguire il push di un repository Git di grandi dimensioni tramite HTTPS.|Modificare la configurazione di git nel computer locale per rendere il `postBuffer` più grande. Ad esempio: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|È stata distribuita un'app Node. js con un _package. JSON_ file che specifica altri moduli necessari.|Esaminare il `npm ERR!` i messaggi di errore prima di questo errore per altre informazioni sul contesto sull'errore. Di seguito sono le cause note di questo errore e il corrispondente `npm ERR!` messaggi:<br /><br />**File package. JSON in formato non corretto**: `npm ERR! Couldn't read dependencies.`<br /><br />**Modulo nativo non ha una distribuzione binaria per Windows**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />oppure <br />`npm ERR! [modulename@version] preinstallare: \make || gmake\`|

## <a name="additional-resources"></a>Risorse aggiuntive

- [Documentazione del progetto Kudu](https://github.com/projectkudu/kudu/wiki)
- [Distribuzione continua in servizio App di Azure](deploy-continuous-deployment.md)
- [Esempio: Creare un'app web e distribuire il codice da un repository Git locale (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Esempio: Creare un'app web e distribuire il codice da un repository Git locale (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
