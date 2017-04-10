---
title: Creare un&quot;applicazione Node.js in un&quot;app Web | Microsoft Docs
description: Distribuire la prima app Node.js Hello World in un&quot;app Web del servizio app in pochi minuti.
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/28/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 22966659db3951316dca11fd555e07a183114062
ms.lasthandoff: 04/04/2017

---
# <a name="create-a-nodejs-application-on-web-app"></a>Creare un'applicazione Node.js in un'app Web

Questa esercitazione introduttiva illustra come sviluppare e distribuire un'app Node.js in Azure. L'app verrà eseguita usando un servizio app di Azure basato su Linux, in cui verrà creata e configurata una nuova app Web usando l'interfaccia della riga di comando di Azure. Si userà quindi Git per distribuire l'app Node.js in Azure.

![Hello World nel browser](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

È possibile eseguire queste procedure con un computer Mac, Windows o Linux. Per completare tutte le procedure descritte di seguito saranno sufficienti circa 5 minuti.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di eseguire l'esempio, installare in locale questi prerequisiti:

1. [Scaricare e installare Git](https://git-scm.com/)
1. [Scaricare e installare Node.js e NPM](https://nodejs.org/)
1. Scaricare e installare l'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Scaricare l'esempio

Clonare il repository dell'app di esempio Hello World nel computer locale.

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

> [!TIP]
> In alternativa, è possibile [scaricare l'esempio](https://github.com/Azure-Samples/nodejs-docs-hello-world/archive/master.zip) come file ZIP ed estrarre i file.

Passare alla directory contenente il codice di esempio.

```bash
cd nodejs-docs-hello-world
```

## <a name="run-the-app-locally"></a>Eseguire l'app in locale

Per eseguire l'applicazione in locale, aprire una finestra del terminale e usare lo script `npm start` per l'esempio per avviare il server HTTP Node.js predefinito.

```bash
npm start
```

Aprire un Web browser e passare all'esempio.

```bash
http://localhost:1337
```

Nella pagina verrà visualizzato il messaggio **Hello World** dell'app di esempio.

![Hello World in localhost nel browser](media/app-service-web-get-started-nodejs-poc/localhost-hello-world-in-browser.png)

Nella finestra del terminale premere **CTRL+C** per uscire dal server Web.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Verrà ora usata l'interfaccia della riga di comando di Azure 2.0 in una finestra del terminale per creare le risorse necessarie per ospitare l'app Node.js in Azure. Accedere alla sottoscrizione di Azure con il comando [az login](/cli/azure/#login) e seguire le istruzioni visualizzate.

```azurecli
az login
```

## <a name="configure-a-deployment-user"></a>Configurare un utente della distribuzione

Per la distribuzione con FTP e l'istanza Git locale, è necessario che nel server sia configurato un utente della distribuzione. La creazione di un utente della distribuzione è una configurazione una tantum. Prendere nota del nome e della password perché verranno usati in un passaggio successivo.

> [!NOTE]
> Un utente della distribuzione è necessario per la distribuzione con FTP e l'istanza Git locale in un'app Web.
> `username` e `password` sono a livello di account e sono quindi diversi dalle credenziali della sottoscrizione di Azure. Queste credenziali devono essere create una sola volta.
>

Usare il comando [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) per creare le credenziali a livello di account.

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con [az group create](/cli/azure/group#create). Un gruppo di risorse di Azure è un contenitore logico in cui vengono distribuite e gestite risorse di Azure come app Web, database e account di archiviazione.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-an-azure-app-service"></a>Creare un servizio app di Azure

Creare un piano di servizio app basato su Linux con il comando [az appservice plan create](/cli/azure/appservice/plan#create).

> [!NOTE]
> Un piano di servizio app rappresenta la raccolta delle risorse fisiche usate per ospitare le app. Tutte le applicazioni assegnate a un piano di servizio app condividono le risorse definite dal piano, in modo da consentire un risparmio sui costi quando si ospitano più app.
>
> I piani di servizio app definiscono:
> * Area (Europa settentrionale, Stati Uniti orientali, Asia sud-orientale)
> * Dimensioni delle istanze (Small, Medium, Large)
> * Numero di scala (una, due o tre istanze e così via)
> * SKU (Gratuito, Condiviso, Basic, Standard, Premium)
>

L'esempio seguente crea un piano di servizio app denominato `quickStartPlan` in ruoli di lavoro Linux, con il piano tariffario **Standard**.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku S1 --is-linux
```

Al termine della creazione del piano di servizio app, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente.

```json
{
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
    "kind": "linux",
    "location": "West Europe",
    "sku": {
    "capacity": 1,
    "family": "S",
    "name": "S1",
    "tier": "Standard"
    },
    "status": "Ready",
    "type": "Microsoft.Web/serverfarms"
}
```

## <a name="create-a-web-app"></a>Creare un'app Web

Ora che è stato creato il piano di servizio app `quickStartPlan`, creare un'app Web in tale piano. L'app Web offre uno spazio di hosting per la distribuzione del codice e fornisce un URL per visualizzare l'applicazione distribuita. Usare il comando [az appservice web create](/cli/azure/appservice/web#create) per creare l'app Web.

Nel comando seguente sostituire il segnaposto <app_name> con il nome univoco della propria app. Dato che verrà usato come sito DNS predefinito per l'app Web, è necessario che il nome sia univoco tra tutte le app in Azure. In un secondo momento è possibile eseguire il mapping di qualsiasi voce DNS personalizzata all'app Web prima di esporla agli utenti.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

Al termine della creazione dell'app Web, l'interfaccia della riga di comando di Azure visualizza informazioni simili all'esempio seguente.

```json
{
    "clientAffinityEnabled": true,
    "defaultHostName": "<app_name>.azurewebsites.net",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>",
    "isDefaultContainer": null,
    "kind": "app",
    "location": "West Europe",
    "name": "<app_name>",
    "repositorySiteName": "<app_name>",
    "reserved": true,
    "resourceGroup": "myResourceGroup",
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
    "state": "Running",
    "type": "Microsoft.Web/sites",
}
```

Passare al sito per visualizzare l'app Web appena creata.

```bash
http://<app_name>.azurewebsites.net
```

![App Web creata nel servizio app](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

È stata così creata una nuova app Web vuota in Azure. Ora si configurerà l'app Web per usare Node.js e si distribuirà l'app nell'app Web.

## <a name="configure-to-use-nodejs"></a>Configurare l'uso di Node.js

Usare il comando [az appservice web config update](/cli/azure/app-service/web/config#update) per configurare l'app Web per l'uso di Node.js versione `6.9.3`.

> [!TIP]
> Impostando la versione di Node.js in questo modo verrà usato un contenitore predefinito fornito dalla piattaforma. Per usare un contenitore personalizzato, vedere le informazioni di riferimento sull'interfaccia della riga di comando relativamente al comando [az appservice web config container update](/cli/azure/appservice/web/config/container#update).

```azurecli
az appservice web config update --linux-fx-version "NODE|6.9.3" --startup-file process.json --name <app_name> --resource-group myResourceGroup
```

## <a name="configure-local-git-deployment"></a>Configurare la distribuzione con l'istanza Git locale

È possibile eseguire la distribuzione nell'app Web in diversi modi, tra cui FTP, istanza Git locale, GitHub, Visual Studio Team Services e Bitbucket.

Usare il comando [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) per configurare l'accesso dell'istanza Git locale all'app Web.

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

Copiare l'output dal terminale, perché verrà usato nel passaggio successivo.

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

## <a name="push-to-azure-from-git"></a>Effettuare il push in Azure da Git

Aggiungere un'istanza remota di Azure al repository Git locale.

```bash
git remote add azure <paste-previous-command-output-here>
```

Effettuare il push all'istanza remota di Azure per distribuire l'applicazione. Verrà richiesta la password specificata in precedenza nel corso della creazione dell'utente della distribuzione.

```azurecli
git push azure master
```

Durante la distribuzione, il servizio app di Azure comunicherà lo stato di avanzamento con Git.

```bash
Counting objects: 23, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (21/21), done.
Writing objects: 100% (23/23), 3.71 KiB | 0 bytes/s, done.
Total 23 (delta 8), reused 7 (delta 1)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'bf114df591'.
remote: Generating deployment script.
remote: Generating deployment script for node.js Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling node.js deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.js'
remote: Copying file: 'package.json'
remote: Copying file: 'process.json'
remote: Deleting file: 'hostingstart.html'
remote: Ignoring: .git
remote: Using start-up script index.js from package.json.
remote: Node.js versions available on the platform are: 4.4.7, 4.5.0, 6.2.2, 6.6.0, 6.9.1.
remote: Selected node.js version 6.9.1. Use package.json file to choose a different version.
remote: Selected npm version 3.10.8
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net:443/<app_name>.git
 * [new branch]      master -> master
```

## <a name="browse-to-the-app"></a>Passare all'app

Passare all'applicazione distribuita con il Web browser.

```bash
http://<app_name>.azurewebsites.net
```

La pagina in cui viene visualizzato il messaggio Hello World viene ora eseguita usando il codice Node.js eseguito come app Web del servizio app di Azure.

## <a name="updating-and-deploying-the-code"></a>Aggiornamento e distribuzione del codice

Usando un editor di testo locale, aprire il file `index.js` nell'app Node.js e apportare una piccola modifica al testo nella chiamata a `response.end`:

```nodejs
response.end("Hello Azure!");
```

Eseguire il commit delle modifiche in Git e quindi effettuare il push delle modifiche al codice in Azure.

```bash
git commit -am "updated output"
git push azure master
```

Al termine della distribuzione, tornare alla finestra del browser aperta nel passaggio "Passare all'app" e fare clic su Aggiorna.

![Hello World nel browser](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Gestire la nuova app Web di Azure

Passare al portale di Azure per esaminare l'app Web appena creata.

A tale scopo, accedere a [https://portal.azure.com](https://portal.azure.com).

Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Web di Azure.

![Passare all'app Web di Azure nel portale](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

Si accede così al _pannello_, ovvero una pagina del portale visualizzata in orizzontale, dell'app Web.

Per impostazione predefinita, nel pannello dell'app Web viene aperta la pagina **Panoramica**, che offre una visualizzazione dello stato dell'app. In questa pagina è anche possibile eseguire attività di gestione di base come esplorare, arrestare, avviare, riavviare ed eliminare. Le schede sul lato sinistro del pannello mostrano le diverse pagine di configurazione che è possibile aprire.

![Pannello del servizio app nel portale di Azure](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

Queste schede del pannello mostrano le numerose utili funzionalità che è possibile aggiungere all'app Web. Nell'elenco seguente sono riportate solo alcune delle possibilità:

* Eseguire il mapping di un nome DNS personalizzato
* Associare un certificato SSL personalizzato
* Configurare la distribuzione continua
* Aumentare le prestazioni e il numero di istanze
* Aggiungere l'autenticazione utente

**Congratulazioni.** La distribuzione della prima app Node.js nel servizio app è stata completata.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

Esplorare gli [script dell'interfaccia della riga di comando per le app Web](app-service-cli-samples.md) già creati.
