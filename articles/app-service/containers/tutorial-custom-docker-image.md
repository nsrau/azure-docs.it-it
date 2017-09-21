---
title: Usare un'immagine Docker personalizzata per App Web di Azure per contenitori | Microsoft Docs
description: Come usare un'immagine Docker personalizzata per App Web di Azure per contenitori.
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
ms.topic: article
ms.date: 09/03/2017
ms.author: cfowler
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 2a14c9539e46bffbe3e11ff372f37190a562e199
ms.contentlocale: it-it
ms.lasthandoff: 09/13/2017

---
# <a name="use-a-custom-docker-image-for-azure-web-app-for-containers"></a>Usare un'immagine Docker personalizzata per App Web di Azure per contenitori

[App Web per contenitori](app-service-linux-intro.md) fornisce immagini Docker predefinite in Linux con il supporto per versioni specifiche, ad esempio PHP 7.0 e Node.js 4.5. App Web per contenitori sfrutta la tecnologia basata su contenitori di Docker per ospitare sia immagini predefinite che immagini personalizzate come piattaforma distribuita come servizio. In questa esercitazione viene illustrato come creare un'immagine Docker personalizzata da usare in App Web per contenitori, il che è un modello comune se non è disponibile un'immagine predefinita per il linguaggio in uso o se l'applicazione richiede una configurazione specifica non fornita nelle immagini predefinite.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

* [Git](https://git-scm.com/downloads)
* Una [sottoscrizione di Azure](https://azure.microsoft.com/pricing/free-trial/) attiva
* [Docker](https://docs.docker.com/get-started/#setup)
* Un [account Docker Hub](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Scaricare l'esempio

In una finestra del terminale eseguire il comando seguente per clonare il repository di app di esempio nel computer locale, quindi passare alla directory che contiene il codice di esempio.

```bash
git clone https://github.com/Azure-Samples/use-custom-docker-image.git
cd use-custom-docker-image
```

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="build-the-image-from-the-docker-file"></a>Creare l'immagine dal file di Docker

Il file di Docker seguente descrive l'ambiente di Python necessario per eseguire l'applicazione. L'immagine configura anche un server [SSH](https://www.ssh.com/ssh/protocol/) per la comunicazione sicura tra il contenitore e l'host.

```docker
# Use an official Python runtime as a parent image
FROM python

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Run python's package manager and install the flask package
RUN pip install flask

# Configure ports
EXPOSE 2222 80

# Run apt-get, to install the SSH server
RUN apt-get update \ 
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "root:Docker!" | chpasswd

#Copy the sshd_config file to its new location
COPY sshd_config /etc/ssh/

# Start the SSH service
RUN service ssh start

# Copy init_container.sh to the /bin directory
COPY init_container.sh /bin/
    
# Run the chmod command to change permissions on above file in the /bin directory
RUN chmod 755 /bin/init_container.sh 

# run commands in init_container.sh
CMD ["/bin/init_container.sh"]
```

Per creare l'immagine Docker, eseguire il comando `docker build` e specificare il nome `mydockerimage` e il tag `v1.0.0`. Sostituire `<docker-id>` con l'ID dell'account Docker Hub.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

L'output generato dal comando sarà simile al seguente:

```bash
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  23.04kB
Step 1/13 : FROM python
 ---> 968120d8cbe8
Step 2/13 : WORKDIR /app
 ---> Using cache
 ---> dd6fdca5aa65
Step 3/13 : ADD . /app
 ---> e05c8f4beeae
Get:1 http://security.debian.org jessie/updates InRelease [63.1 kB]
Ign http://deb.debian.org jessie InRelease
Get:2 http://deb.debian.org jessie-updates InRelease [145 kB]
Get:3 http://deb.debian.org jessie Release.gpg [2373 B]
Fetched 9988 kB in 7s (1266 kB/s)
Reading package lists...
Building dependency tree...
Reading state information...
The following extra packages will be installed:
  init-system-helpers libwrap0 openssh-sftp-server
Suggested packages:
  ssh-askpass rssh molly-guard ufw monkeysphere
Recommended packages:
  tcpd xauth ncurses-term
The following NEW packages will be installed:
  init-system-helpers libwrap0 openssh-server openssh-sftp-server
0 upgraded, 4 newly installed, 0 to remove and 3 not upgraded.
Need to get 442 kB of archives.
After this operation, 1138 kB of additional disk space will be used.
Get:1 http://deb.debian.org/debian/ jessie/main libwrap0 amd64 7.6.q-25 [58.5 kB]
Creating SSH2 RSA key; this may take some time ...
2048 f0:e9:fb:69:de:62:a4:5c:a3:7c:b3:41:e9:2e:96:a3 /etc/ssh/ssh_host_rsa_key.pub (RSA)
Creating SSH2 DSA key; this may take some time ...
1024 4a:5e:89:bd:aa:2d:71:bb:0e:3a:32:94:fb:c0:b1:4d /etc/ssh/ssh_host_dsa_key.pub (DSA)
Processing triggers for systemd (215-17+deb8u7) ...
 ---> 5b416a7dcdca
Removing intermediate container 283b3b4623d7
Step 13/13 : CMD python app.py
 ---> Running in 1c776e5e0772
 ---> 1bfc1bbc968d
Removing intermediate container 1c776e5e0772
Successfully built 1bfc1bbc968d
Successfully tagged <docker-id>/myDockerImage:v1.0.0
```

Testare il funzionamento della build eseguendo il contenitore Docker. Eseguire il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) e passargli il nome e il tag dell'immagine. È anche necessario specificare la porta usando l'argomento `-p`.

```bash
docker run -p 80:2222 <docker-ID>/mydockerimage:v1.0.0
```

Verificare che l'app Web e il contenitore funzionino correttamente esplorando l'app Web in locale.

![Testare l'app Web in locale](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="push-a-docker-image-to-docker-hub"></a>Eseguire il push dell'immagine a Docker Hub

Un registro è un'applicazione che ospita le immagini e fornisce l'immagine dei servizi e i servizi contenitore. Per condividere l'immagine, è necessario eseguirne il push in un registro. 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> Se si esegue il push in un registro Docker privato, vedere le istruzioni facoltative in [Eseguire il push di un'immagine Docker in un registro privato](#push-a-docker-image-to-private-registry-optional).

<!--## [Docker Hub](#tab/docker-hub)-->

Docker Hub è un registro per le immagini Docker che consente di ospitare i propri repository, pubblici o privati. Per eseguire il push di un'immagine Docker personalizzata nell'hub Docker pubblico, usare il comando [docker push](https://docs.docker.com/engine/reference/commandline/push/) e specificare un nome di immagine completo e un tag. Un nome di immagine completo e un tag hanno un aspetto analogo all'esempio seguente:

```bash
<docker-id>/image-name:tag
```

Se non è stato fatto, eseguire l'accesso a Docker Hub usando il comando [docker login](https://docs.docker.com/engine/reference/commandline/login/) prima di provare a eseguire il push di un'immagine.

```bash
docker login --username <docker-id> --password <docker-hub-password>
```

Un messaggio di accesso riuscito conferma che l'accesso è stato eseguito. Dopo avere effettuato l'accesso, è possibile eseguire il push dell'immagine in Docker Hub usando il comando [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0 .
```

Verificare che il push sia riuscito esaminando l'output del comando.

```bash
The push refers to a repository [docker.io/<docker-id>/python-flask]
e9aa2c6d0f34: Pushed
0fdcb490aeec: Pushed
08ae61c7869c: Pushed
2548e7db2a94: Mounted from library/python
325b9d6f2920: Pushed
815acdffadff: Mounted from library/python
97108d083e01: Mounted from library/python
5616a6292c16: Mounted from library/python
f3ed6cb59ab0: Mounted from library/python
654f45ecb7e3: Mounted from library/python
2c40c66f7667: Mounted from library/python
v1: digest: sha256:a910d5b77e6960c01745a87c35f3d1a13ba73231ac9a4664c5011b1422d59b60 size: 2632
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

## <a name="create-web-app-for-containers"></a>Creare un' app Web per contenitori

È possibile ospitare applicazioni Linux native nel cloud usando App Web di Azure. Per creare un'app Web per contenitori, è necessario eseguire i comandi dell'interfaccia della riga di comando di Azure che creano un gruppo, quindi un piano di servizio e infine l'app Web vera e propria. Eseguire prima il comando [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) e passare una posizione e un nome univoco.

```azurecli-interactive
az group create --location "West Europe" --name myResourceGroup
```

L'output sarà simile all'esempio seguente:

```json
{
  "id": "/subscriptions/432849d3e4-4f90-a782-87c11e-5e59d6dd/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

Usare il nome del gruppo per creare un piano di servizio app usando il comando [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create). È anche necessario assegnargli un nome univoco e impostare il flag `--is-linux`.

```azurecli-interactive
az appservice plan create --name myServicePlan --resource-group myResourceGroup --is-linux
```

La creazione di un piano di servizio genera risultati simili all'esempio seguente:

```json
  {- Starting...
  "adminSiteName": null,
  "appServicePlanName": "myServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/resourceGroups/myResourceGroup/provide
rs/Microsoft.Web/serverfarms/myServicePlan",
  "kind": "linux",
  "location": "West Europe", 
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "tier": "Basic"
  },
  "status": "Ready",
  "subscription": "",
  "tags": null,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

Ora che il gruppo di risorse e il piano di servizio sono stati creati, è possibile eseguire il comando [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) per creare l'app Web. Si noti che lo stack di runtime è Python 3.4 e che l'app Web usa il gruppo di risorse e il piano di servizio configurati nei passaggi precedenti.

```azurecli-interactive
az webapp create -g myResourceGroup -p myServicePlan -n <web-app-name> --runtime "python|3.4" 
```

Il comando per creare un'app Web genera l'output illustrato qui:

```json
{- Starting ..
  "availabilityState": "Normal",
   "enabled": true,
  "enabledHostNames": [
    "<web-app-name>.azurewebsites.net",
    "<web-app-name>.scm.azurewebsites.net"
  ],
  "ftpPublishingUrl": "ftp://waws-prod-am2-085.ftp.azurewebsites.windows.net/site/wwwroot",
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<web-app-name>.azurewebsites.net",
    },
  ],
  "hostNames": [
    "<web-app-name>.azurewebsites.net"
  ],
  "hostNamesDisabled": false,
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/5e59d6dd-d3e4-4f90-a782-43284987c11e/resourceGroups/myResourceGroup/providers/Microsoft.
Web/sites/<web-app-name>",
  "lastModifiedTimeUtc": "2017-08-08T21:09:33.693333",
  "location": "West Europe",
  "name": "<web-app-name>",
  "outboundIpAddresses": "13.81.108.99,52.232.76.83,52.166.73.203,52.233.173.39,52.233.159.48",
  "resourceGroup": "myResourceGroup"
}

```

La maggior parte delle app Web ha impostazioni applicazione che è necessario configurare. Se si usa un'immagine Docker esistente compilata da un altro utente, l'immagine potrebbe richiedere una porta diversa dalla porta 80 per l'applicazione. Per impostare `WEBSITES_PORT`, eseguire il comando [az webapp config](https://docs.microsoft.com/cli/azure/webapp/config/appsettings), come illustrato nell'esempio di codice seguente:

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <web-app-name> --settings WEBSITES_PORT=2222
```

> [!NOTE]
> Nelle impostazioni applicazione viene fatta distinzione tra maiuscole e minuscole.
>

Per verificare che l'app Web funzioni, esplorarla. Non dimenticare il numero di porta.

![Testare la configurazione della porta dell'app Web](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="configure-web-app-to-use-docker-container-from-docker-hub"></a>Configurare l'app Web per usare il contenitore Docker da Docker Hub

Il comando [az webapp config](https://docs.microsoft.com/cli/azure/webapp/config) consente di usare un'immagine Docker personalizzata.

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> Se si esegue la distribuzione da un registro Docker privato, vedere le istruzioni facoltative in [Configurare l'app Web per usare il contenitore Docker da un registro privato](#configure-web-app-to-use-docker-container-from-a-private-registry-optional).

<!-- # [Docker Hub](#tab/docker-hub)-->

Per configurare l'app Web per usare un registro Docker pubblico, passare il nome dell'app, il gruppo di risorse, il nome dell'immagine e l'URL al comando [az webapp config container set](https://docs.microsoft.com/cli/azure/webapp/config/container#az_webapp_config_container_set).

```azurecli-interactive
az webapp config container set --name <web-app-name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage
--docker-registry-server-url <docker-id>/myContainerRegistry
```

Una modifica alla configurazione riuscita restituisce informazioni generali sul contenitore.

```bash
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "{docker-id}/mydockerimage:v1.0.0"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "{docker-id}"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

## <a name="test-the-application-in-azure"></a>Testare l'applicazione in Azure

Prima del test, è necessario riavviare l'app Web usando [az webapp restart](https://docs.microsoft.com/cli/azure/webapp#az_webapp_restart) per rendere effettive le modifiche alla configurazione.

```azurecli-interactive
az webapp restart --name <web-app-name> --resource-group myResourceGroup
```

Il comando restart riavvia l'app Web in modalità non interattiva, quindi non vengono visualizzati commenti nel terminale. Quando l'app Web è in esecuzione, testarla aprendo l'URL all'indirizzo `http://<username>.azurewebsites.net`. Verificare che l'app visualizzi il nuovo messaggio di benvenuto.

![Testare l'app Web in Azure](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Modificare e ridistribuire l'app Web

Un file di Python denominato `app.py` esiste nella directory `using-custom-docker-image`. Questo file contiene una riga di codice che visualizza un messaggio `Hello World!`. Modificare la riga in modo che visualizzi il messaggio `Hello World of Web Apps running in Docker Containers!`.

```python
return "Hello World of Web Apps running in Docker Containers!"
```

Dopo avere modificato e salvato il file di Python, è necessario ricompilare la nuova immagine Docker ed eseguirne il push. Riavviare quindi l'app Web per rendere effettive le modifiche. Usare gli stessi comandi già usati in questa esercitazione. Vedere le sezioni [Creare l'immagine dal file di Docker](#build-the-image-from-the-docker-file) ed [Eseguire il push dell'immagine Docker](#push-docker-image). Testare l'app Web seguendo le istruzioni in [Testare l'applicazione in Azure](#tTest-the-application-in-azure)

## <a name="connect-to-web-app-for-containers-using-ssh"></a>Connettersi ad App Web per contenitori usando SSH

SSH consente la comunicazione sicura tra un contenitore e un client. Per fare in modo che un'immagine Docker personalizzata supporti SSH, è necessario compilarla in un file Docker. Si abilita SSH nel file Docker stesso. Le istruzioni SSH sono già state aggiunte al file Docker, quindi è possibile seguire queste istruzioni con l'immagine personalizzata:

* Un'istruzione [RUN](https://docs.docker.com/engine/reference/builder/#run) che chiama `apt-get`, quindi imposta la password per l'account radice su `"Docker!"`.

    ```docker
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

    > [!NOTE]
    > Questa configurazione non consente connessioni esterne al contenitore. SSH è disponibile solo tramite il sito Kudu/SCM. Il sito Kudu/SCM viene autenticato con le credenziali di pubblicazione.

* Un'istruzione [COPY](https://docs.docker.com/engine/reference/builder/#copy) che comunica al motore Docker di copiare il file [sshd_config](http://man.openbsd.org/sshd_config) nella directory */etc/ssh/*. Il file di configurazione deve essere basato sul [file sshd_config](https://github.com/Azure-App-Service/node/blob/master/6.11/sshd_config) nel repository GitHub di Azure-App-Service.

    > [!NOTE]
    > Il file *sshd_config* deve includere gli elementi seguenti: 
    > * `Ciphers` deve includere almeno un elemento di questo elenco: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` deve includere almeno un elemento di questo elenco: `hmac-sha1,hmac-sha1-96`.

    ```docker
    #Copy the sshd_config file to its new location
    COPY sshd_config /etc/ssh/
    ```

* Un'istruzione [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) che espone la porta 2222 nel contenitore. Anche se la password radice è nota, la porta 2222 non è accessibile da Internet. È una porta interna accessibile dai contenitori nella rete bridge di una rete virtuale privata. Al termine, i comandi copiano i dettagli della configurazione SSH e avviano il servizio `ssh`.

    ```docker
    # Configure ports
    EXPOSE 2222 80

    #Copy the sshd_config file to its new location
    COPY sshd_config /etc/ssh/

    # Start the SSH service
    RUN service ssh start
    ```

Il file `init_container.sh` nel codice di esempio contiene le istruzioni per inizializzare il contenitore quando viene eseguito. Il file Docker usa le istruzioni [COPY](https://docs.docker.com/engine/reference/builder/#copy), [RUN](https://docs.docker.com/engine/reference/builder/#run) e [CMD](https://docs.docker.com/engine/reference/builder/#cmd) per avviare correttamente lo script `init_container.sh`.

```docker
# Copy init_container.sh to the /bin directory
COPY init_container.sh /bin/

# Run the chmod command to change permissions on above file in the /bin directory
RUN chmod 755 /bin/init_container.sh

# run commands in init_container.sh
CMD ["/bin/init_container.sh"]
```

È possibile verificare che determinate applicazioni siano in esecuzione nel contenitore. Per ispezionare il contenitore e verificare i processi in esecuzione, iniziare aprendo un browser e passando a `https://<app name>.scm.azurewebsites.net/webssh/host`. Si viene quindi reindirizzati a una pagina che visualizza una console interattiva. Eseguire il comando `top` al prompt.

```bash
top
```

Il comando `top` espone tutti i processi in esecuzione in un contenitore.

```bash
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

Congratulazioni. Si è configurata un'immagine Docker personalizzata per un'app Web di Azure per contenitori.

## <a name="push-a-docker-image-to-private-registry-optional"></a>Eseguire il push di un'immagine Docker in un registro privato (facoltativo)

Registro contenitori di Azure è un servizio Docker gestito di Azure per l'hosting di immagini private. Le distribuzioni possono essere di qualsiasi tipo, inclusi [Docker Swarm](https://docs.docker.com/engine/swarm/), [Kubernetes](https://kubernetes.io/) e contenitori di servizi app di Azure. Usare Registro contenitori di Azure è come usare un registro privato, quindi se è necessario usare il proprio registro privato, i passaggi per completare questa attività saranno simili.

Usare il comando [az acr create](https://docs.microsoft.com/cli/azure/acr#az_acr_create) per creare un registro contenitori di Azure. Passare il nome, il gruppo di risorse e `Basic` per lo SKU. Gli SKU disponibili sono `Classic`, `Basic`, `Standard` e `Premium`.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

La creazione di un contenitore genera l'output seguente:

```bash
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/{azure-container-registry-name>",
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

Per eseguire il push di un'immagine nel registro, è necessario specificare le credenziali in modo che il registro accetti il push. È possibile recuperare queste credenziali usando il comando [az acr show](https://docs.microsoft.com/cli/azure/acr/credential#az_acr_credential_show). 

```azurecli-interactive
az acr credential show --name {azure-container-registry-name}
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
  "username": "<azure-container-registry-name>"
}
```

Dopo avere ottenuto le credenziali necessarie, accedere a Registro contenitori di Azure usando il comando `docker login`. Per accedere al registro, è necessario l'URL. Usare il formato `http://{azure-container-registry-name>.azureacr.io`.

```bash
docker login <azure-container-registry-name>.azureacr.io --username <azure-container-registry-name> --password <password> 
```

Verificare che l'accesso sia riuscito. Eseguire il push dell'immagine usando il comando `docker push` e contrassegnando l'immagine con l'URL completo del registro seguito dal nome dell'immagine e dal tag.

```bash
docker push http://<azure-container-registry-name>.azureacr.io/mydockerimage:v1.0.0
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

## <a name="configure-web-app-to-use-docker-container-from-a-private-registry-optional"></a>Configurare l'app Web per usare il contenitore Docker da un registro privato (facoltativo)

È possibile configurare le app Web in Linux in modo che eseguano un contenitore archiviato in Registro contenitori di Azure. Usare Registro contenitori di Azure è come usare un registro privato, quindi se è necessario usare il proprio registro privato, i passaggi per completare questa attività saranno simili.

Il comando [az acr credential show](https://docs.microsoft.com/cli/azure/acr/credential#az_acr_credential_show) visualizza le password per il registro contenitori. Copiare il nome utente e una delle password per poterli usare per configurare l'app Web nel passaggio successivo.

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
  "username": "<azure-container-registry-name>"
}
```

Eseguire il comando [az webapp config container set](https://docs.microsoft.com/cli/azure/webapp/config/container#az_webapp_config_container_set). Questo comando assegna l'immagine Docker personalizzata all'app Web. Si noti che è necessario un URL nel formato `https://{your-registry-username}.azurecr.io`. Per fare in modo che l'App Web acceda al registro contenitori, sono anche necessari il nome utente e la password ottenuti in un passaggio a precedente.

```azurecli-interactive
az webapp config container set --name <web-app-name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

> [!NOTE]
> È necessario `https` nell'URL del registro.
>

Il comando mostra un output simile alla stringa JSON seguente, indicante che la modifica alla configurazione è riuscita:

```bash
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "mycontainerregistry.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "{azure-container-registry-name}"
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

[Azure App Service Web App for Containers FAQ (Domande frequenti sulle app Web per contenitori del servizio app di Azure)](app-service-linux-faq.md)

