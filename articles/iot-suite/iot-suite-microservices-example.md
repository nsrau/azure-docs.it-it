---
title: Modificare e ridistribuire un microservizio | Microsoft Docs
description: Questa esercitazione illustra come modificare e ridistribuire un microservizio nella soluzione di monitoraggio remoto
services: ''
suite: iot-suite
author: giyeh
manager: hegate
ms.author: giyeh
ms.service: iot-suite
ms.date: 04/19/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: dc18437d554889655963270cdc051fb52325f09e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="customize-and-redeploy-a-microservice"></a>Personalizzare e ridistribuire un microservizio

Questa esercitazione illustra come modificare uno dei microservizi nella soluzione di monitoraggio remoto, creare un'immagine del microservizio, distribuire l'immagine nell'hub Docker e quindi usarla nella soluzione di monitoraggio remoto. Per illustrare questo concetto, l'esercitazione usa uno scenario base in cui si chiama l'API di un microservizio e si modifica il messaggio di stato da "Alive and Well" (Attivo e funzionante) in "New Edits Made Here!" (Nuove modifiche apportate qui)

La soluzione di monitoraggio remoto usa i microservizi compilati usando le immagini Docker che vengono estratte da un hub Docker. 

In questa esercitazione si apprenderà come:

>[!div class="checklist"]
> * Modificare e compilare un microservizio nella soluzione di monitoraggio remoto
> * Creare un'immagine Docker
> * Eseguire il push di un'immagine nell'hub Docker
> * Eseguire il pull della nuova immagine Docker
> * Visualizzare le modifiche 

## <a name="prerequisites"></a>prerequisiti

Per eseguire questa esercitazione, è necessario quanto segue:

>[!div class="checklist"]
> * [Distribuire la soluzione preconfigurata di monitoraggio remoto in locale](iot-suite-remote-monitoring-deploy-local.md)
> * [Un account Docker](https://hub.docker.com/)
> * [Postman](https://www.getpostman.com/) - Necessario per visualizzare la risposta dell'API

## <a name="call-the-api-and-view-response-status"></a>Chiamare l'API e visualizzare lo stato della risposta

In questa parte si chiama l'API del microservizio di gestione dell'hub IoT predefinito. L'API restituisce un messaggio di stato che verrà modificato in un secondo momento personalizzando il microservizio.

1. Verificare che la soluzione di monitoraggio remoto sia in esecuzione in locale nel computer.
2. Individuare la posizione in cui è stato scaricato Postman e aprirlo.
3. In Postman immettere quanto segue in GET: http://localhost:8080/iothubmanager/v1/status.
4. Visualizzare il valore restituito e dovrebbe essere indicato "Status": "OK:Alive and Well". (Stato - OK:Attivo e funzionante).
![Messaggio di Postman Alive and Well](media/iot-suite-microservices-example/postman-alive-well.png) (Attivo e funzionante)

## <a name="change-the-status-and-build-the-image"></a>Modificare lo stato e compilare l'immagine

A questo punto cambiare il messaggio di stato del microservizio di gestione dell'hub IoT in "New Edits Made Here!" ("Nuove modifiche apportate qui)". Ricompilare l'immagine Docker con questo nuovo stato. Se si verificano problemi, vedere la sezione sulla [risoluzione dei problemi](#Troubleshoot).

1. Verificare che il terminale sia aperto e passare alla directory in cui è stata clonata la soluzione di monitoraggio remoto. 
2. Impostare la directory su "..azure-iot-pcs-remote-monitoring-dotnet/iothub-manager/WebService/v1/Controllers".
3. Aprire StatusController.cs con qualsiasi editor di testo o IDE a scelta. 
4. Individuare il codice seguente:

    ```javascript
    return new StatusApiModel(true, "Alive and well");
    ```

    modificarlo nel codice seguente e salvarlo.

    ```javascript
    return new StatusApiModel(true, "New Edits Made Here!");
    ```

5. Tornare al terminale, ma ora passare alla directory seguente: "...azure-iot-pcs-remote-monitoring-dotnet/iothub-manager/scripts/docker".
6. Per compilare la nuova immagine Docker, digitare

    ```cmd/sh
    sh build
    ```

7. Per verificare che la nuova immagine sia stata creata correttamente, digitare

    ```cmd/sh
    docker images 
    ```

Il repository deve essere "azureiotpcs/iothub-manager-dotnet".

![Immagine Docker creata correttamente](media/iot-suite-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>Applicare tag ed eseguire il push dell'immagine
Prima di poter eseguire il push della nuova immagine Docker in un hub Docker, alle immagini devono essere state assegnate tag. Se si verificano problemi, vedere la sezione sulla [risoluzione dei problemi](#Troubleshoot).

1. Individuare l'ID immagine dell'immagine Docker creata digitando:

    ```cmd/sh
    docker images
    ```

2. Per applicare tag all'immagine con "testing" digitare

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Per eseguire il push dell'immagine con il tag appena applicato nell'hub Docker, digitare

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Aprire il Web browser, passare all'[hub Docker](https://hub.docker.com/) ed effettuare l'accesso.
5. A questo punto dovrebbe essere visibile l'immagine Docker di cui è stato appena eseguito il push nell'hub Docker.
![Immagine Docker nell'hub Docker](media/iot-suite-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>Aggiornare la soluzione di monitoraggio remoto
È ora necessario aggiornare il file docker-compose.yml locale per eseguire il pull della nuova immagine Docker dall'hub Docker. Se si verificano problemi, vedere la sezione sulla [risoluzione dei problemi](#Troubleshoot).

1. Tornare al terminale e passare alla directory seguente: "..azure-iot-pcs-remote-monitoring-dotnet/scripts/local".
2. Aprire docker-compose.yml con qualsiasi editor di testo o IDE a scelta.
3. Individuare il codice seguente:

    ```docker
    image: azureiotpcs/pcs-auth-dotnet:testing
    ```

    modificarlo in modo che risulti come l'immagine seguente e salvarlo.

    ```cmd/sh
    image: [docker ID]/pcs-auth-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Visualizzare il nuovo stato della risposta
Completare la ridistribuzione di un'istanza locale della soluzione di monitoraggio remoto e visualizzare la risposta del nuovo stato in Postman.

1. Tornare al terminale e passare alla directory seguente: "..azure-iot-pcs-remote-monitoring-dotnet/scripts/local".
2. Avviare l'istanza locale della soluzione di monitoraggio remoto digitando il comando seguente nel terminale:

    ```cmd/sh
    docker-compose up
    ```

3. Individuare la posizione in cui è stato scaricato Postman e aprirlo.
4. In Postman immettere la seguente richiesta in GET: http://localhost:8080/iothubmanager/v1/status. Dovrebbe essere visualizzato, "Status": "OK: New Edits Made Here!" ("Stato": "OK: Nuove modifiche apportate qui").

![Messaggio Postman New Edits Made Here (Nuove modifiche apportate qui)](media/iot-suite-microservices-example/new-postman-message.png)

## <a name="Troubleshoot"></a>Risoluzione dei problemi

Se si verificano problemi, provare a rimuovere le immagini e i contenitori Docker nel computer locale.

1. Per rimuovere tutti i contenitori, è necessario prima arrestare tutti i contenitori in esecuzione. Aprire il terminale in uso e digitare

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Per rimuovere tutte le immagini, aprire il terminale e digitare 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. È possibile verificare se sono presenti contenitori nel computer digitando

    ```cmd/sh
    docker ps -aq 
    ```

    Se sono stati rimossi correttamente tutti i contenitori, non dovrebbe essere visualizzato alcun risultato.

4. È possibile verificare se sono presenti immagini nel computer digitando

    ```cmd/sh
    docker images
    ```

    Se sono stati rimossi correttamente tutti i contenitori, non dovrebbe essere visualizzato alcun risultato.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è visto come:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Modificare e compilare un microservizio nella soluzione di monitoraggio remoto
> * Creare un'immagine Docker
> * Eseguire il push di un'immagine nell'hub Docker
> * Eseguire il pull della nuova immagine Docker
> * Visualizzare le modifiche 

Come operazione successiva è possibile provare a [personalizzare il microservizio di simulazione dei dispositivi nella soluzione di monitoraggio remoto](iot-suite-remote-monitoring-test.md)

Per altre informazioni per sviluppatori sulla soluzione di monitoraggio remoto, vedere:

* [Guida di riferimento per gli sviluppatori](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

