---
title: Esercitazione - Modificare i moduli Analisi video live di Azure IoT Edge
description: Questa esercitazione mostra come modificare e creare i moduli del gateway Analisi video live usati dal modello di applicazione Analisi video - rilevamento movimento e oggetti.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: c2f6f386f4a8ea062980c0efc97d0cfb4f37f4f2
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92124895"
---
# <a name="tutorial-modify-and-build-the-live-video-analytics-gateway-modules"></a>Esercitazione: Modificare e creare i moduli del gateway Analisi video live

Questa esercitazione mostra come modificare il codice del modulo IoT Edge per i moduli Analisi video live (LVA, Live Video Analytics).

Nelle esercitazioni precedenti vengono usate immagini predefinite dei moduli.

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura di questa esercitazione, è necessario quanto segue:

* [Node.js](https://nodejs.org/en/download/) versione 10 o successiva
* [Visual Studio Code](https://code.visualstudio.com/Download) con l'estensione [TSLint](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-tslint-plugin) installata
* Motore [Docker](https://www.docker.com/products/docker-desktop)
* Un'istanza di [Registro Azure Container](../../container-registry/index.yml) per ospitare le versioni personalizzate dei moduli.
* Un account di [Servizi multimediali di Azure](../../media-services/index.yml). Se sono state completate le esercitazioni precedenti, è possibile riutilizzare quello creato in precedenza.

## <a name="clone-the-repository"></a>Clonare il repository

Se il repository non è ancora stato clonato, usare il comando seguente per clonarlo in una posizione adatta nel computer locale:

```cmd
git clone https://github.com/Azure/live-video-analytics
```

Aprire la cartella del repository *live-video-analytics* locale con VS Code.

## <a name="edit-the-deploymentamd64json-file"></a>Modificare il file deployment.amd64.json

1. Se non è ancora stato fatto, creare una cartella denominata *ref-apps/lva-edge-iot-central-gateway/storage* nella copia locale del repository **lva-gateway**. Questa cartella viene ignorata da Git in modo da impedire l'archiviazione accidentale di informazioni riservate.
1. Copiare il file *deployment.amd64.json* dalla cartella *setup* alla cartella *storage*.
1. In VS Code aprire il file *storage/deployment.amd64.json*.
1. Modificare la sezione `registryCredentials` per aggiungere le credenziali di Registro Azure Container.
1. Modificare la sezione del modulo `LvaEdgeGatewayModule` per aggiungere il nome dell'immagine e il nome dell'account AMS in `env:amsAccountName:value`.
1. Modificare la sezione del modulo `lvaYolov3` e aggiungere il nome dell'immagine.
1. Modificare la sezione del modulo `lvaEdge` e aggiungere il nome dell'immagine.
1. Per informazioni su come completare la configurazione, vedere [Creare un'applicazione di analisi video in Azure IoT Central](tutorial-video-analytics-create-app-yolo-v3.md).

## <a name="build-the-code"></a>Compilare il codice

1. Prima di provare a compilare il codice per la prima volta, usare il terminale di VS Code per eseguire il comando `npm install`. Questo comando installa i pacchetti richiesti ed esegue gli script di installazione.

    > [!TIP]
    > Se si esegue il pull di una versione più recente del repository GitHub, eliminare la cartella *node_modules* ed eseguire di nuovo `npm install`.

1. Modificare il file *./setup/imageConfig.json* per aggiornare il nome dell'immagine in base al nome del registro contenitori:

    ```json
    {
        "arch": "amd64",
        "imageName": "[Server].azurecr.io/lva-edge-gateway"
    }
    ```

1. Usare il terminale di VS Code per eseguire il comando `docker login [your server].azurecr.io`. Usare le stesse credenziali fornite nel manifesto della distribuzione per i moduli.

1. Usare il terminale di VS Code per eseguire il comando **npm version patch**. Questo script di compilazione distribuisce le immagini nel registro contenitori. L'output nella finestra del terminale di VS Code indica se la compilazione ha avuto esito positivo.

1. La versione dell'immagine **LvaEdgeGatewayModule** viene incrementata ogni volta che la compilazione viene completata. È necessario usare questa versione nel file manifesto della distribuzione.

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state apprese le informazioni sul modello di applicazione Analisi video - rilevamento movimento e oggetti e sui moduli Analisi video live di IoT Edge, il passaggio successivo consigliato consiste nell'acquisire altre informazioni su:

> [!div class="nextstepaction"]
> [Creazione di soluzioni per la vendita al dettaglio con Azure IoT Central](overview-iot-central-retail.md)