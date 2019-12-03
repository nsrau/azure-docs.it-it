---
title: Distribuisci dal repository git locale
description: Informazioni su come abilitare la distribuzione dell'archivio Git locale nel servizio app di Azure. Uno dei modi più semplici per distribuire il codice dal computer locale.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 06/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 91d5827a08a600c19c24ac0a96a5f4e3e98e22f2
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671773"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Distribuzione git locale al servizio app Azure

Questa guida illustra come distribuire l'app nel [servizio app Azure](overview.md) da un repository git nel computer locale.

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura illustrata in questa guida dettagliata:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Installare Git](https://www.git-scm.com/downloads).
  
- Disporre di un repository git locale con il codice che si desidera distribuire. Per scaricare un repository di esempio, eseguire il comando seguente nella finestra del terminale locale:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Distribuisci con server di compilazione Kudu

Il modo più semplice per abilitare la distribuzione git locale per l'app con il server di compilazione del servizio app Kudu consiste nell'usare Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Configurare un utente della distribuzione

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Ottenere l'URL di distribuzione

Per ottenere l'URL per abilitare la distribuzione git locale per un'app esistente, eseguire [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) nel cloud Shell. Sostituire \<nome app > e \<nome-gruppo > con i nomi dell'app e il relativo gruppo di risorse di Azure.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

In alternativa, per creare una nuova app abilitata per git, eseguire [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) nel cloud Shell con il parametro `--deployment-local-git`. Sostituire \<nome app >, \<nome-gruppo > e \<nome piano > con i nomi per la nuova app git, il gruppo di risorse di Azure e il relativo piano di servizio app Azure.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Entrambi i comandi restituiscono un URL come: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Usare questo URL per distribuire l'app nel passaggio successivo.

Invece di usare questo URL a livello di account, è anche possibile abilitare git locale usando le credenziali a livello di app. App Azure servizio genera automaticamente le credenziali per ogni app. 

Ottenere le credenziali dell'app eseguendo il comando seguente nella Cloud Shell. Sostituire \<nome app > e \<nome-gruppo > con il nome dell'app e il nome del gruppo di risorse di Azure.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Usare l'URL che restituisce per distribuire l'app nel passaggio successivo.

### <a name="deploy-the-web-app"></a>Distribuire l'app Web

1. Aprire una finestra del terminale locale nel repository git locale e aggiungere un'area remota di Azure. Nel comando seguente sostituire \<URL > con l'URL specifico dell'utente di distribuzione o con l'URL specifico dell'app ottenuto nel passaggio precedente.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Eseguire il push in Azure remote con `git push azure master`. 
   
1. Nella finestra **git Credential Manager** immettere la [password dell'utente di distribuzione](#configure-a-deployment-user)e non la password di accesso di Azure.
   
1. Esaminare l'output. È possibile che venga visualizzata un'automazione specifica del runtime, ad esempio MSBuild per ASP.NET, `npm install` per node. js e `pip install` per Python. 
   
1. Passare all'app nell'portale di Azure per verificare che il contenuto sia distribuito.

## <a name="deploy-with-azure-pipelines-builds"></a>Eseguire la distribuzione con Azure Pipelines compilazioni

Se l'account dispone delle autorizzazioni necessarie, è possibile configurare Azure Pipelines (anteprima) per abilitare la distribuzione git locale per l'app. 

- L'account Azure deve avere le autorizzazioni per scrivere in Azure Active Directory e creare un servizio. 
  
- L'account Azure deve avere il ruolo **proprietario** nella sottoscrizione di Azure.

- È necessario essere un amministratore nel progetto Azure DevOps che si vuole usare.

Per abilitare la distribuzione git locale per l'app con Azure Pipelines (anteprima):

1. Passare alla pagina dell'app del servizio app Azure nel [portale di Azure](https://portal.azure.com)e selezionare **centro distribuzione** nel menu a sinistra.
   
1. Nella pagina **centro distribuzione** selezionare **git locale**e quindi fare clic su **continua**. 
   
   ![Selezionare git locale e quindi fare clic su continua.](media/app-service-deploy-local-git/portal-enable.png)
   
1. Nella pagina **provider di compilazione** selezionare **Azure Pipelines (anteprima)** , quindi selezionare **continua**. 
   
   ![Selezionare Azure Pipelines (anteprima), quindi selezionare continua.](media/app-service-deploy-local-git/pipeline-builds.png)

1. Nella pagina **Configura** configurare una nuova organizzazione DevOps di Azure o specificare un'organizzazione esistente, quindi selezionare **continua**.
   
   > [!NOTE]
   > Se l'organizzazione DevOps di Azure esistente non è elencata, potrebbe essere necessario collegarla alla sottoscrizione di Azure. Per altre informazioni, vedere [definire la pipeline di rilascio del CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
   
1. A seconda del piano [tariffario](https://azure.microsoft.com/pricing/details/app-service/plans/)del piano di servizio app, è possibile che venga visualizzata una pagina **Distribuisci in staging** . Scegliere se [abilitare gli slot di distribuzione](deploy-staging-slots.md), quindi selezionare **continua**.
   
1. Nella pagina **Riepilogo** verificare le impostazioni e quindi fare clic su **fine**.
   
1. Quando la pipeline di Azure è pronta, copiare l'URL del repository git dalla pagina **centro distribuzione** da usare nel passaggio successivo. 
   
   ![Copiare l'URL del repository git](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. Nella finestra del terminale locale aggiungere un'area remota di Azure al repository git locale. Nel comando sostituire \<URL > con l'URL del repository git ottenuto nel passaggio precedente.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Eseguire il push in Azure remote con `git push azure master`. 
   
1. Nella pagina **git Credential Manager** accedere con il nome utente di VisualStudio.com. Per altri metodi di autenticazione, vedere [Cenni preliminari sull'autenticazione Azure DevOps Services](/vsts/git/auth-overview?view=vsts).
   
1. Al termine della distribuzione, visualizzare lo stato di avanzamento della compilazione in `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`e lo stato di avanzamento della distribuzione in `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`.
   
1. Passare all'app nell'portale di Azure per verificare che il contenuto sia distribuito.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Risoluzione dei problemi di distribuzione

Quando si usa Git per la pubblicazione in un'app del servizio app in Azure, è possibile che vengano visualizzati i messaggi di errore comuni seguenti:

|Message|Causa|Risoluzione
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|L'app non è in esecuzione.|avviare l'app nel portale di Azure. La distribuzione Git non è disponibile quando l'app Web è arrestata.|
|`Couldn't resolve host 'hostname'`|Le informazioni sull'indirizzo per il controllo remoto ' Azure ' non sono corrette.|Usare il comando `git remote -v` per elencare tutti i computer remoti, insieme all'URL associato. Verificare che l'URL del repository remoto 'azure' sia corretto. Se necessario, rimuovere e ricreare questo repository remoto usando l'URL corretto.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Non è stato specificato un ramo durante `git push`o non è stato impostato il valore di `push.default` in `.gitconfig`.|Eseguire di nuovo `git push`, specificando il ramo master: `git push azure master`.|
|`src refspec [branchname] does not match any.`|Si è tentato di effettuare il push in un ramo diverso dal master nel computer remoto ' Azure '.|Eseguire di nuovo `git push`, specificando il ramo master: `git push azure master`.|
|`RPC failed; result=22, HTTP code = 5xx.`|questo errore può verificarsi se si tenta di eseguire il push di un repository Git di grandi dimensioni tramite HTTPS.|Modificare la configurazione git nel computer locale per rendere il `postBuffer` più grande. Ad esempio: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|È stata distribuita un'app node. js con un file _Package. JSON_ che specifica altri moduli necessari.|Esaminare i messaggi di errore `npm ERR!` prima di questo errore per un maggiore contesto dell'errore. Di seguito sono riportate le cause note di questo errore e i messaggi di `npm ERR!` corrispondenti:<br /><br />**File Package. JSON non valido**: `npm ERR! Couldn't read dependencies.`<br /><br />**Il modulo nativo non dispone di una distribuzione binaria per Windows**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />Oppure <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Risorse aggiuntive

- [Documentazione del progetto Kudu](https://github.com/projectkudu/kudu/wiki)
- [Distribuzione continua nel servizio app Azure](deploy-continuous-deployment.md)
- [Esempio: creare un'app Web e distribuire il codice da un repository git locale (interfaccia della riga di comando di Azure)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Esempio: creare un'app Web e distribuire il codice da un repository git locale (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
