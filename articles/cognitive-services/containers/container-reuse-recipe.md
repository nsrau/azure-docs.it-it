---
title: Ricette per i contenitori Docker
titleSuffix: Azure Cognitive Services
description: Usare queste soluzioni contenitore come per creare contenitori di servizi cognitivi che può essere riutilizzato. I contenitori possono essere creati con alcune o tutte le impostazioni di configurazione in modo che non sono necessari quando viene avviato il contenitore. Una volta che si dispone di questo nuovo livello di contenitore (con le impostazioni) ed è stata testata in locale, è possibile archiviare il contenitore in un registro contenitori. All'avvio del contenitore, è necessario solo le impostazioni che non sono attualmente archiviate nel contenitore.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 06/12/2019
ms.author: dapine
ms.openlocfilehash: a2fd9073bfb472f52d5dbf60538214c8b4f3d360
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207480"
---
# <a name="create-containers-for-reuse"></a>Creare contenitori per il riutilizzo

Usare queste soluzioni contenitore come per creare contenitori di servizi cognitivi che può essere riutilizzato. I contenitori possono essere creati con alcune o tutte le impostazioni di configurazione in modo che siano _non_ necessarie quando viene avviato il contenitore.

Una volta che si dispone di questo nuovo livello di contenitore (con le impostazioni) ed è stata testata in locale, è possibile archiviare il contenitore in un registro contenitori. All'avvio del contenitore, è necessario solo le impostazioni che non sono attualmente archiviate nel contenitore. Il contenitore del Registro di sistema privato fornisce lo spazio di configurazione per il passaggio di tali impostazioni in.

## <a name="docker-run-syntax"></a>Eseguire la sintassi di docker

Eventuali `docker run` negli esempi in questo documento si presuppone una console di Windows con un `^` carattere di continuazione di riga. Tenere presente quanto segue per uso personale:

* Non modificare l'ordine degli argomenti se non si ha dimestichezza con i contenitori Docker.
* Se si usa un sistema operativo diverso da Windows o una console di console di Windows, usare la console/terminal corretto, sintassi cartella per punti di montaggio e il carattere di continuazione di riga per la console e il sistema.  Poiché il contenitore di servizi cognitivi è un sistema operativo Linux, il montaggio di destinazione utilizza una sintassi di cartella basato su Linux.
* `docker run` Negli esempi viene usata la directory di disattivare il `c:` unità per evitare eventuali conflitti di autorizzazione su Windows. Se è necessario usare una directory specifica come directory di input, potrebbe essere necessario concedere l'autorizzazione per il servizio Docker.

## <a name="store-no-configuration-settings-in-image"></a>Non Store Nessuna impostazione di configurazione nell'immagine

L'esempio `docker run` comandi per ogni servizio non archiviano le impostazioni di configurazione nel contenitore. Quando si avvia il contenitore da un servizio di console o del Registro di sistema, è necessario passare le impostazioni di configurazione. Il contenitore del Registro di sistema privato fornisce lo spazio di configurazione per il passaggio di tali impostazioni in.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Riutilizzare file recipe: archiviare tutte le impostazioni di configurazione con contenitore

Per poter archiviare tutte le impostazioni di configurazione, creare un `Dockerfile` con tali impostazioni.

Problemi con questo approccio:

* Il nuovo contenitore è un nome distinto e un tag dal contenitore originale.
* Per modificare queste impostazioni, si dovrà modificare i valori del Dockerfile, ricompilare l'immagine e pubblicare di nuovo al Registro di sistema.
* Se un utente ottiene accesso per l'host locale o nel registro contenitori, possono eseguire il contenitore e usino gli endpoint di servizi cognitivi.
* Se il servizio cognitivo non richiede input punti di montaggio, non aggiungere il `COPY` righe al Dockerfile.

Creare file di Docker, eseguire il pull dal contenitore di servizi cognitivi esistente da usare, quindi usare i comandi di docker nel Dockerfile o estrarre le informazioni del contenitore deve.

In questo esempio:

* Imposta l'endpoint di fatturazione `{BILLING_ENDPOINT}` dall'host dell'ambiente della chiave usando `ENV`.
* Imposta la chiave API di fatturazione, `{ENDPOINT_KEY}` dall'host dell'ambiente della chiave usando ' ENV.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Riutilizzare file recipe: archiviare le impostazioni di fatturazione con contenitore

In questo esempio viene illustrato come generare sentiment contenitore il testo degli Analitica da un file Docker.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Compilare ed eseguire il contenitore [localmente](#how-to-use-container-on-your-local-host) o dalle [contenitore del Registro di sistema privato](#how-to-add-container-to-private-registry) in base alle esigenze.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Riutilizzare file recipe: archiviare la fatturazione e le impostazioni con contenitore di montaggio

In questo esempio viene illustrato come utilizzare comprensione del linguaggio, il salvataggio di modelli e fatturazione del dockerfile.

* Le copie del file modello LUIS (Language Understanding) dell'host del file system usando `COPY`.
* Il contenitore di LUIS supporta più di un modello. Se tutti i modelli vengono archiviati nella stessa cartella, tutto è necessario uno `COPY` istruzione.
* Eseguire il file di docker dal relativo padre della directory di input del modello. Nell'esempio seguente, eseguire la `docker build` e `docker run` comandi dal relativo padre di `/input`. Il primo `/input` nella `COPY` comando è la directory del computer host. Il secondo `/input` è la directory del contenitore.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Compilare ed eseguire il contenitore [localmente](#how-to-use-container-on-your-local-host) o dalle [contenitore del Registro di sistema privato](#how-to-add-container-to-private-registry) in base alle esigenze.

## <a name="how-to-use-container-on-your-local-host"></a>Come usare i contenitori nell'host locale

Per compilare il file di Docker, sostituire `<your-image-name>` con il nuovo nome dell'immagine, usare:

```console
docker build -t <your-image-name> .
```

Per eseguire l'immagine e lo rimuove quando si arresta il contenitore (`--rm`):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Come aggiungere contenitori in un registro privato

Seguire questi passaggi per usare il Dockerfile e posizionare la nuova immagine nel registro contenitori privato.  

1. Creare un `Dockerfile` con il testo dal file recipe di riutilizzo. Oggetto `Dockerfile` non dispone di un'estensione.

1. Sostituire i valori nelle parentesi acute con i propri valori.

1. Compilare il file in un'immagine nella riga di comando o terminal, usando il comando seguente. Sostituire i valori nelle parentesi acute, `<>`, con il proprio nome di contenitore e tag.  

    L'opzione dei tag, `-t`, consente di aggiungere informazioni su ciò che sono stati modificati per il contenitore. Ad esempio, un nome di contenitore `modified-LUIS` indica il contenitore originale è stata a più livelli. Il nome di un tag di `with-billing-and-model` indica come il contenitore di LUIS (Language Understanding) è stato modificato.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Accedere al comando di Azure da una console. Questo comando apre una finestra del browser e richiede l'autenticazione. Una volta autenticato, è possibile chiudere il browser e continuare a utilizzare la console.

    ```azure-cli
    az login
    ```

1. Accedere al registro privato con CLI di Azure da una console.

    Sostituire i valori nelle parentesi acute, `<my-registry>`, con il nome del Registro di sistema.  

    ```azure-cli
    az acr login --name <my-registry>
    ```

    È anche possibile accedere con accesso a docker se si è assegnati a un'entità servizio.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Contrassegnare il contenitore con il percorso del Registro di sistema privato. Sostituire i valori nelle parentesi acute, `<my-registry>`, con il nome del Registro di sistema. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Se non si usa un nome di tag, `latest` è implicita.

1. Eseguire il push della nuova immagine nel registro contenitori privato. Quando si visualizza il registro contenitori privato, il nome del contenitore usato nel comando seguente dell'interfaccia della riga sarà il nome del repository.

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