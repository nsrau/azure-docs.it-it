---
title: Docker compose recipe di contenitore
titleSuffix: Azure Cognitive Services
description: Informazioni su come distribuire più contenitori di servizi cognitivi. Questa procedura viene illustrato come orchestrare più immagini del contenitore Docker con Docker Compose.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 8afb7e866bc2a5fefe28a71653c4a2a87fdc7a5b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445786"
---
# <a name="use-multiple-containers-in-a-private-network-with-docker-compose"></a>Utilizzare più contenitori in una rete privata con Docker Compose

Informazioni su come distribuire più contenitori di servizi cognitivi. Questa procedura viene illustrato come orchestrare più immagini del contenitore Docker con Docker Compose.

> [Docker Compose](https://docs.docker.com/compose/) è uno strumento per definire ed eseguire applicazioni Docker multi-contenitore. Con Compose si usa un file YAML per configurare i servizi dell'applicazione. Quindi, con un singolo comando, si crea e avvia tutti i servizi dalla configurazione.

Quando appropriato, il coordinamento di più immagini del contenitore in un singolo computer host può essere interessante. In questo articolo, estrarremo il servizio riconoscere Text e il servizio di riconoscimento modulo tra loro.

## <a name="prerequisites"></a>Prerequisiti

Questa procedura richiede diversi strumenti che devono essere installati ed eseguiti in locale.

* Usare una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Motore Docker](https://www.docker.com/products/docker-engine) e verificare che l'interfaccia della riga di comando di Docker funzioni in una finestra della console.
* Una risorsa di Azure con il piano tariffario corretto. Non tutti i piani tariffari funzionano con questo contenitore:
  * **Visione artificiale** livelli solo di risorsa con F0 o prezzo Standard.
  * **Formare riconoscimento** livelli solo di risorsa con F0 o prezzo Standard.
  * Risorsa **Servizi cognitivi** con piano tariffario S0.

## <a name="request-access-to-the-container-registry"></a>Richiedere l'accesso al registro contenitori

Completare e inviare il [modulo di richiesta di contenitori vocale servizi cognitivi](https://aka.ms/speechcontainerspreview/) per richiedere l'accesso al contenitore. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>File docker compose

Il file YAML definisce tutti i servizi da distribuire. Questi servizi si basano su entrambi un `DockerFile` o un'immagine contenitore esistente, in questo caso si userà due immagini di anteprima. Copiare e incollare il file YAML seguente e salvarlo come *docker-Compose. yaml*. Fornire appropriato _apikey_, _fatturazione_, e _URI dell'endpoint_ i valori di _docker-Compose. yml_ file riportato di seguito.

```yaml
version: '3.7'
services:
  forms:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: e:\publicpreview\output
         target: /output
       - type: bind
         source: e:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    environment:
      eula: accept
      apikey: # < Your recognize text API key >
      billing: # < Your recognize text billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Creare le directory nel computer host che vengono specificati sotto il `volumes` nodo. Ciò è necessario perché la directory deve esistere prima di provare a montare un'immagine con le associazioni di volume.

## <a name="start-the-configured-docker-compose-services"></a>Inizio configurato docker compose servizi

Docker compose file consente la gestione di cicli tutti i definito del servizio di vita; avvio/arresto e la ricompilazione di servizi, visualizzando lo stato del servizio e lo streaming dei log. Aprire un'interfaccia della riga di comando dalla directory del progetto (in cui il *docker-Compose. yaml* file risiede).

> [!NOTE]
> Per evitare errori, verificare che il computer host in modo corretto è la condivisione delle unità con il **motore Docker**. Ad esempio, se *e:\publicpreview* viene usato come una directory nel *docker-Compose. yaml* condividono il *unità E* con docker.

Nell'interfaccia della riga di comando, eseguire il comando seguente per avviare (o riavviare) tutti i servizi definiti nel *docker-Compose. yaml*:

```console
docker-compose up
```

Il primo tempo di esecuzione la `docker-compose up` comando con questa configurazione, **Docker** effettuerà il pull di immagini configurate nel `services` nodo, e il download di montaggio li:

```console
Pulling forms (containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:)...
latest: Pulling from microsoft/cognitive-services-form-recognizer
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:)...
latest: Pulling from microsoft/cognitive-services-recognize-text
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

Le immagini vengono scaricate, quindi vengono avviati i servizi di immagine.

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>Verificare la disponibilità dei servizi

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

Di seguito è un esempio di output:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text    latest
```

### <a name="test-the-recognize-text-container"></a>Testare il contenitore di testo di recognize

Aprire un browser nel computer host e passare a `localhost` con la porta specificata dal *docker-Compose. yaml*, ad esempio, `http://localhost:5021/swagger/index.html`. È possibile usare il blocco Try delle funzionalità dell'API e testare l'endpoint di testo di recognize.

![Riconoscere Swagger di testo](media/recognize-text-swagger-page.png)

### <a name="test-the-form-recognizer-container"></a>Il contenitore di riconoscimento modulo di test

Aprire un browser nel computer host e passare a `localhost` con la porta specificata dal *docker-Compose. yaml*, ad esempio, `http://localhost:5010/swagger/index.html`. È possibile usare il blocco Try delle funzionalità dell'API e testare l'endpoint di riconoscimento di form.

![Form per il riconoscimento Swagger](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Contenitori di servizi cognitivi](../cognitive-services-container-support.md)