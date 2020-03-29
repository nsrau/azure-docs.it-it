---
title: Ricette per contenitori Docker
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare, testare e archiviare contenitori con alcune o tutte le impostazioni di configurazione per la distribuzione e il riutilizzo.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 97342f1dd4f6ce343626ba6c294f09dabe3db5c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717198"
---
# <a name="create-containers-for-reuse"></a>Creare contenitori per il riutilizzo

Usare queste ricette del contenitore per creare contenitori di servizi cognitivi che possono essere riutilizzati. I contenitori possono essere compilati con alcune o tutte le impostazioni di configurazione in modo che _non_ siano necessari all'avvio del contenitore.

Dopo aver aggiunto questo nuovo livello di contenitore (con impostazioni) e averlo testato in locale, è possibile archiviare il contenitore in un registro contenitori. All'avvio del contenitore saranno necessarie solo le impostazioni che non sono attualmente archiviate nel contenitore. Il contenitore del Registro di sistema privato fornisce spazio di configurazione per passare tali impostazioni.

## <a name="docker-run-syntax"></a>Sintassi di esecuzione di Docker

Tutti `docker run` gli esempi in questo documento `^` presuppongono una console di Windows con un carattere di continuazione di riga. Si consideri quanto segue per uso personale:Consider the following for your own use:

* Non modificare l'ordine degli argomenti se non si ha dimestichezza con i contenitori Docker.
* Se si utilizza un sistema operativo diverso da Windows o una console diversa da quella di Windows, utilizzare la console/terminale corretto, la sintassi della cartella per i supporti e il carattere di continuazione di riga per la console e il sistema.  Poiché il contenitore Servizi cognitivi è un sistema operativo Linux, il mount di destinazione usa una sintassi di cartella in stile Linux.Because the Cognitive Services container is a Linux operating system, the target mount uses a Linux-style folder syntax.
* `docker run`negli esempi viene `c:` utilizzata la directory all'indisa per evitare conflitti di autorizzazione in Windows. Se è necessario usare una directory specifica come directory di input, potrebbe essere necessario concedere l'autorizzazione per il servizio Docker.

## <a name="store-no-configuration-settings-in-image"></a>Memorizzare nessuna impostazione di configurazione nell'immagine

I `docker run` comandi di esempio per ogni servizio non archiviano le impostazioni di configurazione nel contenitore. Quando si avvia il contenitore da una console o da un servizio del Registro di sistema, è necessario passare tali impostazioni di configurazione. Il contenitore del Registro di sistema privato fornisce spazio di configurazione per passare tali impostazioni.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Riutilizza la ricetta: memorizza tutte le impostazioni di configurazione con il contenitore

Per archiviare tutte le impostazioni `Dockerfile` di configurazione, creare un con tali impostazioni.

Problemi con questo approccio:

* Il nuovo contenitore ha un nome e un tag separati dal contenitore originale.
* Per modificare queste impostazioni, è necessario modificare i valori del dockerfile, ricostruire l'immagine e ripubblicare nel Registro di sistema.
* Se un utente ottiene l'accesso al Registro di sistema del contenitore o all'host locale, può eseguire il contenitore e usare gli endpoint di Servizi cognitivi.
* Se il servizio cognitivo non richiede supporti di `COPY` input, non aggiungere le righe al Dockerfile.

Creare Dockerfile, estraendo dal contenitore servizi cognitivi esistente che si vuole usare, quindi usare i comandi docker nel dockerfile per impostare o estrarre le informazioni necessarie per il contenitore.

Questo esempio:

* Imposta l'endpoint `{BILLING_ENDPOINT}` di fatturazione dalla chiave `ENV`di ambiente dell'host utilizzando .
* Imposta la chiave API `{ENDPOINT_KEY}` di fatturazione, dalla chiave di ambiente dell'host utilizzando 'ENV.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Riutilizza la ricetta: memorizza le impostazioni di fatturazione con il contenitore

Questo esempio mostra come creare il contenitore di valutazione dell'analisi del testo da un Dockerfile.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Compilare ed eseguire il contenitore [in locale](#how-to-use-container-on-your-local-host) o dal [contenitore del Registro di sistema privato](#how-to-add-container-to-private-registry) in base alle esigenze.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Riutilizzare la ricetta: memorizzare le impostazioni di fatturazione e montaggio con il contenitore

In questo esempio viene illustrato come utilizzare La comprensione della lingua, salvando la fatturazione e i modelli dal file Docker.

* Copia il file di modello LUIS (Language Understanding) `COPY`dal file system dell'host utilizzando .
* Il contenitore LUIS supporta più di un modello. Se tutti i modelli sono memorizzati nella `COPY` stessa cartella, è necessaria una istruzione.
* Eseguire il file docker dall'elemento padre relativo della directory di input del modello. Per l'esempio seguente, eseguire i `docker build` comandi e `docker run` dall'elemento padre relativo di `/input`. Il `/input` primo `COPY` sul comando è la directory del computer host. Il `/input` secondo è la directory del contenitore.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Compilare ed eseguire il contenitore [in locale](#how-to-use-container-on-your-local-host) o dal [contenitore del Registro di sistema privato](#how-to-add-container-to-private-registry) in base alle esigenze.

## <a name="how-to-use-container-on-your-local-host"></a>Come usare il contenitore nell'host localeHow to use container on your local host

Per compilare il file `<your-image-name>` Docker, sostituire con il nuovo nome dell'immagine, quindi utilizzare:

```console
docker build -t <your-image-name> .
```

Per eseguire l'immagine e rimuoverla`--rm`quando il contenitore si arresta ( ):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Come aggiungere un contenitore al registro privatoHow to add container to private registry

Seguire questi passaggi per utilizzare dockerfile e inserire la nuova immagine nel registro contenitori privato.  

1. Creare `Dockerfile` un con il testo da riutilizzare ricetta. A `Dockerfile` non ha un'estensione.

1. Sostituire i valori nelle parentesi angolari con valori personalizzati.

1. Compilare il file in un'immagine nella riga di comando o nel terminale, utilizzando il comando seguente. Sostituire i valori tra le `<>`parentesi angolari, , con il nome e il tag del contenitore.  

    L'opzione `-t`tag, , consente di aggiungere informazioni sulle modifiche per il contenitore. Ad esempio, un `modified-LUIS` nome di contenitore indica che il contenitore originale è stato sovrapposto. Il nome `with-billing-and-model` di tag indica come è stato modificato il contenitore LuiS (Language Understanding).

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Accedere all'interfaccia della riga di comando di Azure da una console. Questo comando apre un browser e richiede l'autenticazione. Una volta autenticato, è possibile chiudere il browser e continuare a lavorare nella console.

    ```azurecli
    az login
    ```

1. Accedere al Registro di sistema privato con l'interfaccia della riga di comando di Azure da una console.

    Sostituire i valori tra le `<my-registry>`parentesi angolari, , con il proprio nome del Registro di sistema.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    È anche possibile accedere con l'account di accesso Docker se viene assegnata un'entità servizio.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Contrassegnare il contenitore con il percorso del Registro di sistema privato. Sostituire i valori tra le `<my-registry>`parentesi angolari, , con il proprio nome del Registro di sistema. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Se non si utilizza un `latest` nome di tag, è implicito.

1. Eseguire il push della nuova immagine nel registro contenitori privato. Quando si visualizza il Registro di sistema del contenitore privato, il nome del contenitore utilizzato nel comando dell'interfaccia della riga di comando seguente sarà il nome del repository.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare e usare l'istanza del contenitore di AzureCreate and use Azure Container Instance](azure-container-instance-recipe.md)

<!--
## Store input and output configuration settings

Bake in input params only

FROM containerpreview.azurecr.io/microsoft/cognitive-services-luis:<tag>
COPY luisModel1 /input/
COPY luisModel2 /input/

## Store all configuration settings

If you are a single manager of the container, you may want to store all settings in the container. The new, resulting container will not need any variables passed in to run. 

Issues with this approach:

* In order to change these settings, you will have to change the values of the Dockerfile and rebuild the file. 
* If someone gets access to your container registry or your local host, they can run the container and use the Cognitive Services endpoints. 

The following _partial_ Dockerfile shows how to statically set the values for billing and model. This example uses the 

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing=<billing value>
ENV apikey=<apikey value>
COPY luisModel1 /input/
COPY luisModel2 /input/
```

->