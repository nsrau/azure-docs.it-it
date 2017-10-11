---
title: Esercitazione su Istanze di contenitore di Azure - Preparare l'app | Azure Docs
description: Preparare un'app per la distribuzione in Istanze di contenitore di Azure
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: ca4cd00b3e9e58fd1137b896e7aac96549bf6d05
ms.contentlocale: it-it
ms.lasthandoff: 09/29/2017

---

# <a name="create-container-for-deployment-to-azure-container-instances"></a>Creare un contenitore per la distribuzione in Istanze di contenitore di Azure

Istanze di contenitore di Azure consente la distribuzione di contenitori Docker nell'infrastruttura di Azure senza effettuare il provisioning di macchine virtuali o adottare servizi di livello superiore. In questa esercitazione si compilerà una semplice applicazione Web in Node.js e si creerà un pacchetto in un contenitore che può essere eseguito usando Istanze di contenitore di Azure. Verranno illustrati gli argomenti seguenti:

> [!div class="checklist"]
> * Clonazione dell'origine applicazione da GitHub  
> * Creazione di immagini del contenitore dall'origine applicazione
> * Test delle immagini in un ambiente Docker locale

Nelle esercitazioni successive si caricherà l'immagine in un Registro contenitori di Azure e quindi la si distribuirà in Istanze di contenitore di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Questa esercitazione presuppone una conoscenza di base dei concetti principali di Docker, come contenitori, immagini dei contenitore e comandi essenziali. Se necessario, vedere [Introduzione a Docker]( https://docs.docker.com/get-started/) per una panoramica sui concetti fondamentali relativi al contenitore. 

Per completare questa esercitazione è necessario un ambiente di sviluppo Docker. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) o [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell non include i componenti di Docker necessari per completare ogni passaggio di questa esercitazione. È pertanto consigliabile usare un ambiente di sviluppo completo di Docker.

## <a name="get-application-code"></a>Ottenere il codice dell'applicazione

L'esempio in questa esercitazione include una semplice applicazione Web compilata in [Node.js](http://nodejs.org). L'app gestisce una pagina HTML statica ed è simile alla seguente:

![App dell'esercitazione visualizzata in un browser][aci-tutorial-app]

Usare Git per scaricare l'esempio:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>Compilare l'immagine del contenitore

Il documento Dockerfile fornito nel repository di esempio illustra come viene compilato il contenitore. Viene avviato da un'[immagine Node.js ufficiale][dockerhub-nodeimage] basata su [Alpine Linux](https://alpinelinux.org/), una distribuzione di piccole dimensioni particolarmente adatta per l'uso con i contenitori. Copia quindi i file dell'applicazione nel contenitore, installa le dipendenze usando Gestione pacchetti del nodo e infine avvia l'applicazione.

```
FROM node:8.2.0-alpine
RUN mkdir -p /usr/src/app
COPY ./app/* /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Usare il comando `docker build` per creare l'immagine del contenitore, assegnandole il tag *aci-tutorial-app*:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

Usare `docker images` per visualizzare l'immagine compilata:

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

<!-- LINKS -->
[dockerhub-nodeimage]: https://hub.docker.com/r/library/node/tags/8.2.0-alpine/

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png
