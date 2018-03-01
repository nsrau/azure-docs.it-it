---
title: Esercitazione su Istanze di contenitore di Azure - Preparare l'app
description: Esercitazione di Istanze di contenitore di Azure - Parte 1 di 3 - Preparare un'app per la distribuzione in Istanze di contenitore di Azure
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 5012412ec642a04102836274caea253635376efb
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="create-container-for-deployment-to-azure-container-instances"></a>Creare un contenitore per la distribuzione in Istanze di contenitore di Azure

Istanze di contenitore di Azure consente la distribuzione di contenitori Docker nell'infrastruttura di Azure senza effettuare il provisioning di macchine virtuali o adottare servizi di livello superiore. In questa esercitazione viene compilata una piccola applicazione Web in Node.js e ne viene creato un pacchetto in un contenitore che può essere eseguito usando Istanze di contenitore di Azure.

In questo articolo, che corrisponde alla seconda parte della serie, è possibile eseguire queste operazioni:

> [!div class="checklist"]
> * Clonare il codice sorgente dell'applicazione da GitHub
> * Creazione di un'immagine del contenitore dall'origine applicazione
> * Test dell'immagine in un ambiente Docker locale

Nelle esercitazioni successive l'immagine viene caricata in un Registro contenitori di Azure e quindi distribuita in Istanze di contenitore di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.23 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0][azure-cli-install].

Questa esercitazione presuppone una conoscenza di base dei concetti principali di Docker, come contenitori, immagini dei contenitore e comandi essenziali di `docker`. Se necessario, vedere [Introduzione a Docker][docker-get-started] per una panoramica sui concetti fondamentali relativi al contenitore.

Per completare questa esercitazione è necessario un ambiente di sviluppo Docker installato localmente. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [Mac][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].

Azure Cloud Shell non include i componenti di Docker necessari per completare ogni passaggio di questa esercitazione. È necessario installare l'interfaccia della riga di comando di Azure e l'ambiente di sviluppo Docker nel computer locale per completare questa esercitazione.

## <a name="get-application-code"></a>Ottenere il codice dell'applicazione

L'esempio in questa esercitazione include una semplice applicazione Web compilata in [Node.js][nodejs]. L'app gestisce una pagina HTML statica ed è simile alla seguente:

![App dell'esercitazione visualizzata in un browser][aci-tutorial-app]

Usare Git per scaricare l'esempio:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>Compilare l'immagine del contenitore

Il documento Dockerfile fornito nel repository di esempio illustra come viene compilato il contenitore. Viene avviato da un'[immagine Node.js ufficiale][docker-hub-nodeimage] basata su [Alpine Linux][alpine-linux], una distribuzione di piccole dimensioni particolarmente adatta per l'uso con i contenitori. Copia quindi i file dell'applicazione nel contenitore, installa le dipendenze usando Gestione pacchetti del nodo e infine avvia l'applicazione.

```Dockerfile
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/ /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Usare il comando [docker build][docker-build] per creare l'immagine del contenitore, assegnandole il tag *aci-tutorial-app*:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

L'output dal comando [docker build][docker-build] sarà simile al seguente (troncato per una migliore leggibilità):

```bash
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.9.3-alpine
8.9.3-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.9.3-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

Usare il comando [docker images][docker-images] per visualizzare l'immagine compilata:

```bash
docker images
```

Output:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

## <a name="run-the-container-locally"></a>Eseguire il contenitore in locale

Prima di provare a distribuire il contenitore in Istanze di contenitore di Azure, eseguirlo in locale per verificarne il funzionamento. L'opzione `-d` consente di eseguire il contenitore in background, mentre `-p` consente di eseguire il mapping di una porta arbitraria del computer alla porta 80 del contenitore.

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

Aprire il browser all'indirizzo http://localhost:8080 per verificare che il contenitore sia un esecuzione.

![Esecuzione locale dell'app nel browser][aci-tutorial-app-local]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'immagine del contenitore che può essere distribuita in Istanze di contenitore di Azure. Sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * Clonazione dell'origine applicazione da GitHub
> * Creazione di immagini del contenitore dall'origine applicazione
> * Test del contenitore in locale

Passare alla prossima esercitazione per apprendere informazioni sull'archiviazione delle immagini del contenitore in un Registro contenitori di Azure.

> [!div class="nextstepaction"]
> [Eseguire il push delle immagini nel Registro contenitori di Azure](./container-instances-tutorial-prepare-acr.md)

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png

<!-- LINKS - External -->
[alpine-linux]: https://alpinelinux.org/
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
