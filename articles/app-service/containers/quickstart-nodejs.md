---
title: Creare un Node.js nel Servizio app di Azure in Linux | Microsoft Docs
description: Distribuire la prima app Hello World Node.js in un Servizio app di Azure in Linux in pochi minuti.
services: app-service\web
documentationcenter: ''
author: msangapu
manager: cfowler
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/07/2017
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: eb1c769e034f37d05de63896f65290db79103637
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293905"
---
# <a name="create-a-nodejs-web-app-in-azure-app-service-on-linux"></a>Creare un'app Web Node.js nel Servizio app di Azure in Linux

> [!NOTE]
> Questo articolo consente di distribuire un'app nel servizio app in Linux. Per la distribuzione nel servizio app in _Windows_, vedere [Creare un'app Web Node.js in Azure](../app-service-web-get-started-nodejs.md).
>

Il [Servizio app in Linux](app-service-linux-intro.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione basato sul sistema operativo Linux. Questa guida introduttiva illustra come distribuire un'app Node.js nel Servizio app in Linux usando [Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview).

Questa guida introduttiva verrà completata in Cloud Shell, ma gli stessi comandi possono essere eseguiti anche in locale con l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

![App di esempio in esecuzione in Azure](media/quickstart-nodejs/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="install-web-app-extension-for-cloud-shell"></a>Installare l'estensione di app Web per Cloud Shell

Per completare questa guida introduttiva, è necessario aggiungere l'[estensione di app Web az](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az-extension-add). Se l'estensione è già installata, occorre aggiornarla all'ultima versione. Per aggiornare l'estensione dell'app Web, digitare `az extension update -n webapp`.

Per installare l'estensione dell'app Web, eseguire il comando seguente:

```bash
az extension add -n webapp
```

Una volta installata l'estensione, Cloud Shell mostra le informazioni come nell'esempio seguente:

```bash
The installed extension 'webapp' is in preview.
```

## <a name="download-the-sample"></a>Scaricare l'esempio

In Cloud Shell creare una directory quickstart e passare ad essa.

```bash
mkdir quickstart

cd quickstart
```

Eseguire quindi il comando seguente per clonare il repository dell'app di esempio nella directory quickstart.

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

Durante l'esecuzione, il comando visualizza informazioni simili all'esempio seguente:

```bash
Cloning into 'nodejs-docs-hello-world'...
remote: Counting objects: 40, done.
remote: Total 40 (delta 0), reused 0 (delta 0), pack-reused 40
Unpacking objects: 100% (40/40), done.
Checking connectivity... done.
````

## <a name="create-a-web-app"></a>Creare un'app Web

Passare alla directory contenente il codice di esempio ed eseguire il comando `az webapp up`.

Nell'esempio seguente sostituire <app_name> con un nome di app univoco.

```bash
cd nodejs-docs-hello-world

az webapp up -n <app_name>
```

L'esecuzione del comando può richiedere alcuni minuti. Durante l'esecuzione, il comando visualizza informazioni simili all'esempio seguente:

```json
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app_name>' ....
Webapp creation complete
Updating app settings to enable build after deployment
Creating zip with contents of dir /home/username/quickstart/nodejs-docs-hello-world ...
Preparing to deploy and build contents to app.
Fetching changes.

Generating deployment script.
Generating deployment script.
Generating deployment script.
Running deployment command...
Running deployment command...
Running deployment command...
Deployment successful.
All done.
{
  "app_url": "https://<app_name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app_name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "STANDARD",
  "src_path": "/home/username/quickstart/nodejs-docs-hello-world ",
  "version_detected": "6.9",
  "version_to_create": "node|6.9"
}
```

Il comando `az webapp up` esegue le azioni seguenti:

- Crea un gruppo di risorse predefinito.

- Crea un piano di servizio app predefinito.

- Crea un'app con il nome specificato.

- [Distribuisce con zipdeploy](https://docs.microsoft.com/en-us/azure/app-service/app-service-deploy-zip) i file dalla directory di lavoro corrente all'app Web.

## <a name="browse-to-the-app"></a>Passare all'app

Passare all'applicazione distribuita con il Web browser. Sostituire <app name> con il nome dell'app Web.

```bash
http://<app_name>.azurewebsites.net
```

Il codice di esempio Node.js è in esecuzione in un'app Web con immagine incorporata.

![App di esempio in esecuzione in Azure](media/quickstart-nodejs/hello-world-in-browser.png)

**Congratulazioni** La distribuzione della prima app Node.js nel Servizio app in Linux è stata completata.

## <a name="update-and-redeploy-the-code"></a>Aggiornare e ridistribuire il codice

In Cloud Shell digitare `nano index.js` per aprire l'editor di testo Nano.

![File index.js di Nano](media/quickstart-nodejs/nano-indexjs.png)

 Apportare una piccola modifica al testo nella chiamata a `response.end`:

```nodejs
response.end("Hello Azure!");
```

Salvare le modifiche e uscire da Nano. Usare il comando `^O` per salvare e `^X` per uscire.

A questo punto occorre ridistribuire l'app. Sostituire `<app_name>` con l'app Web.

```bash
az webapp up -n <app_name>
```

Al termine della distribuzione, tornare alla finestra del browser aperta nel passaggio **Passare all'app** e aggiornare la pagina.

![App di esempio aggiornata in esecuzione in Azure](media/quickstart-nodejs/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Gestire la nuova app Web di Azure

Accedere al <a href="https://portal.azure.com" target="_blank">portale di Azure</a> per gestire l'app Web creata.

Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Web di Azure.

![Passare all'app Web di Azure nel portale](./media/quickstart-nodejs/nodejs-docs-hello-world-app-service-list.png)

Verrà visualizzata la pagina di panoramica dell'app Web. Qui è possibile completare attività di gestione di base come l'esplorazione, l'arresto, l'avvio, il riavvio e l'eliminazione dell'app.

![Pagina del servizio app nel portale di Azure](media/quickstart-nodejs/nodejs-docs-hello-world-app-service-detail.png)

Il menu a sinistra fornisce varie pagine per la configurazione dell'app.

## <a name="clean-up-resources"></a>Pulire le risorse

Nei passaggi precedenti sono state create risorse di Azure in un gruppo di risorse. Se non si prevede di aver bisogno di queste risorse in futuro, eliminare il gruppo di risorse da Cloud Shell. Se è stata modificata l'area, aggiornare il nome del gruppo di risorse `appsvc_rg_Linux_CentralUS` sostituendolo con il gruppo di risorse specifico dell'app.

```azurecli-interactive
az group delete --name appsvc_rg_Linux_CentralUS
```

L'esecuzione del comando può richiedere un minuto.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Node.js con MongoDB](tutorial-nodejs-mongodb-app.md)
