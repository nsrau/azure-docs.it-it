---
title: Usare Docker Compose per distribuire più contenitori
titleSuffix: Azure Cognitive Services
description: Informazioni su come distribuire più contenitori di Servizi cognitivi. In questo articolo viene illustrato come orchestrare più immagini contenitore Docker utilizzando Docker Compose.This article shows you how to orchestrate multiple Docker container images by using Docker Compose.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: bfbaa03469ee04ff900a215aadd8c814efcba761
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037529"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>Usare Docker Compose per distribuire più contenitori

Questo articolo illustra come distribuire più contenitori di Servizi cognitivi di Azure.This article shows you how to deploy multiple Azure Cognitive Services containers. In particolare, si apprenderà come utilizzare Docker Compose per orchestrare più immagini del contenitore Docker.Specifically, you'll learn how to use Docker Compose to orchestrate multiple Docker container images.

> [Docker Compose](https://docs.docker.com/compose/) è uno strumento per la definizione e l'esecuzione di applicazioni Docker multi-contenitore. In Componi si utilizza un file YAML per configurare i servizi dell'applicazione. Quindi, creare e avviare tutti i servizi dalla configurazione eseguendo un singolo comando.

Può essere utile orchestrare più immagini contenitore in un singolo computer host. In questo articolo verranno riuniti i contenitori Read e Form Recognizer.

## <a name="prerequisites"></a>Prerequisiti

Questa procedura richiede diversi strumenti che devono essere installati ed eseguiti localmente:

* Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Motore Docker](https://www.docker.com/products/docker-engine). Verificare che l'interfaccia della riga di comando Docker funzioni in una finestra della console.
* Una risorsa di Azure con il piano tariffario corretto. Solo i seguenti piani tariffari funzionano con questo contenitore:
  * **Risorsa Visione artificiale** solo con il piano tariffario F0 o Standard.Computer Vision resource with F0 or Standard pricing tier only.
  * **Risorsa riconoscitore** di moduli solo con il piano tariffario F0 o Standard.Form Recognizer resource with F0 or Standard pricing tier only.
  * Risorsa **Servizi cognitivi** con piano tariffario S0.

## <a name="request-access-to-the-container-registry"></a>Richiedere l'accesso al registro contenitori

Completare e inviare il [modulo di richiesta contenitori di riconoscimento vocale](https://aka.ms/speechcontainerspreview/)di Servizi cognitivi . 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>File Compose docker

Il file YAML definisce tutti i servizi da distribuire. Questi servizi si `DockerFile` basano su un'immagine contenitore o un'immagine del contenitore esistente. In questo caso, useremo due immagini di anteprima. Copiare e incollare il file YAML seguente e salvarlo come *docker-compose.yaml*. Specificare i valori **apikey,** **billing**ed **EndpointUri** appropriati nel file.

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
         source: E:\publicpreview\output
         target: /output
       - type: bind
         source: E:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-read"
    environment:
      eula: accept
      apikey: # < Your computer vision API key >
      billing: # < Your computer vision billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Creare nel computer host le directory specificate nel nodo **dei volumi.** Questo approccio è necessario perché le directory devono esistere prima di provare a montare un'immagine utilizzando le associazioni di volume.

## <a name="start-the-configured-docker-compose-services"></a>Avviare i servizi Docker Compose configurati

Un file Docker Compose consente la gestione di tutte le fasi del ciclo di vita di un servizio definito: avvio, arresto e ricostruzione dei servizi; visualizzazione dello stato del servizio; e log streaming. Aprire un'interfaccia della riga di comando dalla directory del progetto (dove si trova il file docker-compose.yaml).

> [!NOTE]
> Per evitare errori, assicurarsi che il computer host condivida correttamente le unità con Docker Engine. Se, ad esempio, come directory nel file *docker-compose.yaml* viene utilizzato **E** *E:.*

Dall'interfaccia della riga di comando, eseguire il comando seguente per avviare (o riavviare) tutti i servizi definiti nel file *docker-compose.yaml:*

```console
docker-compose up
```

La prima volta che Docker esegue il comando **docker-compose up** utilizzando questa configurazione, estrae le immagini configurate sotto il nodo **dei servizi** e quindi le scarica e le monta:

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
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-read:)...
latest: Pulling from microsoft/cognitive-services-read
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

Dopo aver scaricato le immagini, vengono avviati i servizi immagine:

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

## <a name="verify-the-service-availability"></a>Verificare la disponibilità del servizio

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

Ecco qualche output di esempio:Here's some example output:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-read              latest
```

### <a name="test-containers"></a>Contenitori di prova

Aprire un browser nel computer host e passare a **localhost** utilizzando la porta specificata dal file http://localhost:5021/swagger/index.html *docker-compose.yaml,* ad esempio . Ad esempio, è possibile utilizzare la funzionalità **Prova** nell'API per testare l'endpoint del riconoscimento moduli. Entrambe le pagine swagger contenitore devono essere disponibili e texabili.

![Contenitore riconoscitore di moduli](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Contenitori di Servizi cognitivi](../cognitive-services-container-support.md)
