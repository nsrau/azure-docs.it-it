---
title: "Guida introduttiva: Creare un'app Python in Linux"
description: Introduzione all'uso delle app Linux nel servizio app di Azure distribuendo la prima app Python in un contenitore Linux nel servizio app.
author: msangapu-msft
ms.topic: quickstart
ms.date: 08/23/2019
ms.custom: seodec18
experimental: false
experiment_id: 1e304dc9-5add-4b
ms.openlocfilehash: 7905b086ac390243f2fe02697f68c9b1c05ca28e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688987"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux"></a>Creare un'app Python nel Servizio app di Azure in Linux

In questo argomento di avvio rapido si distribuirà una semplice app Python nel [servizio app in Linux](app-service-linux-intro.md), che fornisce un servizio di hosting web ad alta scalabilità e con funzioni di auto-correzione. Si userà inoltre l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) tramite Azure Cloud Shell, interattivo e basato sul browser, in modo da poter seguire la procedura con un computer Mac, Linux o Windows.

![App di esempio in esecuzione in Azure](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Scaricare l'esempio

In Cloud Shell creare una directory quickstart e passare ad essa.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Eseguire quindi il comando seguente per clonare il repository dell'app di esempio nella directory quickstart.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Durante l'esecuzione, il comando visualizza informazioni simili all'esempio seguente:

```bash
Cloning into 'python-docs-hello-world'...
remote: Enumerating objects: 43, done.
remote: Total 43 (delta 0), reused 0 (delta 0), pack-reused 43
Unpacking objects: 100% (43/43), done.
Checking connectivity... done.
```

## <a name="create-a-web-app"></a>Creare un'app Web

Passare alla directory contenente il codice di esempio ed eseguire il comando `az webapp up`.

Nell'esempio seguente sostituire *\<nome_app>* con un nome app univoco globale. I caratteri validi sono `a-z`, `0-9` e `-`.

```bash
cd python-docs-hello-world

az webapp up -n <app_name>
```

L'esecuzione del comando può richiedere alcuni minuti. Durante l'esecuzione, il comando visualizza informazioni simili all'esempio seguente:

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app_name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app_name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app_name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

Il comando `az webapp up` esegue le azioni seguenti:

- Crea un gruppo di risorse predefinito.

- Crea un piano di servizio app predefinito.

- Crea un'app con il nome specificato.

- [Distribuire file ZIP](https://docs.microsoft.com/azure/app-service/deploy-zip) i dalla directory di lavoro corrente all'app.

## <a name="browse-to-the-app"></a>Passare all'app

Passare all'applicazione distribuita con il Web browser.

```bash
http://<app_name>.azurewebsites.net
```

Il codice di esempio Python è in esecuzione nel servizio app in Linux con un'immagine predefinita.

![App di esempio in esecuzione in Azure](media/quickstart-python/hello-world-in-browser.png)

**Congratulazioni** La distribuzione della prima app Python nel Servizio app di Azure in Linux è stata completata.

## <a name="update-and-redeploy-the-code"></a>Aggiornare e ridistribuire il codice

In Cloud Shell digitare `code application.py` per aprire l'editor di Cloud Shell.

![Code application.py](media/quickstart-python/code-applicationpy.png)

 Apportare una piccola modifica al testo nella chiamata a `return`:

```python
return "Hello Azure!"
```

Salvare le modifiche e uscire dall'editor. Usare il comando `^S` per salvare e `^Q` per uscire.

A questo punto occorre ridistribuire l'app. Sostituire `<app_name>` con l'app.

```bash
az webapp up -n <app_name>
```

Al termine della distribuzione, tornare alla finestra del browser aperta nel passaggio **Passare all'app** e aggiornare la pagina.

![App di esempio aggiornata in esecuzione in Azure](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Gestire la nuova app Azure

Accedere al <a href="https://portal.azure.com" target="_blank">portale di Azure</a> per gestire l'app creata.

Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Azure.

![Passaggio all'app di Azure nel portale](./media/quickstart-python/app-service-list.png)

Verrà visualizzata la pagina Panoramica dell'app. Qui è possibile eseguire attività di gestione di base come l'esplorazione, l'arresto, l'avvio, il riavvio e l'eliminazione dell'app.

![Pagina del servizio app nel portale di Azure](media/quickstart-python/app-service-detail.png)

Il menu a sinistra fornisce varie pagine per la configurazione dell'app. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: App Python con PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Configurare un'app Python](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire la distribuzione da un repository di contenitore privato](tutorial-custom-docker-image.md)
