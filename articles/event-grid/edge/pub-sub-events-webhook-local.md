---
title: Pubblicare, sottoscrivere gli eventi in locale - Azure Event Grid IoT Edge Documenti Microsoft
description: Pubblicare, sottoscrivere gli eventi localmente utilizzando Webhook con Griglia di eventi in Edge IoT
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba82b1bea4753cd51e275a78b248247032d79a01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281002"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>Esercitazione: Pubblicare, sottoscrivere gli eventi in localeTutorial: Publish, subscribe to events locally

Questo articolo illustra tutti i passaggi necessari per pubblicare e sottoscrivere eventi usando Griglia di eventi in Edge IoT.

> [!NOTE]
> Per informazioni sugli argomenti e le sottoscrizioni di Griglia di eventi di Azure, vedere Concetti relativi a [Griglia di](concepts.md)eventi.

## <a name="prerequisites"></a>Prerequisiti 
Per completare questa esercitazione è necessario disporre degli elementi seguenti:

* Sottoscrizione di **Azure:** creare un [account gratuito,](https://azure.microsoft.com/free) se non ne è già disponibile uno. 
* **Hub IoT di Azure e dispositivo Edge IoT:** seguire i passaggi della guida introduttiva per i dispositivi [Linux](../../iot-edge/quickstart-linux.md) o [Windows,](../../iot-edge/quickstart.md) se non ne è già presente uno.

## <a name="deploy-event-grid-iot-edge-module"></a>Distribuire il modulo IoT Edge griglia eventiDeploy Event Grid IoT Edge module

Esistono diversi modi per distribuire i moduli in un dispositivo IoT Edge e tutti funzionano per Griglia di eventi di Azure in IoT Edge.There are several ways to deploy modules to an IoT Edge device and all of them work for Azure Event Grid on IoT Edge. Questo articolo descrive i passaggi per distribuire Griglia di eventi in Edge Edge dal portale di Azure.This article describes the steps to deploy Event Grid on IoT Edge from the Azure portal.

>[!NOTE]
> In questa esercitazione verrà distribuito il modulo Griglia di eventi senza persistenza. Ciò significa che tutti gli argomenti e le sottoscrizioni creati in questa esercitazione verranno eliminati quando si ridistribuisce il modulo. Per ulteriori informazioni su come configurare la persistenza, vedere i seguenti articoli: [Persist state in Linux](persist-state-linux.md) o Persist state in [Windows](persist-state-windows.md). Per i carichi di lavoro di produzione, è consigliabile installare il modulo Griglia di eventi con persistenza.


### <a name="select-your-iot-edge-device"></a>Selezionare il dispositivo IoT Edge

1. Accedere al portale di [AzureSign](https://portal.azure.com) in to the Azure portal
1. Passare all'hub IoT.
1. Selezionare **IoT Edge** dal menu nella sezione **Gestione automatica dispositivi.** 
1. Fare clic sull'ID del dispositivo di destinazione dall'elenco dei dispositivi
1. Selezionare **Set Modules** (Configura i moduli). Tenere aperta la pagina. I passaggi verranno continuati nella sezione successiva.

### <a name="configure-a-deployment-manifest"></a>Configurare un manifesto della distribuzione

Un manifesto della distribuzione è un documento JSON contenente la descrizione dei moduli da distribuire, dei flussi di dati esistenti tra i moduli e delle proprietà desiderate dei moduli gemelli. Il portale di Azure include una procedura guidata che illustra come creare un manifesto di distribuzione, anziché compilare manualmente il documento JSON.  Sono previsti tre passaggi: **Add modules** (Aggiungere moduli), **Specify routes** (Specificare route) e **Review deployment** (Verificare la distribuzione).

### <a name="add-modules"></a>Aggiungere moduli

1. Nella sezione **Moduli di distribuzione** selezionare **Aggiungi**
1. Dai tipi di moduli nell'elenco a discesa, selezionare **Modulo edge IoT**
1. Specificare il nome, l'immagine e le opzioni di creazione del contenitore:

   * **Nome**: eventgridmodule
   * **URI immagine**:`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opzioni di creazione del contenitore**:

   [!INCLUDE [event-grid-edge-module-version-update](../../../includes/event-grid-edge-module-version-update.md)]

    ```json
        {
          "Env": [
            "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. Fare clic su **Save**.
 1. Passare alla sezione successiva per aggiungere il modulo Sottoscrittore Griglia di eventi di Azure prima di distribuirlo insieme.

    >[!IMPORTANT]
    > In questa esercitazione verrà distribuito il modulo Griglia di eventi con l'autenticazione client disabilitata. Per i carichi di lavoro di produzione, è consigliabile abilitare l'autenticazione client. Per ulteriori informazioni su come configurare il modulo Griglia di eventi in modo sicuro, vedere [Sicurezza e autenticazione](security-authentication.md).
    > 
    > Se si usa una macchina virtuale di Azure come dispositivo perimetrale, aggiungere una regola di porta in ingresso per consentire il traffico in ingresso sulla porta 4438.If you are using an Azure VM as an edge device, add an inbound port rule to allow inbound traffic on the port 4438. Per istruzioni sull'aggiunta della regola, vedere [Come aprire le porte a una macchina virtuale.](../../virtual-machines/windows/nsg-quickstart-portal.md)
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Distribuire il modulo IoT Edge del sottoscrittore Griglia di eventiDeploy Event Grid Subscriber IoT Edge module

In questa sezione viene illustrato come distribuire un altro modulo IoT che fungerebbe da gestore eventi a cui gli eventi possono essere recapitati.

### <a name="add-modules"></a>Aggiungere moduli

1. Nella sezione **Moduli di distribuzione** selezionare di nuovo Aggiungi.In the Deployment Modules section, select **Add** again. 
1. Dai tipi di moduli nell'elenco a discesa, selezionare **Modulo edge IoT**
1. Specificare le opzioni di creazione di nome, immagine e contenitore del contenitore:

   * **Nome**: abbonato
   * **URI immagine**:`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Opzioni di creazione contenitore**: NessunaContainer Create Options : None
1. Fare clic su **Save**.
1. Fare clic su **Avanti** per passare alla sezione percorsi

 ### <a name="setup-routes"></a>Impostare i percorsi

Mantenere i percorsi predefiniti e selezionare **Avanti** per continuare con la sezione di revisione

### <a name="submit-the-deployment-request"></a>Inviare la richiesta di distribuzioneSubmit the deployment request

1. La sezione di revisione mostra il manifesto di distribuzione JSON creato in base alle selezioni effettuate nella sezione precedente. Verificare che siano visualizzati entrambi i moduli: **eventgridmodule** e **sottoscrittore** elencati in JSON. 
1. Controllare le informazioni sulla distribuzione e quindi selezionare **Submit** (Invia). Dopo aver inviato la distribuzione, si torna alla pagina del **dispositivo.**
1. Nella **sezione Moduli**verificare che siano elencati sia i moduli **eventgrid** che **i moduli sottoscrittore.** Verificare inoltre che le colonne **Specificato nella distribuzione** e Segnalato dal **dispositivo** siano impostate su **Sì**.

    Per l'avvio del modulo e per il rilevamento da parte dell'hub IoT può essere richiesto un po' di tempo. Aggiornare la pagina per visualizzare lo stato aggiornato.

## <a name="create-a-topic"></a>Creare un argomento

In qualità di editore di un evento, devi creare un argomento della griglia degli eventi. In Griglia di eventi di Azure un argomento fa riferimento a un endpoint a cui gli editori possono inviare eventi.

1. Creare topic.json con il contenuto seguente. Per informazioni dettagliate sul payload, vedere la [documentazione dell'API](api.md).

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. Eseguire il comando seguente per creare un argomento della griglia di eventi. Verificare che venga visualizzato `200 OK`il codice di stato HTTP .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. Eseguire il comando seguente per verificare che l'argomento sia stato creato correttamente. Deve essere restituito il codice di stato HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

   Output di esempio:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1",
            "name": "sampleTopic1",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Creare una sottoscrizione di eventi

I sottoscrittori possono registrarsi per gli eventi pubblicati in un argomento. Per ricevere qualsiasi evento, è necessario creare una sottoscrizione griglia di eventi per un argomento di interesse.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Creare subscription.json con il contenuto seguente. Per informazioni dettagliate sul payload, consulta la [documentazione dell'API](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > La proprietà **endpointType** specifica che il sottoscrittore è un **Webhook.**  **EndpointUrl** specifica l'URL in cui il sottoscrittore è in ascolto di eventi. Questo URL corrisponde all'esempio di Sottoscrittore di Azure distribuito in precedenza.
2. Eseguire il comando seguente per creare una sottoscrizione per l'argomento. Verificare che venga visualizzato `200 OK`il codice di stato HTTP .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. Eseguire il comando seguente per verificare che la sottoscrizione sia stata creata correttamente. Deve essere restituito il codice di stato HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    Output di esempio:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1/eventSubscriptions/sampleSubscription1",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription1",
          "properties": {
            "Topic": "sampleTopic1",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Pubblicare un evento

1. Create event.json con il contenuto seguente. Per informazioni dettagliate sul payload, vedere la [documentazione dell'API](api.md).

    ```json
        [
          {
            "id": "eventId-func-0",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```
1. Eseguire il comando seguente per pubblicare un evento.

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Verificare il recapito degli eventi

1. SSH o RDP nella macchina virtuale IoT Edge.
1. Controllare i registri degli abbonati:

    In Windows, eseguire il comando seguente:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   In Linux, eseguire il comando seguente:

    ```sh
    sudo docker logs subscriber
    ```

    Output di esempio:

    ```sh
        Received Event:
            {
              "id": "eventId-func-0",
              "topic": "sampleTopic1",
              "subject": "myapp/vehicles/motorcycles",
              "eventType": "recordInserted",
              "eventTime": "2019-07-28T21:03:07+00:00",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "make": "Ducati",
                "model": "Monster"
              }
            }
    ```

## <a name="cleanup-resources"></a>Risorse di pulizia

* Eseguire il comando seguente per eliminare l'argomento e tutte le relative sottoscrizioni.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* Eliminare il modulo dell'abbonato dal dispositivo IoT Edge.


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono stati creati un argomento della griglia di eventi, una sottoscrizione e gli eventi pubblicati. Dopo aver conosciuto i passaggi di base, vedere gli articoli seguenti:Now that you know the basic steps, see the following articles: 

- Per risolvere i problemi relativi all'uso di Griglia di eventi di Azure in Edge Edge, vedere [Guida alla risoluzione dei problemi](troubleshoot.md).
- Crea/aggiorna sottoscrizione con [filtri](advanced-filtering.md).
- Abilitare la persistenza del modulo Griglia di eventi in Linux o [WindowsEnable](persist-state-windows.md) persistence of Event Grid module on [Linux](persist-state-linux.md) or Windows
- Seguire la [documentazione](configure-client-auth.md) per configurare l'autenticazione client
- Inoltrare gli eventi a Funzioni di Azure nel cloud seguendo questa [esercitazione](pub-sub-events-webhook-cloud.md)
- [Reagire agli eventi di archiviazione BLOB in Edge EdgeReact to Blob Storage events on IoT Edge](react-blob-storage-events-locally.md)
- [Monitorare argomenti e sottoscrizioni sul bordo](monitor-topics-subscriptions.md)

