---
title: Uso di Registro contenitori di Azure con un cluster DC/OS | Microsoft Docs
description: Usare Registro contenitori di Azure con un cluster DC/OS nel servizio contenitore di Azure
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service, acr, azure-container-registry
keywords: Docker, contenitori, micro-servizi, Mesos, Azure, FileShare, CIFS
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: juliens
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: a8a3716f8d03b596285026426c7514b7b642cb25
ms.lasthandoff: 03/31/2017


---
# <a name="use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>Usare Registro contenitori di Azure con un cluster DC/OS per distribuire l'applicazione

Questo articolo illustra come usare un registro contenitori privato, ad esempio Registro contenitori di Azure, con un cluster DC/OS (cluster di controller di dominio o del sistema operativo). L'uso di Registro contenitori di Azure consente di archiviare le immagini privatamente e mantenerne il controllo, ad esempio sulle le versioni e/o sugli aggiornamenti.

Prima di eseguire questo esempio, è necessario: 
* Un cluster DC/OS configurato nel servizio contenitore di Azure. Vedere [Distribuire un cluster del servizio contenitore di Azure](container-service-deployment.md).
* Un servizio contenitore di Azure distribuito. Vedere [Creare un registro contenitori Docker privati con il Portale di Azure](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal) o [Creare un registro contenitori Docker privati usando l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli)
* Una condivisione di file configurata all'interno del cluster DC/OS. Vedere [Creare e montare una condivisione di file per un cluster DC/OS](container-service-dcos-fileshare.md)
* Capire come distribuire un'immagine Docker in un cluster DC/OS tramite l'[interfaccia utente Web](container-service-mesos-marathon-ui.md) o l'[API REST](container-service-mesos-marathon-rest.md)

## <a name="manage-the-authentication-inside-your-cluster"></a>Gestire l'autenticazione all'interno del cluster

Il modo convenzionale per effettuare il push e pull di un'immagine da un registro privato consiste prima nell'eseguire l'autenticazione. A tale scopo, è necessario usare la riga di comando `docker login` in qualsiasi processo client di Docker che deve usare il Registro di sistema privato.
Nell'ambiente di produzione che usa DC/OS, si vuole essere sicuri di poter effettuare il pull di immagini da qualsiasi nodo. Ciò significa che si vuole automatizzare il processo di autenticazione ed evitare di eseguire la riga di comando in ogni computer, perché, come è facile immaginare, a seconda delle dimensioni del cluster, potrebbe essere un'operazione problematica e complessa. 

Supponendo che è già stata [impostata una condivisione di file all'interno del DC/OS](container-service-dcos-fileshare.md), la condivisione verrà usata in questo modo:

### <a name="from-any-client-machine-recommended-method"></a>Da qualsiasi computer cliente (metodo consigliato)

I seguenti comandi sono eseguibili in tutti gli ambienti (Windows/Mac/Linux):

1. Assicurarsi che vengano soddisfatti i prerequisiti seguenti:
  * Strumento TAR
    * [Windows](http://gnuwin32.sourceforge.net/packages/gtar.htm)
  * Docker 
    * [Windows](https://www.docker.com/docker-windows)
    * [MAC](https://www.docker.com/docker-mac)
    * [Ubuntu](https://www.docker.com/docker-ubuntu)
    * [Altro](https://www.docker.com/get-docker)
  * Condivisione di file montata all'interno del cluster [con il metodo seguente](container-service-dcos-fileshare.md)

2. Avviare l'autenticazione al servizio ACR usando il comando seguente con il terminale preferito: `sudo docker login --username=<USERNAME> --password=<PASSWORD> <ACR-REGISTRY-NAME>.azurecr.io`. È necessario sostituire le variabili `USERNAME`, `PASSWORD` e `ACR-REGISTRY-NAME` con i valori specificati nel Portale di Azure

3. È interessante sapere che quando si esegue un operazione `docker login`, i valori vengono archiviati localmente nel computer nella cartella principale (`cd ~/.docker` in Mac e Linux o `cd %HOMEPATH%` in Windows). Il contenuto di questa cartella verrà compresso tramite il comando `tar czf`.

4. Il passaggio finale consiste nel copiare il file TAR appena creato all'interno della condivisione file [creata come prerequisito](container-service-dcos-fileshare.md). È possibile eseguire questa operazione tramite l'interfaccia della riga di comando di Azure con il comando seguente `az storage file upload -s <shareName> --account-name <storageAccountName> --account-key <storageAccountKey> -source <pathToTheTarFile>`

Per concludere, di seguito viene illustrato un esempio con le seguenti impostazioni (in un ambiente Windows):
* Nome ACR: **`demodcos`**
* Nome utente: **`demodcos`**
* Password: **`+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0`**
* Nome account di archiviazione: **`anystorageaccountname`**
* Chiave account di archiviazione: **`aYGl6Nys4De5J3VPldT1rXxz2+VjgO7dgWytnoWClurZ/l8iO5c5N8xXNS6mpJhSc9xh+7zkT7Mr+xIT4OIVMg==`**
* Nome condivisione creata all'interno dell'account di archiviazione: **`share`**
* Percorso dell'archivio TAR da caricare: **`%HOMEPATH%/.docker/docker.tar.gz`**

```bash
# Changing directory to the home folder of the default user
cd %HOMEPATH%

# Authentication into my ACR
docker login --username=demodcos --password=+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0 demodcos.azurecr.io

# Tar the contains of the .docker folder
tar czf docker.tar.gz .docker

# Upload the tar archive in the fileshare
az storage file upload -s share --account-name anystorageaccountname --account-key aYGl6Nys4De5J3VPldT1rXxz2+VjgO7dgWytnoWClurZ/l8iO5c5N8xXNS6mpJhSc9xh+7zkT7Mr+xIT4OIVMg== --source %HOMEPATH%/docker.tar.gz
```

### <a name="from-the-master-not-recommended-method"></a>Dal master (metodo non consigliato)

Non è consigliabile eseguire le operazione dal master per evitare errori e conseguenze sugli ambienti interi.

1. Innanzitutto, usare SSH nel master (o il primo master) del cluster basato su DC/OS. Ad esempio, `ssh userName@masterFQDN –A –p 22`, dove masterFQDN è il nome di dominio completo della macchina virtuale master. [Per altre informazioni, fare clic qui](https://docs.microsoft.com/azure/container-service/container-service-connect#connect-to-a-dcos-or-swarm-cluster)

2. Avviare l'autenticazione al servizio Registro contenitori di Azure usando il comando seguente: `sudo docker login --username=<USERNAME> --password=<PASSWORD> <ACR-REGISTRY-NAME>.azurecr.io`. È necessario sostituire le variabili `USERNAME`, `PASSWORD` e `ACR-REGISTRY-NAME` con i valori specificati nel Portale di Azure

3. È interessante sapere che quando si esegue un operazione `docker login`, i valori vengono archiviati localmente nel computer nella cartella principale `~/.docker`. Il contenuto di questa cartella verrà compresso tramite il comando `tar czf`.

4. Il passaggio finale consiste nel copiare il file TAR appena creato, all'interno della condivisione file. Questa operazione consentirà di usare queste credenziali ed essere autenticati nel Registro contenitori di Azure, in tutte le macchine virtuali all'interno del cluster.

Per concludere, di seguito viene illustrato un esempio con le impostazioni seguenti:
* Nome ACR: **`demodcos`**
* Nome utente: **`demodcos`**
* Password: **`+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0`**
* Punto di montaggio all'interno del cluster: **`/mnt/share`**

```bash
# Changing directory to the home folder of the default user
cd ~

# Authentication into my ACR
sudo docker login --username=demodcos --password=+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0 demodcos.azurecr.io

# Tar the contains of the .docker folder
sudo tar czf docker.tar.gz .docker

# Copy of the tar file in the file share of my cluster
sudo cp docker.tar.gz /mnt/share
```


## <a name="deploy-an-image-from-acr-with-marathon"></a>Distribuire un'immagine da Registro contenitori di Azure con Marathon

Presumibilmente è già stato eseguito il push delle immagini che si vuole distribuire all'interno del registro contenitori. Vedere [Effettuare il push della prima immagine in un registro contenitori Docker privati tramite l'interfaccia della riga di comando di Docker](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli)

Si supponga si voglia distribuire l'immagine **simple-web** con il tag **2.1** dal registro privato ospitato in Registro contenitori di Azure. A tale scopo verrà usata la configurazione seguente:

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "demodcos.azurecr.io/simple-web:2.1",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  },
  "uris":  [
       "file:///mnt/share/docker.tar.gz"
   ]
}
```

> [!NOTE] 
> Come si può notare, viene usata l'opzione **uris** per specificare dove sono archiviate le credenziali.
>

## <a name="next-steps"></a>Passaggi successivi
* Sono disponibili altre informazioni sulla [gestione dei contenitori DC/OS](container-service-mesos-marathon-ui.md).
* Gestione dei contenitori DC/OS tramite l'[API REST Marathon](container-service-mesos-marathon-rest.md).
