---
title: "Esercitazione: Preparare l'immagine del contenitore per Istanze di Azure Container"
description: Esercitazione di Istanze di Azure Container - Parte 1 di 3 - Preparare un'app in un'immagine del contenitore per la distribuzione in Istanze di Azure Container
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: f5d6ac81cc2553cc4a2d7b86c21417aa5ab1d572
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57990635"
---
# <a name="tutorial-create-a-container-image-for-deployment-to-azure-container-instances"></a>Esercitazione: Creare un'immagine del contenitore per la distribuzione in Istanze di Azure Container

Istanze di Azure Container consente di eseguire la distribuzione di contenitori Docker nell'infrastruttura di Azure senza effettuare il provisioning di macchine virtuali o adottare un servizio di livello superiore. In questa esercitazione si inserisce una piccola applicazione Web Node.js in un'immagine del contenitore eseguibile con Istanze di Azure Container.

In questo articolo, che corrisponde alla seconda parte della serie, è possibile eseguire queste operazioni:

> [!div class="checklist"]
> * Clonare il codice sorgente dell'applicazione da GitHub
> * Creazione di un'immagine del contenitore dall'origine applicazione
> * Test dell'immagine in un ambiente Docker locale

Nella seconda e nella terza parte dell'esercitazione, l'immagine viene caricata in Registro Azure Container e quindi distribuita in Istanze di Azure Container.

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="get-application-code"></a>Ottenere il codice dell'applicazione

L'applicazione di esempio di questa esercitazione è una semplice app Web creata in [Node.js][nodejs]. L'applicazione fornisce una pagina HTML statica e ha un aspetto simile allo screenshot seguente:

![App dell'esercitazione visualizzata in un browser][aci-tutorial-app]

Usare Git per clonare il repository dell'applicazione di esempio:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

È anche possibile [scaricare l'archivio ZIP][aci-helloworld-zip] direttamente da GitHub.

## <a name="build-the-container-image"></a>Compilare l'immagine del contenitore

Il documento Dockerfile nell'applicazione di esempio illustra come viene compilato il contenitore. Viene avviato da un'[immagine Node.js ufficiale][docker-hub-nodeimage] basata su [Alpine Linux][alpine-linux], una distribuzione di piccole dimensioni particolarmente adatta per l'uso con i contenitori. Copia quindi i file dell'applicazione nel contenitore, installa le dipendenze usando Node Package Manager e infine avvia l'applicazione.

```Dockerfile
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/ /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Usare il comando [docker build][docker-build] per creare l'immagine del contenitore e assegnarle il tag *aci-tutorial-app*:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

L'output dal comando [docker build][docker-build] sarà simile al seguente (troncato per una migliore leggibilità):

```console
$ docker build ./aci-helloworld -t aci-tutorial-app
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

L'immagine appena compilata verrà visualizzata nell'elenco:

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 seconds ago    68.1 MB
```

## <a name="run-the-container-locally"></a>Eseguire il contenitore in locale

Prima di distribuire il contenitore in Istanze di Azure Container, usare [docker run][docker-run] per eseguirlo in locale e verificarne il funzionamento. L'opzione `-d` consente di eseguire il contenitore in background, mentre `-p` consente di eseguire il mapping di una porta arbitraria del computer alla porta 80 del contenitore.

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

Se l'esecuzione del comando `docker run` è riuscita, il relativo output mostra l'ID del contenitore in esecuzione:

```console
$ docker run -d -p 8080:80 aci-tutorial-app
a2e3e4435db58ab0c664ce521854c2e1a1bda88c9cf2fcff46aedf48df86cccf
```

Passare quindi a `http://localhost:8080` nel browser per verificare che il contenitore sia in esecuzione. Dovrebbe essere visualizzata una pagina Web simile alla seguente:

![Esecuzione locale dell'app nel browser][aci-tutorial-app-local]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'immagine del contenitore distribuibile in Istanze di Azure Container e ne è stata verificata l'esecuzione in locale. Finora, sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * Clonazione dell'origine applicazione da GitHub
> * Creazione di un'immagine del contenitore dall'origine applicazione
> * Test del contenitore in locale

Passare all'esercitazione successiva per apprendere come archiviare l'immagine del contenitore in Registro Azure Container:

> [!div class="nextstepaction"]
> [Eseguire il push dell'immagine in Registro Azure Container](container-instances-tutorial-prepare-acr.md)

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png

<!-- LINKS - External -->
[aci-helloworld-zip]: https://github.com/Azure-Samples/aci-helloworld/archive/master.zip
[alpine-linux]: https://alpinelinux.org/
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
