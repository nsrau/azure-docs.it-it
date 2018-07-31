---
title: Distribuire un'app Python in App Web per contenitori di Azure
description: Come distribuire un'immagine Docker che esegue un'applicazione Python in App Web per contenitori di Azure.
keywords: servizio app di azure, app web, python, docker, contenitore
services: app-service
author: cephalin
manager: jeconnoc
ms.service: app-service
ms.devlang: python
ms.topic: quickstart
ms.date: 07/13/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 5686266774603413fc255c53a0d1ad30f9baa8eb
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173861"
---
# <a name="deploy-a-python-web-app-in-web-app-for-containers"></a>Distribuire un'app Web Python in App Web per contenitori

Il [Servizio app in Linux](app-service-linux-intro.md) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione basato sul sistema operativo Linux. Questa guida introduttiva mostra come creare un'app Web e distribuire un'app Flask semplice in tale app Web tramite un'immagine Docker Hub personalizzata. Creare l'app Web usando l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

![App di esempio in esecuzione in Azure](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

* <a href="https://git-scm.com/" target="_blank">Installare Git</a>
* <a href="https://www.docker.com/community-edition" target="_blank">Installare Docker Community Edition</a>
* <a href="https://hub.docker.com/" target="_blank">Iscriversi per ottenere un account Docker Hub</a>

## <a name="download-the-sample"></a>Scaricare l'esempio

In una finestra del terminale eseguire i comandi seguenti per clonare l'applicazione di esempio nel computer locale e passare alla directory che contiene il codice di esempio.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

Questo repository contiene una semplice applicazione Flask nella cartella _/app_ e un _Dockerfile_ che specifica tre cose:

- Usare l'immagine di base [tiangolo/uwsgi-nginx-flask:python3.6-alpine3.7](https://hub.docker.com/r/tiangolo/uwsgi-nginx-flask/).
- Il contenitore deve essere in ascolto sulla porta 8000.
- Copiare la directory `/app` nella directory `/app` del contenitore.

La configurazione segue le [istruzioni per l'immagine di base](https://hub.docker.com/r/tiangolo/uwsgi-nginx-flask/).

## <a name="run-the-app-locally"></a>Eseguire l'app in locale

Eseguire l'app in un contenitore Docker.

```bash
docker build --rm -t flask-quickstart .
docker run --rm -it -p 8000:8000 flask-quickstart
```

Aprire un Web browser e passare all'app di esempio all'indirizzo `http://localhost:8000`.

Nella pagina verrà visualizzato il messaggio **Hello World** dell'app di esempio.

![App di esempio in esecuzione in locale](media/quickstart-python/localhost-hello-world-in-browser.png)

Nella finestra del terminale premere **CTRL+C** per arrestare il contenitore.

## <a name="deploy-image-to-docker-hub"></a>Distribuire l'immagine in Docker Hub.

Accedere al proprio account Docker Hub. Rispondere alla richiesta di immettere le credenziali di Docker Hub.

```bash
docker login
```

Contrassegnare l'immagine ed effettuarne il push in un nuovo repository _pubblico_ nell'account Docker Hub denominato `flask-quickstart`. Sostituire *\<dockerhub_id>* con l'ID di Docker Hub.

```bash
docker tag flask-quickstart <dockerhub_id>/flask-quickstart
docker push <dockerhub_id>/flask-quickstart
```

> [!NOTE]
> `docker push` crea un repository pubblico se il repository specificato non viene trovato. In questa guida introduttiva si presuppone l'uso di un repository pubblico in Docker Hub. Se si preferisce eseguire il push in un repository privato, è necessario configurare le credenziali di Docker Hub in Servizio app di Azure in un secondo momento. Vedere [Creare un'app Web](#create-a-web-app).

Una volta completato il push dell'immagine, si è pronti per usarla nell'app Web di Azure.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Creare un'app Web

Creare un'[app Web](../app-service-web-overview.md) nel piano di servizio app `myAppServicePlan` con il comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). Sostituire *\<app name>* con un nome di app globalmente univoco e sostituire  *\<dockerhub_id>* con l'ID di Docker Hub.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name <dockerhub_id>/flask-quickstart
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
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Se il caricamento è stato eseguito in un repository privato in precedenza, è necessario anche configurare le credenziali di Docker Hub nel servizio app. Per altre informazioni, vedere [Usare un'immagine privata dal Docker Hub](tutorial-custom-docker-image.md#use-a-private-image-from-docker-hub-optional).

### <a name="specify-container-port"></a>Specificare la porta del contenitore

Come specificato nel _Dockerfile_, il contenitore è in ascolto sulla porta 8000. Per fare in modo che Servizio app instradi la richiesta alla porta corretta, è necessario configurare l'impostazione dell'app *WEBSITES_PORT*.

In Cloud Shell eseguire il comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).


```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings WEBSITES_PORT=8000
```

## <a name="browse-to-the-app"></a>Passare all'app

```bash
http://<app_name>.azurewebsites.net/
```

![App di esempio in esecuzione in Azure](media/quickstart-python/hello-world-in-browser.png)

> [!NOTE]
> L'avvio dell'app Web richiede tempo perché l'immagine di Docker Hub deve essere scaricata ed eseguita quando l'app viene richiesta per la prima volta. Se inizialmente viene visualizzato un errore dopo un lungo periodo, è sufficiente aggiornare la pagina.

**Congratulazioni** È stata distribuita un'immagine Docker personalizzata che esegue un'app Python in App Web per contenitori.

## <a name="update-locally-and-redeploy"></a>Eseguire l'aggiornamento e la ridistribuzione in locale

Usando un editor di testo locale, aprire il file `app/main.py` nell'app Python e apportare una piccola modifica al testo accanto all'istruzione `return`:

```python
return 'Hello, Azure!'
```

Ricompilare l'immagine ed eseguirne nuovamente il push in Docker Hub.

```bash
docker build --rm -t flask-quickstart .
docker tag flask-quickstart <dockerhub_id>/flask-quickstart
docker push <dockerhub_id>/flask-quickstart
```

In Cloud Shell riavviare l'app. Il riavvio dell'app garantisce che tutte le impostazioni vengano applicate e che venga estratto dal registro il contenitore più recente.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

Attendere circa 15 secondi il completamento del pull dell'immagine aggiornata dal servizio app. Tornare alla finestra del browser aperta nel passaggio **Passare all'app** e aggiornare la pagina.

![App di esempio aggiornata in esecuzione in Azure](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-azure-web-app"></a>Gestire l'app Web di Azure

Accedere al [portale di Azure](https://portal.azure.com) per visualizzare l'app Web creata.

Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Web di Azure.

![Passare all'app Web di Azure nel portale](./media/quickstart-python/app-service-list.png)

Per impostazione predefinita, il portale visualizza la pagina **Panoramica** dell'app Web. che offre una visualizzazione dello stato dell'app. In questa pagina è anche possibile eseguire attività di gestione di base come esplorare, arrestare, avviare, riavviare ed eliminare. Le schede sul lato sinistro della pagina mostrano le diverse pagine di configurazione che è possibile aprire.

![Pagina del servizio app nel portale di Azure](./media/quickstart-python/app-service-detail.png)

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Python con PostgreSQL](tutorial-docker-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Use custom images](tutorial-custom-docker-image.md) (Usare immagini personalizzate)