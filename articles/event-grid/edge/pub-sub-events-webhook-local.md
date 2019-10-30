---
title: 'Pubblicare, sottoscrivere gli eventi in locale: griglia di eventi di Azure IoT Edge | Microsoft Docs'
description: Pubblicare, sottoscrivere gli eventi in locale usando webhook con griglia di eventi in IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 169b0c8084259ac27b466dbfd3606e465da35d99
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73098628"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>Esercitazione: pubblicare, sottoscrivere gli eventi in locale

Questo articolo illustra tutti i passaggi necessari per pubblicare e sottoscrivere gli eventi usando griglia di eventi in IoT Edge.

> [!NOTE]
> Per informazioni sugli argomenti e sulle sottoscrizioni di griglia di eventi di Azure, vedere [concetti relativi a griglia di eventi](concepts.md).

## <a name="prerequisites"></a>Prerequisiti 
Per completare questa esercitazione, è necessario:

* **Sottoscrizione di Azure** : creare un [account gratuito](https://azure.microsoft.com/free) se non ne è già disponibile uno. 
* **Hub Azure e dispositivo IOT Edge** : seguire la procedura descritta nella Guida introduttiva per i [dispositivi](../../iot-edge/quickstart.md) [Linux](../../iot-edge/quickstart-linux.md) o Windows se non ne è già presente uno.

## <a name="deploy-event-grid-iot-edge-module"></a>Distribuisci modulo di IoT Edge griglia di eventi

Esistono diversi modi per distribuire i moduli in un dispositivo IoT Edge e tutti funzionano per griglia di eventi di Azure su IoT Edge. Questo articolo descrive i passaggi per la distribuzione di griglia di eventi in IoT Edge dalla portale di Azure.

>[!NOTE]
> In questa esercitazione verrà distribuito il modulo di griglia di eventi senza persistenza. Ciò significa che tutti gli argomenti e le sottoscrizioni create in questa esercitazione verranno eliminati quando si ridistribuisce il modulo. Per altre informazioni su come configurare la persistenza, vedere gli articoli seguenti: [persistenza dello stato in Linux](persist-state-linux.md) o [persistenza in Windows](persist-state-windows.md). Per i carichi di lavoro di produzione, è consigliabile installare il modulo di griglia di eventi con la persistenza.


### <a name="select-your-iot-edge-device"></a>Selezionare il dispositivo IoT Edge

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Passare all'hub IoT.
1. Selezionare **IOT Edge** dal menu nella sezione **gestione automatica dei dispositivi** . 
1. Fare clic sull'ID del dispositivo di destinazione dall'elenco di dispositivi
1. Selezionare **Set Modules** (Configura i moduli). Mantieni aperta la pagina. Si procederà con la procedura descritta nella sezione successiva.

### <a name="configure-a-deployment-manifest"></a>Configurare un manifesto della distribuzione

Un manifesto della distribuzione è un documento JSON contenente la descrizione dei moduli da distribuire, dei flussi di dati esistenti tra i moduli e delle proprietà desiderate dei moduli gemelli. Il portale di Azure dispone di una procedura guidata che illustra la creazione di un manifesto di distribuzione, anziché compilare manualmente il documento JSON.  Sono previsti tre passaggi: **Add modules** (Aggiungere moduli), **Specify routes** (Specificare route) e **Review deployment** (Verificare la distribuzione).

### <a name="add-modules"></a>Aggiungere moduli

1. Nella sezione **moduli di distribuzione** selezionare **Aggiungi**
1. Dai tipi di moduli nell'elenco a discesa selezionare **IOT Edge modulo**
1. Specificare il nome, l'immagine e le opzioni di creazione del contenitore del contenitore:

   * **Nome**: eventgridmodule
   * **URI immagine**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opzioni di creazione del contenitore**:

    ```json
        {
          "Env": [
            "inbound:clientAuth:clientCert:enabled=false",
            "outbound:webhook:httpsOnly=false"
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
 1. Fare clic su **Salva**.
 1. Passare alla sezione successiva per aggiungere il modulo funzioni di Azure prima di distribuirli insieme.

    >[!IMPORTANT]
    > In questa esercitazione verrà distribuito il modulo di griglia di eventi con autenticazione client disabilitata e Consenti Sottoscrittori HTTP. Per i carichi di lavoro di produzione, è consigliabile abilitare l'autenticazione client e consentire solo i sottoscrittori HTTPs. Per altre informazioni su come configurare il modulo di griglia di eventi in modo sicuro, vedere [sicurezza e autenticazione](security-authentication.md).
    > 
    > Se si usa una macchina virtuale di Azure come dispositivo perimetrale, aggiungere una regola per la porta in ingresso per consentire il traffico in ingresso sulla porta 4438. Per istruzioni sull'aggiunta della regola, vedere [How to open ports to a VM](../../virtual-machines/windows/nsg-quickstart-portal.md).
    

## <a name="deploy-azure-function-iot-edge-module"></a>Distribuire il modulo IoT Edge di funzioni di Azure

Questa sezione illustra come distribuire il modulo di Azure Functions, che funge da Sottoscrittore di griglia di eventi a cui è possibile recapitare gli eventi.

>[!IMPORTANT]
>In questa sezione verrà distribuito un modulo di sottoscrizione di esempio basato su funzioni di Azure. Ovviamente è possibile che si tratta di un qualsiasi modulo di Internet delle cose personalizzato in grado di restare in ascolto delle richieste HTTP POST.


### <a name="add-modules"></a>Aggiungere moduli

1. Nella sezione **moduli di distribuzione** selezionare di nuovo **Aggiungi** . 
1. Dai tipi di moduli nell'elenco a discesa selezionare **IOT Edge modulo**
1. Fornire il nome, l'immagine e le opzioni di creazione del contenitore:

   * **Nome**: Sottoscrittore
   * **URI immagine**: `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber-azfunc:latest`
   * **Opzioni di creazione del contenitore**:

       ```json
            {
              "HostConfig": {
                "PortBindings": {
                  "80/tcp": [
                    {
                      "HostPort": "8080"
                    }
                  ]
                }
              }
            }
       ```

1. Fare clic su **Salva**.
1. Fare clic su **Avanti** per passare alla sezione Route

 ### <a name="setup-routes"></a>Route di installazione

Mantenere le route predefinite e fare clic su **Avanti** per passare alla sezione Revisione

### <a name="submit-the-deployment-request"></a>Inviare la richiesta di distribuzione

1. La sezione Review (revisione) Mostra il manifesto di distribuzione JSON che è stato creato in base alle selezioni effettuate nella sezione precedente. Verificare che siano visualizzati i moduli: **eventgridmodule** e **Subscriber** elencati nel file JSON. 
1. Controllare le informazioni sulla distribuzione e quindi selezionare **Submit** (Invia). Dopo aver inviato la distribuzione, tornare alla pagina del **dispositivo** .
1. Nella **sezione moduli**verificare che siano elencati i moduli **eventgrid** e **Subscriber** . Verificare che le colonne **specificato in distribuzione** e **segnalato da dispositivo** siano impostate su **Sì**.

    Per l'avvio del modulo e per il rilevamento da parte dell'hub IoT può essere richiesto un po' di tempo. Aggiornare la pagina per visualizzare lo stato aggiornato.

## <a name="create-a-topic"></a>Creare un argomento

Come server di pubblicazione di un evento, è necessario creare un argomento di griglia di eventi. In griglia di eventi di Azure un argomento si riferisce a un endpoint in cui i publisher possono inviare eventi a.

1. Creare Topic. JSON con il contenuto seguente. Per informazioni dettagliate sul payload, vedere la [documentazione dell'API](api.md).

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. Eseguire il comando seguente per creare un argomento di griglia di eventi. Verificare che venga visualizzato il codice di stato HTTP `200 OK`.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. Eseguire il comando seguente per verificare che l'argomento sia stato creato correttamente. Viene restituito il codice di stato HTTP 200 OK.

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

I sottoscrittori possono registrarsi per gli eventi pubblicati in un argomento. Per ricevere qualsiasi evento, è necessario creare una sottoscrizione di griglia di eventi per un argomento di interesse.

1. Creare Subscription. JSON con il contenuto seguente. Per informazioni dettagliate sul payload, vedere la [documentazione dell'API](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "http://subscriber:80/api/subscriber"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > La proprietà **EndpointType** specifica che il Sottoscrittore è un **webhook**.  **EndpointUrl** specifica l'URL in cui il Sottoscrittore è in ascolto di eventi. Questo URL corrisponde all'esempio di funzione di Azure distribuito in precedenza.
2. Eseguire il comando seguente per creare una sottoscrizione per l'argomento. Verificare che venga visualizzato il codice di stato HTTP `200 OK`.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. Eseguire il comando seguente per verificare che la sottoscrizione sia stata creata correttamente. Viene restituito il codice di stato HTTP 200 OK.

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
                "endpointUrl": "http://subscriber:80/api/subscriber"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Pubblicare un evento

1. Creare event. JSON con il contenuto seguente. Per informazioni dettagliate sul payload, vedere la [documentazione dell'API](api.md).

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
1. Controllare i log del Sottoscrittore:

    In Windows eseguire il comando seguente:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   In Linux eseguire il comando seguente:

    ```sh
    sudo docker logs subscriber
    ```

    Output di esempio:

    ```sh
        Received event data [
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
          ]
    ```

## <a name="cleanup-resources"></a>Risorse di pulizia

* Eseguire il comando seguente per eliminare l'argomento e tutte le relative sottoscrizioni.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* Eliminare il modulo Subscriber dal dispositivo IoT Edge.


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono stati creati un argomento di griglia di eventi, una sottoscrizione e gli eventi pubblicati. Ora che si conoscono i passaggi di base, vedere gli articoli seguenti: 

- Per risolvere i problemi relativi all'uso di griglia di eventi di Azure in IoT Edge, vedere [Guida alla risoluzione dei problemi](troubleshoot.md).
- Crea/aggiorna la sottoscrizione con i [filtri](advanced-filtering.md).
- Abilitare la persistenza del modulo di griglia di eventi in [Linux](persist-state-linux.md) o [Windows](persist-state-windows.md)
- Segui la [documentazione](configure-client-auth.md) per configurare l'autenticazione client
- Eseguire il provisioning di eventi in funzioni di Azure nel cloud seguendo questa [esercitazione](pub-sub-events-webhook-cloud.md)
- [Reagire agli eventi di archiviazione BLOB in IoT Edge](react-blob-storage-events-locally.md)

