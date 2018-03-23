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
ms.date: 03/05/2018
ms.author: dariagrigoriu;cephalin
ms.openlocfilehash: 4cbe26055bdbf906223a327ab8cf94bebe9e7998
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
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

## <a name="prepare-your-repository"></a>Preparare il repository

Assicurarsi che nel progetto siano presenti i file corretti per la radice del repository.

| Runtime | File della directory radice |
|-|-|
| ASP.NET (solo Windows) | file con estensione _sln_, _csproj_ o _default.aspx_ |
| ASP.NET Core | file con estensione _sln_ o _csproj_ |
| PHP | _index.php_ |
| Ruby (solo Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ o _package.json_ con uno script di avvio |
| Python (solo Windows) | _\*.py_, _requirements.txt_ o _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ o _iisstart.htm_ |
| WebJobs | _\<nome_processo>/run.\<estensione>_ in _App\_Data/jobs/continuous_ (per Processi Web continui) o _App\_Data/jobs/triggered_ (per Processi Web attivati). Per altre informazioni, vedere [Kudu WebJobs documentation](https://github.com/projectkudu/kudu/wiki/WebJobs) (Documentazione di Kudu sui Processi Web) |
| Funzioni | Vedere [Distribuzione continua per Funzioni di Azure](../azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Per personalizzare la distribuzione, è possibile includere un file _.deployment_ nella radice del repository. Per altre informazioni, vedere [Customizing deployments](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) (Personalizzazione delle distribuzioni) e [Custom deployment script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) (Script di distribuzione personalizzata).

> [!NOTE]
> Assicurarsi di `git commit` tutte le modifiche da distribuire.
>
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user.md)]

## <a name="enable-git-for-your-app"></a>Abilitare la distribuzione Git per l'app

Per abilitare la distribuzione Git per un'app del servizio app esistente, eseguire [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_local_git) in Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Per creare un'app abilitata per Git, eseguire invece [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) in Cloud Shell con il parametro `--deployment-local-git`.

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

## <a name="deploy-your-project"></a>Distribuire il progetto

Nella _finestra del terminale locale_ aggiungere un'istanza remota di Azure al repository Git locale. Sostituire _\<url>_ con l'URL del repository Git remoto ottenuto al passaggio [Abilitare la distribuzione Git per l'app](#enable-git-for-you-app).

```bash
git remote add azure <url>
```

Effettuare il push all'istanza remota di Azure per distribuire l'app con il comando seguente. Quando viene richiesta una password, assicurarsi di immettere la password creata nella sezione relativa alla [configurazione di un utente della distribuzione](#configure-a-deployment-user), anziché quella usata per accedere al portale di Azure.

```bash
git push azure master
```

Nell'output potrebbe essere visualizzata l'automazione specifica di runtime, ad esempio MSBuild per ASP.NET, `npm install` per Node.js e `pip install` per Python. 

Al termine della distribuzione, l'app nel portale di Azure dovrebbe includere un record dell'operazione `git push` nella pagina **Opzioni di distribuzione**.

![](./media/app-service-deploy-local-git/deployment_history.png)

Passare all'app per verificare che il contenuto sia stato distribuito.

## <a name="troubleshooting"></a>risoluzione dei problemi

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
