---
title: Ricette per i contenitori Docker
titleSuffix: Azure Cognitive Services
description: Usare queste ricette contenitore per creare contenitori di servizi cognitivi che possono essere riutilizzati. I contenitori possono essere compilati con alcune o tutte le impostazioni di configurazione in modo che non siano necessarie quando il contenitore viene avviato. Quando si dispone di questo nuovo livello di contenitore (con impostazioni) ed è stato testato localmente, è possibile archiviare il contenitore in un registro contenitori. Quando il contenitore viene avviato, saranno necessarie solo le impostazioni non attualmente archiviate nel contenitore.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: a8162f96051a73b9f6e6a6fe3ece020e0a94f08f
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70068833"
---
# <a name="create-containers-for-reuse"></a>Creare contenitori da riutilizzare

Usare queste ricette contenitore per creare contenitori di servizi cognitivi che possono essere riutilizzati. I contenitori possono essere compilati con alcune o tutte le impostazioni di configurazione in modo che _non_ siano necessarie quando il contenitore viene avviato.

Quando si dispone di questo nuovo livello di contenitore (con impostazioni) ed è stato testato localmente, è possibile archiviare il contenitore in un registro contenitori. Quando il contenitore viene avviato, saranno necessarie solo le impostazioni non attualmente archiviate nel contenitore. Il contenitore del registro di sistema privato fornisce spazio di configurazione in cui è possibile passare tali impostazioni.

## <a name="docker-run-syntax"></a>Sintassi di Docker Run

Tutti `docker run` gli esempi in questo documento presuppongono una console `^` di Windows con un carattere di continuazione di riga. Tenere presente quanto segue per uso personale:

* Non modificare l'ordine degli argomenti se non si ha dimestichezza con i contenitori Docker.
* Se si utilizza un sistema operativo diverso da Windows o una console diversa dalla console di Windows, utilizzare la console/terminale, la sintassi di cartella per i montaggi e il carattere di continuazione di riga corretti per la console e il sistema.  Poiché il contenitore di servizi cognitivi è un sistema operativo Linux, il montaggio di destinazione usa una sintassi di cartella di tipo Linux.
* `docker run`gli esempi usano la directory `c:` dall'unità per evitare eventuali conflitti di autorizzazione in Windows. Se è necessario usare una directory specifica come directory di input, potrebbe essere necessario concedere l'autorizzazione per il servizio Docker.

## <a name="store-no-configuration-settings-in-image"></a>Archivia nessuna impostazione di configurazione nell'immagine

I comandi `docker run` di esempio per ogni servizio non archiviano le impostazioni di configurazione nel contenitore. Quando si avvia il contenitore da un servizio console o registro di sistema, le impostazioni di configurazione devono essere passate. Il contenitore del registro di sistema privato fornisce spazio di configurazione in cui è possibile passare tali impostazioni.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Riutilizza la ricetta: archivia tutte le impostazioni di configurazione con il contenitore

Per archiviare tutte le impostazioni di configurazione, creare un `Dockerfile` con tali impostazioni.

Problemi con questo approccio:

* Il nuovo contenitore ha un nome e un tag separati dal contenitore originale.
* Per modificare queste impostazioni, sarà necessario modificare i valori di Dockerfile, ricompilare l'immagine e ripubblicarla nel registro.
* Se un utente ottiene l'accesso al registro contenitori o all'host locale, può eseguire il contenitore e usare gli endpoint di servizi cognitivi.
* Se il servizio cognitivo non richiede montaggi di input, non aggiungere `COPY` le righe alla Dockerfile.

Creare Dockerfile, effettuando il pull dal contenitore di servizi cognitivi esistente che si vuole usare, quindi usare i comandi di Docker nel Dockerfile per impostare o inserire le informazioni necessarie per il contenitore.

Questo esempio:

* Imposta l'endpoint `{BILLING_ENDPOINT}` di fatturazione dalla chiave dell'ambiente dell'host tramite `ENV`.
* Imposta la chiave API di fatturazione, `{ENDPOINT_KEY}` dalla chiave dell'ambiente dell'host usando "ENV".

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Riutilizza la ricetta: archivia le impostazioni di fatturazione con il contenitore

Questo esempio illustra come creare il contenitore di valutazione di Analisi del testo ' da un Dockerfile.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Compilare ed eseguire il contenitore [localmente](#how-to-use-container-on-your-local-host) o dal [contenitore del registro di sistema privato](#how-to-add-container-to-private-registry) in base alle esigenze.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Riutilizza la ricetta: archivia le impostazioni di fatturazione e montaggio con il contenitore

Questo esempio illustra come usare Language Understanding, salvando la fatturazione e i modelli dal Dockerfile.

* Copia il file del modello di Language Understanding (LUIS) dal file system dell'host `COPY`usando.
* Il contenitore LUIS supporta più di un modello. Se tutti i modelli sono archiviati nella stessa cartella, è necessario disporre di `COPY` un'istruzione.
* Eseguire il file Docker dall'elemento padre relativo della directory di input del modello. Per l'esempio seguente, eseguire i `docker build` comandi `docker run` e dal padre relativo di `/input`. Il primo `/input` `COPY` del comando è la directory del computer host. Il secondo `/input` è la directory del contenitore.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Compilare ed eseguire il contenitore [localmente](#how-to-use-container-on-your-local-host) o dal [contenitore del registro di sistema privato](#how-to-add-container-to-private-registry) in base alle esigenze.

## <a name="how-to-use-container-on-your-local-host"></a>Come usare un contenitore nell'host locale

Per compilare il file Docker, sostituire `<your-image-name>` con il nuovo nome dell'immagine, quindi usare:

```console
docker build -t <your-image-name> .
```

Per eseguire l'immagine e rimuoverla quando il contenitore si interrompe (`--rm`):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Come aggiungere un contenitore al registro privato

Seguire questa procedura per usare Dockerfile e inserire la nuova immagine nel registro contenitori privato.  

1. Creare un `Dockerfile` oggetto con il testo dalla ricetta per il riutilizzo. Un `Dockerfile` oggetto non ha un'estensione.

1. Sostituire i valori nelle parentesi acute con i valori personalizzati.

1. Compilare il file in un'immagine nella riga di comando o nel terminale, usando il comando seguente. Sostituire i valori tra parentesi acute, `<>`, con il nome del contenitore e il tag.  

    L'opzione Tag, `-t`, è un modo per aggiungere informazioni su ciò che è stato modificato per il contenitore. Ad esempio, un nome di `modified-LUIS` contenitore indica che il contenitore originale è stato sovrapposto. Il nome di `with-billing-and-model` un tag indica il modo in cui il contenitore Language Understanding (Luis) è stato modificato.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Accedere all'interfaccia della riga di comando di Azure da una console. Questo comando apre un browser ed è richiesta l'autenticazione. Una volta autenticato, è possibile chiudere il browser e continuare a lavorare nella console.

    ```azurecli
    az login
    ```

1. Accedere al registro privato con l'interfaccia della riga di comando di Azure da una console.

    Sostituire i valori tra parentesi acute, `<my-registry>`, con il proprio nome del registro di sistema.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    È anche possibile accedere con Docker login se viene assegnata un'entità servizio.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Contrassegnare il contenitore con il percorso del registro di sistema privato. Sostituire i valori tra parentesi acute, `<my-registry>`, con il proprio nome del registro di sistema. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Se non si usa un nome di tag `latest` , è implicito.

1. Eseguire il push della nuova immagine nel registro contenitori privato. Quando si Visualizza il registro contenitori privato, il nome del contenitore usato nel comando CLI seguente sarà il nome del repository.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare e usare l'istanza di contenitore di Azure](azure-container-instance-recipe.md)

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