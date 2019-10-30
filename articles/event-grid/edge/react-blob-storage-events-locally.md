---
title: Reagire agli eventi del modulo di archiviazione BLOB-IoT Edge di griglia di eventi di Azure | Microsoft Docs
description: Reagire agli eventi del modulo di archiviazione BLOB
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 10/02/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 851b5607ad5413cd1a594f788cb294ee7790e8eb
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043177"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Esercitazione: rispondere agli eventi di archiviazione BLOB in IoT Edge (anteprima)
Questo articolo illustra come distribuire il servizio di archiviazione BLOB di Azure nel modulo Internet, che fungerebbe da server di pubblicazione di griglia di eventi per inviare gli eventi durante la creazione e l'eliminazione di BLOB in griglia di eventi.  

Per una panoramica dell'archiviazione BLOB di Azure in IoT Edge, vedere [archiviazione BLOB di Azure in IOT Edge](../../iot-edge/how-to-store-data-blob.md) e le relative funzionalità.

> [!WARNING]
> Archiviazione BLOB di Azure in IoT Edge integrazione con griglia di eventi è in anteprima

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
           "inbound:serverAuth:tlsPolicy=enabled",
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
 1. Passare alla sezione successiva per aggiungere il modulo funzioni di Azure

    >[!IMPORTANT]
    > In questa esercitazione verrà distribuito il modulo di griglia di eventi per consentire richieste HTTP/HTTPs, autenticazione client disabilitata e Consenti Sottoscrittori HTTP. Per i carichi di lavoro di produzione, è consigliabile abilitare solo le richieste HTTPs e i sottoscrittori con autenticazione client abilitata. Per altre informazioni su come configurare il modulo di griglia di eventi in modo sicuro, vedere [sicurezza e autenticazione](security-authentication.md).
    

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
1. Passare alla sezione successiva per aggiungere il modulo di archiviazione BLOB di Azure

> [!NOTE]
> Il modulo di archiviazione BLOB pubblica gli eventi tramite HTTP. Verificare che il modulo griglia di eventi consenta le richieste HTTP e HTTPS con la configurazione seguente: `inbound:serverAuth:tlsPolicy=enabled`.

## <a name="deploy-azure-blob-storage-module"></a>Distribuire il modulo di archiviazione BLOB di Azure

Questa sezione illustra come distribuire il modulo di archiviazione BLOB di Azure, che fungerà da server di pubblicazione per la pubblicazione di un BLOB di eventi creati ed eliminati.

### <a name="add-modules"></a>Aggiungere moduli

1. Nella sezione **moduli di distribuzione** selezionare **Aggiungi**
2. Dai tipi di moduli nell'elenco a discesa selezionare **IOT Edge modulo**
3. Fornire il nome, l'immagine e le opzioni di creazione del contenitore:

   * **Nome**: azureblobstorageoniotedge
   * **URI immagine**: MCR.Microsoft.com/Azure-BLOB-Storage:1.2.2-Preview
   * **Opzioni di creazione del contenitore**:

```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
```

4. Aggiornare il codice JSON copiato con le informazioni seguenti:

   - Sostituire `<your storage account name>` con un nome facile da ricordare. I nomi degli account devono avere una lunghezza di 3 e 24 caratteri, con lettere minuscole e numeri. Nessun spazio.

   - Sostituire `<your storage account key>` con una chiave Base64 a 64 byte. È possibile generare una chiave con strumenti quali [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Queste credenziali verranno usate per accedere all'archiviazione BLOB da altri moduli.

   - Sostituire `<event grid module name>` con il nome del modulo di griglia di eventi.
   - Sostituire `<storage mount>` in base al sistema operativo del contenitore.
     - Per i contenitori Linux, **My-volume:/blobroot**
     - Per i contenitori di Windows,**My-volume: C:/BlobRoot**

5. Fare clic su **Salva**.
6. Fare clic su **Avanti** per passare alla sezione Route

 ### <a name="setup-routes"></a>Route di installazione

Mantenere le route predefinite e fare clic su **Avanti** per passare alla sezione Revisione

### <a name="review-deployment"></a>Verificare la distribuzione

1. La sezione Review (revisione) Mostra il manifesto di distribuzione JSON che è stato creato in base alle selezioni effettuate nella sezione precedente. Verificare che siano visualizzati i quattro moduli seguenti: **$edgeAgent**, **$edgeHub**, **eventgridmodule**, **Subscriber** e **azureblobstorageoniotedge** che tutti vengono distribuiti.
2. Controllare le informazioni sulla distribuzione e quindi selezionare **Submit** (Invia).

## <a name="verify-your-deployment"></a>Verificare la distribuzione

1. Dopo aver inviato la distribuzione, tornare alla pagina IoT Edge dell'hub Internet.
2. Selezionare il **dispositivo IOT Edge** di destinazione della distribuzione per aprirne i dettagli.
3. Nei dettagli del dispositivo verificare che i moduli eventgridmodule, Subscriber e azureblobstorageoniotedge siano elencati sia come **specificato nella distribuzione** che come **segnalato dal dispositivo**.

   Per l'avvio del modulo e per il rilevamento da parte dell'hub IoT può essere richiesto un po' di tempo. Aggiornare la pagina per visualizzare lo stato aggiornato.

## <a name="publish-created-and-deleted-events"></a>Pubblica eventi creati ed eliminati

1. Questo modulo crea automaticamente l'argomento **MicrosoftStorage**. Verificare che esista
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    Output di esempio:

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

2. I sottoscrittori possono registrarsi per gli eventi pubblicati in un argomento. Per ricevere qualsiasi evento, è necessario creare una sottoscrizione di griglia di eventi per l'argomento **MicrosoftStorage** .
    1. Creare blobsubscription. JSON con il contenuto seguente. Per informazioni dettagliate sul payload, vedere la [documentazione dell'API](api.md)

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
    curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
    ```

    3. Eseguire il comando seguente per verificare che la sottoscrizione sia stata creata correttamente. Viene restituito il codice di stato HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
    ```

    Output di esempio:

    ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "http://subscriber:80/api/subscriber"
              }
            }
          }
        }
    ```

2. Scaricare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) e [connetterlo alla risorsa di archiviazione locale](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>Verificare il recapito degli eventi

### <a name="verify-blobcreated-event-delivery"></a>Verificare il recapito degli eventi di BlobCreated

1. Caricare i file come BLOB in blocchi nella risorsa di archiviazione locale da Azure Storage Explorer e il modulo pubblicherà automaticamente gli eventi di creazione. 
2. Per creare un evento, vedere i log del Sottoscrittore. Seguire i passaggi per [verificare il recapito degli eventi](pub-sub-events-webhook-local.md#verify-event-delivery)

    Output di esempio:

    ```json
            Received event data [
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
          ]
    ```

### <a name="verify-blobdeleted-event-delivery"></a>Verificare il recapito degli eventi di BlobDeleted

1. Eliminare i BLOB dalla risorsa di archiviazione locale usando Azure Storage Explorer e il modulo pubblicherà automaticamente gli eventi Delete. 
2. Esaminare i log del Sottoscrittore per l'evento Delete. Seguire i passaggi per [verificare il recapito degli eventi](pub-sub-events-webhook-local.md#verify-event-delivery)

    Output di esempio:
    
    ```json
            Received event data [
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
          ]
    ```

Congratulazioni. L'esercitazione è stata completata. Le sezioni seguenti forniscono informazioni dettagliate sulle proprietà dell'evento.

### <a name="event-properties"></a>Proprietà degli eventi

Di seguito è riportato l'elenco delle proprietà di evento supportate, i relativi tipi e descrizioni. 

| Proprietà | Type | Description |
| -------- | ---- | ----------- |
| argomento | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| subject | string | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | string | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| id | string | Identificatore univoco dell'evento. |
| data | object | Dati relativi all'evento di archiviazione BLOB. |
| dataVersion | string | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | string | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | Description |
| -------- | ---- | ----------- |
| api | string | L'operazione che ha attivato l'evento. Può essere uno dei valori seguenti: <ul><li>BlobCreated: i valori consentiti sono: `PutBlob` e `PutBlockList`</li><li>BlobDeleted: i valori consentiti sono `DeleteBlob`, `DeleteAfterUpload` e `AutoDelete`. <p>L'evento `DeleteAfterUpload` viene generato quando il BLOB viene eliminato automaticamente perché la proprietà desiderata deleteAfterUpload è impostata su true. </p><p>`AutoDelete` evento viene generato quando il BLOB viene eliminato automaticamente perché il valore della proprietà desiderata deleteAfterMinutes è scaduto.</p></li></ul>|
| clientRequestId | string | ID richiesta fornito dal client per l'operazione dell'API di archiviazione. Questo ID può essere usato per la correlazione ai log di diagnostica di archiviazione di Azure usando il campo "client-Request-ID" nei log e può essere specificato nelle richieste client usando l'intestazione "x-MS-client-Request-ID". Per informazioni dettagliate, vedere [formato del log](/rest/api/storageservices/storage-analytics-log-format). |
| requestId | string | ID di richiesta generato dal servizio per l'operazione API di archiviazione. Può essere usato per la correlazione ai log di diagnostica di Archiviazione di Azure usando il campo "request-id-header" nei log e viene restituito dall'avvio di una chiamata API nell'intestazione 'x-ms-request-id'. Vedere [Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Formato del log). |
| eTag | string | Il valore che è possibile usare per eseguire le operazioni in modo condizionale. |
| contentType | string | Il tipo di contenuto specificato per il BLOB. |
| contentLength | integer | La dimensione del BLOB in byte. |
| blobType | string | Il tipo di BLOB. I valori validi sono "BlockBlob" o "PageBlob". |
| url | string | Percorso del BLOB. <br>Se il client usa un'API REST BLOB, l'URL ha questa struttura: *\<storage-account-name \>. blob.core.windows.net/\<container-name \> / \<file-name \>* . <br>Se il client usa un'API REST di Data Lake Storage, l'URL ha questa struttura: *\<storage-account-name \>. dfs.core.windows.net/\<file-System-name \> / \<file-name \>* . |


## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti della documentazione relativa all'archiviazione BLOB:

- [Filtrare gli eventi di archiviazione BLOB](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Procedure consigliate per l'utilizzo di eventi di archiviazione BLOB](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

In questa esercitazione sono stati pubblicati eventi mediante la creazione o l'eliminazione di BLOB in un archivio BLOB di Azure. Vedere le altre esercitazioni per informazioni su come eseguire il provisioning di eventi nel cloud (hub eventi di Azure o hub Internet Azure): 

- [Inviare eventi a griglia di eventi di Azure](forward-events-event-grid-cloud.md)
- [Inviare eventi all'hub Azure.](forward-events-iothub.md)