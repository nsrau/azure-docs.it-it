---
title: Usare Docker Compose per distribuire più contenitori
titleSuffix: Azure Cognitive Services
description: Informazioni su come distribuire più contenitori di servizi cognitivi. Questo articolo illustra come orchestrare più immagini del contenitore Docker usando Docker Compose.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 95ec80af88e0b89f61bebed08f4b96a09947f401
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311543"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>Usare Docker Compose per distribuire più contenitori

Questo articolo illustra come distribuire più contenitori di servizi cognitivi di Azure. In particolare, si apprenderà come usare Docker Compose per orchestrare più immagini del contenitore docker.

> [Docker compose](https://docs.docker.com/compose/) è uno strumento per la definizione e l'esecuzione di applicazioni Docker con più contenitori. In compose si usa un file YAML per configurare i servizi dell'applicazione. Quindi, è possibile creare e avviare tutti i servizi dalla configurazione eseguendo un unico comando.

Può essere utile per orchestrare più immagini del contenitore in un singolo computer host. In questo articolo verrà riunito il riconoscimento del testo e i contenitori di riconoscimento form.

## <a name="prerequisites"></a>Prerequisiti

Per questa procedura sono necessari diversi strumenti che devono essere installati ed eseguiti localmente:

* Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Motore Docker](https://www.docker.com/products/docker-engine). Verificare che l'interfaccia della riga di comando di Docker funzioni in una finestra della console.
* Una risorsa di Azure con il piano tariffario corretto. Con questo contenitore funzionano solo i piani tariffari seguenti:
  * **Visione artificiale** risorse solo con F0 o il piano tariffario standard.
  * **Creare** una risorsa di riconoscimento con F0 o solo il piano tariffario standard.
  * Risorsa **Servizi cognitivi** con piano tariffario S0.

## <a name="request-access-to-the-container-registry"></a>Richiedere l'accesso al registro contenitori

Completare e inviare il [modulo di richiesta dei contenitori di sintesi vocale di servizi cognitivi](https://aka.ms/speechcontainerspreview/). 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>File Docker Compose

Il file YAML definisce tutti i servizi da distribuire. Questi servizi si basano su `DockerFile` un'immagine del contenitore o esistente. In questo caso, verranno usate due immagini di anteprima. Copiare e incollare il file YAML seguente e salvarlo come *Docker-compose. YAML*. Specificare i valori **APIKEY**, **Billing**e **EndpointUri** appropriati nel file.

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
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    environment:
      eula: accept
      apikey: # < Your recognize text API key >
      billing: # < Your recognize text billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Creare le directory nel computer host specificate nel nodo **volumi** . Questo approccio è necessario perché le directory devono esistere prima di provare a montare un'immagine usando i binding del volume.

## <a name="start-the-configured-docker-compose-services"></a>Avviare i servizi Docker Compose configurati

Un file di Docker Compose consente la gestione di tutte le fasi del ciclo di vita di un servizio definito: avvio, arresto e ricompilazione dei servizi; visualizzazione dello stato del servizio; e streaming di log. Aprire un'interfaccia della riga di comando dalla directory del progetto (in cui si trova il file Docker-compose. YAML).

> [!NOTE]
> Per evitare errori, verificare che il computer host condivida correttamente le unità con il motore docker. Ad esempio, se E:\publicpreview viene usato come una directory nel file Docker-compose. YAML, condividere l'unità E con Docker.

Dall'interfaccia della riga di comando, eseguire il comando seguente per avviare (o riavviare) tutti i servizi definiti nel file Docker-compose. YAML:

```console
docker-compose up
```

La prima volta che Docker esegue il comando **Docker-compose up** usando questa configurazione, estrae le immagini configurate nel nodo **Servizi** e quindi le Scarica e le monta:

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

Dopo aver scaricato le immagini, vengono avviati i servizi immagini:

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

Di seguito è riportato un esempio di output:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text    latest
```

### <a name="test-the-recognize-text-container"></a>Testare il contenitore di riconoscimento del testo

Aprire un browser nel computer host e passare a **localhost** usando la porta specificata dal file Docker-compose. YAML, ad esempio http://localhost:5021/swagger/index.html. È possibile usare la funzionalità "prova" nell'API per testare l'endpoint riconoscimento del testo.

![Contenitore di riconoscimento del testo](media/recognize-text-swagger-page.png)

### <a name="test-the-form-recognizer-container"></a>Testare il contenitore dei form Recognizer

Aprire un browser nel computer host e passare a **localhost** usando la porta specificata dal file Docker-compose. YAML, ad esempio http://localhost:5010/swagger/index.html. È possibile usare la funzionalità "prova" nell'API per testare l'endpoint del sistema di riconoscimento del modulo.

![Contenitore riconoscimento form](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Contenitori di servizi cognitivi](../cognitive-services-container-support.md)
