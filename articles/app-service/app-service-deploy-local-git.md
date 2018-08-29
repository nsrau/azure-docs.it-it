---
title: Distribuzione dell'archivio Git locale nel servizio app di Azure
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
ms.date: 06/05/2018
ms.author: dariagrigoriu;cephalin
ms.openlocfilehash: f461a9a7cc900ce5f8fdba7b255417b1790d3f4d
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2018
ms.locfileid: "42146252"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Distribuzione dell'archivio Git locale nel servizio app di Azure

Questa guida dettagliata illustra come distribuire il codice nel [servizio app di Azure](app-service-web-overview.md) da un repository Git nel computer locale.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura illustrata in questa guida dettagliata:

* [Installare Git](http://www.git-scm.com/downloads).
* Mantenere un repository Git locale con il codice da distribuire.

Per proseguire usando un repository di esempio, eseguire il comando seguente nella finestra terminale locale:

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git-with-kudu-builds"></a>Distribuire dall'archivio Git locale con le compilazioni Kudu

Il modo più semplice per abilitare la distribuzione dell'archivio Git locale per l'app con il server di compilazione Kudu è usare Cloud Shell.

### <a name="create-a-deployment-user"></a>Creare un utente di distribuzione

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Abilitare l'archivio Git locale con Kudu

Per abilitare la distribuzione dell'archivio Git locale per l'app con il server di compilazione Kudu, eseguire [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) in Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Per creare un'app abilitata per Git, eseguire invece [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) in Cloud Shell con il parametro `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

L'output del comando `az webapp create` dovrebbe essere simile al seguente:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>Distribuire il progetto

Nella _finestra del terminale locale_ aggiungere un'istanza remota di Azure al repository Git locale. Sostituire _\<url>_ con l'URL del repository Git remoto ottenuto al passaggio [Abilitare la distribuzione Git per l'app](#enable-git-for-you-app).

```bash
git remote add azure <url>
```

Effettuare il push all'istanza remota di Azure per distribuire l'app con il comando seguente. Quando viene richiesta una password, assicurarsi di immettere la password creata nella sezione relativa alla [configurazione di un utente della distribuzione](#configure-a-deployment-user), anziché quella usata per accedere al portale di Azure.

```bash
git push azure master
```

Nell'output potrebbe essere visualizzata l'automazione specifica di runtime, ad esempio MSBuild per ASP.NET, `npm install` per Node.js e `pip install` per Python. 

Passare all'app per verificare che il contenuto sia stato distribuito.

## <a name="deploy-from-local-git-with-vsts-builds"></a>Distribuire dall'archivio Git locale con le compilazioni VSTS

> [!NOTE]
> Perché il servizio app crei la compilazione e le definizioni di versione necessarie nell'account di VSTS, l'account di Azure deve avere il ruolo di **Proprietario** nella sottoscrizione di Azure.
>

Per abilitare la distribuzione dell'archivio Git locale per l'app con il server di compilazione Kudu, accedere all'app dal [portale di Azure](https://portal.azure.com).

Nel riquadro di spostamento a sinistra della pagina dell'app fare clic su **Centro distribuzione** > **Archivio Git locale** > **Continua**. 

![](media/app-service-deploy-local-git/portal-enable.png)

Fare clic su **Distribuzione continua di VSTS** > **Continua**.

![](media/app-service-deploy-local-git/vsts-build-server.png)

Nella pagina **Configura** configurare un nuovo account VSTS o specificare un account esistente. Al termine dell'operazione, fare clic su **Continua**.

> [!NOTE]
> Se si vuole usare un account VSTS esistente che non è elencato, è necessario [collegare l'account VSTS alla sottoscrizione di Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Nella pagina **Test** scegliere se abilitare i test di carico e fare clic su **Continua**.

In base al [piano tariffario](https://azure.microsoft.com/pricing/details/app-service/plans/) del piano di servizio app, è anche possibile visualizzare la pagina **Distribuisci nell'ambiente di staging**. Scegliere se abilitare gli slot di distribuzione e fare clic su **Continua**.

Nella pagina **Riepilogo** verificare le opzioni e fare clic su **Fine**.

Sono necessari alcuni minuti prima che l'account VSTS sia pronto. Quando è pronto, copiare l'URL dell'archivio Git nel centro di distribuzione.

![](media/app-service-deploy-local-git/vsts-repo-ready.png)

Nella _finestra del terminale locale_ aggiungere un'istanza remota di Azure al repository Git locale. Sostituire _\<url>_ con l'URL ottenuto con l'ultimo passaggio.

```bash
git remote add vsts <url>
```

Effettuare il push all'istanza remota di Azure per distribuire l'app con il comando seguente. Quando richiesto da Git Credential Manager, accedere con l'account utente di visualstudio.com. Per altri metodi di autenticazione, vedere [VSTS authentication overview](/vsts/git/auth-overview?view=vsts) (Panoramica dell'autenticazione VSTS).

```bash
git push vsts master
```

Al termine della distribuzione, è possibile trovare lo stato di avanzamento della compilazione in `https://<vsts_account>.visualstudio.com/<project_name>/_build` e lo stato di avanzamento della distribuzione in `https://<vsts_account>.visualstudio.com/<project_name>/_release`.

Passare all'app per verificare che il contenuto sia stato distribuito.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshooting-kudu-deployment"></a>Risoluzione dei problemi relativi alla distribuzione Kudu

Di seguito sono riportati gli errori o i problemi comuni che si verificano durante l'uso di Git per la pubblicazione in un'app del servizio app in Azure:

---
**Sintomo**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**Causa**: questo errore può verificarsi se l'app non è in esecuzione.

**Soluzione**: avviare l'app nel portale di Azure. Se l'app Web viene arrestata, la distribuzione Git non sarà disponibile.

---
**Sintomo**: `Couldn't resolve host 'hostname'`

**Causa**: questo errore può verificarsi se le informazioni sull'indirizzo immesse durante la creazione del repository remoto "azure" non sono corrette.

**Soluzione**: usare il comando `git remote -v` per elencare tutti i repository remoti, insieme agli URL associati. Verificare che l'URL del repository remoto 'azure' sia corretto. Se necessario, rimuovere e ricreare questo repository remoto usando l'URL corretto.

---
**Sintomo**: `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**Causa**: questo errore può verificarsi se non si specifica un ramo durante l'operazione `git push` oppure se non è stato impostato il valore `push.default` in `.gitconfig`.

**Soluzione**: eseguire nuovamente l'operazione `git push`, specificando il ramo master. Ad esempio: 

```bash
git push azure master
```

---
**Sintomo**: `src refspec [branchname] does not match any.`

**Causa**: questo errore può verificarsi se si tenta di eseguire il push in un ramo diverso dal master nel repository remoto "azure".

**Soluzione**: eseguire nuovamente l'operazione `git push`, specificando il ramo master. Ad esempio: 

```bash
git push azure master
```

---
**Sintomo**: `RPC failed; result=22, HTTP code = 5xx.`

**Causa**: questo errore può verificarsi se si tenta di eseguire il push di un repository Git di grandi dimensioni tramite HTTPS.

**Soluzione**: modificare la configurazione Git nel computer locale per ingrandire il postBuffer

```bash
git config --global http.postBuffer 524288000
```

---
**Sintomo**: `Error - Changes committed to remote repository but your web app not updated.`

**Causa**: questo errore può verificarsi se si distribuisce un'app Node.js contenente un file _package.json_ che specifica altri moduli necessari.

**Soluzione**: i messaggi aggiuntivi contenenti "npm ERR!" dovrebbero essere registrati prima di questo errore e possono fornire contesto aggiuntivo sul problema. Di seguito sono riportate le cause note di questo errore e del corrispondente messaggio 'npm ERR!' messaggio:

* **File package.json in formato non corretto**: npm ERR! Non è stato possibile leggere le dipendenze.
* **Modulo nativo senza una distribuzione binaria per Windows**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      Oppure
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>Risorse aggiuntive

* [Documentazione del progetto Kudu](https://github.com/projectkudu/kudu/wiki)
* [Distribuzione continua nel servizio app di Azure](app-service-continuous-deployment.md)
* [Esempio: creare un'app Web e distribuire il codice da un repository Git locale (interfaccia della riga di comando di Azure)](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
* [Esempio: creare un'app Web e distribuire il codice da un repository Git locale (PowerShell)](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
