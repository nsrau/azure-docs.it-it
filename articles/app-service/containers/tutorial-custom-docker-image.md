---
title: "Esercitazione: Creare ed eseguire un'immagine personalizzata"
description: Informazioni su come creare un'immagine Linux personalizzata che può essere eseguita in Servizio app di Azure, distribuirla in Registri Azure Container ed eseguirla in Servizio app.
keywords: Servizio app di Azure, app Web, Linux, Docker, contenitore
author: msangapu-msft
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: d960af01eed9fae0fec2566772799e4972053d7b
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687489"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>Esercitazione: Compilare un'immagine personalizzata ed eseguirla nel servizio app da un registro privato

Il [servizio app](app-service-linux-intro.md) fornisce immagini Docker predefinite in Linux con il supporto per versioni specifiche, ad esempio PHP 7.3 e Node.js 10.14. Il servizio app usa la tecnologia basata su contenitori Docker per ospitare sia immagini predefinite che immagini personalizzate come piattaforma distribuita come servizio. Questa esercitazione mostra come compilare un'immagine personalizzata ed eseguirla nel Servizio app di Azure. Questo modello è utile quando le immagini incorporate non includono il linguaggio scelto o quando l'applicazione richiede una configurazione specifica che non è fornita all'interno delle immagini incorporate.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Distribuire un'immagine personalizzata in un registro contenitori privato
> * Eseguire l'immagine personalizzata nel Servizio app di Azure
> * Configurare le variabili di ambiente
> * Aggiornare e ridistribuire l'immagine
> * Accedere ai log di diagnostica
> * Connettersi al contenitore con SSH

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

* [Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)

## <a name="download-the-sample"></a>Scaricare l'esempio

In una finestra del terminale eseguire il comando seguente per clonare il repository di app di esempio nel computer locale, quindi passare alla directory che contiene il codice di esempio.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Creare l'immagine dal file Docker

Nel repository Git dare un'occhiata a _Dockerfile_. Il file descrive l'ambiente di Python necessario per eseguire l'applicazione. L'immagine configura anche un server [SSH](https://www.ssh.com/ssh/protocol/) per la comunicazione sicura tra il contenitore e l'host.

```Dockerfile
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

Compilare l'immagine Docker con il comando `docker build`.

```bash
docker build --tag mydockerimage .
```

Testare il funzionamento della build eseguendo il contenitore Docker. Eseguire il comando [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) passando il nome e il tag dell'immagine. Accertarsi di specificare la porta usando l'argomento `-p`.

```bash
docker run -p 8000:8000 mydockerimage
```

Verificare che l'app Web e il contenitore funzionino correttamente esplorando `http://localhost:8000`.

![Testare l'app Web in locale](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Distribuire l'app in Azure

Per creare un'app che usa l'immagine appena creata, si eseguono i comandi dell'interfaccia della riga di comando di Azure che creano un gruppo di risorse, eseguono il push dell'immagine e quindi creano l'app Web del piano di servizio app per eseguirla.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>Creare un'istanza di Registro Azure Container

In Cloud Shell usare il comando [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) per creare un'istanza di Registro Azure Container.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>Accedere a Registro Azure Container

Per eseguire il push di un'immagine nel registro, è necessario autenticarsi con il registro privato. In Cloud Shell usare il comando [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) per recuperare le credenziali dal registro creato.

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

L'output mostra due password assieme al nome utente.

```json
<
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "<registry-username>"
}
```

Dalla finestra del terminale locale accedere a Registro Azure Container usando il comando `docker login`, come illustrato nell'esempio seguente. Sostituire *\<azure-container-registry-name>* e *\<registry-username>* con i valori per il registro. Quando richiesto, digitare una delle password del passaggio precedente.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Verificare che l'accesso sia riuscito.

### <a name="push-image-to-azure-container-registry"></a>Eseguire il push dell'immagine in Registro Azure Container

Assegnare un tag all'immagine locale per il Registro Azure Container. Ad esempio:
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Eseguire il push dell'immagine usando il comando `docker push`. Contrassegnare l'immagine con il nome del registro seguito dal nome dell'immagine e dal tag.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

In Cloud Shell, verificare che il push abbia esito positivo.

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

Dovrebbe essere visualizzato l'output seguente.

```json
[
  "mydockerimage"
]
```

### <a name="create-app-service-plan"></a>Creare un piano di servizio app

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>Crea app Web

In Cloud Shell creare un'[app Web](app-service-linux-intro.md) nel piano di servizio app `myAppServicePlan` con il comando [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Sostituire _\<app-name>_ con un nome app univoco e _\<azure-container-registry-name>_ con il nome del registro.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-container-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
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
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-registry-credentials-in-web-app"></a>Configurare le credenziali del registro nell'app Web

Per eseguire il pull dell'immagine privata con il Servizio app di Azure sono necessarie informazioni sul registro e sull'immagine. In Cloud Shell, fornirle con il comando [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set). Sostituire *\<app-name>* , *\<azure-container-registry-name>* , _\<registry-username>_ e _\<password>_ .

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> Quando si usa un registro diverso dall'hub Docker, `--docker-registry-server-url` deve essere formattato come `https://` seguito dal nome di dominio completo del registro.
>

### <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

La maggior parte delle immagini Docker usa variabili di ambiente personalizzate, ad esempio una porta diversa da 80. Comunicare al Servizio app di Azure la porta usata dall'immagine usando l'impostazione dell'app `WEBSITES_PORT`. La pagina di GitHub per l'[esempio di Python in questa esercitazione](https://github.com/Azure-Samples/docker-django-webapp-linux) indica che è necessario impostare `WEBSITES_PORT` su _8000_.

Per configurare le impostazioni dell'app, usare il comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in Cloud Shell. Le impostazioni delle app applicano la distinzione tra maiuscole e minuscole e sono separate da spazi.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>Testare l'app Web

Verificare che l'app Web funzioni, esplorandola (`http://<app-name>.azurewebsites.net`).

> [!NOTE]
> Il primo accesso all'app potrebbe richiedere del tempo perché il Servizio app di Azure deve eseguire il pull dell'intera immagine. Se il browser raggiunge il timeout, è sufficiente aggiornare la pagina.

![Testare la configurazione della porta dell'app Web](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Modificare e ridistribuire l'app Web

Nel repository Git locale, aprire app/templates/app/index.html. Individuare il primo elemento HTML e modificarlo come segue.

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
      </div>
    </div>
  </nav>
```

Dopo avere modificato e salvato il file di Python, è necessario ricompilare la nuova immagine Docker ed eseguirne il push. Riavviare quindi l'app Web per rendere effettive le modifiche. Usare gli stessi comandi già usati in questa esercitazione. Vedere le sezioni [Creare l'immagine dal file di Docker](#build-the-image-from-the-docker-file) ed [Eseguire il push dell'immagine in Registro Azure Container](#push-image-to-azure-container-registry). Testare l'app Web seguendo le istruzioni in [Testare l'applicazione in Azure](#test-the-web-app).

## <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="enable-ssh-connections"></a>Abilitare le connessioni SSH

SSH consente la comunicazione sicura tra un contenitore e un client. Per abilitare la connessione SSH al contenitore, l'immagine personalizzata deve essere configurata appositamente. Di seguito viene esaminato il repository di esempio che ha già la configurazione necessaria.

* Nel [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile), il codice seguente installa il server SSH e imposta anche le credenziali di accesso.

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Questa configurazione non consente connessioni esterne al contenitore. SSH è disponibile solo tramite il sito Kudu/SCM. Il sito Kudu/SCM viene autenticato con l'account Azure.

* Il [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18) copia il file [sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config) del repository nella directory */etc/ssh/* .

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* Il [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22) espone la porta 2222 nel contenitore. È una porta interna accessibile dai contenitori nella rete bridge di una rete virtuale privata. 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* Lo [script di ingresso](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5) avvia il server SSH.

    ```bash
    #!/bin/bash
    service ssh start
    ```

### <a name="open-ssh-connection-to-container"></a>Aprire una connessione SSH a un contenitore

La connessione SSH è disponibile solo tramite il sito Kudu, accessibile da `https://<app-name>.scm.azurewebsites.net`.

Per connettersi, passare a `https://<app-name>.scm.azurewebsites.net/webssh/host` e accedere con l'account di Azure.

Si viene quindi reindirizzati a una pagina che visualizza una console interattiva.

È possibile verificare che determinate applicazioni siano in esecuzione nel contenitore. Per ispezionare il contenitore e verificare i processi in esecuzione, eseguire il comando `top` al prompt.

```bash
top
```

Il comando `top` espone tutti i processi in esecuzione in un contenitore.

```
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

Congratulazioni! È stato configurato un contenitore Linux personalizzato nel Servizio app di Azure.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Distribuire un'immagine personalizzata in un registro contenitori privato
> * Eseguire l'immagine personalizzata nel Servizio app di Azure
> * Configurare le variabili di ambiente
> * Aggiornare e ridistribuire l'immagine
> * Accedere ai log di diagnostica
> * Connettersi al contenitore con SSH

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato all'app.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il mapping di un nome DNS personalizzato all'app Web](../app-service-web-tutorial-custom-domain.md)

In alternativa, consultare altre risorse:

> [!div class="nextstepaction"]
> [Configurare il contenitore personalizzato](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Esercitazione: App WordPress multi-contenitore](tutorial-multi-container-app.md)
