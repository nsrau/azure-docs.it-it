---
title: Creare un'app Web Node.js in Azure | Microsoft Docs
description: Distribuire la prima app Node.js Hello World in un'app Web del servizio app di Azure in pochi minuti.
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/27/2018
ms.author: cephalin;msangapu
ms.custom: mvc, devcenter
ms.openlocfilehash: 05dd53fdfda5446cf848a7b8503a09bc5e5c2d20
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433464"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Creare un'app Web Node.js in Azure

> [!NOTE]
> Questo articolo consente di distribuire un'app nel servizio app in Windows. Per la distribuzione nel servizio app in _Linux_, vedere [Creare un'app Web Node.js nel Servizio app di Azure in Linux](./containers/quickstart-nodejs.md).
>

Le [app Web di Azure](app-service-web-overview.md) forniscono un servizio di hosting Web ad alta scalabilità e con funzioni di auto-correzione.  Questa guida introduttiva illustra come distribuire un'app Node.js in un'app Web di Azure. Si creerà l'app Web usando l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) e si userà ZipDeploy per distribuire il codice Node.js di esempio nell'app Web.

![App di esempio in esecuzione in Azure](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

È possibile eseguire queste procedure con un computer Mac, Windows o Linux. Una volta installati i prerequisiti, sono necessari circa cinque minuti per completare la procedura.   

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva:

* <a href="https://nodejs.org/" target="_blank">Installare Node.js e NPM</a>

## <a name="download-the-sample"></a>Scaricare l'esempio

Scaricare il progetto di esempio di Node.js da [https://github.com/Azure-Samples/nodejs-docs-hello-world/archive/master.zip](https://github.com/Azure-Samples/nodejs-docs-hello-world/archive/master.zip) ed estrarre l'archivio ZIP.

In una finestra del terminale passare alla directory radice del progetto Node.js di esempio (quello contenente _index.js_).

> [!NOTE]
> Non è necessario usare l'app di esempio, se si desidera, è possibile usare il proprio codice Node. Tenere presente, tuttavia, che la porta per l'app verrà impostata in fase di esecuzione da Azure, ed è disponibile come `process.env.PORT`. Se si usa express, assicurarsi di avere un controllo all'avvio (`app.listen`) per `process.env.PORT || 3000`. Se si non esegue questa operazione e la porta non corrisponde a ciò che viene impostata in fase di esecuzione da Azure, verrà visualizzato un messaggio `Service Unavailable`. 

## <a name="run-the-app-locally"></a>Eseguire l'app in locale

Eseguire l'applicazione in locale, in modo da verificare l'aspetto che assumerà dopo la distribuzione in Azure. Aprire una finestra del terminale e usare lo script `npm start` per avviare il server HTTP Node.js predefinito.

```bash
npm start
```

Aprire un Web browser e passare all'app di esempio all'indirizzo `http://localhost:1337`.

Nella pagina verrà visualizzato il messaggio **Hello World** dell'app di esempio.

![App di esempio in esecuzione in locale](media/app-service-web-get-started-nodejs-poc/localhost-hello-world-in-browser.png)

Nella finestra del terminale premere **CTRL+C** per uscire dal server Web.

> [!NOTE]
> Nel servizio app di Azure l'app viene eseguita in IIS usando [iisnode](https://github.com/tjanczuk/iisnode). Per potere eseguire l'app con iisnode, la directory radice dell'app contiene un file web.config. Il file è leggibile da IIS e le impostazioni correlate a iisnode sono documentate nel [repository GitHub di iisnode](https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config).

[!INCLUDE [Create ZIP file](../../includes/app-service-web-create-zip.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-scus.md)] 

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-scus.md)] 

## <a name="create-a-web-app"></a>Creare un'app Web

In Cloud Shell creare un'app Web nel piano di servizio app `myAppServicePlan` con il comando [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). 

Nell'esempio seguente sostituire `<app_name>` con un nome app univoco globale. I caratteri validi sono `a-z`, `0-9` e `-`.

```azurecli-interactive
# Bash and Powershell
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name>
```

Dopo la creazione dell'app Web, l'interfaccia della riga di comando di Azure mostra un output simile all'esempio seguente:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="set-nodejs-runtime"></a>Impostare runtime Node.js

Impostare runtime Node su 8.11.1. <!-- To see all supported runtimes, run [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes). -->

```azurecli-interactive
# Bash and Powershell
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITE_NODE_DEFAULT_VERSION=8.11.1
```

Passare all'app Web appena creata. Sostituire _&lt;nome app>_ con un nome di app univoco.

```bash
http://<app name>.azurewebsites.net
```

Ecco l'aspetto che avrà la nuova app Web:

![Pagina dell'app Web vuota](media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]

## <a name="browse-to-the-app"></a>Passare all'app

Passare all'applicazione distribuita con il Web browser.

```
http://<app_name>.azurewebsites.net
```

Il codice di esempio Node.js è in esecuzione in un'app Web del servizio app di Azure.

![App di esempio in esecuzione in Azure](media/app-service-web-get-started-nodejs-poc/hello-world-in-browser.png)

**Congratulazioni** La distribuzione della prima app Node.js nel servizio app è stata completata.

## <a name="update-and-redeploy-the-code"></a>Aggiornare e ridistribuire il codice

Usando un editor di testo, aprire il file `index.js` nell'app Node.js e apportare una piccola modifica al testo nella chiamata a `response.end`:

```nodejs
response.end("Hello Azure!");
```

Nella finestra del terminale locale passare alla directory radice dell'applicazione e creare un nuovo file ZIP per il progetto aggiornato.

```
# Bash
zip -r myUpdatedAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myUpdatedAppFiles.zip
``` 

Distribuire il nuovo file ZIP nel servizio app seguendo la stessa procedura disponibile in [Distribuisci il file ZIP](#deploy-zip-file).

Tornare alla finestra del browser aperta nel passaggio **Passare all'app** e aggiornare la pagina.

![App di esempio aggiornata in esecuzione in Azure](media/app-service-web-get-started-nodejs-poc/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Gestire la nuova app Web di Azure

Accedere al <a href="https://portal.azure.com" target="_blank">portale di Azure</a> per gestire l'app Web creata.

Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Web di Azure.

![Passare all'app Web di Azure nel portale](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

Verrà visualizzata la pagina di panoramica dell'app Web. Qui è possibile eseguire attività di gestione di base come l'esplorazione, l'arresto, l'avvio, il riavvio e l'eliminazione dell'app. 

![Pagina del servizio app nel portale di Azure](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

Il menu a sinistra fornisce varie pagine per la configurazione dell'app. 

## <a name="video"></a>Video

Guardare il video per osservare il funzionamento di questa guida introduttiva e quindi seguire personalmente la procedura per pubblicare la prima app Node.js in Azure.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-for-Node-Developers/Create-a-Nodejs-app-in-Azure-Quickstart/player]   

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Node.js con MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)
