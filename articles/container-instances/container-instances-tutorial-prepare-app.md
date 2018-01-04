---
title: Esercitazione su Istanze di contenitore di Azure - Preparare l'app
description: 'Azure istanze di contenitori esercitazione parte 1 di 3: preparare un''app per la distribuzione in istanze di contenitori di Azure'
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: fc16be80e776d1472be775fa32354ba157d16545
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="create-container-for-deployment-to-azure-container-instances"></a>Creare un contenitore per la distribuzione in Istanze di contenitore di Azure

Istanze di contenitore di Azure consente la distribuzione di contenitori Docker nell'infrastruttura di Azure senza effettuare il provisioning di macchine virtuali o adottare servizi di livello superiore. In questa esercitazione viene compilata una piccola applicazione Web in Node.js e ne viene creato un pacchetto in un contenitore che può essere eseguito usando Istanze di contenitore di Azure.

In questo articolo, parte della serie, è:

> [!div class="checklist"]
> * Clonare il codice sorgente dell'applicazione da GitHub
> * Creare un'immagine contenitore dall'origine dell'applicazione
> * Testare l'immagine in un ambiente locale in Docker

Nelle esercitazioni successive l'immagine viene caricata in un Registro contenitori di Azure e quindi distribuita in Istanze di contenitore di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Questa esercitazione, è necessario che sia in esecuzione l'interfaccia CLI di Azure versione 2.0.23 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario installare o eseguire l'aggiornamento, vedere [installare Azure CLI 2.0][azure-cli-install].

Questa esercitazione presuppone una conoscenza di base dei concetti principali di Docker, come contenitori, immagini dei contenitore e comandi essenziali di `docker`. Se necessario, vedere [Introduzione a Docker] [ docker-get-started] per una panoramica sui concetti fondamentali di contenitore.

Per completare questa esercitazione, è necessario un ambiente di sviluppo Docker installato localmente. Docker offre pacchetti che consente di configurare facilmente Docker in qualsiasi [Mac][docker-mac], [Windows][docker-windows], o [Linux] [ docker-linux] sistema.

Azure Cloud Shell non include i componenti di Docker necessari per completare ogni passaggio di questa esercitazione. Nel computer locale per completare questa esercitazione, è necessario installare l'ambiente di sviluppo CLI di Azure e Docker.

## <a name="get-application-code"></a>Ottenere il codice dell'applicazione

L'esempio in questa esercitazione include una semplice applicazione web incorporata [Node.js][nodejs]. L'app gestisce una pagina HTML statica ed è simile alla seguente:

![App dell'esercitazione visualizzata in un browser][aci-tutorial-app]

Usare Git per scaricare l'esempio:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>Compilare l'immagine del contenitore

Il documento Dockerfile fornito nel repository di esempio illustra come viene compilato il contenitore. Viene avviato da un [ufficiale immagine Node.js] [ docker-hub-nodeimage] in base a [Linux Alpine][alpine-linux], una distribuzione di piccole dimensioni che è adatta per l'utilizzo contenitori. Copia quindi i file dell'applicazione nel contenitore, installa le dipendenze usando Gestione pacchetti del nodo e infine avvia l'applicazione.

```Dockerfile
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/* /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Utilizzare il [compilazione docker] [ docker-build] comando per creare l'immagine contenitore, come la codifica *aci-esercitazione-app*:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

Output di [compilazione docker] [ docker-build] comando è simile al seguente (troncato per migliorare la leggibilità):

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

Utilizzare il [immagini docker] [ docker-images] comando per visualizzare l'immagine incorporata:

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
