---
title: Esercitazione su Istanze di contenitore di Azure - Preparare l'app
description: Preparare un'app per la distribuzione in Istanze di contenitore di Azure
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 52d99411b2dc9ae9c3f2ebd3b9f346973a91e7c9
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2017
---
# <a name="create-container-for-deployment-to-azure-container-instances"></a>Creare un contenitore per la distribuzione in Istanze di contenitore di Azure

Istanze di contenitore di Azure consente la distribuzione di contenitori Docker nell'infrastruttura di Azure senza effettuare il provisioning di macchine virtuali o adottare servizi di livello superiore. In questa esercitazione viene compilata una piccola applicazione Web in Node.js e ne viene creato un pacchetto in un contenitore che può essere eseguito usando Istanze di contenitore di Azure. Argomenti trattati:

> [!div class="checklist"]
> * Clonazione dell'origine applicazione da GitHub
> * Creazione di immagini del contenitore dall'origine applicazione
> * Test delle immagini in un ambiente Docker locale

Nelle esercitazioni successive l'immagine viene caricata in un Registro contenitori di Azure e quindi distribuita in Istanze di contenitore di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.20 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli).

Questa esercitazione presuppone una conoscenza di base dei concetti principali di Docker, come contenitori, immagini dei contenitore e comandi essenziali di `docker`. Se necessario, vedere [Introduzione a Docker]( https://docs.docker.com/get-started/) per una panoramica sui concetti fondamentali relativi al contenitore.

Per completare questa esercitazione è necessario un ambiente di sviluppo Docker. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) o [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell non include i componenti di Docker necessari per completare ogni passaggio di questa esercitazione. È quindi consigliabile un'installazione locale dell'ambiente di sviluppo dell'interfaccia della riga di comando di Azure e di Docker.

## <a name="get-application-code"></a>Ottenere il codice dell'applicazione

L'esempio in questa esercitazione include una semplice applicazione Web compilata in [Node.js](http://nodejs.org). L'app gestisce una pagina HTML statica ed è simile alla seguente:

![App dell'esercitazione visualizzata in un browser][aci-tutorial-app]

Usare Git per scaricare l'esempio:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>Compilare l'immagine del contenitore

Il documento Dockerfile fornito nel repository di esempio illustra come viene compilato il contenitore. Viene avviato da un'[immagine Node.js ufficiale][dockerhub-nodeimage] basata su [Alpine Linux](https://alpinelinux.org/), una distribuzione di piccole dimensioni particolarmente adatta per l'uso con i contenitori. Copia quindi i file dell'applicazione nel contenitore, installa le dipendenze usando Gestione pacchetti del nodo e infine avvia l'applicazione.

```Dockerfile
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
[dockerhub-nodeimage]: https://store.docker.com/images/node

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png