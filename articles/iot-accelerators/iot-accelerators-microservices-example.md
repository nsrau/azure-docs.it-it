---
title: Modificare e ridistribuire un microservizio - Azure | Microsoft Docs
description: Questa esercitazione illustra come modificare e ridistribuire un microservizio nella soluzione di monitoraggio remoto
author: dominicbetts
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.openlocfilehash: 563de3d062b2c49d6b7ba23ae405e75283270815
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53631900"
---
# <a name="customize-and-redeploy-a-microservice"></a>Personalizzare e ridistribuire un microservizio

Questa esercitazione illustra come modificare uno dei [microservizi](https://azure.com/microservices) nella soluzione di monitoraggio remoto, creare un'immagine del microservizio, distribuire l'immagine nell'hub Docker e quindi usarla nella soluzione di monitoraggio remoto. Per illustrare questo concetto, l'esercitazione usa uno scenario base in cui si chiama l'API di un microservizio e si modifica il messaggio di stato da "Alive and Well" (Attivo e funzionante) in "New Edits Made Here!" (Nuove modifiche apportate qui)

La soluzione di monitoraggio remoto usa i microservizi compilati usando le immagini Docker che vengono estratte da un hub Docker. 

In questa esercitazione si apprenderà come:

>[!div class="checklist"]
> * Modificare e compilare un microservizio nella soluzione di monitoraggio remoto
> * Creare un'immagine Docker
> * Eseguire il push di un'immagine nell'hub Docker
> * Eseguire il pull della nuova immagine Docker
> * Visualizzare le modifiche 

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questa esercitazione, è necessario quanto segue:

>[!div class="checklist"]
> * [Distribuire l'acceleratore di soluzioni di monitoraggio remoto localmente](iot-accelerators-remote-monitoring-deploy-local.md)
> * [Un account Docker](https://hub.docker.com/)
> * [Postman](https://www.getpostman.com/) - Necessario per visualizzare la risposta dell'API

## <a name="call-the-api-and-view-response-status"></a>Chiamare l'API e visualizzare lo stato della risposta

In questa parte si chiama l'API del microservizio di gestione dell'hub IoT predefinito. L'API restituisce un messaggio di stato che verrà modificato in un secondo momento personalizzando il microservizio.

1. Verificare che la soluzione di monitoraggio remoto sia in esecuzione in locale nel computer.
2. Individuare la posizione in cui è stato scaricato Postman e aprirlo.
3. In Postman immettere quanto segue in GET: http://localhost:8080/iothubmanager/v1/status.
4. Visualizzare il valore restituito. Dovrebbe essere indicato "Status": "OK:Alive and Well". (Stato: OK:Attivo e funzionante).

    ![Messaggio di Postman Alive and Well (Attivo e funzionante)](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>Modificare lo stato e compilare l'immagine

A questo punto cambiare il messaggio di stato del microservizio di gestione dell'hub IoT in "New Edits Made Here!" ("Nuove modifiche apportate qui)". Ricompilare l'immagine Docker con questo nuovo stato. Se si verificano problemi, vedere la sezione sulla [risoluzione dei problemi](#Troubleshoot).

1. Verificare che il terminale sia aperto e passare alla directory in cui è stata clonata la soluzione di monitoraggio remoto. 
1. Passare alla directory "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/Services".
1. Aprire StatusService.cs in un editor di testo o IDE a scelta. 
1. Individuare il codice seguente:

    ```csharp
    var result = new StatusServiceModel(true, "Alive and well!");
    ```

    modificarlo nel codice seguente e salvarlo.

    ```csharp
    var result = new StatusServiceModel(true, "New Edits Made Here!");
    ```

5. Tornare al terminale, ma ora passare alla directory seguente: "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/scripts/docker".
6. Per compilare la nuova immagine Docker, digitare

    ```sh
    sh build
    ```
    
    oppure in Windows:
    
    ```cmd
    ./build.cmd
    ```

7. Per verificare che la nuova immagine sia stata creata correttamente, digitare

    ```cmd/sh
    docker images 
    ```

Il repository deve essere "azureiotpcs/iothub-manager-dotnet".

![Immagine Docker creata correttamente](./media/iot-accelerators-microservices-example/successful-docker-image.png)

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
![Immagine Docker nell'hub Docker](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>Aggiornare la soluzione di monitoraggio remoto
È ora necessario aggiornare il file docker-compose.yml locale per eseguire il pull della nuova immagine Docker dall'hub Docker. Se si verificano problemi, vedere la sezione sulla [risoluzione dei problemi](#Troubleshoot).

1. Tornare al terminale e passare alla directory seguente: "azure-iot-pcs-remote-monitoring-dotnet/services/scripts/local".
2. Aprire docker-compose.yml con qualsiasi editor di testo o IDE a scelta.
3. Individuare il codice seguente:

    ```docker
    image: azureiotpcs/iothub-manager-dotnet:testing
    ```

    modificarlo in modo che risulti come l'immagine seguente e salvarlo.

    ```cmd/sh
    image: [docker ID]/iothub-manager-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Visualizzare il nuovo stato della risposta
Completare la ridistribuzione di un'istanza locale della soluzione di monitoraggio remoto e visualizzare la risposta del nuovo stato in Postman.

1. Tornare al terminale e passare alla directory seguente: "azure-iot-pcs-remote-monitoring-dotnet/scripts/local".
2. Avviare l'istanza locale della soluzione di monitoraggio remoto digitando il comando seguente nel terminale:

    ```cmd/sh
    docker-compose up
    ```

3. Individuare la posizione in cui è stato scaricato Postman e aprirlo.
4. In Postman immettere la seguente richiesta in GET: http://localhost:8080/iothubmanager/v1/status. Ora dovrebbe essere visualizzato "Status": "OK: New Edits Made Here!" (Stato: OK:Nuove modifiche apportate).

![Messaggio Postman New Edits Made Here (Nuove modifiche apportate qui)](./media/iot-accelerators-microservices-example/new-postman-message.png)

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

Come operazione successiva è possibile provare a [personalizzare il microservizio di simulazione dei dispositivi nella soluzione di monitoraggio remoto](iot-accelerators-microservices-example.md)

Per altre informazioni per sviluppatori sulla soluzione di monitoraggio remoto, vedere:

* [Guida di riferimento per gli sviluppatori](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

