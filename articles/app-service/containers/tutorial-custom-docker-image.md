---
title: Usare un'immagine Docker personalizzata per l'app Web per contenitori di Azure | Microsoft Docs
description: Come usare un'immagine Docker personalizzata per l'app Web per contenitori.
keywords: Servizio app di Azure, app Web, Linux, Docker, contenitore
services: app-service
documentationcenter: 
author: SyntaxC4
manager: SyntaxC4
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 2580c2109ce33b1ce99aa491f7d0002edf060693
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="use-a-custom-docker-image-for-web-app-for-containers"></a>Usare un'immagine Docker personalizzata per l'app Web per contenitori

[App Web per contenitori](app-service-linux-intro.md) fornisce immagini Docker predefinite in Linux con il supporto per versioni specifiche, ad esempio PHP 7.0 e Node.js 4.5. App Web per contenitori usa la tecnologia basata su contenitori di Docker per ospitare sia immagini predefinite che immagini personalizzate come piattaforma distribuita come servizio. In questa esercitazione vengono fornite informazioni su come compilare un'immagine Docker personalizzata e distribuirla all'app Web per contenitori. Questo modello è utile quando le immagini incorporate non includono il linguaggio scelto o quando l'applicazione richiede una configurazione specifica che non è fornita all'interno delle immagini incorporate.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

* [Git](https://git-scm.com/downloads)
* Una [sottoscrizione di Azure](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) attiva
* [Docker](https://docs.docker.com/get-started/#setup)
* Un [account Docker Hub](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Scaricare l'esempio

In una finestra del terminale eseguire il comando seguente per clonare il repository di app di esempio nel computer locale, quindi passare alla directory che contiene il codice di esempio.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Creare l'immagine dal file di Docker

Nel repository Git dare un'occhiata a _Dockerfile_. Il file descrive l'ambiente di Python necessario per eseguire l'applicazione. L'immagine configura anche un server [SSH](https://www.ssh.com/ssh/protocol/) per la comunicazione sicura tra il contenitore e l'host.

```docker
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

Per creare l'immagine Docker, eseguire il comando `docker build` e specificare il nome `mydockerimage` e il tag `v1.0.0`. Sostituire `<docker-id>` con l'ID dell'account Docker Hub.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

L'output generato dal comando sarà simile al seguente:

```
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  5.558MB
Step 1/13 : FROM python:3.4
 ---> 9ff45ddb54e9
Step 2/13 : RUN mkdir /code
 ---> Using cache
 ---> f3f3ac01db0a
Step 3/13 : WORKDIR /code
 ---> Using cache
 ---> 38b32f15b442
.
.
.
Step 13/13 : ENTRYPOINT init.sh
 ---> Running in 5904e4c70043
 ---> e7cf08275692
Removing intermediate container 5904e4c70043
Successfully built e7cf08275692
Successfully tagged cephalin/mydockerimage:v1.0.0
```

Testare il funzionamento della build eseguendo il contenitore Docker. Eseguire il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) e passargli il nome e il tag dell'immagine. Accertarsi di specificare la porta usando l'argomento `-p`.

```bash
docker run -p 2222:8000 <docker-ID>/mydockerimage:v1.0.0
```

Verificare che l'app Web e il contenitore funzionino correttamente esplorando `http://localhost:2222`.

![Testare l'app Web in locale](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="push-the-docker-image-to-docker-hub"></a>Eseguire il push dell'immagine Docker a Docker Hub

Un registro è un'applicazione che ospita le immagini e fornisce l'immagine dei servizi e i servizi contenitore. Per condividere l'immagine, è necessario eseguirne il push in un registro. 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> Se si esegue il push in un registro Docker privato, vedere le istruzioni facoltative in [Eseguire il push di un'immagine Docker in un registro privato](#push-a-docker-image-to-private-registry-optional).

<!--## [Docker Hub](#tab/docker-hub)-->

Docker Hub è un registro per le immagini Docker che consente di ospitare i propri repository, pubblici o privati. Per eseguire il push di un'immagine Docker personalizzata nell'hub Docker pubblico, usare il comando [docker push](https://docs.docker.com/engine/reference/commandline/push/) e specificare un nome di immagine completo e un tag. Un nome di immagine completo e un tag hanno un aspetto analogo all'esempio seguente:

```
<docker-id>/image-name:tag
```

Se non è stato fatto, eseguire l'accesso a Docker Hub usando il comando [docker login](https://docs.docker.com/engine/reference/commandline/login/) prima di provare a eseguire il push di un'immagine.

```bash
docker login --username <docker-id> --password <docker-hub-password>
```

Un messaggio di accesso riuscito conferma che l'accesso è stato eseguito. Dopo avere effettuato l'accesso, è possibile eseguire il push dell'immagine in Docker Hub usando il comando [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Verificare che il push sia riuscito esaminando l'output del comando.

```
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
c33197c3f6d4: Pushed
ccd2c850ee43: Pushed
02dff2853466: Pushed
6ce78153632a: Pushed
efef3f03cc58: Pushed
3439624d77fb: Pushed
3a07adfb35c5: Pushed
2fcec228e1b7: Mounted from library/python
97d2d3bae505: Mounted from library/python
95aadeabf504: Mounted from library/python
b456afdc9996: Mounted from library/python
d752a0310ee4: Mounted from library/python
db64edce4b5b: Mounted from library/python
d5d60fc34309: Mounted from library/python
c01c63c6823d: Mounted from library/python
v1.0.0: digest: sha256:21f2798b20555f4143f2ca0591a43b4f6c8138406041f2d32ec908974feced66 size: 3676
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Distribuire l'app in Azure

È possibile ospitare applicazioni Linux native nel cloud usando App Web di Azure. Per creare un'app Web per contenitori, è necessario eseguire i comandi dell'interfaccia della riga di comando di Azure che creano un gruppo, quindi un piano di servizio e infine l'app Web vera e propria. 

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-linux-app-service-plan"></a>Creare un nuovo piano di servizio app Linux

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)] 

### <a name="create-a-web-app"></a>Creare un'app Web

In Cloud Shell creare un'[app Web](app-service-linux-intro.md) nel piano di servizio app `myAppServicePlan` con il comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). Non dimenticare di sostituire `<app_name>` con un nome univoco dell'app e < docker-ID > con l'ID di Docker.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --deployment-container-image-name <docker-ID>/mydockerimage:v1.0.0
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
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

La maggior parte delle immagini Docker è costituita da variabili di ambiente che devono essere configurate. Se si usa un'immagine Docker esistente compilata da un altro utente, l'immagine potrebbe usare una porta diversa dalla porta 80. Comunicare ad Azure la porta usata dall'immagine tramite l'impostazione dell'app `WEBSITES_PORT`. La pagina di GitHub per l'[esempio di Python in questa esercitazione](https://github.com/Azure-Samples/docker-django-webapp-linux) indica che è necessario impostare `WEBSITES_PORT` su _8000_.

Per impostare le impostazioni dell'app, utilizzare il [az webapp configurazione appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) comando nella Shell di Cloud. Le impostazioni delle app applicano la distinzione tra maiuscole e minuscole e sono separate da spazi.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_PORT=8000
```

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> Se si esegue la distribuzione da un registro Docker privato, vedere le istruzioni facoltative in [Configurare l'app Web per usare il contenitore Docker da un registro privato](#configure-web-app-to-use-docker-container-from-a-private-registry-optional).

<!-- # [Docker Hub](#tab/docker-hub)-->

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

### <a name="test-the-web-app"></a>Testare l'app Web

Verificare che l'app Web funzioni, esplorandola (`http://<app_name>azurewebsites.net`). 

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

Dopo avere modificato e salvato il file di Python, è necessario ricompilare la nuova immagine Docker ed eseguirne il push. Riavviare quindi l'app Web per rendere effettive le modifiche. Usare gli stessi comandi già usati in questa esercitazione. Vedere le sezioni [Creare l'immagine dal file di Docker](#build-the-image-from-the-docker-file) ed [Eseguire il push dell'immagine Docker a Docker Hub](#push-the-docker-image-to-docker-hub). Testare l'app Web seguendo le istruzioni in [Testare l'applicazione in Azure](#test-the-web-app).

## <a name="connect-to-web-app-for-containers-using-ssh"></a>Connettersi ad App Web per contenitori usando SSH

SSH consente la comunicazione sicura tra un contenitore e un client. Per fare in modo che un'immagine Docker personalizzata supporti SSH, è necessario compilarla in un file Docker. Si abilita SSH nel file Docker stesso. Le istruzioni SSH sono già state aggiunte al file Docker, quindi è possibile seguire queste istruzioni con l'immagine personalizzata:

* Un'istruzione [RUN](https://docs.docker.com/engine/reference/builder/#run) che chiama `apt-get`, quindi imposta la password per l'account radice su `"Docker!"`.

    ```docker
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Questa configurazione non consente connessioni esterne al contenitore. SSH è disponibile solo tramite il sito Kudu/SCM. Il sito Kudu/SCM viene autenticato con le credenziali di pubblicazione.

* Un'istruzione [COPY](https://docs.docker.com/engine/reference/builder/#copy) che comunica al motore Docker di copiare il file [sshd_config](http://man.openbsd.org/sshd_config) nella directory */etc/ssh/*. Il file di configurazione deve essere basato su [questo file sshd_config](https://github.com/Azure-App-Service/node/blob/master/6.11.1/sshd_config).

    ```docker
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Il file *sshd_config* deve includere gli elementi seguenti: 
    > * `Ciphers` deve includere almeno un elemento di questo elenco: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` deve includere almeno un elemento di questo elenco: `hmac-sha1,hmac-sha1-96`.

* Un'istruzione [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) che espone la porta 2222 nel contenitore. Anche se la password radice è nota, la porta 2222 non è accessibile da Internet. È una porta interna accessibile dai contenitori nella rete bridge di una rete virtuale privata. Al termine, i comandi copiano i dettagli della configurazione SSH e avviano il servizio `ssh`.

    ```docker
    EXPOSE 8000 2222
    ```

* Assicurarsi di [avviare il servizio ssh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh) utilizzando uno script della shell nella directory /bin.
 
    ```bash
    #!/bin/bash
    service ssh start
    ```
     
### <a name="open-ssh-connection-to-container"></a>Aprire una connessione SSH a un contenitore

App Web per contenitori non consente connessioni esterne al contenitore. SSH è disponibile solo tramite il sito Kudu, accessibile da `https://<app_name>.scm.azurewebsites.net`.

Per connettersi, passare a `https://<app_name>.scm.azurewebsites.net/webssh/host` e accedere con l'account di Azure.

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

Congratulazioni! Si è configurata un'immagine Docker personalizzata per un'app Web per contenitori.

## <a name="use-a-private-image-from-docker-hub-optional"></a>Usare un'immagine privata dal Docker Hub (facoltativo)

In [Crea un'app Web](#create-a-web-app) specificare un'immagine nel Docker Hub nel comando `az webapp create`. Questa operazione risulta particolarmente funzionante per un'immagine pubblica. Per usare un'immagine privata, è necessario configurare l'ID account Docker e la password nell'app web di Azure.

In Cloud Shell seguire il comando `az webapp create` con [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set). Sostituire *\<app_name>*, _<docker-id>_ e _<password>_ con l'ID e la password del Docker in uso.

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

Il comando mostra un output simile alla stringa JSON seguente, indicante che la modifica alla configurazione è riuscita:

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "false"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<docker-id>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "DOCKER|<image-name-and-tag>"
  }
]
```

## <a name="use-a-docker-image-from-any-private-registry-optional"></a>Usare un'immagine Docker da un registro privato (facoltativo)

In questa sezione viene illustrato come usare un'immagine Docker da un registro privato nell'App Web per contenitori e viene usato il Registro contenitori di Azure come esempio. La procedura per l'uso di registri privati di tipo diverso è simile. 

Registro contenitori di Azure è un servizio Docker gestito di Azure per l'hosting di immagini private. Le distribuzioni possono essere di qualsiasi tipo, inclusi [Docker Swarm](https://docs.docker.com/engine/swarm/), [Kubernetes](https://kubernetes.io/) e app Web per contenitori. 

### <a name="create-an-azure-container-registry"></a>Creare un Registro contenitori di Azure

In Cloud Shell usare il comando [az acr create](/cli/azure/acr?view=azure-cli-latest#az_acr_create) per creare un Registro contenitori di Azure. Passare il nome, il gruppo di risorse e `Basic` per lo SKU. Gli SKU disponibili sono `Classic`, `Basic`, `Standard` e `Premium`.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

La creazione di un contenitore genera l'output seguente:

```
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name>",
  "location": "westeurope",
  "loginServer": "<azure-container-registry-name>.azurecr.io",
  "name": "<azure-container-registry-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myazurecontainerre042025"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="log-in-to-azure-container-registry"></a>Accedere al Registro contenitori di Azure

Per eseguire il push di un'immagine nel registro, è necessario specificare le credenziali in modo che il registro accetti il push. È possibile recuperare queste credenziali usando il comando [az acr show](/cli/azure/acr?view=azure-cli-latest#az_acr_show) in Cloud Shell. 

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

Il comando mostra due password che possono essere usate con il nome utente.

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

Dalla finestra del terminale locale accedere al Registro contenitori di Azure tramite il comando `docker login`. Per l'accesso è necessario il nome del server. Usare il formato `{azure-container-registry-name>.azurecr.io`.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username> --password <password> 
```

Verificare che l'accesso sia riuscito. 

### <a name="push-an-image-to-azure-container-registry"></a>Eseguire il push di un'immagine nel Registro contenitori di Azure

> [!NOTE]
> Se si usa la propria immagine, contrassegnare l'immagine come indicato di seguito:
> ```bash
> docker tag <azure-container-registry-name>.azurecr.io/mydockerimage
> ```

Eseguire il push dell'immagine usando il comando `docker push`. Contrassegnare l'immagine con il nome del registro seguito dal nome dell'immagine e dal tag.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Verificare che il push abbia aggiunto un contenitore al registro elencando i repository di record di controllo di accesso. 

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

L'elenco delle immagini nel registro conferma che `mydockerimage` è nel registro.

```json
[
  "mydockerimage"
]
```

### <a name="configure-web-app-to-use-the-image-from-azure-container-registry-or-any-private-registry"></a>Configurare l'app Web per usare l'immagine dal Registro contenitori di Azure (o da qualsiasi registro privato)

È possibile configurare l'app Web per contenitori in modo che esegua un contenitore archiviato in Registro contenitori di Azure. Usare Registro contenitori di Azure è come usare un registro privato, quindi se è necessario usare il proprio registro privato, i passaggi per completare questa attività saranno simili.

In Cloud Shell eseguire [az acr credential show](/cli/azure/acr/credential?view=azure-cli-latest#az_acr_credential_show) per visualizzare il nome utente e la password per il Registro contenitori di Azure. Copiare il nome utente e una delle password per poterli usare per configurare l'app Web nel passaggio successivo.

```bash
az acr credential show --name <azure-container-registry-name>
```

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "password"
    },
    {
      "name": "password2",
      "value": "password2"
    }
  ],
  "username": "<registry-username>"
}
```

In Cloud Shell eseguire il comando [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set) per assegnare l'immagine Docker personalizzata all'app web. Sostituire *\<app_name>*, *\<docker-registry-server-url>*, _\<<registry-username>_ e _\<password>_. Per il Registro contenitori di Azure, *\<docker-registry-server-url>* si presenta nel formato `https://<azure-container-registry-name>.azurecr.io`. 

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> `https://` è necessario in *\<docker-registry-server-url>*.
>

Il comando mostra un output simile alla stringa JSON seguente, indicante che la modifica alla configurazione è riuscita:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "<azure-container-registry-name>.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<registry-username>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Compilare un'app Web Python Docker e PostgreSQL in Azure](tutorial-docker-python-postgresql-app.md)
